# advanced-java-mastery

My 10-week journey to mastering modern Java, JVM internals, concurrency, performance engineering and advanced Java concepts — documented in public, week by week, as I actually study. An open-source, practical learning resource for Java developers.

> **Status:** Week 0 — curriculum defined, study not yet started. See [PROGRESS.md](PROGRESS.md).

## Why this repository exists

I have roughly 14 years of professional Java experience, mostly with Java 7, EJB, SOAP, JPA and enterprise applications, plus Spring and Spring Boot. This repository is not a beginner course. Its goal is to understand modern Java (JDK 25 LTS) deeply enough to answer, for each important feature:

- Why does it exist, and what problem does it solve?
- How does it work internally, and what happens inside the JVM?
- What is guaranteed by the specification, and what is only a HotSpot/OpenJDK implementation detail?
- What are its advantages, limitations, hidden costs, and performance and memory implications?
- When should I use it, when should I avoid it, and what are the alternatives?

## This is a real learning journey, not a pre-generated course

Only the plan exists today. Lessons, notes, experiments, benchmarks and project code are added **progressively, in the week I actually study them**. Empty week folders are intentional. Git history is the record of the journey.

## How the repository is organised

| Path | Purpose |
|---|---|
| [curriculum/10-week-curriculum.md](curriculum/10-week-curriculum.md) | The full 10-week plan: objectives, daily sequence, readings, assessment, engineering decision guide |
| [curriculum/jdk-26-27-tracker.md](curriculum/jdk-26-27-tracker.md) | Reference-only tracker of JDK 26 and 27 changes relevant to the curriculum (not mandatory) |
| [ROADMAP.md](ROADMAP.md) | Week overview, the four progressive projects, and the optional post-course extension |
| [PROGRESS.md](PROGRESS.md) | Weekly status and study log |
| [resources/README.md](resources/README.md) | Curated, authoritative references |
| `weeks/week-NN-*/` | One folder per week, populated only when that week is studied |

## Environment

- **Primary:** JDK 25 (LTS). Preview features are used only where explicitly marked and require `--enable-preview`.
- **Comparisons:** earlier releases (8, 11, 17, 21) where the evolution of a feature matters.
- **Newer releases:** JDK 26 and 27 are non-LTS; their relevant changes are tracked separately and are not required.

## Conventions used in all notes

**Source of a claim**

- **[Spec]** — guaranteed by the JLS or JVMS (with section reference).
- **[API contract]** — guaranteed by the Javadoc.
- **[HotSpot/OpenJDK]** — an implementation detail (source file or JEP); may change between releases.
- **[Measured]** — observed in my own experiment, with environment documented.

**Strength of performance evidence**

- **Theoretical** — algorithmic complexity or design analysis.
- **Documented** — stated in a spec, Javadoc or JEP.
- **Empirically measured** — measured with JMH (microbenchmarks only), JFR/load tests (system behaviour) or GC logs (GC behaviour), with hardware, OS, JDK build, flags and limitations recorded.

**Feature status** (as of JDK 25 unless stated): Final · Preview · Incubator · Experimental · Product (opt-in) · Deprecated / Deprecated for removal · Removed — each with its JEP.

## Using this repository yourself

You are welcome to follow along, fork it, or reuse the material. Please treat the notes as one experienced developer's study record: verify claims against the linked primary sources, and re-run measurements on your own hardware before relying on them.

## Licensing

This repository uses two licences:

| Content | Licence | File |
|---|---|---|
| Original **source code**: `*.java`, build files (e.g. `pom.xml`, `build.gradle`), scripts and other executable material | [MIT](https://opensource.org/license/mit) | [LICENSE](LICENSE) |
| Original **documentation**: Markdown files, notes, diagrams, images and other non-code material | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) | [LICENSE-docs](LICENSE-docs) |

- MIT lets you reuse the code freely, including commercially, provided the copyright and licence notice is kept.
- CC BY 4.0 lets you share and adapt the documentation, including commercially, provided you give appropriate credit (for example: "Based on *advanced-java-mastery* by nishat-anjum, CC BY 4.0"), link to the licence and indicate changes.
- **Third-party material** (quoted text, linked documentation, OpenJDK source excerpts, specifications, books) remains under its own licence and is not relicensed by this repository.

Copyright (c) 2026 nishat-anjum.
