# Personal Software Design Principles

(TODO: order the principles) In no particular order:

## General Software Design
- __Everything is single responsibility__: functions, modules, services, tools.
  Look to solve for a specific case first, then periodically look for ways to
  make the system more generic.
- __You should be able to hold all components/concepts of a system in your
  head__: Having too many parts to think about is an indication that the system
  needs to be decomposed into smaller subsystems
- __Treat everything as immutable and throwaway__, including core pieces of
  code, services, data, infrastructure. All pieces should be easily swappable -
  Decoupling over local efficiency and tight integration. "High cohesion, low
  coupling"
- __Don't try to build complex pieces of software. Look to glue existing single
  responsibility components together in simple ways.__ Bias towards using
  preexisting solutions instead of building your own. Big systems aren't built
  all at once, they're built in phases out of smaller subsystems
- __Be wary of code rot.__ Address it ASAP. The longer it rots, the harder it
  will be to get rid of it. If new requirements can't be fulfilled within the 
  contraints of an existing design, consider whether the system needs to be 
  redesigned before "bolting new functionality on" to the existing system, which
  weakens/muddles/mutates the overall design. Rewriting a system shouldn't be as
  expensive as one might think if it already uses modular components
- __No circular dependencies anywhere.__ Dependency graphs should be strict
  DAGs. Dependencies flow only in one direction

## Code Style
- __Readability/maintainability over conciseness/"elegance"/doing as many things
  with as little as possible.__ Also weigh readability over marginal increases
  in performance
- __Care about readability.__ Much more time is spent (re)reading code than
  writing new code
- __Defend against code rot.__ Because enterprise software tends to outlive the
  tenure of those who work on it and multiple contributors will touch any piece
  of software, the original design intent should be very clear (clear interfaces
  and docs). Subsequent contributors should make sure to understand the original
  intent and be very careful about mutating the design
- __Refactor one thing at a time__

## Testing
- __Smaller, faster, more targeted tests are preferred.__ This means writing
  code with easily testable interfaces at all levels of abstraction
- __Developers are responsible for their own tests and their own quality.__ Yes,
  it can be useful to get an outside perspective when testing, but testing code
  and writing code are usually coupled such that the quality of the tests
  depends on the quality of the code and the quality of the code depends on how
  the code will be tested. Therefore it makes sense for the same
  person/role/party to be responsible for both
  - Corollary: __Having tests does not guarantee quality code. Quality code
    guarantees quality code.__
  - Corollary: __Complex code results in complex tests.__ This is also why I
    think that "external" testers shouldn't be held responsible for
    quality--they have very little control over the actual quality of the code.
- __Fix bugs as quickly as possible.__ Bugs are more expensive to find and fix
  the longer they remain undiscovered. The longer a bug remains undiscovered,
  the harder it will be for the developer to remember the context around the bug
  and the more likely that additional code will be built in top of the bug. This
  has several implications:
    1. You need a fast feedback loop, which usually implies a fast test suite
    that runs often and good monitoring. __Tests need to be as fast as possible.__
    Not just because we as developers hate waiting to verify our code, but also
    because iteration speed is probably one of the most important competitive
    advantages in enterprise software.
    1. Prioritize fixing important bugs. Even non-important bugs can fester and
    grow in annoyance
    1. Write the smallest, fastest, most granular tests possible. Prefer unit
    tests to cover functionality when you can. If you can't, write integration
    tests. If you can't, write end to end tests

## Project Management
- __Under promise, over deliver.__
- __Only work on the most important problem.__
- __Work on one project at a time, scope each project to one thing, deliver
  value incrementally.__ This results in a faster feedback loop, reduces
  complexity and risk of the project completely failing, reduces amount of waste
  if the project fails, and makes it more likely that the components will be
  salvageable. A project with a 6 month timeline might have too big of a goal. 
  There are probably smaller milestones that it can be broken down into, which
  allow for more incremental delivery of value. Big projects have bigger risks,
  bigger opportunity costs, and more resistance to course-correcting. There's a
  difference between a project and a roadmap. 
- __Personal rule of thumb: 1.5-2 months is a good size for an infra/tooling
  project.__ This may or may not apply to product code.
- __Try to identify unknown unknowns and mitigate risks upfront.__ Do enough
  research upfront to prevent timelines from running away because you came across
  complicating factors later in the project.
- __Validate proofs of concept in the simplest way possible, leaving out any
  complicating factors initially.__ i.e. build POCs using toys rather than than
  trying to prove a concept with existing production code
- __Separate R&D/POCs from production implementation.__ Risks should be taken in
  a low-stakes setting (like a POC/R&D project) rather than when trying to ship
  something to production. Only vetted techniques and technologies should be used
  when shipping to production
- __R&D is expensive.__ Doing a unique task or using a new technology for the
  first time is going to take extra time because there are likely a lot of 
  unknown unknowns. 4x your time estimate as a rule of thumb
- __Iterating/experimenting quickly is more important than getting it perfect the
  first time around.__ Many times, the right answer/best solution/best product is 
  discovered rather than foreseen. Minimize the cost of experiments, and be able
  to roll back quickly.
- __Finish projects and do project retrospectives.__ The worst case scenario is
  spending 90% of the resources and reaping 0% of the value. Reap extra value from
  each project by learning from what went well and what didn't go well to improve
  future projects.
- __Infrastructure migrations are expensive__ because they are usually unique,
  one-off tasks with complicated edge cases. Budget accordingly.
- __Do not overengineer solutions.__ Solve for the problem at hand. Be proactive
  about updating the solution if requirements change rather than trying to
  anticipate requirements ahead of time.

## Useful References
- [Code Style](https://medium.com/coding-skills/clean-code-101-meaningful-names-and-functions-bf450456d90c)
- [12 Factor App](https://12factor.net/)
- [Packaging Gradient](https://sedimental.org/the_packaging_gradient.html)
- [Production Readiness Checklist](https://cdn-images-1.medium.com/max/2400/1*-nYI19LZZKDQjdAx0qhlFw.png)
    - [In More Detail](https://www.gruntwork.io/devops-checklist/)
