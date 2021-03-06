# SmartProcure Contributor Coding Guidelines #

This document contains guidelines for contributing to SmartProcure code, across all repositories. It is designed to eliminate confusion and shorten PR feedback. These are common forms of feedback and exist on top of our programmatically enforced rules (like test coverage requirements, standardjs, etc. which might differ slightly per project).

## Mechanical Applicable (e.g. Potentially ESLint-able) ##
- Always use explict composition over implict
  - (e.g. to calculate the average of a `grade` property, prefer `flow(map('score'), reduce(sum), divide(grades.length))(grades)` over `divide(grades.length, reduce(sum, map('score', grades)))`
- Create temporary variables for flows iff:
  - They are reused in more than one place, _OR_
  - They require a comment documenting _what_ they do (so use a name instead of comment)
- Destructure parameters in the function definition when possible
  - e.g. prefer `({firstName, lastName}) => handlePerson(firstName, lastName)` over 
    ```js
      person =>
        let {firstName, lastName} = person
        return handlePerson(firstName, lastName)
    ```
- Aim for arrow functions that are simple enough to avoid a return

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
- When modeling state, it's usually a good idea to try to use a similar or exact structure to what is going to and from the server. This eliminates extra mapping code and simplifies everything - but this also goes both ways. Try to use a single structure in both the db and gui that work well where possible.
- Broad browser support
  - Many of our clients are government agencies and small businesses who use older browsers.  All code should support IE10 and above, and all versions of Chrome, Firefox and Safari
- Including external libraries at run-time
  - No external library loaded at run-time should be able to prevent our application from running (i.e include a try-catch).  Although the particular feature may fail, the rest of the application should continue running. 

## Some Clarifications ##

#### Async/Await ####
Async/Await is a _direct_ syntactial sugar to promises and doesn’t force anything about control flow on your code. Every bluebird method still works, it’s just sugar on `.then`. If you want a few methods to run in parallel, you can `await Promise.all([asyncMethod1(), asyncMethod2()])`. You can also still use `Promise.props`, `Promise.race` and all the other fancy things. There is generally no situation in which async/await is not better than directly using promises, with a few exceptions for serious edge cases (e.g. async uses native promises which are still slower than bluebird promises). If anyone has any questions on this please let me ([Sam](https://github.com/daedalus28)) know and we can go over them--and if there’s enough confusion, it might be worth adding some notes about this to our onboarding doc.

#### Futil-js Conversions ####
We have standardized conventions for lodash conversion methods in futil. We spent a fair amount of time debating what the names should be, and every developer who was here at the time had the opportunity to comment on it. Things ending in `In` are flipped, things ending in `On` are mutable, and we don't currently have one for uncap because so far we haven't actually needed it (e.g. things like `_.toPairs` tend to be more explict). If these names are unclear and someone has a better suggestion, let's open an issue on the github repo and discuss it - but no developer at SmartProcure should be unfamiliar with the methods in futil. We even maintain the most meticulous, detailed changelog of anything we've ever produced and enforce 100% test coverage.


## Development Process ##

### Tickets ###
All development should happen on github issues which should be linked to whatever ticketing system is relevant to the project (typically, github). When creating a PR, be sure to include `Fixes #123`, where `123` is the id of the issue it closes in the _body_ of the PR as per [this article](https://github.com/blog/1506-closing-issues-via-pull-requests). Make sure github issues are properly linked to the relevant ticketing system - and if they can't be, include a link to the github issue and ticket as comments in each system.

### Statuses ###
Also, be sure to set the proper status labels on PRs - `Needs Review` is used when a PR is ready for feedback, and `In Progress` are things you're working in. `Ready` is for things that are ready for you to work but aren't in progress. PRs will be set to `Ready` after review if there are changes requested. Waffle.io provides a nice interface to this workflow because you can drag tickets between columns to change statuses.

### Pull Requests ###
When you open a PR, make sure to **review it yourself before asking for feedback**. Chances are you'll find something that can and should be better - and chances are you're just going to waste someone's time if you don't fix those things first. Make sure the PR looks good to you and that you've made a solid effort to produce something that is clean, concise and easy to understand - really _read_ your diffs, because that's exactly what you're expecting the reviewer to do. And remember that the review process isn't perfect, so just because something is elsewhere in the codebase, it doesn't mean it's the right way to do it. If you see a pattern in the code, don't just blindly copy it. If it looks messy to you - _ask someone_ about it. There's a good chance it can be improved, too.

### Flow ###
GH Issues and PRs should stay assigned to the person working on it throughout the process. Once PRs are ready for review, add the review in the reviewer column and change the status to needs review - but don't reassign the issue or PR to the reviewer.
