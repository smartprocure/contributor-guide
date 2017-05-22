# SmartProcure Contributor Coding Guidelines #

This document contains guidelines for contributing to SmartProcure code, across all repositories. It is designed to eliminate confusion and shorten PR feedback. These are common forms of feedback and exist on top of our programmatically enforced rules (like test coverage requirements, standardjs, etc. which might differ slightly per project).

## Mechanical Applicable (e.g. Potentially ESLint-able) ##
- Always use explict composition over implict
  - (e.g. to calculate the average of a `grade` property, prefer `flow(map('score'), reduce(sum), divide(grades.length))(grades)` over `divide(grades.length, reduce(sum, map('score', grades)))`
- Create temporary variables for flows if:
  - They are reused in more than one place, _OR_
  - They require a comment documenting _what_ they do (so use a name instead of comment)
- Put flows on multiple lines if:
  - There are 3 or more steps (like the example above) _OR_
  - There are two steps and they are dense (subjective, but an easy indicator is if line length is approaching 80 characters)
  - e.g. instead of `flow(map('score'), reduce(sum), divide(grades.length))(grades)`, prefer
    ```js
      flow(
        map('score'),
        reduce(sum),
        divide(grades.length)
      )(grades)
    ```

## General Things to follow ##
- Use lodash/fp
- Use futil-js
- Avoid `this` and try to keep methods as pure as possible
- Use modern js with spread and destructuring, use async/await, use lodash method shorthands, etc.
- When possible, try to make the structure of the result of the function as self evident as possible (e.g. prefer push/concat over unshift, etc.)
- Try to write methods as a single flow
  - All functions can be created as a single flow--in many functional languages it is the only option
  - If you're having a lot of trouble making something a single flow, it's probably a sign that it needs to be broken into smaller methods
- Keep state minimal and derive as much as possible
  - With mobx/knockout, use computeds over manually updated observables/subscriptions
  - With redux, use selectors
- Broad browser support
  - Many of our clients are government agencies and small businesses who use older browsers.  All code should support IE10 and above, and all versions of Chrome, Firefox and Safari
- Including external libraries at run-time
  - No external library loaded at run-time should be able to prevent our application from running (i.e include a try-catch).  Although the particular feature may fail, the rest of the application should continue running. 

## Some Clarifications ##

#### Async/Await ####
Async/Await is a _direct_ syntactial sugar to promises and doesn’t force anything about control flow on your code. Every bluebird method still works, it’s just sugar on `.then`. If you want a few methods to run in parallel, you can `await Promise.all([asyncMethod1(), asyncMethod2()])`. You can also still use `Promise.props`, `Promise.race` and all the other fancy things. There is generally no situation in which async/await is not better than directly using promises, with a few exceptions for serious edge cases (e.g. async uses native promises which are still slower than bluebird promises). If anyone has any questions on this please let me ([Sam](https://github.com/daedalus28)) know and we can go over them--and if there’s enough confusion, it might be worth adding some notes about this to our onboarding doc.

#### Futil-js Conversions ####
We have standardized conventions for lodash conversion methods in futil. We spent a fair amount of time debating what the names should be, and every developer who was here at the time had the opportunity to comment on it. Things ending in `In` are flipped, things ending in `On` are mutable, and we don't currently have one for uncap because so far we haven't actually needed it (e.g. things like `_.toPairs` tend to be more explict). If these names are unclear and someone has a better suggestion, let's open an issue on the github repo and discuss it - but no developer at SmartProcure should be unfamiliar with the methods in futil. We even maintain the most meticulous, detailed changelog of anything we've ever produced and enforce 100% test coverage.
