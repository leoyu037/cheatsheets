# Personal Software Design Principles

(TODO: order the principles) In no particular order:

## General Software Design
- Everything is single responsibility: functions, modules, services, tools. Look
  to solve for a specific case first, then periodically look for ways to make
  the component more generic
- You should be able to hold all components/concepts of a system in your head.
  Having too many parts to think about is an indication that the system needs to
  be decomposed into smaller subsystems
- Treat everything as immutable and throwaway, including core pieces of code,
  services, data, infrastructure. All pieces should be easily swappable
- Decoupling over local efficiency and tight integration. "High cohesion, low
  coupling"
- Don't try to build complex pieces of software. Look to glue existing single
  responsibility components together in simple ways. Bias towards using
  preexisting solutions instead of building your own. Big systems aren't built
  all at once, they're built in phases out of smaller subsystems
- No circular dependencies anywhere. Dependency graphs should be strict DAGs.
  Dependencies flow only in only direction
- Validate proofs of concept in the simplest way possible, leaving out any
  complicating factors initially. i.e. build POCs using toys rather than than
  trying to prove a concept with existing production code

## Code Style
- Readability/maintainability over conciseness/"elegance"/doing as many things
  with as little as possible. Also weigh readability over marginal increases in
  performance
- Account for code rot. Because enterprise software tends to outlive the tenure
  of those who work on it and multiple contributors will touch any piece of
  software, original design intent should be very clear (clear interfaces and
  docs). Subsequent contributors should make sure to understand the original
  intent and be very careful about mutating the design
- Refactor one thing at a time

## Testing
- Smaller, faster, more targeted tests are preferred. This means writing code
  with easily testable interfaces at all levels of abstraction
- Developers are responsible for their own tests and their own quality. Yes, it
  can be useful to get an outside perspective when testing, but testing code and
  writing code are usually coupled such that the quality of the tests depend on
  the quality of the code and the quality of the code depends on how the code
  will be tested. Therefore it makes sense for the same person/role/party to be
  responsible for both
- Fix bugs as quickly as possible. Bugs are more expensive to find and fix the
  longer they remain undiscovered. The longer a bug remains undiscovered, the
  harder it will be for the developer to remember the context around the bug and
  the more likely that additional code will be built in top of the bug. This has
  several implications:
    1. You need a fast feedback loop, which usually implies a fast test suite
    that runs often and good monitoring
    1. Prioritize fixing important bugs. Even non-important bugs can fester and
    grow in annoyance
    1. Write the smallest, fastest, most granular tests possible. Prefer unit
    tests to cover functionality when you can. If you can't, write integration
    tests. If you can't, write end to end tests

# Project Management
- Work on one project at a time, scope each project to one thing, deliver value
  incrementally. Faster feedback loop, less risk of project completely failing,
  smaller potential waste, more likely that the components are salvageable
- Personal rule of thumb: 1.5-2 months is a good size for a software
  (infrastructure/tooling) project. This may or may not apply to product code
