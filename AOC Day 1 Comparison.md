# Comparing Apples and Oranges: Rust and JavaScript in AOC 2023 Day 1

I just wrote [a rather long-winded article](https://dev.to/jwrunge/calibrating-an-elven-trebuchet-in-rust-advent-of-code-2023-day-1-15kj) sharing my experience completing the [Advent of Code 2023 Day 1 challenge](https://adventofcode.com/2023/day/1) in Rust. I *really* like Rust, but like most frontend-brained devs giving into the Stockholm Syndrome that JavaScript inflicts on us, I found myself constantly saying, "This would be 15 minutes and 10 lines of code in JavaScript."

Let's put that to the test.

If you haven't read my other article, don't worry about it -- you can pick it up later. Here, I'm just going to highlight some obvious differences between the two implementations, discuss how each "felt" to code, and then do some benchmarking, 'cause why not?

(spoiler alert: there are some surprising results in those benchmarks. Rustaceans, help me out here -- what's going on??? Or maybe this is some magic happening with Node???)

Context, if you need it: This code is meant to loop through 1000 strings and extract the first and last number (maybe typed out, like "eight," and maybe numeric, like 8), concatenate them (4 and 8 makes 48), then add their results.

## My 10 lines of JavaScript

Comparing the code length and development speed of writing this challenge in Rust vs. in JavaScript isn't really fair. For one, I'm a Rust newb, and I've been programming in JavaScript pretty much daily for a several years. Also, I wrote the Rust implementation *first*, knew it worked, and then followed the same pattern in the JavaScript. Heading in *knowing* the solution and how to arrive at it, knowing the language-agnostic gotchas, and slipping into the warm, comfortable embrace of JS, I was feeling pretty good.

That said, it wasn't all rainbows and sunshine. Here's my 10 lines of JS (Node) in 32 lines:

```javascript
const fs = require("fs");
const words = { "one": "1", "two": "2", "three": "3", "four": "4", "five": "5", "six": "6", "seven": "7", "eight": "8", "nine": "9" };
const lines = fs.readFileSync("./coords.txt", "utf8").split("\r\n");
let total = 0;

function getFirstOrLastNumber(line, last) {
    if(last) line = line.split("").reverse().join("");
    for(let char of line) {
        try { 
            const parsed = parseInt(char); 
            if(!isNaN(parsed)) return parsed;
        }
        catch(_) { true }
    }
}

for(let line of lines) {
    let newLine = "";
    for(let i=0; i < line.length; i++) {
        for(const word in words) {
            const substr = line.slice(i, i + word.length);
            if(substr === word) {
                newLine += words[word];
            }
        }
        newLine += line[i];
    }
    let newNum = `${getFirstOrLastNumber(newLine)}${getFirstOrLastNumber(newLine, true)}`
    total += parseInt(newNum) || 0;
}

console.log(total);
```

Look, it's not 10 lines, I get it. I could get it down to 1 line if I minified it, leave me alone.

The point is, it's over a third the line length of the Rust code. I didn't work as hard to break everything out into functions, but it also *feels* a lot less necessary here. Compared to Rust, it feels just much more natural to do string manipulation in JS.

I'm not going to get into time-to-completion much here, because there's really no objective way to look at it. I got the JS working mostly as-intended in about 15 minutes, I would guess. But again, I'm much more comfortable with the language; it's a lot higher-level, really well-suited for string manipulation; and *I was literally porting the Rust code I had already written.*

Despite getting it up-and-running so quickly, I actually spent another 20 minutes or so debugging. Why? Well, some weird stuff cropped up.

Notice that try-catch block in `getFirstOrLastNumber()`? Yeah, that's because I wasn't sure what would happen if I fed `parseInt` something that couldn't be parsed as a number. It turns out, `parseInt` always returns `NaN` in that case, whether you give it a string, or `null`, or `undefined`, or some random object. And `NaN` doesn't fail the try-catch block, so I was getting `NaN` for a lot of the strings I passed in.

(Fun fact: My previous statement about `parseInt` always returning `NaN` isn't actually true. `parseInt([4, 5, 6])` returns `6`. Isn't JavaScript great? /s)

To account for that, I added the `isNan()` check. The try-catch block is actually completely unnecessary. But it goes to show you how, even in a language I work with regularly, one that is ostensibly "simple" and "easy," there are some hidden complexities that make code behavior not so clear-cut.

The same goes for that `total += parseInt(newNum) || 0;` line near the end of the code. I pretty much superstitiously add `|| someDefault` after any function from which I expect a return value, because I never know if the return value will be falsey or not.

None of this is to say that JavaScript is behaving unpredictably or even inconsistently -- it's kind of a skill issue, I'll admit. And some of this would have alleviated had I used TypeScript. But at the end of the day, it also goes to show that JavaScript's attempts to hide complexity from the programmer are a double-edged sword that sometimes (often) result in a different, more frustrating kind of complexity.

## The Rust

I won't go into detail here -- you can check my other post for that. Since the last post, I did do a bit of refactoring to DRY the code out a bit. Here's the result:

```rust
use std::{fs, collections::HashMap};

fn get_calibration(filename: &str) -> i32 {
    let contents = fs::read_to_string(filename).expect("Something went wrong reading the file");
    let lines = contents.split("\n");
    
    let mut sum: i32 = 0;
    for line in lines {
        sum += number_from_line(line);
    }

    sum
}

fn number_from_line(line: &str) -> i32 {
    let replaced: String = check_for_word(line);
    let first_num = first_or_last_number(&replaced, false);
    let last_num = first_or_last_number(&replaced, true);
    let combo_str = format!("{}{}", first_num.unwrap(), last_num.unwrap());
    let as_num = combo_str.parse::<i32>().expect("Combo string should parse to i32");
    as_num
}

fn first_or_last_number(input: &str, reverse: bool) -> Option<char> {
    let new_str = match reverse {
        true => input.chars().rev().collect::<String>(),
        false => input.to_string()
    };

    println!("New string: {}", new_str);

    for c in new_str.chars() {
        if c.is_digit(10) {
            return Some(c);
        }
    }

    None
}

fn check_for_word(input: &str) -> String {
    let words: HashMap<String, &str> = HashMap::from([
        ("one".to_string(), "1"),
        ("two".to_string(), "2"),
        ("three".to_string(), "3"),
        ("four".to_string(), "4"),
        ("five".to_string(), "5"),
        ("six".to_string(), "6"),
        ("seven".to_string(), "7"),
        ("eight".to_string(), "8"),
        ("nine".to_string(), "9"),
    ]);

    let mut replaced = "".to_string();
    let mut i = 0;
    for char in input.chars() {
        for word in words.keys() {
            let substr = match input.get(i..i+word.len()).ok_or("Out of bounds") {
                Ok(str)=> str,
                _ => {continue;}
            };

            if substr == word {
                replaced.push_str(words.get(word).unwrap());
            }
        }

        replaced.push_str(char.to_string().as_str());
        i += 1;
    }

    replaced
}

fn main() {
    let coords = get_calibration("coords.txt");
    println!("Calibration: {}", coords);
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_first_num() {
        assert_eq!(first_or_last_number(&"ab12cd34ef", false).unwrap(), '1');
    }

    #[test]
    fn test_last_num() {
        assert_eq!(first_or_last_number(&"ab12cd34ef", true).unwrap(), '4');
    }

    #[test]
    fn test_check_for_word() {
        assert_eq!(check_for_word(&"jjhxddmg5mqxqbgfivextlcpnvtwothreetwonerzk"), "jjhxddmg5mqxqbg5fivextlcpnv2two3three2tw1onerzk");
    }

    #[test]
    fn test_check_for_word_nums() {
        assert_eq!(number_from_line("two1nine"), 29);
        assert_eq!(number_from_line("eightwothree"), 83);
        assert_eq!(number_from_line("abcone2threexyz"), 13);
        assert_eq!(number_from_line("xtwone3four"), 24);
        assert_eq!(number_from_line("4nineeightseven2"), 42);
        assert_eq!(number_from_line("zoneight234"), 14);
        assert_eq!(number_from_line("7pqrstsixteen"), 76);
    }
}
```

A lot bigger, huh? And apparently more complex.

But I'd argue it's *not*, really. Yeah, if you read my breakdown of the Rust code in my other post, you'll know I struggled a bit with this. But I don't think I struggled nearly as much with the Rust syntax as I did with the AOC prompt and figuring out how to transform those spelled-out numbers to numeric ones without mangling other spelled-out numbers.

I thought about types *WAY* more in my JS code than I did in Rust, where the LSP kindly annotated everything for me; the compiler clearly explained when my expectations didn't align with reality; functions clearly signaled when they might panic and variables had to be unwrapped and every potentiality accounted for.

I ran my Rust code completely confident that it would work the way I intended it to work, and that any errors came from a flaw in my logic, *not* from unclear expectations about what the code would do.

Also, that thing I said about the code being 3x as large as the JavaScript? It's also better structured, more readable, and more modular. Plus a third of it is built-in tests.

Oh yeah -- the tests! It's so nice to just plunk some tests in there and assert equality without having to resort to just randomly printing to the console or installing Jest or something. So, so good.

## Performance

We've come this far, why not? I know, it's a small, frivolous program and Rust is clearly faster. But I think this kind of gets at the heart of the matter -- whichever language you choose, you're making tradeoffs. With JavaScript, I'm sacrificing clarity, explicitness, and easy validation; with Rust, development speed, ease of expression, and lines of code. So what do these tradeoffs get you in terms of performance?

To test execution speed, I'll be using PowerShell's `measure-command` (I use Windows, btw -- what?), a rough equivalent to the `time` command in Unix. I'll run each 5 times and take the average. Not the most scientific comparison, I'm sure, and there may be folks out there with ideas of how to optimize one or the other -- let me know in the comments!

I've compiled the Rust code using `cargo build --release`.

Running each, we end up with: JavaScript, avg 39.92548ms; Rust: 18.3171ms. Rust is faster by ~54%.

Let's see if that scales. I'm going to run each on a loop (internal to the code, not via the command line) 1000 times and see if there is any slowdown.

...And we're back! JavaScript: 3260.6001ms; Rust: 6564.07532ms. Oof... Rust is more than twice as *slow*. That doesn't feel right...

I thought perhaps that Rust's file reader implementation just wasn't up to snuff, assuming Node's `readFileSync` implements some battle-tested C or C++ function, but no... removing file reads seemed to have little affect on either implementation.

Both programs scaled well; with 1000x the work, the JavaScript code was only ~82x slower and the Rust code was only ~358x slower. That leads me to believe that there is a warmup period factoring into the execution times (unless there is some kind of cacheing going on). But I would have expected the Rust code to maintain its lead over time.

I'm curious -- what could be the cause of this discrepancy? Is Node doing some kind of caching? Is there some inefficiency in my Rust code that is causing slowdown? If you have any insights, let me know in the comments!
