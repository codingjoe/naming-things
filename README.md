# _Naming Things_

**A humble effort to solve computer science's second-hardest problem.**

> [!TIP]
> **Usage:**
> Simply copy the following snippet into your `AGENTS.md` or `CONTRIBUTING.md` file.
>
> ```markdown
> When writing code, you MUST ALWAYS follow the [naming-things](https://raw.githubusercontent.com/codingjoe/naming-things/refs/heads/main/README.md) guidelines.
> <!--
> Bots/Agents MUST use:
> curl -sSL https://raw.githubusercontent.com/codingjoe/naming-things/refs/heads/main/README.md | head -n 500
> -->
> ```

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=2 --minlevel=2 -->

- [Classes and Functions](#classes-and-functions)
- [Unit Tests](#unit-tests)
- [Time](#time)
- [Abbreviations](#abbreviations)
- [Metrics, Measurements, and Units](#metrics-measurements-and-units)
- [Utils & Helpers](#utils--helpers)
- [Synonyms](#synonyms)
- [Avoid Negations](#avoid-negations)
- [Versioning](#versioning)

<!-- mdformat-toc end -->

# Preamble

> There are only two hard things in Computer Science: cache invalidation and naming things.
>
> — Phil Karlton

This document concerns natural language conventions, not syntax or code style. Rules are language-agnostic, but examples are given in Python.

[![Permanence
](https://imgs.xkcd.com/comics/permanence.png)](https://xkcd.com/910/)

## Classes and Functions

### Classes

Class names are nouns or noun phrases. Think German compound nouns. E.g., `UserProfile`, `OrderItem`, `PaymentProcessor`.

Class names are singular because while its instances may represent multiple entities (e.g., a `User` class representing multiple user instances), the class itself is a blueprint for a single entity.

#### Inheritance

Specialize, don't generalize. If you feel the urge to name a base class `BaseSomething` or `AbstractSomething`, go the other way.
Make children more specific, not parents more general.

##### Do's

```python
class Vehicle:
    """A means of transporting people or goods."""


class Car(Vehicle):
    """A road vehicle, typically with four wheels, powered by an internal combustion engine or electric motor."""


class SUV(Car):
    """A pedestrian death machine."""
```

##### Don'ts

```python
class BaseCar:
    """A road vehicle, typically with four wheels, powered by an internal combustion engine or electric motor."""


class SportsCar(BaseCar):
    """Only fun in Germany."""
```

### Functions

Function represents an action a caller can perform. Use verbs or verb phrases. E.g., `send()`, `calculate_total()`.

Function names must clearly communicate their external behavior, including side effects. E.g., `fetch_or_404()` makes it explicit that it may raise a 404 error.
They must not expose internal implementation details. E.g., avoid `send_via_smtp()`; use `send()` instead.

Loose functions should be the exception, not the rule. Prefer class methods or instance methods to group related functionality. If a function includes a noun in its name, it probably belongs to that noun's class. E.g., instead of `fetch_user_profile(user_id)`, implement `UserProfile.fetch(user_id)`.

### Methods

Avoid including object names, as the method is probably attached to the wrong class. E.g., instead of `user.send_email()`, use `UserEmail(user).send()`.

### Variables

Avoid assigning variables that are only used once. If a value is asserted immediately or returned in the next line without further access, use it inline.

## Unit Tests

Unit tests should match their API counterparts to be easily navigable and discoverable. A simple text search for a function or class must reveal both its implementation and tests quickly.

Test names use double underscores to separate the function or class name from the scenario (e.g., `test_get_user__ok`, `test_get_user__raise_value_error`). Test classes are prefixed with `Test` in Python or wrapped in `describe` blocks in JavaScript. Test descriptions must use imperative mood and avoid redundant words like "should", "expect", or "it". Assertion messages must add meaningful context beyond the assertion itself or be omitted. In Node.js, use strict assertions by default with `import assert from "node:assert/strict"` for shorter function names and stricter equality checks.

### Do's

```python
def get_user(user_id):
    """Fetch user from database."""
    ...


class TestGetUser:  # prefix with Test
    def test_get_user__ok(self):  # double underscore separates scenario
        """Return user when found."""  # imperative mood
        assert isinstance(get_user(1), User)  # inline single-use values

    def test_get_user__raise_value_error(self):  # descriptive scenario name
        """Raise ValueError when user ID is invalid."""  # meaningful context
        with pytest.raises(ValueError):
            get_user(-1)
```

```javascript
import assert from "node:assert/strict"; // strict assertions by default

function getUser(userId) {
    // Fetch user from database
}

describe("getUser", () => { // wrap in describe block
    test("ok", () => { // no "should", "expect", or "it"
        assert(getUser(1) instanceof User); // inline single-use values
    });

    test("raise ValueError", () => { // descriptive scenario
        assert.throws(() => getUser(-1), ValueError);
    });
});

// assertion messages add context or are omitted
assert.ok(user.age >= 18, "User must be adult to access premium features");
assert.equal(user.name, "Alice"); // omit when self-explanatory
```

### Don'ts

```python
def test_validate_email(self):
    """It should return True when email is valid."""  # avoid "should", "it"


def test_validate_email(self):
    """We expect True for valid emails."""  # avoid "expect"


def test_create_user__ok(self):
    """Return user instance."""
    user = create_user("Alice")  # unnecessary variable
    assert isinstance(user, User)
```

```javascript
test("validateEmail", () => {
    // It should return true when email is valid  // avoid "it", "should"
});

test("createUser__ok", () => {
    const user = createUser("Alice"); // unnecessary variable
    assert(user instanceof User);
});

assert.equal(user.name, "Alice", "user.name should equal Alice"); // repeats assertion
```

## Time

_[Time zones are hard](https://www.youtube.com/watch?v=-5wpm-gesOY); don't make it harder._

### Events & Points in Time

Points in time should always have a little `at`-suffix to communicate they represent a specific moment rather than a duration or interval.

Furthermore, they must be in the language's date type (e.g., `datetime` in Python, `Date` in JavaScript) as well as timezone-aware.

Hindsight is 20/20; name all dates in the past tense, e.g., `created_at`, `updated_at`, `deleted_at`. Even if the event is in the future, e.g., `scheduled_at`, `expired_at`, `started_at`. Time passes. By the time you are debugging code, everything is in the past.

Avoid locale-specific string representations or include a timezone suffix. Suffix dates according to their [IANA timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).

#### Do's

```python
import datetime

# base case
created_at: datetime.datetime = datetime.datetime.now(tz=datetime.timezone.utc)

# local date with timezone
created_at_europe_berlin: datetime.date = datetime.date.today()
created_at_utc: datetime.date = datetime.date.today()

# UNIX Epoch timestamp
created_at_ts: float = datetime.datetime.now(tz=datetime.timezone.utc).timestamp()
# ISO 8601 string with timezone
created_at_iso: str = datetime.datetime.now(tz=datetime.timezone.utc).isoformat()
```

#### Don'ts

```python
import datetime

# present tense
start = datetime.datetime.now(tz=datetime.timezone.utc)

# no event suffix
created = datetime.datetime.now()

# naive datetime
created_at: datetime.datetime = datetime.datetime.now()
```

### Durations and intervals

Durations should be either unambiguously typed (e.g., `timedelta` in Python, `Duration` in Java) or have a suffix indicating the unit of time (e.g., `secs`, `ms`, `mins`, `hours`, `days`).

#### Do's

```python
import datetime

# typed duration
timeout: datetime.timedelta = datetime.timedelta(seconds=30)
# unit suffix
timeout_secs: int = 30
timeout_ms: int = 30000
```

#### Don'ts

```python
# no interval specific type or unit
timeout: int = 30
```

## Abbreviations

> Abbreviations rely on context you may or may not have.
>
> — [CodeAesthetic](https://www.youtube.com/watch?v=-J3wNP6u5YU)

[![Nomenclature](https://imgs.xkcd.com/comics/nomenclature.png)](https://xkcd.com/1221/)

**Don't use abbreviations!**

Unless… they are technical acronyms that are universally known outside your team's domain, e.g., `HTML`, `URL`. Use them if they are more common than their unabbreviated counterparts.

### Do's

- **HTML** (HyperText Markup Language)
- **URL** (Uniform Resource Locator)
- **URI** (Uniform Resource Identifier)
- **CPU** (Central Processing Unit)
- **GPU** (Graphics Processing Unit)
- **RAM** (Random Access Memory)
- **JSON** (JavaScript Object Notation)
- **XML** (eXtensible Markup Language)
- **HTTP** (HyperText Transfer Protocol)
- **HTTPS** (HyperText Transfer Protocol Secure)
- **FTP** (File Transfer Protocol)
- **SMTP** (Simple Mail Transfer Protocol)
- **DNS** (Domain Name System)
- **TLS** (Transport Layer Security)
- **SSL** (Secure Sockets Layer)
- **TCP** (Transmission Control Protocol)
- **UDP** (User Datagram Protocol)
- **SQL** (Structured Query Language)
- **API** (Application Programming Interface)
- **GUI** (Graphical User Interface)
- **IDE** (Integrated Development Environment)
- **OS** (Operating System)
- **IPv4** (Internet Protocol Version 4)
- **IPv6** (Internet Protocol Version 6)

### Don'ts

- IP – could mean `Intellectual Property`, use `IPv4` or `IPv6`
- temp – use `temporary` or `temperature`
- addr – use `address`
- num – use `number`
- cnt – use `count`
- cfg – use `config` or `configuration`
- msg – use `message`
- calc – use `calculate` or `calculation`
- init – use `initialize` or `initialization`
- var – use `variable`
- obj – use `object`
- func – use `function` or `method`
- btn – use `button`
- usr – use `user`
- pwd – use `password`
- db – use `database`

## Metrics, Measurements, and Units

### Units

Add an explicit unit suffix to all measurements. Use [SI unit symbols](https://en.wikipedia.org/wiki/International_System_of_Units#Unit_symbols) for brevity.

When persisting metrics, consider using SI (metric) units instead of [freedom units](https://en.wiktionary.org/wiki/freedom_units), as they are the international standard and simplify conversions.

#### Do's

```python
class WeatherReport:
    temperature_c: float  # temperature in degrees Celsius
    distance_km: float  # distance in kilometers
    weight_kg: float  # weight in kilograms
    speed_kmh: float  # speed in kilometers per hour
    volume_l: float  # volume in liters
```

#### Don'ts

```python
class WeatherReport:
    temperature: float  # ambiguous
    distance: float  # ambiguous
    weight: float  # ambiguous
    speed: float  # ambiguous
    volume: float  # ambiguous
```

### Sizes

Always be explicit about sizes. Size matters! Do you know the size of a BIGINT or a SMALLINT in your database of choice?

#### Do's

```python
from PIL import Image


class Profile:
    picture_w1200: Image  # if width is 1200 pixels and height is variable
    picture_w400_h300: Image  # if width is 400 pixels and height is 300 pixels
```

#### Don'ts

```python
from PIL import Image


class Profile:
    picture_small: Image  # ambiguous
    picture_large: Image  # ambiguous
    picture_thumbnail: Image  # ambiguous
```

## Utils & Helpers

> **[Resterampe /ˈʁɛstɐˌʁampə/](https://en.wiktionary.org/wiki/Resterampe)**
>
> A German term for a place where leftover goods are collected and sold cheaply.

Avoid generic names like `utils`, `helpers`, `common`, `shared`, `lib`, `core`, `base`, `foundation`, `services`, `components`, etc.

For type-agnostic functions, use inheritance and class methods to group them meaningfully.
E.g., instead of a `utils` module with a function `to_json(obj)`, create a `Object.to_json(self)` method on relevant classes.

If there isn't a type yet, create one. E.g., instead of a `helpers` module with a function `send_email(to, subject, body)`, create an `EmailClient` class with a `send_email(self, to, subject, body)` method.

## Synonyms

Avoid synonyms to reduce cognitive load. Pick one term and stick with it throughout your codebase.

Here's a non-exhaustive list of common synonyms and their preferred alternatives:

| Avoid                          | Prefer    |
| ------------------------------ | --------- |
| fetch/retrieve                 | fetch     |
| search/query/find              | search    |
| get/load/access                | get       |
| send/dispatch/transmit         | send      |
| create/make/build              | create    |
| delete/remove/destroy          | delete    |
| update/modify/change           | update    |
| calculate/compute/determine    | calculate |
| item/thing/object              | item      |
| data/info/information          | data      |
| value/val/amount               | value     |
| list/array/collection          | list      |
| clean/sanitize/normalize       | clean     |
| start/begin/initiate           | start     |
| stop/end/terminate             | stop      |
| many/multiple/numerous/several | multiple  |

Be specific and avoid vague terms. E.g., instead of `number`, use `count`, `index`, `mean`, etc.

Here's a non-exhaustive list of ambiguous terms and their preferred alternatives:

| Avoid   | Prefer            |
| ------- | ----------------- |
| number  | count/index/mean  |
| average | mean/median       |
| amount  | sum/count/min/max |

### Exceptions

Some terms have contextual meanings and should be used explicitly in those contexts.

#### Get vs. Fetch vs. Search

- **Get**: Use for simple, synchronous access to data already in memory or readily available.
- **Fetch**: Use for asynchronous or remote data retrieval, such as from a database or API.
- **Search**: Use when querying data based on specific criteria or filters with an unknown result set including zero results.

#### Set vs. Send

- **Set**: Use for assigning values to variables, properties, or configurations.
- **Send**: Use for transmitting data or messages over a network or between components. If HTTP is involved, always use the correct request method (e.g., `post()`, `put()`).

## Avoid Negations

Use positive language in naming to enhance code clarity and avoid cognitive overhead from double negations.

### Why Positive Language Matters

**Readability**: Positive terms are more straightforward and easier to understand at a glance.
E.g., `if (is_enabled)` is clearer than `if (!is_disabled)`.

**Double Negations**: Negative names create confusing logic when used with negative conditions.
E.g., `if (!is_not_valid)` is much harder to parse than `if (is_valid)`.

**Cognitive Load**: Our brains process positive statements faster than negative ones. Using positive language reduces mental effort when reading and reviewing code.

**Consistency**: Positive naming encourages consistent boolean logic patterns across your codebase, making it easier for teams to collaborate.

### Do's

```python
# Configuration flags
enable_feature_x = False  # positive language
allow_guest_access = True  # positive language
show_preview = True  # positive language
use_cache = False  # positive language

# State checks
is_active = True  # positive language
is_valid = False  # positive language
is_authenticated = True  # positive language
has_permission = False  # positive language

# Conditional logic
if is_enabled:
    activate()

if has_access:
    grant_permission()

if is_complete:
    finalize()
```

```javascript
// Configuration flags
const enableFeatureX = false; // positive language
const allowGuestAccess = true; // positive language
const showPreview = true; // positive language
const useCache = false; // positive language

// State checks
const isActive = true; // positive language
const isValid = false; // positive language
const isAuthenticated = true; // positive language
const hasPermission = false; // positive language
```

### Don'ts

```python
# Configuration flags
disable_feature_x = True  # negative language creates confusion
disallow_guest_access = False  # double negation: disallow=False means allow
hide_preview = False  # double negation: hide=False means show
no_cache = True  # negative language

# State checks
is_not_active = False  # double negation: not_active=False means active
is_invalid = True  # negative language
is_not_authenticated = False  # double negation
lacks_permission = True  # negative language

# Conditional logic - confusing patterns
if not is_disabled:  # double negation
    activate()

if not lacks_access:  # double negation
    grant_permission()

if not is_incomplete:  # double negation
    finalize()
```

```javascript
// Configuration flags
const disableFeatureX = true; // negative language creates confusion
const disallowGuestAccess = false; // double negation
const hidePreview = false; // double negation
const noCache = true; // negative language
```

### Word Suggestions

When writing code or documentation, use these positive alternatives:

| Avoid (Negative)       | Prefer (Positive)     |
| ---------------------- | --------------------- |
| disable_feature        | enable_feature        |
| is_disabled            | is_enabled            |
| is_invalid             | is_valid              |
| is_not_found           | is_found              |
| is_not_ready           | is_ready              |
| disallow_access        | allow_access          |
| hide_element           | show_element          |
| no_cache               | use_cache             |
| without_authentication | with_authentication   |
| lacks_permission       | has_permission        |
| cannot_edit            | can_edit              |
| should_not_process     | should_process        |
| is_not_empty           | has_items             |
| is_unavailable         | is_available          |
| is_inactive            | is_active             |
| is_incomplete          | is_complete           |
| does_not_exist         | exists                |
| is_forbidden           | is_allowed            |
| is_denied              | is_granted            |
| is_not_visible         | is_visible / is_shown |
| prevent_access         | allow_access          |
| block_requests         | allow_requests        |
| reject_input           | accept_input          |
| exclude_user           | include_user          |
| ignore_warnings        | process_warnings      |
| suppress_errors        | report_errors         |
| is_not_supported       | is_supported          |
| is_not_compatible      | is_compatible         |
| is_not_available       | is_available          |
| unset_flag             | set_flag              |

### Exception: When Negation Is Acceptable

Sometimes negative terms are the most natural expression of a concept. Use them when:

1. The negative form is the standard industry term (e.g., `disabled` for UI elements, `invalid` for form validation)
1. The positive alternative would be awkward or unclear (e.g., `is_optional` is clearer than `is_required = False`)
1. The concept is inherently negative (e.g., `error`, `exception`, `failure`)

```python
# Acceptable negative terms when they are clearest
class FormField:
    is_optional = True  # clearer than requiring `is_required = False`
    is_readonly = True  # standard term for form fields


# Natural negative concepts
def validate_email(email):
    if not is_valid_format(email):
        raise ValidationError("Invalid email format")  # natural term
```

## Versioning

It's simple: if your project does continuous releases, use [Semantic Versioning](https://semver.org/).
If you are on a fixed release cycle, use [calver](https://calver.org/) `YYYY.MINOR.MICRO`
. E.g., `2024.2.3` for the third patch of the second minor release in 2024.

Here's a diagram to help you decide:

```mermaid
flowchart TD
    A(Start) --> B{releases}
    B -- continuous --> D[semver]
    B -- fixed cycle --> C[calver]
```

Do not invent your own versioning scheme.

# Honorable mentions

- [Naming Things in Code](https://www.youtube.com/watch?v=-J3wNP6u5YU) by CodeAesthetic

# License

This work is licensed under a [CC0 1.0 Universal License](https://creativecommons.org/publicdomain/zero/1.0/).
Do with it as you please; maybe leave a star on [GitHub](https://github.com/codingjoe/naming-things). Thanks \<3
