# Writing an ES6 Dependency Updater in Node.js

If there's anything I enjoy as much as building apps, it's distracting myself from building apps by building tools to help me build apps.

I've been working on a hybrid-native app in Svelte using Ionic's Capacitor library to integrate native Android and iOS features and prepare the build files. As soon as I got my head around the individual steps to deploy to the web, Android, and iOS, the first thing I did was to write up a little script to automate versioning increases and syncing the version numbers across the different platforms and as Git tags. No more confusing version numbers!

But now I'm facing a new problem: I'm doing a bit (a lot) of refactoring across 50-60 Svelte and TypeScript files, and that involves moving exported functions out of rapidly bloating modules into their own, and just generally doing some reorganizing. If I were just to rename the module files or move them to another location, VSCode (or some extension?) does a great job of updating the imports in other files. But more granular moving around? That's *minutes* of work hunting down those imports and manually extracting them into separate ones. If there is one thing I can't stand, it's doing an occasional 15 minutes of work that can be solved with an hour of work once.

We need to write a script!

## Getting started

