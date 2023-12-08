# Backend and HTMX

## virtues of htmx, but also critique

Whether or not this backend-first approach, wherein state is represented as HTML and provided by backend templating rather than frontend framework logic, is the ideal world or a dystopia I guess is in the eye of the beholder. It is easy to say that it *should* be the backend that determines what HTML is rendered: the front-end is laid out and open for all to see; it's easy to change it, and potentially dangerous to do so without relying on the backend for auth checks; it's a more controlled environment, and it's less error-prone to make decisions where the data originates. But that's only part of the story: htmx makes calls to the backend feel like using a front-end SPA most of the time, but there are other times when ...

In general, I think the conclusion I've come to is that the backend should do backend things and the frontend should do frontend things, and the developer needs to be smart enough to know where to draw the line. Knowing where that line should be drawn is the hard part. Here are two important axioms that you must keep in mind, however:

1. The majority of the work should happen on the backend. Front-end frameworks mostly have this wrong.
2. If you want interactive applications, you need to write some JavaScript. Some things can't be represented by backend state. htmx has this wrong.

Let's interrogate these two points a bit. I'll start with #2, because I think that's easier; there are more React- and Vue- and Angular- and Svelte-users out there than there are WordPress devs cranking out MPAs. (among points, htmx isn't really wrong -- but the discourse surrounding it seem to breed contempt for JS)

To illustrate Point #1, and in general get at the point of this article, let's talk about my work on the Radicle app.

For those of us who are very used to doing everything on the frontend, this feels very strange. If you're old enough... blah blah. But if you've spent the last few years living in Framework Land like I have, it's very tempting to see the backend as a set of distributed APIs rather than a source of truth. My immediate criticism of htmx was that it was pushing too much work off on the server. You're re-rendering whole pages just to get a fragment of updated content? You're doing all that processing on the server for possibly tens or hundreds of thousands of requests when it could be done on each individual client?

But we were already *doing* a lot of work on the server. Our API and our cloud server, which managed connections to IoT devices and to the apps, were constantly churning through database requests for stored protobuf data, converting them into megabytes of JSON, and sending them to the frontend... where, of course, the data was processed again, converted to and from protobuf values on the front-end and mapped to HTML structures. What's more, it was going just fine and dandy. What was struggling to keep up was the frontend.

htmx doesn't account for WebSocket servers. (Early on, we explored gRPC, and decided it didn't work for our use case.) It doesn't account for frontend-only interfaces.

The web doesn't need to be simple, but the stuff that runs on user's browsers NEEDS to be simple. Or at least as simple as possible. (What about Mapbox or Canva or design software?)

(note - multiple apps showing different lab statuses)