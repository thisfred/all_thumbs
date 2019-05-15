# All Thumbs: A Repository of Rules of Thumb for Writing Maintainable Code

## Mission Statement

The goal is for this repository to consist of a living set of documents
of rules of thumb I currently believe to be helpful in writing
maintainable software.

The rules themselves will probably mostly be at the level of
implementation patterns, as defined by Kent Beck's book of the same
title:

https://www.oreilly.com/library/view/implementation-patterns/9780321413093/

It's not that I don't have opinions at architecture/design level, but
they tend to be even more dependent on context, so it's not as easy to
formulate them as something pithy and easy to remember.

I will attempt to link to sources where I remember them, because
it's unlikely that any of these are of my own invention. (Please feel
free to open an issue if you know of a source I should add.)

I will also try to be very specific about when and where a particular
rule of thumb does or does not apply. To be very clear up front:
treating any of these as absolute laws is not going to lead to good
results: In most cases, existing practices and patterns in your project
or organization should trump these *opinions* wherever they conflict.

Even if you are starting a new project from scratch, try to build
consensus and document your own decisions and guidelines. I will try to
make the individual rules easy to link to.

I will try to slowly organize rules by topic, and not move things around
too much after that.

If I change my mind about certain rules, I will comment to that effect
and somehow mark the rule as deprecated, but not delete them.

I will try to come up with some guidelines that apply regardless of
environment or technologies used, but since I spend most of my time in
Python, it's likely that a fair number of them will be specific to that
language.

If you disagree with any of these opinions I invite you to try to change
my mind. Even if that does not succeed, I might add a note with the
counter argument, with your permission. I want to stress again though,
that these are opinions, and that holding different or opposite ones
won't make me think less of you as a software developer or human being.
:)

## Index

* [Tests](#tests)
    * [Separate Test Code From Application Code](#separate-test-code-from-application-code)

### Tests

#### Separate Test Code From Application Code

```
project/
  src/
    feature1/
    feature2/
  tests/
    feature1/
    feature2/
```

rather than:

```
project/
  src/
    feature1/
      tests/
    feature2/
      tests/
```

Organizing your project to have two separate directory trees for
production code and test code provides several benefits: Though test
code and production code are both important and should both be subject
to high quality standards, it's not always the *same* standards. I'm
often a lot more forgiving of code repetition between tests, if it aids
readability and makes each test tell a complete story.

This means that sometimes style checkers, linters and other static
analysis tools should be run with different configurations on test code
than on production code. That is a lot easier to do when they are
completely separate trees, than if they are intermingled.

Additionally, in interpreted languages, it becomes easier to package
and deploy *just* the production code to your server, and not the test
code, which is good practice and prevents production code ever depending
on parts of your test code.
