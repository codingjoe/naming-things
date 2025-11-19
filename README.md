# _Naming Things_

**A humble effort to solve computer science second-hardest problem.**

<!-- mdformat-toc start --slug=github --no-anchors --maxlevel=2 --minlevel=2 -->

- [Abstraction](#abstraction)
- [Time](#time)
- [Abbreviations](#abbreviations)
- [Metrics, Measurements, and Units](#metrics-measurements-and-units)
- [Versioning](#versioning)

<!-- mdformat-toc end -->

# Preamble

> There are only two hard things in Computer Science: cache invalidation and naming things.
>
> — Phil Karlton

This document concerns natural language conventions not syntax or code style. Rules are language-agnostic, but examples are given in Python.

[![Permanence
](https://imgs.xkcd.com/comics/permanence.png)](https://xkcd.com/910/)

## Abstraction

### Inheritance

Specialize, don't generalize. If you feel the urge to name a base class `BaseSomething` or `AbstractSomething`, go the other way.
Make children more specific, not parents more general.

#### Do's

```python
class Vehicle:
    """A means of transporting people or goods."""


class Car(Vehicle):
    """A road vehicle, typically with four wheels, powered by an internal combustion engine or electric motor."""


class SUV(Car):
    """A pedestrian death machine."""
```

#### Don'ts

```python
class BaseCar:
    """A road vehicle, typically with four wheels, powered by an internal combustion engine or electric motor."""


class SportsCar(BaseCar):
    """Only fun in Germany."""
```

## Time

_[Timezones are hard](https://www.youtube.com/watch?v=-5wpm-gesOY), don't make it harder._

### Events – Points in time

Points in time should always have a little `at`-suffix to communicate they represent a specific moment rather than a duration or interval.

Furthermore, they must be in the language's date type (e.g., `datetime` in Python, `Date` in JavaScript) as well as timezone-aware.

Hindsight is 20/20, name all dates in the past tense, e.g., `created_at`, `updated_at`, `deleted_at`. Even if the event is in the future, e.g., `scheduled_at`, `expired_at`, `started_at`. Time passes. By the time you are debugging code, everything is in the past.

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

Durations should be either be unambiguously typed (e.g., `timedelta` in Python, `Duration` in Java) or have a suffix indicating the unit of time (e.g., `secs`, `ms`, `mins`, `hours`, `days`).

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

Unless... they are technical acronyms, that are universally known outside your team's domain, e.g., `HTML`, `URL`. Use them if they are more common than their unabbreviated counterparts.

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
- **IPV4** (Internet Protocol Version 4)
- **IPV6** (Internet Protocol Version 6)

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

When persisting metrics consider using SI (metric) units instead of [freedom units](https://en.wiktionary.org/wiki/freedom_units) since they are SI based. You will have less conversion headaches down the road.

#### Do's

```python
class WeatherReport:
    temperature_celsius: float  # temperature in degrees Celsius
    distance_km: float  # distance in kilometers
    weight_kg: float  # weight in kilograms
    speed_kmh: float  # speed in kilometers per hour
    volume_liters: float  # volume in liters
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
Do with it as you please, maybe leave a star on [GitHub](https://github.com/codingjoe/naming-things). Thanks \<3
