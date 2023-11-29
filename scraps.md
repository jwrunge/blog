# Scraps

Ever since JQuery showed that the web could be more than just an assortment of static pages, we've been scrambling to find newer, better ways to build apps in the web. And in so doing, we've added so much bloat and complexity to our apps that our build pipelines are beginning to rival those of our C++-writing brothers and sisters.

It's perfectly reasonable to use Node.js to orchestrate a build system that transpiles your TypeScript to JavaScript, bundles it up using non-Node-compatible module syntax, polyfills for Node-compatible (but not browser-compatible) node modules, rewrites parts of it to be compliant with a wider range of browsers, extracts SCSS from your code, compiles it to CSS, re-injects it back into your code, minifies your bundle, and adds source maps so that your single line of bundled code can be traced back to the 10,000 lines of code that it came from. Tell me that's not at least as complex as CMake.
