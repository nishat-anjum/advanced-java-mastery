# JDK 26 and 27 Change Tracker

**Reference only — not part of the mandatory curriculum.** The core programme uses JDK 25 (LTS). JDK 26 (GA March 2026) and JDK 27 (GA September 2026) are non-LTS feature releases. This page records changes that affect topics in the curriculum, so notes written against JDK 25 can point out what has since changed.

Sources: [JDK 26 project page](https://openjdk.org/projects/jdk/26/), [JDK 27 project page](https://openjdk.org/projects/jdk/27/), and the individual JEP pages linked below. Statuses were checked against those pages on 2026-11-13.

## JDK 26

| JEP | Feature | Status | Relevant week | What changes relative to JDK 25 |
|---|---|---|---|---|
| [500](https://openjdk.org/jeps/500) | Prepare to Make Final Mean Final | Delivered (warnings) | 10, extension | Warnings when deep reflection mutates `final` fields; prepares for a future restriction |
| [504](https://openjdk.org/jeps/504) | Remove the Applet API | Removed | 1 | Applet API (deprecated for removal in JDK 17) is removed |
| [516](https://openjdk.org/jeps/516) | Ahead-of-Time Object Caching with Any GC | Delivered | 8 | The AOT cache's cached objects can be used with any GC, including ZGC |
| [517](https://openjdk.org/jeps/517) | HTTP/3 for the HTTP Client API | Delivered | 10 | `HttpClient` gains HTTP/3 support |
| [522](https://openjdk.org/jeps/522) | G1 GC: Improve Throughput by Reducing Synchronization | Delivered | 9 | Less synchronisation between application and GC threads in G1 |
| [525](https://openjdk.org/jeps/525) | Structured Concurrency (Sixth Preview) | Preview | 7 | Still preview |
| [526](https://openjdk.org/jeps/526) | Lazy Constants (Second Preview) | Preview | — | Continues the Stable Values preview ([JEP 502](https://openjdk.org/jeps/502)) under a new name |
| [530](https://openjdk.org/jeps/530) | Primitive Types in Patterns, instanceof, and switch (Fourth Preview) | Preview | 2 | Still preview |
| [529](https://openjdk.org/jeps/529) | Vector API (Eleventh Incubator) | Incubator | extension | Still incubating |
| [524](https://openjdk.org/jeps/524) | PEM Encodings of Cryptographic Objects (Second Preview) | Preview | out of scope | — |

## JDK 27

| JEP | Feature | Status | Relevant week | What changes relative to JDK 25 |
|---|---|---|---|---|
| [523](https://openjdk.org/jeps/523) | Make G1 the Default Garbage Collector in All Environments | Delivered | 9 | G1 becomes the default everywhere, not only in server-class environments |
| [534](https://openjdk.org/jeps/534) | Compact Object Headers by Default | Delivered | 8, 9 | Compact object headers (opt-in in JDK 25, [JEP 519](https://openjdk.org/jeps/519)) become the default layout |
| [533](https://openjdk.org/jeps/533) | Structured Concurrency (Seventh Preview) | Preview | 7 | Still preview |
| [532](https://openjdk.org/jeps/532) | Primitive Types in Patterns, instanceof, and switch (Fifth Preview) | Preview | 2 | Still preview — not final |
| [531](https://openjdk.org/jeps/531) | Lazy Constants (Third Preview) | Preview | — | Still preview |
| [536](https://openjdk.org/jeps/536) | JFR In-Process Data Redaction | Delivered | 9 | JFR redacts command-line arguments and initial environment/system-property values in recordings |
| [527](https://openjdk.org/jeps/527) | Post-Quantum Hybrid Key Exchange for TLS 1.3 | Delivered | out of scope | — |
| [537](https://openjdk.org/jeps/537) | Vector API (Twelfth Incubator) | Incubator | extension | Still incubating |
| [538](https://openjdk.org/jeps/538) | PEM Encodings of Cryptographic Objects (Third Preview) | Preview | out of scope | — |

## How to use this tracker

- When a weekly note describes JDK 25 behaviour that later changed, it links to the relevant row here.
- Experiments may optionally be re-run on JDK 26 or 27 to observe a change (for example JEP 534's effect on object footprint), but this is never required.
- When a new JDK is released, add a section after re-checking statuses against its project page.
