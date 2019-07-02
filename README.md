# All Thumbs - Rules of Thumb for Writing Maintainable Code

## Mission Statement

The goal is for this repository to consist of a living set of documents
of rules of thumb I currently believe to be helpful in writing
maintainable software.

It's a place for me to point to (or look up) when someone asks me why
I made a particular decision, or why I'm advocating for a certain change
in code review: it's easy for some of these to turn into muscle memory,
but writing down the rationale, and periodically examining it is a good
way to check that I'm not doing things just because I've been doing them
for a long time.

I will attempt to link to sources where I remember them, because
it's unlikely that any of these are of my own invention. (Please feel
free to open an issue if you know of a source I should add.)

I will also try to be very specific about when and where a particular
rule of thumb does or does not apply. To be very clear up front:
treating any of these as absolute laws is not going to lead to good
results. In most cases, existing practices and patterns in your project
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
* [Python](#python)
  * [Use dateutil tzinfo Objects Over pytz Ones Where Possible](#use-dateutil-tzinfo-objects-over-pytz-ones-where-possible)
* [Code Organization](#code-organization)
  * [Group Related Objects, Not Similar Objects](#group-related-objects-not-similar-objects)
  * [Optimize Imports For Readability](#optimize-imports-for-readability)
  * [Avoid Wildcard Imports](#avoid-wildcard-imports)

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

See also: <https://docs.pytest.org/en/latest/goodpractices.html#tests-outside-application-code>

### Python

#### Use dateutil tzinfo Objects Over pytz Ones Where Possible

pytz tzinfo objects have a very unusual API, and are too easy to do the
wrong thing with (the `.replace` method, for instance, does something
that is almost guaranteed to not be what you want.)

In cases an underlying library forces pytz tzinfo objects on you, take
great care to read up on how the `.localize` and `.normalize` methods
work, and generally *don't* use `.replace`.

Sources:

* [Paul Ganssle, Working With Timezones: Everything You Wish You Didn't Need to Know (Pycon 2019)](https://www.youtube.com/watch?v=rz3D8VG_2TY)

### Code Organization

#### Group Related Objects, Not Similar Objects

```python
a = 'foo'
last_char_a = a[-1]

b = 'bar'
last_char_b = b[-1]
```

rather than:

```python
a = 'foo'
b = 'bar'

last_char_a = a[-1]
last_char_b = b[-1]
```

While the second way to write this may look neater, it hides
opportunities to refactor. Putting variable definitions as close as
possible to their earliest usage, and clumping their usages together
makes recurring patterns that can be factored out into functions,
methods or even classes more obvious.


#### Optimize Imports for Readability

Import names so that their use is clear and not repetitive. For
instance:

```python
from datetime import datetime, timedelta

five_days_ago = datetime.now() - timedelta(days=5)
```

rather than:

```python
import datetime

five_days_ago = datetime.datetime.now() - datetime.timedelta(days=5)
```

Blanket recommendations to always import whole modules, or always
explicitly import everything you need explicitly from the module it's
defined in are too rigid, in my opinion.

The other side of this is: when defining classes and functions you
expect to be used by others, give thought to how they will be imported
and used when naming them and the modules they are in, and try to
minimize repetition between all the elements of the full
namespace/import path where possible. So `accounting.models.Ledger`,
rather than `accounting.accounting_models.LedgerModel`: This gives users
of your code the choice to import and use the model as just `Ledger`
where that name is unambiguous, but if there are other things named
`Ledger` they can use `models.Ledger` or even
`accounting.models.Ledger`.

Whether you have any choice in the way you import is language dependent,
but it's true for at least Python and Scala. Giving thought to naming of
modules is always recommended.

See also: [Avoid Wildcard Imports](#avoid-wildcard-imports)

#### Avoid Wildcard Imports

```python
from accounting.models import Ledger
```
or:

```python
from accounting import models
```

rather than:

```python
from acounting.models import *
```

Wildcard imports make it harder to see where a name in the code is
defined just by scanning the code, and make it harder to see where names
defined in one module are actually used. (Which, among other things,
makes it harder to see if they are used at *all*, or could be removed.)

If you're using Python, and are the proud owner of an existing code base
where wildcard imports are prevalent, I recommend this excellent tool as
a quick and safe way to get rid of them:

<https://github.com/zestyping/star-destroyer>

See also: [Optimize Imports For Readability](#optimize-imports-for-readability)
