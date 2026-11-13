# Resources

Curated references used in this programme. Primary sources come first. Every claim in the notes should be traceable to one of these, or to my own documented measurement.

Legend: **Free** = freely available online · **Paid** = book or paid material.

## Specifications (primary)

| Resource | Use | Access |
|---|---|---|
| [Java Language Specification, SE 25](https://docs.oracle.com/javase/specs/jls/se25/html/index.html) | Language semantics; the Java Memory Model (chapter 17) | Free |
| [Java Virtual Machine Specification, SE 25](https://docs.oracle.com/javase/specs/jvms/se25/html/index.html) | Class files, loading, linking, bytecode | Free |
| [Java SE specifications index](https://docs.oracle.com/javase/specs/) | Earlier editions for comparison | Free |
| [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2) | Entity rules; records as embeddables | Free |

## Official JDK documentation

| Resource | Use | Access |
|---|---|---|
| [JDK 25 documentation home](https://docs.oracle.com/en/java/javase/25/) | Entry point to all guides | Free |
| [JDK 25 API documentation](https://docs.oracle.com/en/java/javase/25/docs/api/index.html) | API contracts | Free |
| [JDK 25 tool reference](https://docs.oracle.com/en/java/javase/25/docs/specs/man/) | `java`, `javap`, `jcmd`, `jfr`, `jdeps`, `jdeprscan` | Free |
| [Java Language Updates](https://docs.oracle.com/en/java/javase/25/language/) | Modern language features by release | Free |
| [Java Core Libraries guide](https://docs.oracle.com/en/java/javase/25/core/) | Virtual threads, structured concurrency, scoped values, FFM, networking | Free |
| [Java Virtual Machine Guide](https://docs.oracle.com/en/java/javase/25/vm/) | HotSpot features and options | Free |
| [Garbage Collection Tuning Guide](https://docs.oracle.com/en/java/javase/25/gctuning/) | GC algorithms and tuning | Free |
| [Troubleshooting Guide](https://docs.oracle.com/en/java/javase/25/troubleshoot/) | Diagnostics tools and procedures | Free |
| [JDK Migration Guide](https://docs.oracle.com/en/java/javase/25/migrate/) | Removed and changed APIs | Free |
| [JDK 25 release notes](https://jdk.java.net/25/release-notes) | Detailed changes in JDK 25 | Free |
| [dev.java — Learn](https://dev.java/learn/) | Official tutorials from the Java team | Free |
| [inside.java](https://inside.java/) | Articles and talks by the OpenJDK team at Oracle | Free |

## OpenJDK

| Resource | Use | Access |
|---|---|---|
| [JEP index](https://openjdk.org/jeps/0) | Feature motivation, design and status | Free |
| [JDK 25](https://openjdk.org/projects/jdk/25/), [JDK 26](https://openjdk.org/projects/jdk/26/), [JDK 27](https://openjdk.org/projects/jdk/27/) project pages | Features per release | Free |
| [openjdk/jdk25u](https://github.com/openjdk/jdk25u) | JDK 25 update-release source | Free |
| [openjdk/jdk](https://github.com/openjdk/jdk) | Mainline source | Free |
| [HotSpot Runtime Overview](https://openjdk.org/groups/hotspot/docs/RuntimeOverview.html), [HotSpot Glossary](https://openjdk.org/groups/hotspot/docs/HotSpotGlossary.html) | HotSpot terminology and structure | Free |
| [ZGC wiki](https://wiki.openjdk.org/display/zgc/Main), [Shenandoah wiki](https://wiki.openjdk.org/display/shenandoah/Main) | Collector design and options | Free |

## Measurement and diagnostics tools

| Tool | Use | Access |
|---|---|---|
| [JMH](https://github.com/openjdk/jmh) and [samples](https://github.com/openjdk/jmh/tree/master/jmh-samples/src/main/java/org/openjdk/jmh/samples) | Microbenchmarks | Free |
| [jcstress](https://github.com/openjdk/jcstress) | Concurrency correctness testing | Free |
| [JOL](https://github.com/openjdk/jol) | Object layout and footprint | Free |
| [JDK Mission Control](https://github.com/openjdk/jmc) | JFR recording analysis | Free |
| [async-profiler](https://github.com/async-profiler/async-profiler) | CPU and allocation profiling | Free |
| [Eclipse Memory Analyzer (MAT)](https://eclipse.dev/mat/) | Heap dump analysis | Free |
| [JITWatch](https://github.com/AdoptOpenJDK/jitwatch) | JIT compilation log analysis | Free |
| [jextract](https://github.com/openjdk/jextract) | FFM bindings (extension only) | Free |

## Expert articles

| Resource | Topic | Access |
|---|---|---|
| Aleksey Shipilëv, [JVM Anatomy Quarks](https://shipilev.net/jvm/anatomy-quarks/) | Short HotSpot internals investigations | Free |
| Aleksey Shipilëv, [Java Memory Model Pragmatics](https://shipilev.net/blog/2014/jmm-pragmatics/) | JMM | Free |
| Aleksey Shipilëv, [Close Encounters of the JMM Kind](https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/) | JMM pitfalls | Free |
| Aleksey Shipilëv, [Nanotrusting the Nanotime](https://shipilev.net/blog/2014/nanotrusting-nanotime/) | Timer behaviour and benchmarking | Free |
| Doug Lea, [Using JDK 9 Memory Order Modes](https://gee.cs.oswego.edu/dl/html/j9mm.html) | VarHandle access modes | Free |
| Doug Lea, [The JSR-133 Cookbook for Compiler Writers](https://gee.cs.oswego.edu/dl/jmm/cookbook.html) | Barriers and reorderings | Free |
| Doug Lea et al., [When to use parallel streams](https://gee.cs.oswego.edu/dl/html/StreamParallelGuidance.html) | Parallel stream cost model | Free |
| Jeremy Manson and Brian Goetz, [JSR 133 (Java Memory Model) FAQ](https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html) | JMM introduction | Free |
| Brian Goetz, [State of the Lambda](https://cr.openjdk.org/~briangoetz/lambda/lambda-state-final.html) | Lambda design | Free |
| Brian Goetz, [Translation of Lambda Expressions](https://cr.openjdk.org/~briangoetz/lambda/lambda-translation.html) | Lambda compilation strategy | Free |
| Brian Goetz, [Data Oriented Programming in Java](https://www.infoq.com/articles/data-oriented-programming-java/) | Records, sealed types, patterns | Free |
| Angelika Langer, [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html) | Generics in depth (pre-dates recent releases) | Free |
| [Spring Data JPA — Projections](https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html) | Records as projections | Free |

## Books

| Book | Topic | Access |
|---|---|---|
| Brian Goetz et al., *Java Concurrency in Practice* | Concurrency foundations (predates virtual threads) | Paid |
| Joshua Bloch, *Effective Java*, 3rd ed. | API and language best practice (covers up to Java 9) | Paid |
| Richard Jones, Antony Hosking, Eliot Moss, *The Garbage Collection Handbook*, 2nd ed. | GC algorithms | Paid |

## How references are used

- Prefer the specification or Javadoc for guarantees, JEPs for motivation and status, and OpenJDK source for implementation detail.
- Articles and books are secondary: check their claims against the current JDK, especially for older material.
- Do not cite a source for something it does not say.
