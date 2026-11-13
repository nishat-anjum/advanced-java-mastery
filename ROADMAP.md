# Roadmap

Ten weeks · six study days per week · two hours per day · **120 hours** in total. Primary environment: **JDK 25 LTS**.

The full specification of every week is in [curriculum/10-week-curriculum.md](curriculum/10-week-curriculum.md). This page is the overview.

## Weekly overview

| Week | Title | Focus | Hours |
|---|---|---|---|
| 1 | Java 8 → 25 evolution and the measurement toolkit | Release model, feature lifecycle, removals, JMH/JOL/JFR/jcmd, evidence conventions | 12 |
| 2 | Modern language features and data-oriented programming | Records, sealed classes, pattern matching, switch expressions, text blocks, JDK 25 language finals | 12 |
| 3 | Collections internals and generics | ArrayList/LinkedList/HashMap/TreeMap internals, Sequenced Collections, erasure, wildcards | 12 |
| 4 | Lambdas, streams and Optional | Lambda translation, stream pipeline internals, collectors, gatherers, parallel streams | 12 |
| 5 | Threads, the Java Memory Model, locks and atomics | Happens-before, volatile, monitors, AQS, CAS, VarHandles, jcstress | 12 |
| 6 | Executors, CompletableFuture and concurrent collections | ThreadPoolExecutor, ForkJoinPool, CompletableFuture, ConcurrentHashMap | 12 |
| 7 | Virtual threads, structured concurrency and scoped values | Continuations, pinning, StructuredTaskScope (preview), ScopedValue (final) | 12 |
| 8 | JVM architecture: class loading, bytecode and JIT | Class files, loading/linking, dispatch, tiered JIT, escape analysis, AOT cache | 12 |
| 9 | Memory management, GC and diagnostics | G1, ZGC, Shenandoah, GC logs, JFR/JMC, heap dumps, NMT | 12 |
| 10 | IO, NIO, networking, HttpClient and runtime metadata | Buffers, channels, selectors, HttpClient, modules/reflection/annotations at decision depth | 12 |

### Weighting rationale

| Area | Weeks | Share |
|---|---|---|
| Language, collections, generics, functional programming, streams | 2–4 | ~30% |
| Concurrency (JMM → executors → virtual threads) | 5–7 | ~30% |
| JVM internals, JIT, memory, GC, diagnostics | 8–9 | ~20% |
| Evolution and measurement toolkit | 1 | ~10% |
| IO/NIO/networking and runtime metadata | 10 | ~10% |

Concurrency and JVM behaviour receive the most time because they carry the most engineering risk and the largest gap from Java 7. Classic IO, reflection and annotations are compressed because of prior EJB/JPA experience. Day 6 of every week is consolidation, assessment and buffer.

## Engineering decision guide (built progressively)

Each comparison is written in the week it is taught, from my own evidence. What each entry must cover is defined in the curriculum.

| Comparison | Week |
|---|---|
| POJO vs Record | 2 |
| Inheritance vs Composition vs Sealed Classes | 2 |
| ArrayList vs LinkedList | 3 |
| HashMap vs ConcurrentHashMap | 3, 6 |
| Loops vs Streams | 4 |
| synchronized vs ReentrantLock vs Atomic variables | 5 |
| Future vs CompletableFuture | 6 |
| Parallel Streams vs ExecutorService | 6 |
| Platform Threads vs Virtual Threads | 7 |
| G1 vs ZGC | 9 |
| Classic IO vs NIO | 10 |

## Practical projects

Four projects are built progressively. Only their descriptions and objectives are defined now; design, tasks and code are added during the relevant weeks.

### 1. Employee Analytics Engine — Weeks 2–4 (revisited in Week 9)

An in-memory analytics engine over a realistic employee/organisation dataset (departments, salaries, roles, history), answering aggregation and reporting queries.

Learning objectives:

- Model a domain with records and sealed hierarchies; use pattern matching for exhaustive processing.
- Choose collections deliberately and measure their footprint with JOL.
- Express queries with streams, collectors and gatherers; compare with loops using JMH.
- In Week 9, profile its allocation and GC behaviour under different collectors.

### 2. Concurrent Order Processing System — Weeks 5–7

A pipeline that accepts, validates, prices, reserves inventory for and completes orders concurrently, with shared state, failures and backpressure.

Learning objectives:

- Protect shared state correctly with locks, atomics and concurrent collections; reason with happens-before.
- Build the pipeline on platform-thread executors and CompletableFuture; size pools with a written rationale.
- Migrate to virtual threads and structured concurrency (preview) and compare behaviour, code shape and measured throughput/latency.

### 3. High-Concurrency HTTP Client — Weeks 7 and 10

A client that issues many concurrent HTTP requests against a local test server with timeouts, retries, cancellation and request-scoped context.

Learning objectives:

- Use `java.net.http.HttpClient` in both blocking-on-virtual-threads and asynchronous CompletableFuture styles.
- Propagate request context with scoped values.
- Understand the NIO layer underneath (channels, selectors) and measure the two styles with a load test and JFR, not JMH.

### 4. JVM Performance Investigation Lab — set up in Week 1; main work in Weeks 8–10

A collection of documented investigations: JIT behaviour, allocation and escape analysis, GC comparisons, and diagnosis of problems found in Projects 1–3.

Learning objectives:

- Apply JMH correctly where microbenchmarks are appropriate, and know when they are not.
- Use JFR, JMC, GC logs, heap dumps, NMT and async-profiler to explain observed behaviour.
- Record every measurement with its environment, configuration and limitations.

## Optional post-course extension

Not part of the 120-hour core. Planned at roughly 8–10 hours.

- **Foreign Function & Memory API** (Final since JDK 22, [JEP 454](https://openjdk.org/jeps/454)): `Arena`, `MemorySegment`, `Linker`, `jextract`; comparison with JNI and `sun.misc.Unsafe`.
- **Integrity by default:** JNI restrictions ([JEP 472](https://openjdk.org/jeps/472)), deprecation of `Unsafe` memory-access methods ([JEP 471](https://openjdk.org/jeps/471), [JEP 498](https://openjdk.org/jeps/498)).
- **Optional extra:** Vector API (Incubator in JDK 25, [JEP 508](https://openjdk.org/jeps/508)).

## Explicitly out of scope

Valhalla value classes, GraalVM native image, framework internals (Spring, Hibernate), and security/cryptography APIs. These may become future extensions.

## Workload expectations

Twelve hours per week is enough to understand the important mechanisms and trade-offs and to build evidence-based judgement. It is not enough to master every Java feature. If a week overruns, the plan is to extend the programme rather than skim.
