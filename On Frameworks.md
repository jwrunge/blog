## Intro

Job boards are advertising more for "React developers" instead of "JavaScript developers." Svelte is the most-loved framework on StackOverflow. More?

...

It's easy to lose sight of what the web *used* to be like: mostly markup and styling, with a bit of JavaScript for that extra *oomph.* Web code was small; inefficent, but fast. It was easy to write and easy to read. It was easy to change and maintain. I'm not saying that being able to make whole-ass apps that run in your web browser is a bad thing; it's kind of my thing, actually. What I am saying is that we lose a lot of simplicity and flexibility by tying ourselves to frameworks and build systems, and I think that complexity actually strains the potential for the web to do and to be even more than it is.

You probably already guessed I'm going to be talking about [htmx](./), but believe it or not, this is more about Svelte than anything. Or really, it's about frameworks and the things we sacrifice on their altars. Spoiler alert: much as I love Svelte, I'm going to be talking about where its frameworkiness rears its ugly head. I'll be calling out some pain points and suggesting what we, as a community of frontend devs, can be on the lookout for as we seek to build better tools and better apps.

## Disclaimer!

Full disclosure, I wrote reactive state library called [Copper](./). Yes, *another one*. Look, I'm not selling anything here (although if you like this article, I'm not opposed [to a coffee](./)).

Let me make you a promise: this article is *not* about Copper. It's about why I rebuffed React's advances, fell in love with Svelte, had a brief affair with htmx, and decided to build my own wife from scratch. I mean, write my own library. It's about why I felt that was necessary - about the state of frontend development - not about the library itself.

Here's the deal: I'll talk specifically about Copper *only* briefly, *only* in the last section. Check it out if you want. Skip over it if you want.

Now let's talk frameworks.

## Section about liking Svelte a lot

For me, Svelte is everything that a framework should be: it's small, it's fast, it's pretty much just JavaScript, but if JavaScript were a lot cooler. It works with pretty much any other library you want to use, syntax is dead simple, reactive statements are wicked powerful, and derived stores? *\*chef's kiss\** Compare that to the bloat and lock-in of React, with its cumbersome JSX syntax, its complex tooling, and - *blech* - it's popularity and ubiquity. (Yes, I like an underdog. More on that in a bit.)

Cool story, guys, envy me: When I interviewed as a software engineer with my current employer, I was the first front-end guy on the team. I remember explicitly saying, "All of my work so far has been in JQuery or Vue or Svelte, but I could pick up React if you want me to build with something more familiar as you hire more frontend developers." My interviewer, now my manager - a C developer and electrical engineer - said, shrugging, "I don't care what you use." I was hired. No joke - someone payed me, a dude who has never written more than a few lines of React, to write a hybrid-native app in Svelte. Full time. For two years and counting. Follow your dreams, kids.

Look, I *love* Svelte. I've written and maintained a big boy, whole-ass app (technical term) in Svelte, and you know, there's an old saying: "You always hate the ones you love."

I might be getting that quote wrong, but it's true. I don't know many people who aren't more critical of their spouses, their children, *themselves* than they are anybody else. It's true for the languages you choose, it's true for for the tools you use, and it's especially true for JavaScript frameworks. You are most critical of the things you know best. Familiarity breeds contempt. (Oh shoot, that's a phrase, isn't it? I should have led with that one.)

## Section about hating Svelte

Look, I still love Svelte. I would choose it over any other framework that's out today. But it's still a framework. There is still lock-in. There is still some inflexibility.

Our app is used seasonally, and in the lead-up to Fall (when our app would be in heaviest use and under the most scrutiny), I wanted to do a bit of refactoring. I wanted to make the app more modular, more easily maintainable, more visually consistent. I wanted to shore up some shoddy UI by ditching our custom design system and switching to [Ionic](./). I pitched it as a UI refactor - quick and painless, no touching the business logic. I also endeavored to yank out all the goofy custom routing and bring in [SvelteKit](./) to handle all that. It should take a week, tops.

It took three months.

Here's what slowed me down:

1. Tooling. Vite, rollup, adapters, and TypeScript.
2. So... routing still sucks.
3. Pages aren't pages. The problem with frontend templating.
4. SPA means no MPA
5. Frontend business logic and the allure (and far-off promise) of htmx.
6. The Svelte store debacle.

## What htmx taught us

Given framework fatigue and the ubiquity of React and React-likes, it's not hard to see why htmx became such a hot topic this past year: it's very much the antithesis of what frontend development has become; a return to the old days when business logic and templating happened on the backend, but without trading in the aesthetics and UX of SPA-style reactivity. It's no wonder [Fireship awarded htmx "JavaScript framework of the year,"](./) even though htmx *is NOT* a framework, *STOP*.

But intrigued as I am with the promise of htmx, I also understand why [so many are skeptical](./). The htmx website itself is not shy about saying it isn't a solution for every problem. htmx assumes a lot about your infrastructure: namely, that your server serves HTML and not JSON or protobuf or random bits of text. It also shifts what may be a lot of processing that can be done for free on your millions of clients to your maybe singular server, an exchange that can cost a good deal of money.

I also understand why some may be suspicious of new frameworks in general (even if they aren't frameworks). We get new do-everything frameworks and libraries, it seems, about every other month now, and chasing the new shiny gets old. Framework fatigue is real. Even for not-a-frameworks.

Critically, while htmx isn't a fit for every situation and every developer; and it isn't the end-all, be-all solution for building web apps; it *has* proven to be a catalyst for conversation about the state of the web: what it has been, what it is, and where it has the potential to go. It has demonstrated that, despite our best efforts to shove our square-peg little apps into JavaScript's round hole, those apps could still do with some rounding out.

## Synthesize discussions of svelte and htmx

Svelte's flaws aren't really flaws, as far as frameworks go. They're tradeoffs, and they are minimal compared to other frameworks.

Svelte's pain points are so minor, compared to something like React, that they are hard to notice, expecially at smaller scales. But to be completely fair to React for a change, it suffers mainly because it was among the pioneers of the reactive front-end and because it remains so popular that it can't change too drastically. And as Svelte gains popularity, there will inevitably be more and more resistance to change. Without commenting on the [upcoming Svelte 5 and its runes](./) and regardless of whether or not it is the right direction for Svelte, it is safe to say [reactions have been mixed](./) at best. This is not unlike [the pushback SvelteKit received in its transition to 1.0 this year](./).

And that's the thing, really. When a framework changes, your entire app changes. I can stay on Svelte 4.0 forever, really, or use Svelte 5's promised backward compatability, but practically I'll need to move to 6.0 one day, which may require a nearly ground-up rewrite. It feels petty to groan about work that I may have to do in three or four years' time, and really I won't mind it, but it does make me wonder why I have to be locked in to a framework at all. Why can't I implement the reactivity I choose, the templating I choose, the server-side rendering, the component system, the animation and transitions system? Why can't I swap them in and out as I please, a la carte, without months of refactoring?

Oh, I can. I can use htmx with a [Laravel](./) backend, and web components, and [Alpine.js](./) for frontend reactivity, and [GreenSock]() or [Anime.js]() for transitions.

The solutions are all out there, but they're not all in one place, and they aren't all super convenient (*cough* web components *cough*). But I think, as app builders, we need to start branching out more and relying less on all-in-one frameworks; and as tool makers, we need to start thinking about how to provide experiences that are as convenient as frameworks and as flexible as libraries, but without compromizing on what should be a central feature of the web: simplicity.

I think, collectively, we're starting to understand what we want. Let's start making it.

## Copper

So, here we are. Money where my mouth is.

I wrote a thing. I called it Copper... because in my head, I was making something that was like the wiring in your house: something that you can plug into to link up and power all your devices and appliances, something that links up to a larger power source, but that stays hidden and inconspicuous in your walls. It's flexible, it's modular, it's easy to add or remove from a project. It requires no build system and is made with progressive enhancement in mind. (Ah... you forgot about progressive enhancement in all this framework hullabaloo, didn't you?)

It's *very* small for what it does. It's so small, it's smallness actually made it harder to write instead of easier. I agonized over *bytes* of this thing, let me tell you.

This is what Copper was designed to be. This is what it became. Heavily inspired by Svelte and grudgingly inspired by htmx, with some cues from Vue. But wicked small and dead simple.

Copper consists of three modular parts:

Copper achieves these goals:

Here are some examples of how it works:

I'll stop talking it up now. I think it's pretty good. I'm going to use it. Don't tell my boss, but it might replace Svelte in some future version of the app.

Check the github page to see the code, browse the docs, or try it out. I'd love to hear your thoughts.
