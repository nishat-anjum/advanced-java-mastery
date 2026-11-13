# Advanced Java Mastery — 10-Week Curriculum

**Primary environment:** JDK 25 (LTS) · **Schedule:** 6 days × 2 hours per week · **Total:** 120 hours
**Learner profile:** ~14 years of Java (Java 7, EJB, SOAP, JPA, enterprise systems; familiar with Spring/Spring Boot). Not a beginner course.

This document is the plan. Lessons, notes, experiments and code are written in the `weeks/` folders **only when each week is actually studied**.

---

## Contents

1. [Goals and non-goals](#1-goals-and-non-goals)
2. [Conventions](#2-conventions)
3. [Feature status reference (JDK 25)](#3-feature-status-reference-jdk-25)
4. [Weekly curriculum](#4-weekly-curriculum)
5. [Engineering decision guide](#5-engineering-decision-guide)
6. [Practical projects](#6-practical-projects)
7. [Optional post-course extension](#7-optional-post-course-extension)
8. [Core references](#8-core-references)

---

## 1. Goals and non-goals

For every important feature, the aim is to be able to explain: why it exists; what problem it solves; how it works internally and what happens in the JVM; what is guaranteed versus implementation-specific; its advantages, limitations and hidden costs; its performance and memory implications; when to use it, when to avoid it, and the alternatives.

**Non-goals.** Ten weeks of 12 hours do not produce mastery of every Java feature. The programme aims for a deep understanding of the mechanisms that matter most in production and the habit of deciding from evidence. Out of scope: Valhalla value classes, GraalVM native image, framework internals, security/cryptography APIs. The FFM API is an [optional extension](#7-optional-post-course-extension).

**Prioritisation.** Time is not split equally across topics. Concurrency (weeks 5–7) and JVM behaviour (weeks 8–9) get the largest share because they carry the most production risk and the largest gap from Java 7. Classic IO, reflection and annotations are compressed because of existing EJB/JPA experience.

**Pacing.** Day 6 of every week is consolidation, assessment, decision-guide writing and buffer. If a week overruns, extend the programme instead of skimming.

---

## 2. Conventions

### 2.1 Source of a claim: specification vs implementation

Every note labels its claims:

| Label | Meaning | Stability |
|---|---|---|
| **[Spec]** | Required by the [JLS](https://docs.oracle.com/javase/specs/jls/se25/html/index.html) or [JVMS](https://docs.oracle.com/javase/specs/jvms/se25/html/index.html); cite the section | Holds for every conforming implementation |
| **[API contract]** | Stated in the Javadoc of the class or method | Holds for every conforming JDK |
| **[HotSpot/OpenJDK]** | Behaviour of the OpenJDK/HotSpot implementation; cite the source file or JEP | May change in any release, or differ in another JVM |
| **[Measured]** | Observed in my own experiment | Valid only for the documented environment |

Typical boundaries, examined explicitly in the relevant weeks:

| Week | Guaranteed | Implementation detail |
|---|---|---|
| 3 | HashMap API contract (e.g. no ordering guarantee) | Hash spreading, resize policy, treeification ([JEP 180](https://openjdk.org/jeps/180)) |
| 4 | Stream API semantics (laziness, encounter order) | Pipeline classes (`AbstractPipeline`, `Sink`), fusion details |
| 5 | Happens-before, `volatile`, final-field semantics (JLS §17.4–17.5) | Monitor implementation, object header lock states, biased locking |
| 7 | Virtual thread semantics in the `Thread` API | The ForkJoinPool-based scheduler, continuation implementation, specific pinning cases |
| 8 | Class loading, linking, verification, initialisation (JVMS ch. 5); bytecode semantics (JVMS ch. 6) | Interpreter, tiered compilation, inlining, escape analysis, TLABs, object layout |
| 9 | Only that unreachable objects are reclaimed by automatic storage management (JVMS §2.5.3) | Every garbage collector and every GC flag |

### 2.2 Strength of performance evidence

| Level | What it is | Example |
|---|---|---|
| **Theoretical** | Algorithmic complexity or design analysis | `LinkedList.get(i)` is O(n) |
| **Documented** | Stated in a spec, Javadoc or JEP | The `ArrayList` Javadoc states that `add` runs in amortised constant time |
| **Empirically measured** | My own measurement, environment recorded | A JMH result with error bounds on a stated machine |

Theoretical complexity does not predict real performance on its own: constant factors, cache behaviour, allocation and JIT optimisation often dominate.

### 2.3 Choosing the measurement tool

| Question | Tool | Not appropriate |
|---|---|---|
| Cost of an isolated, small code path (loop vs stream, CAS under contention, collection operation) | [JMH](https://github.com/openjdk/jmh) | `System.nanoTime()` loops in `main` |
| Correctness of concurrent code under the memory model | [jcstress](https://github.com/openjdk/jcstress) | Unit tests that "usually pass" |
| Object and collection footprint | [JOL](https://github.com/openjdk/jol) | Guessing from field counts |
| Whole-system throughput/latency (order pipeline, HTTP client) | Load test + JFR | JMH |
| GC behaviour | GC logs (`-Xlog:gc*`) + JFR/JMC | JMH scores |
| CPU and allocation hot spots | JFR, [async-profiler](https://github.com/async-profiler/async-profiler) | Intuition |

### 2.4 Benchmark record (required for every measured claim)

Each measurement in the repository records:

- **Question** being answered and the hypothesis.
- **Hardware:** CPU model, cores/threads, RAM; laptop or server; power and thermal conditions.
- **OS** and version; container or not.
- **JDK:** vendor and exact build (`java -version`), JVM flags, garbage collector, heap size.
- **Tool configuration:** e.g. JMH version, mode, forks, warm-up and measurement iterations, threads.
- **Results** with error bounds, not single numbers.
- **Interpretation:** what the result does and does not show; profiler evidence explaining *why*.
- **Limitations:** e.g. single machine, laptop frequency scaling, dataset size, results that may not transfer to production or another JVM.

### 2.5 Feature status labels

**Final** (permanent feature) · **Preview** ([JEP 12](https://openjdk.org/jeps/12); requires `--enable-preview`; may change or be removed) · **Incubator** ([JEP 11](https://openjdk.org/jeps/11); `jdk.incubator.*` module) · **Experimental** (VM feature behind `-XX:+UnlockExperimentalVMOptions`) · **Product** (supported VM feature; may still be opt-in) · **Deprecated / Deprecated for removal** · **Removed**. Every status is cited with its JEP.

---

## 3. Feature status reference (JDK 25)

Checked against the [JDK 25 project page](https://openjdk.org/projects/jdk/25/) and the individual JEP pages. Changes in later releases are in the [JDK 26/27 tracker](jdk-26-27-tracker.md).

### 3.1 JEPs delivered in JDK 25

| Status in JDK 25 | Feature | JEP | Curriculum week |
|---|---|---|---|
| Final | Scoped Values | [506](https://openjdk.org/jeps/506) | 7 |
| Final | Module Import Declarations | [511](https://openjdk.org/jeps/511) | 2 |
| Final | Compact Source Files and Instance Main Methods | [512](https://openjdk.org/jeps/512) | 2 |
| Final | Flexible Constructor Bodies | [513](https://openjdk.org/jeps/513) | 2 |
| Final | Key Derivation Function API | [510](https://openjdk.org/jeps/510) | out of scope |
| Final | Ahead-of-Time Command-Line Ergonomics | [514](https://openjdk.org/jeps/514) | 8 |
| Final | Ahead-of-Time Method Profiling | [515](https://openjdk.org/jeps/515) | 8 |
| Final | JFR Cooperative Sampling | [518](https://openjdk.org/jeps/518) | 9 |
| Final | JFR Method Timing & Tracing | [520](https://openjdk.org/jeps/520) | 9 |
| Product (opt-in, not default) | Compact Object Headers | [519](https://openjdk.org/jeps/519) | 8 |
| Product | Generational Shenandoah | [521](https://openjdk.org/jeps/521) | 9 |
| Preview (5th) | Structured Concurrency | [505](https://openjdk.org/jeps/505) | 7 |
| Preview (3rd) | Primitive Types in Patterns, instanceof, and switch | [507](https://openjdk.org/jeps/507) | 2 |
| Preview | Stable Values | [502](https://openjdk.org/jeps/502) | tracker only |
| Preview | PEM Encodings of Cryptographic Objects | [470](https://openjdk.org/jeps/470) | out of scope |
| Incubator (10th) | Vector API | [508](https://openjdk.org/jeps/508) | extension |
| Experimental (Linux only) | JFR CPU-Time Profiling | [509](https://openjdk.org/jeps/509) | 9 (awareness) |
| Removed | 32-bit x86 port | [503](https://openjdk.org/jeps/503) | 1 |

### 3.2 Key features from Java 8 to 24 used in this curriculum

| Release | Feature | JEP | Status |
|---|---|---|---|
| 8 | Lambda expressions and default methods | [126](https://openjdk.org/jeps/126) | Final |
| 8 | Bulk data operations (streams) | [107](https://openjdk.org/jeps/107) | Final |
| 8 | Date & Time API | [150](https://openjdk.org/jeps/150) | Final |
| 8 | Concurrency updates (ConcurrentHashMap, CompletableFuture, LongAdder, StampedLock, common pool) | [155](https://openjdk.org/jeps/155) | Final |
| 8 | HashMap collisions handled with balanced trees | [180](https://openjdk.org/jeps/180) | Implementation change |
| 8 | Permanent generation removed (Metaspace) | [122](https://openjdk.org/jeps/122) | Removed |
| 9 | Module system | [261](https://openjdk.org/jeps/261) | Final |
| 9 | Variable handles | [193](https://openjdk.org/jeps/193) | Final |
| 9 | More concurrency updates (Flow, CompletableFuture enhancements) | [266](https://openjdk.org/jeps/266) | Final |
| 9 | Collection factory methods | [269](https://openjdk.org/jeps/269) | Final |
| 9 | Compact strings | [254](https://openjdk.org/jeps/254) | Implementation change |
| 9 | Indified string concatenation | [280](https://openjdk.org/jeps/280) | Implementation change |
| 9 | G1 becomes the default GC (server-class machines) | [248](https://openjdk.org/jeps/248) | Final |
| 9 | Unified JVM logging | [158](https://openjdk.org/jeps/158) | Final |
| 10 | Local-variable type inference (`var`) | [286](https://openjdk.org/jeps/286) | Final |
| 11 | HTTP Client API | [321](https://openjdk.org/jeps/321) | Final |
| 11 | Java EE and CORBA modules (JAX-WS, JAXB, …) | [320](https://openjdk.org/jeps/320) | Removed |
| 11 | Launch single-file source-code programs | [330](https://openjdk.org/jeps/330) | Final |
| 13 | Legacy Socket API reimplemented | [353](https://openjdk.org/jeps/353) | Implementation change |
| 14 | Switch expressions | [361](https://openjdk.org/jeps/361) | Final |
| 14 | Helpful NullPointerExceptions | [358](https://openjdk.org/jeps/358) | Final |
| 14 | CMS garbage collector | [363](https://openjdk.org/jeps/363) | Removed |
| 15 | Text blocks | [378](https://openjdk.org/jeps/378) | Final |
| 15 | Hidden classes | [371](https://openjdk.org/jeps/371) | Final |
| 15 | Legacy DatagramSocket API reimplemented | [373](https://openjdk.org/jeps/373) | Implementation change |
| 15 | Biased locking | [374](https://openjdk.org/jeps/374) | Deprecated and disabled by default |
| 15 | Nashorn JavaScript engine | [372](https://openjdk.org/jeps/372) | Removed |
| 16 | Records | [395](https://openjdk.org/jeps/395) | Final |
| 16 | Pattern matching for `instanceof` | [394](https://openjdk.org/jeps/394) | Final |
| 16 | Unix-domain socket channels | [380](https://openjdk.org/jeps/380) | Final |
| 17 | Sealed classes | [409](https://openjdk.org/jeps/409) | Final |
| 17 | Strongly encapsulate JDK internals | [403](https://openjdk.org/jeps/403) | Final |
| 17 | Applet API | [398](https://openjdk.org/jeps/398) | Deprecated for removal |
| 17 | Security Manager | [411](https://openjdk.org/jeps/411) | Deprecated for removal |
| 17 | RMI Activation | [407](https://openjdk.org/jeps/407) | Removed |
| 17 | Experimental AOT and JIT compiler (Graal-based) | [410](https://openjdk.org/jeps/410) | Removed |
| 18 | UTF-8 by default | [400](https://openjdk.org/jeps/400) | Final |
| 18 | Core reflection reimplemented with method handles | [416](https://openjdk.org/jeps/416) | Implementation change |
| 18 | Finalization | [421](https://openjdk.org/jeps/421) | Deprecated for removal |
| 21 | Virtual threads | [444](https://openjdk.org/jeps/444) | Final |
| 21 | Sequenced collections | [431](https://openjdk.org/jeps/431) | Final |
| 21 | Record patterns | [440](https://openjdk.org/jeps/440) | Final |
| 21 | Pattern matching for `switch` | [441](https://openjdk.org/jeps/441) | Final |
| 21 | Generational ZGC | [439](https://openjdk.org/jeps/439) | Product |
| 21 | Prepare to disallow dynamic loading of agents | [451](https://openjdk.org/jeps/451) | Final (warnings) |
| 22 | Unnamed variables and patterns | [456](https://openjdk.org/jeps/456) | Final |
| 22 | Foreign Function & Memory API | [454](https://openjdk.org/jeps/454) | Final |
| 23 | ZGC generational mode by default | [474](https://openjdk.org/jeps/474) | Product |
| 23 | `sun.misc.Unsafe` memory-access methods | [471](https://openjdk.org/jeps/471) | Deprecated for removal |
| 24 | Stream gatherers | [485](https://openjdk.org/jeps/485) | Final |
| 24 | Class-File API | [484](https://openjdk.org/jeps/484) | Final |
| 24 | Ahead-of-time class loading & linking | [483](https://openjdk.org/jeps/483) | Final |
| 24 | Synchronize virtual threads without pinning | [491](https://openjdk.org/jeps/491) | Final |
| 24 | Security Manager permanently disabled | [486](https://openjdk.org/jeps/486) | Disabled |
| 24 | ZGC non-generational mode | [490](https://openjdk.org/jeps/490) | Removed |
| 24 | Prepare to restrict JNI | [472](https://openjdk.org/jeps/472) | Final (warnings) |
| 24 | Warn upon use of `Unsafe` memory-access methods | [498](https://openjdk.org/jeps/498) | Final (warnings) |

The complete list is in the [JEP index](https://openjdk.org/jeps/0).

---

## 4. Weekly curriculum

Each week: 12 hours. Typical split: ~40% reading primary sources, ~45% hands-on investigation, ~15% writing notes and decision-guide entries.

---

### Week 1 — Java 8 → 25 evolution and the measurement toolkit

**Learning objectives**

- Understand the release model, LTS line and feature lifecycle, and classify any JDK 25 feature by status with a JEP citation.
- Map the changes from Java 8 to 25 that matter to an engineer coming from Java 7 / Java EE.
- Establish the measurement and evidence conventions used for the rest of the programme.

**Topics and subtopics**

- Six-month release cadence; the LTS line (8, 11, 17, 21, 25); the JEP process; preview ([JEP 12](https://openjdk.org/jeps/12)) and incubator ([JEP 11](https://openjdk.org/jeps/11)) mechanics.
- Evolution tour, 8 → 17: lambdas and streams, modules, `var`, HttpClient, switch expressions, text blocks, records, sealed classes, helpful NPEs, compact strings, indified string concatenation.
- Evolution tour, 17 → 25: virtual threads, pattern matching, sequenced collections, FFM, gatherers, scoped values, JDK 25 language finals, AOT cache, compact object headers.
- Removals and deprecations relevant to Java 7 / Java EE codebases: Java EE and CORBA modules ([JEP 320](https://openjdk.org/jeps/320)), Nashorn, Security Manager ([JEP 411](https://openjdk.org/jeps/411), [JEP 486](https://openjdk.org/jeps/486)), finalization ([JEP 421](https://openjdk.org/jeps/421)), PermGen → Metaspace ([JEP 122](https://openjdk.org/jeps/122)), strong encapsulation ([JEP 403](https://openjdk.org/jeps/403)).
- Migration tooling: `jdeps`, `jdeprscan`.
- Measurement toolkit: JMH (and the classic pitfalls: dead-code elimination, constant folding, insufficient warm-up, loop optimisations), JOL, JFR, `jcmd`.
- The conventions of this curriculum: [Spec]/[API contract]/[HotSpot]/[Measured]; the three levels of performance evidence; the benchmark record.

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Release model, LTS, JEP process, feature lifecycle labels |
| 2 | Evolution tour Java 8 → 17 through the JEPs |
| 3 | Evolution tour Java 17 → 25; what is preview, incubator and experimental in JDK 25 |
| 4 | Removals, deprecations and migration of Java 7 / Java EE code; `jdeps`, `jdeprscan` |
| 5 | JMH, JOL, JFR, `jcmd`; benchmarking pitfalls; when JMH is the wrong tool |
| 6 | Consolidation: personal feature-status table, measurement environment record, assessment |

**Official reading**

- [JDK 25 project page](https://openjdk.org/projects/jdk/25/) and [JEP index](https://openjdk.org/jeps/0)
- [JEP 12: Preview Features](https://openjdk.org/jeps/12), [JEP 11: Incubator Modules](https://openjdk.org/jeps/11)
- [JDK 25 release notes](https://jdk.java.net/25/release-notes)
- [Oracle JDK Migration Guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/migrate/)
- [JDK 25 tool reference: `jdeps`, `jdeprscan`, `jcmd`, `jfr`, `javap`](https://docs.oracle.com/en/java/javase/25/docs/specs/man/)
- [JMH](https://github.com/openjdk/jmh) and the [JMH samples](https://github.com/openjdk/jmh/tree/master/jmh-samples/src/main/java/org/openjdk/jmh/samples)
- [JOL](https://github.com/openjdk/jol)
- Aleksey Shipilëv, [Nanotrusting the Nanotime](https://shipilev.net/blog/2014/nanotrusting-nanotime/)

**Practical learning objectives**

- Set up a JDK 25 project with JMH, JOL and JFR available; decide the build tool.
- Run `jdeps`/`jdeprscan` against a sample of legacy-style code and interpret the output.
- Record the measurement environment once, for reuse in all later benchmark records.

**Expected outcomes**

- A personal, JEP-cited feature-status table.
- A working measurement setup and a documented environment record.

**Assessment criteria**

- Can classify any JDK 25 feature as final/preview/incubator/experimental/product/deprecated/removed and cite its JEP.
- Can explain at least three ways a naive microbenchmark produces misleading results, and how JMH addresses them.
- Can explain what happened to JAX-WS, JAXB and the Security Manager, and what replaces them.

---

### Week 2 — Modern language features and data-oriented programming

**Learning objectives**

- Understand what each modern language feature compiles to and what the JVM sees.
- Design with records, sealed hierarchies and pattern matching; know their limits.
- Decide between POJO and record, and between inheritance, composition and sealed hierarchies.

**Topics and subtopics**

- `var` ([JEP 286](https://openjdk.org/jeps/286)): inference rules and readability trade-offs.
- Switch expressions ([JEP 361](https://openjdk.org/jeps/361)) and text blocks ([JEP 378](https://openjdk.org/jeps/378)): semantics, compiled form.
- Records ([JEP 395](https://openjdk.org/jeps/395)): canonical and compact constructors, generated accessors, `equals`/`hashCode`/`toString` bootstrapped via `invokedynamic` (`java.lang.runtime.ObjectMethods`), the `Record` class attribute, serialization semantics, shallow immutability.
- **Records and JPA:**
  - Jakarta Persistence 3.2 states that a record may not be designated as an entity ([spec](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)).
  - Why records are unsuitable as conventional entities: entities require a no-arg constructor and must not be final, with non-final persistent fields; records are final with final fields and only a canonical constructor. Entities are mutable, managed objects (dirty checking, merge, generated identifiers assigned after construction). Providers typically use subclass-based proxies for lazy loading, which a final class cannot support. Record equality covers all components, whereas entity equality must be identity-based and stable across lifecycle states without touching lazy associations.
  - Where records fit: DTOs and query projections (JPQL constructor expressions `SELECT new …`; class-based projections in [Spring Data JPA](https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html)), immutable results crossing layer boundaries, and — since Jakarta Persistence 3.2 — embeddable classes and ID classes.
- Sealed classes ([JEP 409](https://openjdk.org/jeps/409)): `permits`, the `PermittedSubclasses` attribute, exhaustiveness.
- Pattern matching: `instanceof` ([JEP 394](https://openjdk.org/jeps/394)), `switch` ([JEP 441](https://openjdk.org/jeps/441)), record patterns ([JEP 440](https://openjdk.org/jeps/440)), unnamed variables and patterns ([JEP 456](https://openjdk.org/jeps/456)); guards, dominance, exhaustiveness and `MatchException`.
- JDK 25 finals: flexible constructor bodies ([JEP 513](https://openjdk.org/jeps/513)), compact source files and instance main methods ([JEP 512](https://openjdk.org/jeps/512)), module import declarations ([JEP 511](https://openjdk.org/jeps/511)).
- JDK 25 preview: primitive types in patterns ([JEP 507](https://openjdk.org/jeps/507)) — awareness only, requires `--enable-preview`.
- Data-oriented programming versus classic OOP.

**Spec vs implementation:** language semantics are [Spec] (JLS); the use of `invokedynamic` for record methods is a javac/JDK implementation choice.

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | `var`, switch expressions, text blocks; inspect compiled output with `javap` |
| 2 | Records: semantics, bytecode, limitations; records and JPA |
| 3 | Sealed classes and exhaustiveness; inheritance vs composition vs sealed |
| 4 | Pattern matching: `instanceof`, `switch`, record patterns, guards, unnamed patterns |
| 5 | JDK 25 language finals (513, 512, 511), preview 507; data-oriented design |
| 6 | Consolidation; decision-guide entries; Project 1 domain design |

**Official reading**

- [JLS SE 25](https://docs.oracle.com/javase/specs/jls/se25/html/index.html): §8.10 (records), §8.1.1.2 (sealed classes), §14.11 and §15.28 (switch), §14.30 (patterns)
- [Java Language Updates (JDK 25)](https://docs.oracle.com/en/java/javase/25/language/)
- The JEPs listed above
- Brian Goetz, [Data Oriented Programming in Java](https://www.infoq.com/articles/data-oriented-programming-java/) (InfoQ)
- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)

**Practical learning objectives**

- Read the bytecode of a record, a sealed hierarchy and a pattern-matching switch with `javap -c -p -v`.
- Model a small domain with records and sealed interfaces and process it exhaustively.

**Expected outcomes**

- Decision-guide entries: *POJO vs Record*; *Inheritance vs Composition vs Sealed Classes*.
- Project 1 domain model design.

**Assessment criteria**

- Can explain what each feature compiles to and which parts are [Spec] versus javac implementation.
- Can explain precisely why a record cannot be a JPA entity and where records are the right choice in a JPA application.
- Can justify a record-or-class and a sealed-or-open decision for a given design.

---

### Week 3 — Collections internals and generics

**Learning objectives**

- Understand the internal structure, complexity and memory footprint of the core collections.
- Understand generics as a compile-time system: erasure, bridges, wildcards and their consequences.

**Topics and subtopics**

- `ArrayList`: backing array, growth policy, `System.arraycopy`, `modCount` and fail-fast iteration.
- `LinkedList`: node structure, per-node overhead, poor cache locality. `ArrayDeque` as the usual queue/deque choice.
- `HashMap`: hash spreading, table sizing and resize, load factor, collision handling and treeification ([JEP 180](https://openjdk.org/jeps/180)); `LinkedHashMap` (access order, LRU); `TreeMap` (red-black tree).
- `equals`/`hashCode`/`compareTo` contracts and what breaks when they are violated.
- Immutable collections ([JEP 269](https://openjdk.org/jeps/269)) versus unmodifiable views; Sequenced Collections ([JEP 431](https://openjdk.org/jeps/431)).
- Footprint measurement with JOL.
- Generics: erasure, bridge methods, reifiable vs non-reifiable types, arrays vs generics, PECS and wildcards, type inference, recursive bounds, heap pollution and `@SafeVarargs`, and what the `Signature` attribute preserves for reflection.

**Spec vs implementation:** complexity guarantees and contracts are [API contract]; resize policy and treeification are [HotSpot/OpenJDK]; erasure and bridge methods are [Spec] (JLS §4.6, §8.4.8.3, §15.12.4.5).

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Lists and deques: read `ArrayList`, `LinkedList`, `ArrayDeque` source; ArrayList vs LinkedList |
| 2 | `HashMap` and `LinkedHashMap` source; hashing contracts; treeification |
| 3 | `TreeMap`, immutable vs unmodifiable, Sequenced Collections; footprint with JOL |
| 4 | Generics I: erasure, bridge methods (with `javap`), reifiability, arrays vs generics |
| 5 | Generics II: wildcards, PECS, inference, heap pollution |
| 6 | Consolidation; decision-guide entries; Project 1 core data structures |

**Official reading**

- API docs: [`ArrayList`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/ArrayList.html), [`HashMap`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/HashMap.html), [`SequencedCollection`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/SequencedCollection.html)
- OpenJDK source (`src/java.base/share/classes/java/util/`): [`ArrayList.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/ArrayList.java), [`HashMap.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/HashMap.java), [`TreeMap.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/TreeMap.java)
- [JLS SE 25 chapter 4 (types)](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html)
- Angelika Langer, [Java Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)
- Joshua Bloch, *Effective Java*, 3rd ed., chapter 5 (book)

**Practical learning objectives**

- Measure the footprint of equivalent `ArrayList`/`LinkedList`/`HashMap` contents with JOL.
- Observe a bridge method and heap pollution in compiled code.

**Expected outcomes**

- Decision-guide entries: *ArrayList vs LinkedList*; the HashMap half of *HashMap vs ConcurrentHashMap*.
- Project 1 core collections chosen with written justification.

**Assessment criteria**

- Can predict the internal state of a `HashMap` after a series of operations and resizes.
- Can explain a compiler-generated bridge method and why it exists.
- Can support an ArrayList-vs-LinkedList decision with theoretical, documented and measured evidence, clearly separated.

---

### Week 4 — Lambdas, streams and Optional

**Learning objectives**

- Understand how lambdas and method references are compiled and linked at run time.
- Understand stream pipeline internals well enough to predict cost and behaviour.
- Know when parallel streams help, and what Optional is for.

**Topics and subtopics**

- Functional interfaces; default methods; effectively final capture.
- Lambda translation: `invokedynamic`, `LambdaMetafactory`, hidden classes ([JEP 371](https://openjdk.org/jeps/371)); capturing versus non-capturing lambdas; the four kinds of method reference.
- Stream internals: `AbstractPipeline`, `Sink` chains, `Spliterator`, stream flags, laziness, short-circuiting, stateful operations.
- Collectors: characteristics, `groupingBy` with downstream collectors, `toMap` pitfalls (duplicates, nulls), `teeing`.
- Stream gatherers ([JEP 485](https://openjdk.org/jeps/485), final since JDK 24).
- Primitive streams and boxing cost.
- Parallel streams: splitting, the ForkJoin common pool, ordering and reductions, the N×Q cost model.
- `Optional`: intended use as a return type; misuse as fields, parameters, collection elements.

**Spec vs implementation:** stream semantics are [API contract] (`java.util.stream` package documentation); pipeline classes and lambda metafactory strategy are [HotSpot/OpenJDK].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Functional interfaces and lambda translation (`javap -c -p`, `invokedynamic`), capture |
| 2 | Stream pipeline internals: source reading |
| 3 | Collectors and gatherers |
| 4 | Parallel streams: splitting, common pool, when it helps and when it hurts |
| 5 | Optional; loops vs streams — theoretical, documented and measured (JMH) |
| 6 | Consolidation; decision-guide entry; Project 1 analytics complete |

**Official reading**

- [`java.util.stream` package documentation](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/stream/package-summary.html)
- [`LambdaMetafactory`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/invoke/LambdaMetafactory.html), [`Gatherer`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/stream/Gatherer.html), [`Optional`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/Optional.html)
- Brian Goetz, [State of the Lambda](https://cr.openjdk.org/~briangoetz/lambda/lambda-state-final.html) and [Translation of Lambda Expressions](https://cr.openjdk.org/~briangoetz/lambda/lambda-translation.html)
- Doug Lea et al., [When to use parallel streams](https://gee.cs.oswego.edu/dl/html/StreamParallelGuidance.html)
- [JEP 485: Stream Gatherers](https://openjdk.org/jeps/485)

**Practical learning objectives**

- Trace a stream pipeline from source to terminal operation in the debugger and in source.
- Benchmark a loop versus an equivalent stream with JMH and explain the result with a profiler.

**Expected outcomes**

- Decision-guide entry: *Loops vs Streams*.
- Project 1 analytics queries complete, with at least one documented benchmark.

**Assessment criteria**

- Can explain how a lambda becomes an object at run time and what it costs.
- Can trace one pipeline call by call and identify where boxing and allocation occur.
- Can explain a measured loop-vs-stream difference with evidence, and state its limitations.

---

### Week 5 — Threads, the Java Memory Model, locks and atomics

**Learning objectives**

- Reason about concurrent correctness using happens-before rather than intuition.
- Understand how monitors, AQS-based locks and CAS-based atomics work and what they cost.

**Topics and subtopics**

- Platform threads: lifecycle, mapping to OS threads, stack size, context-switch cost, interruption, thread dumps.
- Java Memory Model (JLS §17.4): actions, program order, synchronises-with, happens-before, data races, sequential consistency for data-race-free programs; `volatile`; final-field semantics (§17.5); safe publication.
- Hardware memory models and fences (overview only).
- VarHandle access modes ([JEP 193](https://openjdk.org/jeps/193)): plain, opaque, acquire/release, volatile.
- `synchronized`: monitors, the object header, lock states; biased locking deprecated and disabled ([JEP 374](https://openjdk.org/jeps/374)).
- `AbstractQueuedSynchronizer`; `ReentrantLock` and `Condition`; `ReentrantReadWriteLock`; `StampedLock`.
- CAS, `Atomic*`, ABA, `LongAdder` (contention striping), false sharing and `@Contended`.
- jcstress for testing concurrent code.

**Spec vs implementation:** happens-before, `volatile` and final-field rules are [Spec]; lock acquisition paths, object header layout and spinning strategies are [HotSpot/OpenJDK].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Platform threads: cost, states, interruption, thread dumps |
| 2 | JMM I: JLS §17.4, happens-before, `volatile`, safe publication |
| 3 | JMM II: final fields, VarHandle access modes, memory order modes; jcstress |
| 4 | `synchronized` internals; AQS; `ReentrantLock`, `Condition`, read-write and stamped locks |
| 5 | CAS, atomics, `LongAdder`, false sharing; contention measurement |
| 6 | Consolidation; decision-guide entry; Project 2 design |

**Official reading**

- [JLS SE 25 chapter 17: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se25/html/jls-17.html)
- [`java.util.concurrent` package documentation](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/package-summary.html) (memory consistency properties), [`VarHandle`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/invoke/VarHandle.html), [`AbstractQueuedSynchronizer`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/locks/AbstractQueuedSynchronizer.html)
- Doug Lea, [Using JDK 9 Memory Order Modes](https://gee.cs.oswego.edu/dl/html/j9mm.html) and [The JSR-133 Cookbook for Compiler Writers](https://gee.cs.oswego.edu/dl/jmm/cookbook.html)
- Jeremy Manson and Brian Goetz, [JSR 133 (Java Memory Model) FAQ](https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html)
- Aleksey Shipilëv, [Java Memory Model Pragmatics](https://shipilev.net/blog/2014/jmm-pragmatics/) and [Close Encounters of the JMM Kind](https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/)
- [jcstress](https://github.com/openjdk/jcstress) and its samples
- Brian Goetz et al., *Java Concurrency in Practice* (book)

**Practical learning objectives**

- Demonstrate a data race and its fix with jcstress.
- Measure `synchronized`, `ReentrantLock`, `AtomicLong` and `LongAdder` under varying contention with JMH.

**Expected outcomes**

- Decision-guide entry: *synchronized vs ReentrantLock vs Atomic variables*.
- Project 2 design, with shared state and its protection identified.

**Assessment criteria**

- Can explain, using happens-before, why a given racy program is broken and which change fixes it — and confirm with jcstress.
- Can explain what AQS provides and how `ReentrantLock` builds on it.
- Can separate [Spec] guarantees from HotSpot locking implementation details.

---

### Week 6 — Executors, CompletableFuture and concurrent collections

**Learning objectives**

- Configure executors deliberately: pool type, size, queue and rejection policy.
- Compose asynchronous work with CompletableFuture and know exactly which thread runs what.
- Understand ConcurrentHashMap and other concurrent collections internally.

**Topics and subtopics**

- `ThreadPoolExecutor`: the `ctl` state field, core/max sizes, queue types, rejection policies, backpressure; pool sizing with Little's law.
- `ScheduledThreadPoolExecutor`; `ExecutorService` lifecycle and `AutoCloseable` (since JDK 19).
- `ForkJoinPool`: work stealing, the common pool, managed blocking.
- `Future` limitations; `CompletableFuture`: completion stages, async vs non-async variants, which thread runs callbacks, default executor, exception propagation and `CompletionException`, `orTimeout`/`completeOnTimeout` ([JEP 266](https://openjdk.org/jeps/266)), cancellation semantics.
- `ConcurrentHashMap` (JDK 8 redesign, [JEP 155](https://openjdk.org/jeps/155)): CAS on empty bins, per-bin locking, cooperative resize, counter cells, `computeIfAbsent` caveats, weakly consistent iteration, no `null`.
- `CopyOnWriteArrayList`, `ConcurrentLinkedQueue`, `BlockingQueue` implementations, `ConcurrentSkipListMap`.

**Spec vs implementation:** memory consistency effects of executors and concurrent collections are [API contract]; bin locking and resize transfer are [HotSpot/OpenJDK].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | `ThreadPoolExecutor` source; sizing; rejection and backpressure |
| 2 | `ForkJoinPool` and work stealing; parallel streams vs ExecutorService |
| 3 | Future vs CompletableFuture; composition and callback threads |
| 4 | CompletableFuture error handling, timeouts, cancellation, pitfalls |
| 5 | `ConcurrentHashMap` internals and other concurrent collections |
| 6 | Consolidation; decision-guide entries; Project 2 on platform threads |

**Official reading**

- API docs: [`ThreadPoolExecutor`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html), [`ForkJoinPool`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/ForkJoinPool.html), [`CompletableFuture`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/CompletableFuture.html), [`ConcurrentHashMap`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)
- OpenJDK source: [`ThreadPoolExecutor.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/concurrent/ThreadPoolExecutor.java), [`ConcurrentHashMap.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/concurrent/ConcurrentHashMap.java), [`CompletableFuture.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/concurrent/CompletableFuture.java)
- [JEP 155](https://openjdk.org/jeps/155), [JEP 266](https://openjdk.org/jeps/266)
- *Java Concurrency in Practice*, chapters 6–8 (book)

**Practical learning objectives**

- Configure an executor for a stated workload and observe rejection and queueing behaviour.
- Log callback threads in a CompletableFuture chain to verify predictions.

**Expected outcomes**

- Decision-guide entries: *Future vs CompletableFuture*; *Parallel Streams vs ExecutorService*; completion of *HashMap vs ConcurrentHashMap*.
- Project 2 running on platform-thread executors.

**Assessment criteria**

- Can size and configure a pool for a described workload with a written rationale.
- Can predict which thread runs each stage of a CompletableFuture chain.
- Can explain how ConcurrentHashMap achieves thread safety without a global lock, and its consistency limits.

---

### Week 7 — Virtual threads, structured concurrency and scoped values

**Learning objectives**

- Understand why virtual threads exist and how they are implemented.
- Know when virtual threads help, when they do not, and what can still go wrong.
- Use structured concurrency (preview) and scoped values (final) correctly.

**Topics and subtopics**

- Motivation: thread-per-request vs asynchronous styles; Little's law ([JEP 444](https://openjdk.org/jeps/444)).
- Implementation: continuations, carrier threads, the ForkJoinPool-based scheduler, mounting/unmounting, stack chunks on the heap; blocking operations in the JDK adapted for virtual threads.
- Pinning: since [JEP 491](https://openjdk.org/jeps/491) (JDK 24), `synchronized` no longer pins. Remaining cases documented in JEP 491 include native frames (native methods or FFM calls that call back into Java code that blocks) and blocking inside a class initializer. The `jdk.VirtualThreadPinned` JFR event.
- `ThreadLocal` costs with many threads; pooling virtual threads is an anti-pattern; limiting concurrency with semaphores; no benefit for CPU-bound work.
- Structured concurrency — **Preview (5th) in JDK 25** ([JEP 505](https://openjdk.org/jeps/505)): `StructuredTaskScope.open()`, `Joiner` policies, `fork`/`join`, cancellation and failure propagation, observability. Requires `--enable-preview`; the API has changed across previews and is still changing (see the [tracker](jdk-26-27-tracker.md)).
- Scoped values — **Final in JDK 25** ([JEP 506](https://openjdk.org/jeps/506)): binding, rebinding, inheritance by structured-concurrency subtasks, comparison with `ThreadLocal`.
- Observability: thread dumps (`jcmd <pid> Thread.dump_to_file`), JFR events.

**Spec vs implementation:** virtual thread behaviour visible through the `Thread` API is [API contract]; the scheduler, continuation mechanism and the specific pinning cases are [HotSpot/OpenJDK].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Why virtual threads: Little's law, blocking vs async; the API |
| 2 | Internals: continuations, scheduler, mounting; reading `VirtualThread.java` |
| 3 | Pinning, JEP 491, JFR events, `ThreadLocal`; platform vs virtual measurements |
| 4 | Structured concurrency (preview): scopes, joiners, cancellation, failure policies |
| 5 | Scoped values vs `ThreadLocal`; inheritance into subtasks |
| 6 | Consolidation; decision-guide entry; Project 2 migration; Project 3 kick-off |

**Official reading**

- [JEP 444](https://openjdk.org/jeps/444), [JEP 491](https://openjdk.org/jeps/491), [JEP 505](https://openjdk.org/jeps/505), [JEP 506](https://openjdk.org/jeps/506)
- [Java Core Libraries guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/core/) — virtual threads, structured concurrency and scoped values chapters
- API docs: [`Thread`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/Thread.html), [`ScopedValue`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/ScopedValue.html), [`StructuredTaskScope`](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/StructuredTaskScope.html)
- OpenJDK source: [`VirtualThread.java`](https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/lang/VirtualThread.java)

**Practical learning objectives**

- Detect a pinning event with JFR and explain its cause.
- Measure platform vs virtual threads on an IO-bound and a CPU-bound workload with a load test and JFR.

**Expected outcomes**

- Decision-guide entry: *Platform Threads vs Virtual Threads*.
- Project 2 migrated to virtual threads and structured concurrency; Project 3 started.

**Assessment criteria**

- Can explain how a virtual thread unmounts on blocking IO and what prevents unmounting.
- Can explain when virtual threads will not improve throughput.
- Can state the status of structured concurrency and scoped values in JDK 25 with their JEPs.

---

### Week 8 — JVM architecture: class loading, bytecode and JIT

**Learning objectives**

- Understand the class file, class loading and bytecode execution as specified by the JVMS.
- Understand how HotSpot turns bytecode into optimised machine code, and how to observe it.

**Topics and subtopics**

- Run-time data areas (JVMS §2.5) and their HotSpot realisation: heap, Metaspace, code cache, thread stacks.
- Class file format (JVMS ch. 4); `javap`; the Class-File API ([JEP 484](https://openjdk.org/jeps/484)).
- Loading, linking (verification, preparation, resolution) and initialisation (JVMS ch. 5); class loader delegation; built-in loaders since JDK 9.
- Class Data Sharing and the AOT cache: [JEP 483](https://openjdk.org/jeps/483), [JEP 514](https://openjdk.org/jeps/514), [JEP 515](https://openjdk.org/jeps/515).
- Method invocation: `invokestatic`/`invokevirtual`/`invokeinterface`/`invokespecial`/`invokedynamic`; vtables, itables, class hierarchy analysis; constant dynamic.
- Interpreter and tiered compilation (C1/C2), profiling, inlining, deoptimisation, on-stack replacement, code cache; intrinsics.
- Escape analysis and scalar replacement; TLAB allocation.
- Object layout: headers, compressed oops and class pointers; compact object headers ([JEP 519](https://openjdk.org/jeps/519), product but opt-in in JDK 25).

**Spec vs implementation:** class file format, loading/linking/initialisation and bytecode semantics are [Spec] (JVMS); everything about interpretation strategy, JIT, escape analysis, TLABs and object layout is [HotSpot/OpenJDK].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Run-time data areas and class file structure; JVMS ch. 2 and 4 |
| 2 | Class loading and initialisation; CDS and AOT cache experiments |
| 3 | Bytecode execution, invocation instructions, dispatch, `invokedynamic` |
| 4 | JIT: tiered compilation, inlining, deoptimisation (`-XX:+PrintCompilation`, JFR, JITWatch) |
| 5 | Escape analysis, scalar replacement, TLABs, object layout and compact headers (JOL) |
| 6 | Consolidation; Project 4 JIT and allocation investigations |

**Official reading**

- [JVMS SE 25](https://docs.oracle.com/javase/specs/jvms/se25/html/index.html): [ch. 2](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-2.html), [ch. 4](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-4.html), [ch. 5](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-5.html), [ch. 6](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-6.html)
- [Java Virtual Machine Guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/vm/)
- [HotSpot Runtime Overview](https://openjdk.org/groups/hotspot/docs/RuntimeOverview.html) and [HotSpot Glossary](https://openjdk.org/groups/hotspot/docs/HotSpotGlossary.html)
- Aleksey Shipilëv, [JVM Anatomy Quarks](https://shipilev.net/jvm/anatomy-quarks/)
- [JITWatch](https://github.com/AdoptOpenJDK/jitwatch)

**Practical learning objectives**

- Observe a method being compiled, inlined and deoptimised.
- Show allocation removed by escape analysis, and compare object sizes with and without compact object headers.

**Expected outcomes**

- First Project 4 investigation reports (JIT and allocation).

**Assessment criteria**

- Can walk through loading, linking and initialisation of a class with JVMS references.
- Can explain tiered compilation and a deoptimisation trigger with observed evidence.
- Can clearly separate what the JVMS guarantees from what HotSpot happens to do.

---

### Week 9 — Memory management, GC and diagnostics

**Learning objectives**

- Understand how the main OpenJDK collectors work and the trade-offs between them.
- Diagnose memory and GC problems from evidence (logs, JFR, heap dumps), not folklore.

**Topics and subtopics**

- Fundamentals: GC roots, reachability, marking, copying and compaction, generational hypothesis, write and load barriers, safepoints.
- Serial and Parallel collectors.
- G1: regions, remembered sets, SATB marking, young and mixed collections, humongous objects, pause-time goal.
- ZGC: concurrent relocation, coloured pointers and load barriers; generational since JDK 21 ([JEP 439](https://openjdk.org/jeps/439)), default mode in 23 ([JEP 474](https://openjdk.org/jeps/474)), the only mode since 24 ([JEP 490](https://openjdk.org/jeps/490)).
- Shenandoah, including generational mode as a product feature in JDK 25 ([JEP 521](https://openjdk.org/jeps/521)); Epsilon.
- Tuning methodology: throughput, latency and footprint goals; heap sizing; container awareness.
- Diagnostics: unified GC logging ([JEP 158](https://openjdk.org/jeps/158)), JFR and JMC, heap dumps and Eclipse MAT, Native Memory Tracking, async-profiler; JFR features in JDK 25 ([JEP 518](https://openjdk.org/jeps/518), [JEP 520](https://openjdk.org/jeps/520); [JEP 509](https://openjdk.org/jeps/509) is experimental and Linux-only).
- Reference types (soft, weak, phantom), `Cleaner` vs finalization ([JEP 421](https://openjdk.org/jeps/421)); direct and off-heap memory.

**Spec vs implementation:** the JVMS requires only automatic storage management (§2.5.3) and specifies no algorithm — every collector and flag is [HotSpot/OpenJDK]. Reference-type semantics are [API contract].

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | GC fundamentals; Serial and Parallel; reading GC logs |
| 2 | G1 internals and tuning |
| 3 | ZGC and Shenandoah; G1 vs ZGC experiments |
| 4 | Diagnostics: JFR/JMC, heap dumps, MAT, NMT, async-profiler |
| 5 | Leaks, reference types, `Cleaner`, direct memory; container memory sizing |
| 6 | Consolidation; decision-guide entry; Project 4 GC report; Project 1 revisited under different collectors |

**Official reading**

- [HotSpot Virtual Machine Garbage Collection Tuning Guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/gctuning/)
- [Troubleshooting Guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/troubleshoot/)
- [`java` command reference](https://docs.oracle.com/en/java/javase/25/docs/specs/man/java.html) (GC and `-Xlog` options)
- [ZGC wiki](https://wiki.openjdk.org/display/zgc/Main), [Shenandoah wiki](https://wiki.openjdk.org/display/shenandoah/Main)
- [JDK Mission Control](https://github.com/openjdk/jmc), [Eclipse MAT](https://eclipse.dev/mat/), [async-profiler](https://github.com/async-profiler/async-profiler)
- Richard Jones, Antony Hosking, Eliot Moss, *The Garbage Collection Handbook*, 2nd ed. (book)

**Practical learning objectives**

- Run the same workload under G1 and ZGC and compare pause times, throughput, CPU and memory from GC logs and JFR.
- Find a deliberately introduced leak from a heap dump.

**Expected outcomes**

- Decision-guide entry: *G1 vs ZGC*.
- Project 4 GC investigation report.

**Assessment criteria**

- Can explain how G1 and ZGC each achieve their pause behaviour and what they pay for it.
- Can choose a collector for a described workload from collected evidence.
- Can diagnose a memory leak and an allocation hot spot with tools, not guesses.

---

### Week 10 — IO, NIO, networking, HttpClient and runtime metadata

This week is deliberately lighter: about 8 hours on IO, NIO, networking and HttpClient; about 2 hours on modules, reflection and annotations at decision depth; about 2 hours on the final assessment and retrospective. The FFM API is in the [optional extension](#7-optional-post-course-extension).

**Learning objectives**

- Understand the NIO model (buffers, channels, selectors) and how it relates to classic IO and virtual threads.
- Use `HttpClient` effectively in blocking and asynchronous styles.
- Understand modules, reflection and annotations well enough to make framework and migration decisions.

**Topics and subtopics**

- Classic IO internals: decorator streams, buffering, character encoding (UTF-8 by default, [JEP 400](https://openjdk.org/jeps/400)).
- NIO buffers: heap vs direct, position/limit/capacity; channels; `FileChannel`, memory-mapped files, `transferTo`. NIO.2 (`Path`, `Files`) as a brief recap.
- Non-blocking sockets and selectors; socket reimplementations ([JEP 353](https://openjdk.org/jeps/353), [JEP 373](https://openjdk.org/jeps/373)) and their importance for virtual threads; Unix-domain socket channels ([JEP 380](https://openjdk.org/jeps/380)).
- `HttpClient` ([JEP 321](https://openjdk.org/jeps/321)): HTTP/1.1 and HTTP/2, connection reuse, sync vs async APIs, timeouts, cancellation, body handlers. (HTTP/3 arrives in JDK 26 — see the [tracker](jdk-26-27-tracker.md).)
- Modules at decision depth: `module-info`, exports vs opens, strong encapsulation ([JEP 403](https://openjdk.org/jeps/403)) and why frameworks need `--add-opens`; `jlink` (awareness).
- Reflection: reimplementation on method handles ([JEP 416](https://openjdk.org/jeps/416)); `MethodHandle` vs reflection.
- Annotations: retention policies, where annotations live in the class file, annotation processing overview.

**Spec vs implementation:** IO/NIO and HttpClient behaviour are [API contract]; module semantics are [Spec] (JLS §7.7, JVMS); socket and selector implementations are [HotSpot/OpenJDK] and platform-specific.

**Daily learning sequence**

| Day | Focus |
|---|---|
| 1 | Classic IO vs NIO: buffers, channels, mapped files, zero-copy transfer |
| 2 | Non-blocking networking: selectors; socket reimplementations and virtual threads |
| 3 | `HttpClient`: protocols, connection reuse, sync/async, timeouts and cancellation; finish Project 3 |
| 4 | Measure Project 3 (blocking on virtual threads vs async) with a load test and JFR; Classic IO vs NIO entry |
| 5 | Modules, reflection and annotations at decision depth |
| 6 | Final assessment, retrospective, roadmap update |

**Official reading**

- API docs: [`java.nio` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/nio/package-summary.html), [`java.nio.channels` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/nio/channels/package-summary.html), [`java.net.http` module](https://docs.oracle.com/en/java/javase/25/docs/api/java.net.http/module-summary.html)
- [Java Core Libraries guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/core/)
- [JEP 321](https://openjdk.org/jeps/321), [JEP 353](https://openjdk.org/jeps/353), [JEP 380](https://openjdk.org/jeps/380), [JEP 261](https://openjdk.org/jeps/261), [JEP 403](https://openjdk.org/jeps/403), [JEP 416](https://openjdk.org/jeps/416)
- [JLS SE 25 §7.7 (module declarations)](https://docs.oracle.com/javase/specs/jls/se25/html/jls-7.html#jls-7.7)

**Practical learning objectives**

- Compare classic stream copying with `FileChannel.transferTo` and memory-mapped reads, with a documented benchmark record.
- Build and measure the two styles of Project 3.

**Expected outcomes**

- Decision-guide entry: *Classic IO vs NIO*.
- Project 3 complete; Project 4 final report; completed decision guide; retrospective in `PROGRESS.md`.

**Assessment criteria**

- Can explain the buffer/channel/selector model and when non-blocking IO is still worth its complexity now that virtual threads exist.
- Can explain why a framework fails under strong encapsulation and how `opens`/`--add-opens` resolves it.
- Can present the full decision guide with evidence for each entry.

---

## 5. Engineering decision guide

Each entry is written in the week the topic is studied, using my own evidence. Every entry must cover: **implementation**, **performance**, **memory**, **scalability**, **limitations** and **appropriate usage** — with each claim labelled by source ([Spec]/[API contract]/[HotSpot]/[Measured]) and evidence level (theoretical/documented/measured).

| Comparison | Week | What the curriculum must teach |
|---|---|---|
| **Loops vs Streams** | 4 | *Implementation:* plain bytecode loop vs pipeline objects, `Sink` chain and lambda dispatch. *Performance:* the JIT can often inline simple pipelines, but not always — measure, do not assume. *Memory:* pipeline stage objects, lambda capture, boxing in non-primitive streams. *Scalability:* streams can be parallelised; loops cannot without restructuring. *Limitations:* no `break`, awkward checked exceptions, harder debugging. *Usage:* streams for declarative transformation; loops for hot paths, complex control flow or early exit. |
| **ArrayList vs LinkedList** | 3 | *Implementation:* contiguous array vs doubly linked nodes. *Performance:* theoretical O(1) insert in LinkedList is usually outweighed by O(n) traversal and cache misses — demonstrate with JMH. *Memory:* per-node object overhead measured with JOL. *Limitations:* LinkedList random access is O(n). *Usage:* ArrayList by default; ArrayDeque for queues and deques; LinkedList rarely justified. |
| **HashMap vs ConcurrentHashMap** | 3, 6 | *Implementation:* single table with no synchronisation vs CAS on empty bins plus per-bin locking and cooperative resize. *Performance:* uncontended single-thread cost vs scalable concurrent access. *Memory:* additional structure in CHM. *Scalability:* CHM avoids a global lock. *Limitations:* CHM forbids `null`; iterators are weakly consistent; `compute*` functions run while holding a bin lock. *Usage:* HashMap for confined data; CHM for shared mutable maps; `Collections.synchronizedMap` rarely. |
| **Platform Threads vs Virtual Threads** | 7 | *Implementation:* 1:1 OS thread vs continuation scheduled on carrier threads. *Performance:* throughput gains for IO-bound concurrency, none for CPU-bound work. *Memory:* fixed native stack vs heap-allocated stack chunks. *Scalability:* millions of virtual threads vs thousands of platform threads. *Limitations:* remaining pinning cases, `ThreadLocal` costs, no pooling. *Usage:* virtual threads for thread-per-request IO; platform threads for CPU-bound pools. |
| **Future vs CompletableFuture** | 6 | *Implementation:* blocking result holder vs completion stage graph. *Performance:* blocking threads vs non-blocking composition. *Memory:* dependent-stage objects. *Scalability:* composition without blocking threads. *Limitations:* CF callbacks may run on unexpected threads; `cancel` does not interrupt the running task; exception wrapping. *Usage:* Future for simple submit-and-wait; CF for asynchronous pipelines — and reconsider both now that virtual threads make blocking cheap. |
| **synchronized vs ReentrantLock vs Atomic variables** | 5 | *Implementation:* monitor vs AQS queue vs CAS. *Performance:* behaviour under low and high contention, measured. *Memory:* header-based monitor vs lock object vs field. *Scalability:* `LongAdder` vs `AtomicLong` under contention. *Limitations:* `synchronized` lacks `tryLock`, timeouts, fairness and multiple conditions; atomics only cover single-variable invariants. *Usage:* `synchronized` for simple critical sections; `ReentrantLock` for advanced locking features; atomics for single-variable state. Since JEP 491, virtual threads are no longer a reason to prefer `ReentrantLock`. |
| **Parallel Streams vs ExecutorService** | 6 | *Implementation:* data-parallel splitting on the shared ForkJoin common pool vs explicit task submission to a chosen pool. *Performance:* depends on N×Q, splitting cost and merge cost. *Memory:* intermediate results per split. *Scalability:* CPU-bound only. *Limitations:* shared common pool, blocking operations starve it, little control. *Usage:* parallel streams for large CPU-bound data-parallel operations; executors for task parallelism, IO and isolation. |
| **POJO vs Record** | 2 | *Implementation:* hand-written class vs transparent data carrier with generated members (`ObjectMethods` bootstrap). *Performance:* equivalent field access; generated `equals`/`hashCode`. *Memory:* same field layout. *Scalability:* immutability simplifies sharing. *Limitations:* final, no extra instance fields, shallow immutability, no setters, not usable as JPA entities. *Usage:* records for values, DTOs, projections, messages and keys; classes for mutable or identity-bearing objects such as JPA entities. |
| **Inheritance vs Composition vs Sealed Classes** | 2 | *Implementation:* open subclassing vs delegation vs closed hierarchy recorded in `PermittedSubclasses`. *Performance:* dispatch differences are usually negligible; CHA and inlining matter more (week 8). *Memory:* delegation adds objects. *Scalability:* sealed hierarchies support exhaustive switching without visitors. *Limitations:* fragile base class; delegation boilerplate; sealed hierarchies cannot be extended by clients. *Usage:* composition by default; sealed classes for closed domain alternatives; inheritance for designed extension points. |
| **Classic IO vs NIO** | 10 | *Implementation:* byte/char streams vs buffers and channels with optional selectors. *Performance:* buffering, direct buffers, zero-copy transfer, mapped files — measured. *Memory:* heap vs direct buffers and their release. *Scalability:* selectors scale connections per thread; virtual threads now give blocking IO similar scalability with simpler code. *Limitations:* NIO complexity; mapped-file limits; direct memory is outside the heap. *Usage:* classic or NIO.2 `Files` for most file work; channels for large or zero-copy transfers; selectors only where virtual threads do not fit. |
| **G1 vs ZGC** | 9 | *Implementation:* region-based collection with a pause-time goal vs mostly concurrent collection with coloured pointers and load barriers. *Performance:* throughput vs latency, measured on the same workload. *Memory:* ZGC typically needs heap headroom. *Scalability:* behaviour with large heaps. *Limitations:* barrier and CPU overheads; tuning surfaces. *Usage:* decide from GC logs and JFR for the actual workload, not from defaults or folklore. |

---

## 6. Practical projects

Only descriptions and objectives are defined here. Design, tasks and code are added in the relevant weeks.

| Project | Weeks | Core learning |
|---|---|---|
| **Employee Analytics Engine** | 2–4, revisited 9 | Records, sealed types, collections, generics, streams, collectors, gatherers; footprint and loop-vs-stream evidence; allocation and GC profile |
| **Concurrent Order Processing System** | 5–7 | Locks, atomics, concurrent collections, executors, CompletableFuture, backpressure; migration to virtual threads and structured concurrency with comparison |
| **High-Concurrency HTTP Client** | 7, 10 | `HttpClient` blocking-on-virtual-threads vs async; scoped values for request context; timeouts, cancellation; NIO underneath; load-test measurement |
| **JVM Performance Investigation Lab** | 1, 8–10 | JMH (where appropriate), JFR, JMC, GC logs, heap dumps, NMT, async-profiler; documented investigations of Projects 1–3 |

Detailed descriptions are in [ROADMAP.md](../ROADMAP.md#practical-projects).

---

## 7. Optional post-course extension

Not part of the 120-hour core; approximately 8–10 hours.

- **Foreign Function & Memory API** — Final since JDK 22 ([JEP 454](https://openjdk.org/jeps/454)): `Arena` and memory lifetimes, `MemorySegment`, `MemoryLayout`, `Linker` downcalls and upcalls, `jextract`; comparison with JNI and `sun.misc.Unsafe`; virtual-thread pinning across native frames (week 7).
- **Integrity by default:** JNI restrictions ([JEP 472](https://openjdk.org/jeps/472)); `Unsafe` memory-access deprecation ([JEP 471](https://openjdk.org/jeps/471)) and warnings ([JEP 498](https://openjdk.org/jeps/498)); final-field mutation warnings in JDK 26 ([JEP 500](https://openjdk.org/jeps/500)).
- **Optional extra:** Vector API — Incubator in JDK 25 ([JEP 508](https://openjdk.org/jeps/508)).

Reading: [JEP 454](https://openjdk.org/jeps/454), [`java.lang.foreign` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/foreign/package-summary.html), [Java Core Libraries guide (JDK 25)](https://docs.oracle.com/en/java/javase/25/core/), [jextract](https://github.com/openjdk/jextract).

---

## 8. Core references

See [resources/README.md](../resources/README.md) for the full annotated list. Primary sources:

- [Java Language Specification, SE 25](https://docs.oracle.com/javase/specs/jls/se25/html/index.html)
- [Java Virtual Machine Specification, SE 25](https://docs.oracle.com/javase/specs/jvms/se25/html/index.html)
- [JDK 25 API documentation](https://docs.oracle.com/en/java/javase/25/docs/api/index.html)
- [JDK 25 documentation home](https://docs.oracle.com/en/java/javase/25/)
- [JEP index](https://openjdk.org/jeps/0)
- [OpenJDK JDK 25 updates source](https://github.com/openjdk/jdk25u) and [OpenJDK mainline source](https://github.com/openjdk/jdk)
- [JMH](https://github.com/openjdk/jmh), [jcstress](https://github.com/openjdk/jcstress), [JOL](https://github.com/openjdk/jol)
