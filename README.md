# Testing C# and ASP.NET Applications with .NET

**A complete, guided path from absolute zero to confident test engineer and application builder.**

This repository is a book-length tutorial written as one single README. It teaches automated testing in the .NET world the way a patient mentor would: first you understand, then you install the tools, then you type every command and every line of code yourself, one small step at a time, until you can design, build, and test a complete C# application with an ASP.NET Core HTTP API.

You need **no prior knowledge**. Chapter 1 starts from the question "what is a software test?" and Chapter 30 ends with a mastery checklist for people who want to think like professional test engineers.

> **How to use this guide**
> - Read the chapters **in order**. Each chapter assumes the vocabulary and the code of the chapters before it.
> - **Type the commands yourself** instead of only copying and pasting them. Your fingers learn the command line.
> - After every meaningful code change, **run the tests**. Green is information; red is information.
> - When a test fails, **study the failure before changing any code**: expected value, actual value, stack trace, file, and line.
> - Do the capstone challenge in Chapter 28 without looking back at the guide. That is where learning turns into ability.

This guide uses **C#**, the **.NET SDK**, the **.NET CLI**, and **xUnit.net** as the main test framework. Every concept transfers to MSTest, NUnit, TUnit, and other .NET test frameworks, because the ideas are bigger than any one library's attribute names.

The examples target **.NET 8 or later**. If you have the .NET 10 SDK installed, you can use `net10.0` everywhere instead of `net8.0`. If you do not, use `net8.0` and everything in this guide works unchanged.

## The journey in one paragraph

You will grow one small application called **Store**. You begin with a tiny class library and your very first unit test. You then add business rules, dependencies that must be replaced with controlled test doubles, asynchronous inventory checks, a real ASP.NET Core HTTP API, integration tests that exercise that API without opening a network port, a database-testing strategy, code coverage, a continuous integration pipeline in GitHub Actions, and finally the advanced techniques that separate practitioners from masters. By the end, the repository you built with your own hands looks like this:

```text
Store.sln
├── src
│   ├── Store.Domain          (business rules: cart, items, totals)
│   ├── Store.Application     (services: checkout, orders, policies)
│   └── Store.Api             (ASP.NET Core HTTP endpoints)
└── tests
    ├── Store.Domain.UnitTests
    ├── Store.Application.UnitTests
    └── Store.Api.IntegrationTests
```

---

## Table of contents

**Part I: Understand before you type**
- [Chapter 0: How to read this guide](#chapter-0-how-to-read-this-guide)
- [Chapter 1: Introduction to software testing](#chapter-1-introduction-to-software-testing)
- [Chapter 2: A short history of software testing](#chapter-2-a-short-history-of-software-testing)
- [Chapter 3: What this guide offers you](#chapter-3-what-this-guide-offers-you)
- [Chapter 4: Why you should learn to test](#chapter-4-why-you-should-learn-to-test)

**Part II: The words and the tools**
- [Chapter 5: The vocabulary you must understand](#chapter-5-the-vocabulary-you-must-understand)
- [Chapter 6: The technology stack piece by piece](#chapter-6-the-technology-stack-piece-by-piece)
- [Chapter 7: Installing and checking the tools](#chapter-7-installing-and-checking-the-tools)

**Part III: Your first test from absolute zero**
- [Chapter 8: The Calculator walkthrough](#chapter-8-the-calculator-walkthrough)
- [Chapter 9: C# language foundations for test code](#chapter-9-c-language-foundations-for-test-code)

**Part IV: The Store domain and its unit tests**
- [Chapter 10: Creating the Store solution](#chapter-10-creating-the-store-solution)
- [Chapter 11: Building the Store domain](#chapter-11-building-the-store-domain)
- [Chapter 12: Test naming and the Arrange Act Assert pattern](#chapter-12-test-naming-and-the-arrange-act-assert-pattern)
- [Chapter 13: Writing useful unit tests](#chapter-13-writing-useful-unit-tests)
- [Chapter 14: Facts, theories, assertions, and exceptions](#chapter-14-facts-theories-assertions-and-exceptions)

**Part V: Dependencies and isolation**
- [Chapter 15: Testing dependencies with fakes, stubs, and mocks](#chapter-15-testing-dependencies-with-fakes-stubs-and-mocks)
- [Chapter 16: Testing asynchronous C# code](#chapter-16-testing-asynchronous-c-code)
- [Chapter 17: Dependency injection, time, randomness, and seams](#chapter-17-dependency-injection-time-randomness-and-seams)

**Part VI: Integration and the whole system**
- [Chapter 18: Integration testing an ASP.NET Core API](#chapter-18-integration-testing-an-aspnet-core-api)
- [Chapter 19: Testing persistence and databases](#chapter-19-testing-persistence-and-databases)
- [Chapter 20: Fixtures, lifetime, cleanup, and parallel execution](#chapter-20-fixtures-lifetime-cleanup-and-parallel-execution)

**Part VII: Professional workflows**
- [Chapter 21: Selecting, categorizing, and debugging tests](#chapter-21-selecting-categorizing-and-debugging-tests)
- [Chapter 22: Code coverage](#chapter-22-code-coverage)
- [Chapter 23: Test driven development](#chapter-23-test-driven-development)
- [Chapter 24: Testing the whole system](#chapter-24-testing-the-whole-system)
- [Chapter 25: Continuous integration with GitHub Actions](#chapter-25-continuous-integration-with-github-actions)

**Part VIII: Mastery**
- [Chapter 26: Test design and maintainability](#chapter-26-test-design-and-maintainability)
- [Chapter 27: Advanced and mastery topics](#chapter-27-advanced-and-mastery-topics)
- [Chapter 28: The capstone challenge](#chapter-28-the-capstone-challenge)
- [Chapter 29: Troubleshooting](#chapter-29-troubleshooting)
- [Chapter 30: A practical mastery checklist](#chapter-30-a-practical-mastery-checklist)

**Appendices**
- [Appendix A: Glossary](#appendix-a-glossary)
- [Appendix B: References](#appendix-b-references)
- [Appendix C: Quick command reference](#appendix-c-quick-command-reference)

---
# Part I: Understand before you type

The four chapters of this part follow one deliberate sequence: **introduction, history, what it offers, why you should use it**. You will meet the same sequence again in Chapter 6 for every individual technology (C#, .NET, ASP.NET Core, xUnit). Once you can place a tool in time, explain what it gives you, and argue why it earns its place, you understand it. After this part you will know exactly what you are about to learn and why it matters, before you install anything.

# Chapter 0: How to read this guide

## 0.1 Who this guide is for

This guide assumes you are starting from **absolute zero**:

- You may have never written a line of C#.
- You may have never opened a terminal with intent.
- You may have heard words like "unit test" or "CI" without knowing what they mean.

It also works if you already write C# but have never tested it, or if you have tested it with copy-pasted examples you never truly understood. Every concept is built from its foundations, and every line of code you are asked to type is explained twice: once **line by line**, and for the most important lines, **word by word**.

## 0.2 What you will be able to do at the end

By the end of this guide you should be able to:

1. Explain why automated tests exist and which risks they reduce.
2. Create a .NET solution that contains production projects and test projects.
3. Write readable unit tests for normal behavior, edge cases, invalid input, and exceptions.
4. Test synchronous and asynchronous C# code correctly.
5. Replace external dependencies with controlled test doubles (fakes, stubs, and mocks).
6. Test an ASP.NET Core application through HTTP without starting a real production server.
7. Choose an appropriate database-testing strategy and defend the choice.
8. Run one test, one class, one category, one project, or an entire solution.
9. Measure code coverage without confusing coverage with correctness.
10. Design tests that stay useful while production code is refactored.
11. Put the whole test suite into a continuous integration pipeline.
12. Recognize test smells and deliberately improve an existing test suite.

## 0.3 The learning order and why it is ordered this way

The order of this guide is intentional, and each stage justifies the next:

1. You first learn **what a test is** (Part I). Vocabulary before action.
2. You then meet **the tools** and install them (Part II). No tool is introduced before you know why it exists.
3. You write **one tiny test from zero** (Part III). An early success builds confidence.
4. You learn **the small amount of C# needed to read test code fluently** (Part III). Not all of C#, only the load-bearing parts.
5. You build **a real domain component and test its behavior** (Part IV). Tests stop being toys.
6. You add **collaborators and learn isolation** (Part V). This is where most real testing skill lives.
7. You move **outward from unit tests to integration tests** (Part VI). The system boundary.
8. You learn **execution, coverage, TDD, whole-system strategy, and CI** (Part VII). Professional workflow.
9. You finish with **design, advanced techniques, a capstone, troubleshooting, and self-assessment** (Part VIII). Mastery.

Do not skip the early chapters because the later chapters assume their vocabulary. If you meet a word you do not recognize, it is defined in Chapter 5 and in Appendix A.

## 0.4 The rule for every code example: read it in four passes

Every code block in this guide should be read in four passes:

1. **Shape:** What files, classes, methods, and namespaces exist? Where does this code live?
2. **Syntax:** What does each keyword, symbol, attribute, and punctuation mark do?
3. **Behavior:** What input enters the code, and what output or side effect leaves it?
4. **Test meaning:** Which behavior is protected by the test, and what failure would the test reveal?

Example. Later you will type this line:

```csharp
Assert.Equal(5, actual);
```

- **Shape:** a static method call on the `Assert` class provided by xUnit.
- **Syntax:** `Assert` is the class; `Equal` is the method; `(5, actual)` passes two arguments; `;` ends the statement.
- **Behavior:** compares the expected value `5` with the observed value stored in `actual`; if they differ, the method throws an exception.
- **Test meaning:** the test fails exactly when the code under test produced something other than `5`. The assertion is the entire point of the test.

If you apply these four passes to every example, no line of this guide will remain magical.

## 0.5 The equipment you need

- A computer running Windows, macOS, or Linux. .NET is cross-platform.
- An internet connection for the initial downloads (SDK, NuGet packages).
- A terminal: PowerShell on Windows (the guide shows PowerShell), or any shell on macOS/Linux.
- A text editor or IDE: Visual Studio, Visual Studio Code with the C# extension, or JetBrains Rider all work. The guide uses no IDE-specific features.
- No paid software. Every tool used here is free.

## 0.6 Typographic conventions used in this guide

- Text like `dotnet test` in monospace is a command or code you type exactly as shown.
- Lines beginning with `#` inside PowerShell blocks are comments: they explain, they are not typed.
- The phrase **line by line** introduces an explanation of each line of the preceding block.
- The phrase **word by word** introduces a table that explains every token of one important line: keywords, modifiers, types, names, operators, and even punctuation.
- Boxes starting with **Note** contain context that is useful but not required for the next step.

---

# Chapter 1: Introduction to software testing

## 1.1 A definition

A **software test** is an executable check that compares an observed result with an expected result.

Unpacked, one sentence at a time:

- **Executable** — it is itself a program. You run it; it does not need a human to interpret it.
- **Check** — it examines one specific piece of behavior.
- **Observed result** — what the code actually produced when you ran it: a return value, a state change, an HTTP status code, a thrown exception.
- **Expected result** — what you previously decided should happen: the value, state, status, or exception you consider correct.

Every automated test in the world, whatever its language or framework, performs the same four steps:

1. **We prepare a situation.** Create objects, supply inputs, put the system into a known starting state.
2. **We run the code.** Call the method, hit the endpoint, trigger the event.
3. **We observe what happened.** Capture the return value, the state, the exception.
4. **We decide whether what happened is acceptable.** This decision is mechanical, written as an assertion.

## 1.2 A test is not a print statement

Many beginners write this and call it testing:

```csharp
Console.WriteLine(calculator.Add(2, 3)); // prints 5
```

This is **debugging output**, not a test. The printed value still requires a human to look at it and judge it. If nobody looks, nothing is checked. If the human is tired, the judgment is unreliable.

An automated test contains the judgment inside the code:

```csharp
Assert.Equal(5, calculator.Add(2, 3));
```

Now the machine decides. The check can run thousands of times, on every commit, on every machine, at three in the morning, without a human watching. That difference — the decision moved from a human into executable code — is the entire foundation of this guide.

The .NET documentation describes automated tests as a way to check that application code behaves as its authors intend, and it distinguishes unit tests, integration tests, and load tests as the main families you will meet.[1]

## 1.3 The three families of automated tests

You will meet these words constantly. Here is the one-paragraph introduction; Chapter 5 defines them precisely.

- A **unit test** checks one small piece of behavior — usually one method — with everything external (databases, files, networks, clocks) removed or replaced. It runs in milliseconds.
- An **integration test** checks that two or more pieces work **together** — a service with a real HTTP pipeline, a query with a real database. It runs slower because it involves more of the real world.
- An **end-to-end test** drives the application the way a real user would, through its outermost boundary. It is the most realistic and the most expensive to run and diagnose.

A professional suite contains all three, in carefully chosen proportions. Deciding which test type answers which question is a skill you will build through the whole guide, and Chapter 24 turns it into a strategy.

## 1.4 What problem does testing solve

Software changes create risk. Every time code changes, any of the following can happen:

- an existing feature breaks silently;
- the code works for normal input but fails for empty, huge, negative, or extreme input;
- a dependency is used incorrectly;
- an API returns the wrong HTTP status code;
- incorrect data is saved;
- behavior changes when operations run asynchronously;
- the code passes on the developer's machine and fails in CI or on another operating system.

A test suite gives the team a **repeatable** way to discover some of these problems within seconds or minutes instead of in production. "Repeatable" is the key word: the same tests, over the same code, give the same verdict.

## 1.5 What a test proves and what it does not prove

This is the most important paragraph in this chapter.

A test does **not** prove that the application has no bugs. It proves only that **the tested behaviors passed under the conditions represented by the tests**. If you never tested what happens with an empty string, your green test suite says nothing about empty strings. Tests are evidence about the behaviors they encode — nothing more, and that is already enormously valuable.

Holding this thought has two practical consequences you will use for the rest of your career:

- When you write a test, you are deciding which behavior deserves a guarantee. Choose deliberately.
- When a suite is green, ask "what is it not telling me?" before celebrating.

---

# Chapter 2: A short history of software testing

## 2.1 Before test frameworks

For most of computing history, verification meant manual effort. A developer would run the program, enter a few values, eyeball the output, and repeat after every change. As programs grew, this became slow, boring, and inconsistent — precisely the qualities that make humans miss things.

The problems with manual checking were structural, not personal:

- The same check performed twice was not really the same check.
- The checks could not run unattended.
- Nobody could list exactly which checks had been performed.
- The cost of checking grew with the program instead of with the changes.

## 2.2 The idea of the unit

The central idea of unit testing grew together with **modular software design**: if a program is divided into small pieces with clear responsibilities and clear boundaries, then each piece — each *unit* — can be checked independently of the others. Kent Beck, working in the Smalltalk community in the late 1980s, both championed this design style and built the tooling for it. His paper "Simple Smalltalk Testing: With Patterns" (1989) described the pattern that every modern test framework still follows: a test fixture that sets up a known state, test methods that exercise behavior, and a way to report failures.

Notably, the idea of writing the check *before* the code is older than the modern movement: engineers on 1960s aerospace projects, including people who later wrote about it such as Gerald Weinberg, have described preparing test procedures before the flight code existed. What arrived later was the tooling and the discipline that made the idea cheap.

## 2.3 SUnit and the xUnit family pattern

Beck's Smalltalk framework was called **SUnit**. In 1997, Beck and Erich Gamma ported its ideas to Java — famously writing much of the first version of **JUnit** together on a flight to a conference. JUnit spread explosively, and its design became a template: other communities ported it to their languages, and the ports took the name **xUnit**, where the `x` is the language or platform.

The pattern that defines the whole family:

- Tests are ordinary methods in ordinary classes.
- A marker (an attribute, annotation, or naming convention) tells the framework which methods are tests.
- Assertions compare expected with actual values.
- A runner discovers the tests, runs them, and reports each pass or fail.
- Setup and teardown hooks give each test a known starting state.

Once you learn the pattern in one language, you recognize it in all of them.

## 2.4 The .NET testing story

When .NET arrived in 2002, the xUnit pattern arrived with it, through several doors:

- **NUnit** began as a direct port of JUnit to .NET. Its version 2.0 was a major rewrite that made it a native .NET framework, and it became the dominant third-party test framework in the .NET ecosystem for a decade.
- **MSTest** is Microsoft's own framework, introduced with the testing tools in Visual Studio 2005. Being bundled with the IDE made it many teams' first encounter with unit testing.
- **xUnit.net** appeared in 2008, created by James Newkirk — one of the original authors of NUnit v2 — together with Brad Wilson. They used the lessons of a decade of practice to rethink lifecycle and parallelism: for example, xUnit.net creates a **fresh instance of the test class for every test**, which removes an entire class of accidental state-sharing bugs, and it runs test collections **in parallel by default**. This guide uses xUnit.net.
- **TUnit** is a newer framework built for **Microsoft.Testing.Platform**, the modern alternative execution layer described below.

Microsoft's .NET testing overview documents parts of this lineage: NUnit was initially ported from JUnit, and xUnit.net was created by the original inventor of NUnit v2.[1] The history explains something you will notice throughout this guide: the frameworks share the same skeleton and differ mainly in attribute names and assertion APIs.

## 2.5 The tooling layers under your tests

The modern .NET testing stack has four layers. Knowing them exists will save you real confusion later:

1. A **test framework** defines the code you write: xUnit, NUnit, MSTest, or TUnit. It gives you attributes, assertions, and lifecycle features.
2. A **test adapter** connects a framework to a platform, so that tooling can discover and run that framework's tests (for example, `xunit.runner.visualstudio`).
3. A **test platform or runner** is the execution engine: historically **VSTest** (the long-established engine behind `dotnet test`), and more recently **Microsoft.Testing.Platform (MTP)**, a lighter alternative that the .NET 10 SDK era supports alongside it. Microsoft's current guidance is to pick one platform consistently per repository rather than mixing VSTest-based and MTP-based projects in one run configuration.[2]
4. The **.NET CLI or your IDE** provides the human surface: the `dotnet test` command or the Test Explorer window.

For a beginner, the practical takeaway is simple: write framework-level tests, run them with `dotnet test` (or your IDE), and defer the platform questions until you configure CI, native AOT, or a large repository.

## 2.6 TDD and the test pyramid in history

Two later ideas shaped how practitioners use the machinery above:

- **Test-driven development (TDD)**, articulated by Kent Beck in "Test-Driven Development: By Example" (2002), turns testing into a design rhythm: write a failing test, write the simplest code that passes, refactor with the tests still green. You will practice this cycle in Chapter 23.
- **The test pyramid**, usually credited to Mike Cohn in "Succeeding with Agile" (2009) and popularized further by Alister Scott's writing and by Ham Vocke's "The Practical Test Pyramid" on martinfowler.com (2018), is a strategy heuristic: many fast unit tests, fewer integration tests, few end-to-end tests. You will apply it in Chapter 24.

## 2.7 Why this history matters to you

History explains why you will meet several valid styles in real codebases:

- `[Fact]` in xUnit, `[TestMethod]` in MSTest, `[Test]` in NUnit;
- `dotnet test` on the command line and a test window in the IDE, both running the same test assembly;
- older projects on VSTest and newer projects on Microsoft.Testing.Platform.

The transferable skill is **not** memorizing one framework's decorations. The transferable skill is expressing behavior clearly and executing it reliably. Frameworks are dialects; testing is the language.

---

# Chapter 3: What this guide offers you

## 3.1 One continuous project, not disconnected snippets

Most tutorials give each topic its own throwaway example. This guide does the opposite. Everything you build belongs to **one growing application called Store**, a small online shop. Each chapter adds one capability to Store, and each capability drags in exactly the testing problem that chapter teaches:

| Guide part | What you add to Store | What you learn to test |
|---|---|---|
| Part III | A `Calculator` warm-up project | Your first unit test, `dotnet test`, reading failures |
| Part IV | Domain: `CartItem`, `ShoppingCart` | Unit tests, facts and theories, AAA, naming |
| Part V | `CheckoutService`, `OrderService`, policies, gateways | Test doubles, async tests, seams for time and randomness |
| Part VI | ASP.NET Core API (`/health`, `/products/{sku}`) | Integration tests with `WebApplicationFactory`, databases |
| Part VII | Coverage, CI workflow | Selecting tests, measuring coverage, TDD, the pyramid, GitHub Actions |
| Part VIII | Refinements and a capstone extension | Test design, smells, advanced techniques |

Because every chapter works on the same code, you experience what real projects feel like: tests accumulate, structure matters, and early decisions shape later possibilities.

## 3.2 The tools you will actually learn

By the end you will have used, deliberately and with understanding:

- **C#** — the language, including the subset every test author must read fluently.
- **.NET SDK and .NET CLI** — building solutions, projects, references, packages, tests, and reports from the terminal.
- **xUnit.net** — facts, theories, assertions, fixtures, traits, and parallel execution.
- **ASP.NET Core** — a minimal HTTP API with routing and dependency injection.
- **WebApplicationFactory** — in-memory integration testing of that API.
- **Moq** (optional) — a mocking library, so you can read code that uses one.
- **Coverlet and ReportGenerator** — code coverage collection and HTML reports.
- **GitHub Actions** — continuous integration that runs your suite on every push.

## 3.3 The skills ladder

The guide is a ladder, and every rung is a Part:

- **Beginner (Parts I–IV):** you can explain what a test is, create test projects, write clear facts and theories, and run them.
- **Intermediate (Parts V–VI):** you can isolate dependencies with fakes, test async code, integration-test an HTTP API, and choose a database strategy.
- **Advanced (Part VII):** you can select and categorize tests, interpret coverage, practice TDD, reason about the whole test portfolio, and run everything in CI.
- **Mastery (Part VIII):** you can design code and tests together, recognize and repair test smells, apply advanced techniques where they pay off, and — most importantly — state precisely what your suite proves and what it does not.

Chapter 30 turns this ladder into a checklist you can honestly grade yourself against.

## 3.4 What this guide deliberately does not cover

To keep the path complete and walkable, some topics are named but not taught in depth, so you know where the path continues:

- Entity Framework Core internals (Chapter 19 discusses testing strategy, not ORM mastery).
- Front-end and browser-driven UI testing (Chapter 24 places end-to-end testing in the strategy).
- Load-testing tooling such as k6 or NBomber (Chapter 24 covers the concepts and the discipline).
- Deployment, containers, and release management (CI here stops at green tests on every push).

## 3.5 How much time this takes

Working at a careful pace — typing everything, breaking things on purpose, reading every failure — the guide takes roughly 20 to 40 hours. It is designed for evenings-and-weekends learning, not a single sitting. The state of your work after any chapter is a valid stopping point: everything builds, everything passes.

---

# Chapter 4: Why you should learn to test

## 4.1 The professional reasons

Well-designed tests give a team abilities that are hard to obtain any other way:

- **Fast feedback.** A unit test suite runs in milliseconds to seconds. You learn within seconds of a change whether you broke something, instead of discovering it in a demo.
- **Regression protection.** Old tests are rerun after every new change. "It used to work" becomes "it still works, and here is the proof."
- **Executable documentation.** A well-named test describes behavior in code: `Add_ZeroQuantity_ThrowsArgumentOutOfRangeException` is a sentence about the system that cannot go stale the way comments do — when the behavior changes, the test fails and forces the document to be updated.
- **Refactoring confidence.** Refactoring means changing internal structure without changing behavior. Only tests give you a way to *check* that promise cheaply.
- **Design feedback.** Code that is hard to test is usually too tangled: hidden dependencies, mixed responsibilities, global state. Difficulty writing a test is diagnostic information about your design, not just an inconvenience.
- **Team communication.** A pull request containing a test states precisely what the author believes the new behavior is. Reviewers argue with a test, not with prose.
- **Automation.** `dotnet test` runs identically in a terminal, an IDE, and a CI pipeline. The check scales to every commit for free.

Microsoft's best-practices guidance names speed, isolation, repeatability, self-checking behavior, and timeliness as the characteristics of good unit tests.[4] You will use that exact list as a quality bar in Chapter 26.

## 4.2 The economic reason

A long line of software engineering research — going back to Barry Boehm's studies in the 1970s and 1980s — found that the cost of fixing a defect grows steeply the later it is discovered. A bug caught by a unit test seconds after it is written costs minutes. The same bug discovered by an angry customer costs an investigation, a hotfix, a deployment, an apology, and trust. Automated tests are how a team moves defect discovery from the expensive end of that curve toward the cheap end, permanently.

## 4.3 What testing does not give you automatically

Honesty about limits is part of the skill. Testing is **not** a substitute for:

- secure design and threat modeling;
- code review;
- logging, monitoring, and production observability;
- performance measurement under realistic load;
- manual usability testing;
- requirements analysis — tests encode intentions; they cannot invent them.

Testing is one engineering activity inside a larger quality process. A suite of green tests plus no monitoring is a car with excellent brakes and no windshield.

## 4.4 Testing as career capital

In the .NET ecosystem, the ability to write trustworthy tests is a baseline expectation for professional roles, not a specialty. Interview loops for C# positions routinely include "write a test for this method" and "this test is flaky — why?". The vocabulary of this guide (SUT, test double, seam, contract test, coverage versus correctness) is the shared language of .NET teams. Learning it here is cheaper than learning it under deadline.

## 4.5 A realistic promise

After finishing this guide you will not be a person who has *heard* of unit testing. You will be a person who can, from an empty folder, produce a solution with production code and a trustworthy, fast, well-named test suite; put it into CI; read failures without fear; and explain exactly what the suite does and does not guarantee. That is the working definition of a confident test engineer, and it is entirely reachable from zero.

---

# Part II: The words and the tools

You cannot follow instructions that use words you do not own. This part gives you the complete vocabulary of .NET testing (Chapter 5), then introduces each technology with the same four-part structure used for the guide itself — introduction, history, what it offers, why you should use it (Chapter 6) — and finally installs and verifies everything (Chapter 7).

# Chapter 5: The vocabulary you must understand

Every term below appears in later chapters without further ceremony. Read this chapter once now, and return whenever a word feels loose. Appendix A repeats these definitions in alphabetical order.

## 5.1 The platform words

| Word | Plain meaning | Why it matters here |
|---|---|---|
| **Application** | Software built for a purpose: a web API, a desktop program, a command-line tool, a background worker. | The Store you build is an application; the tests are also, technically, small applications. |
| **.NET** | Microsoft's open-source, cross-platform development platform: runtimes, base libraries, compilers, SDK tools, and frameworks such as ASP.NET Core. | Everything in this guide runs on it. |
| **C#** | The programming language used in this guide. C# source compiles to an intermediate representation that a .NET runtime executes. | The language of both production code and test code. |
| **Project** | The unit of compilation and packaging, described by a `.csproj` file: which sources, which target framework, which package dependencies, which build settings. | `dotnet new` creates projects; `dotnet add` modifies them. |
| **Solution** | A container (`.sln` file) for related projects. | Store's production and test projects live in one solution so one command builds and tests everything. |
| **Namespace** | A naming mechanism that groups types (`Store.Domain`, `Store.Domain.UnitTests`). Not security, not a project — just names. | Lets your test project reference production types by short names. |
| **Class library** | A project that produces a reusable assembly (a `.dll`) rather than a directly launched program. | Store.Domain is a class library; so is a test project, with a special flavor. |
| **Target framework** | The .NET API surface a project expects, written in the `.csproj` (for example `net8.0` or `net10.0`). | All projects in one build must be compatible; mismatched target frameworks cause real errors. |
| **NuGet package** | The .NET ecosystem's unit of shared code, restored by the `dotnet` CLI from package feeds. | xUnit, Moq, and the ASP.NET test host are NuGet packages. |

## 5.2 The test-design words

| Word | Plain meaning | Why it matters here |
|---|---|---|
| **System Under Test (SUT)** | The object, method, service, API, or application whose behavior a test examines. | Naming the SUT focuses a test. If a test calls `cart.Total(...)`, the SUT is `ShoppingCart.Total`. |
| **Unit test** | A test of one small unit of behavior, with databases, file systems, networks, clocks, and other infrastructure removed or replaced. Microsoft defines it as exercising an individual component or method and recommends keeping infrastructure out of it.[1] | The bulk of Parts III–V. |
| **Integration test** | A test that two or more components work **together** — possibly through a database, an HTTP pipeline, serialization, or a test web host.[1] | Part VI. |
| **End-to-end test** | A test exercising a broad path through a system, close to how a real user works. Costly; kept few. | Chapter 24. |
| **Load or performance test** | A test asking "does the system stay responsive under a specified workload?" rather than "is this result correct?"[1] | Chapter 24, concepts only. |
| **Test case** | One specific scenario, for example: *given an empty cart, when the subtotal is calculated, then the subtotal is zero.* | A parameterized test can create many test cases from one method. |
| **Test suite** | A collection of tests: all tests in a project, a solution, or a selected group. | The thing you grow and curate. |
| **Assertion** | A statement that must be true for the test to pass; the mechanical judge. | `Assert.Equal(10, actual)` compares and fails loudly. |
| **Regression** | A defect introduced by a change that breaks behavior which previously worked. | The event your suite exists to catch. |
| **Deterministic test** | A test that produces the same result whenever code and inputs are unchanged. | Non-determinism (time, randomness, shared state) is the root of flaky tests. |
| **Test double** | A replacement for a real collaborator, used to control a test. Umbrella term. | The central tool of Part V. |
| **Stub** | A test double that supplies controlled data. | The `FixedDiscountPolicy` you will write. |
| **Fake** | A lightweight working implementation, such as an in-memory repository. | The `FakeInventoryGateway` you will write. |
| **Mock** | A test double that records or verifies interactions, in the terminology of many .NET tools. | The Moq example in Chapter 15. |
| **Spy** | A double that records calls so the test can inspect them afterward. | Appears mainly in literature; tools blur it with mock. |
| **Seam** | A controlled place where production code and test code can provide different implementations. | Chapter 17 is entirely about seams. |

A warning straight from Microsoft's own guidance: *fake, stub, and mock are used differently across testing literature*.[4] Do not fight about the words; make sure the sentence "this double supplies data" versus "this double verifies an interaction" is clear in your head, and the vocabulary war will never touch you.

## 5.3 The execution words

| Word | Plain meaning | Why it matters here |
|---|---|---|
| **Test framework** | The authoring API: attributes, assertions, lifecycle, conventions. xUnit, NUnit, MSTest, TUnit.[1] | You write against this. |
| **Test adapter** | The connector that lets a platform run a framework's tests (for example `xunit.runner.visualstudio`). | Usually invisible until it is missing. |
| **Test platform / runner** | The engine that discovers and executes tests and reports results: VSTest historically, Microsoft.Testing.Platform as the modern alternative.[2] | Explains why `dotnet test` behaves slightly differently across project styles. |
| **Fixture** | Shared test setup or a shared resource with a defined lifetime. | Chapter 20: class fixtures, collection fixtures, per-test construction. |
| **Trait** | A key-value label on a test (for example `Category=Unit`) usable as a filter. | Chapter 21: run only what you need. |
| **Flaky test** | A test whose result changes without a relevant code change. | The enemy. Chapters 26 and 29. |

---

# Chapter 6: The technology stack piece by piece

Each technology below is presented in the same order you met this guide: **introduction, history, what it offers, why you should use it**. After this chapter, no tool you are asked to install will be a stranger.

## 6.1 C#

**Introduction.** C# is a statically typed, object-oriented (and increasingly multi-paradigm) language created by Microsoft. In C# you describe types (classes, records, interfaces) and the operations on them; the compiler checks that names, types, and calls fit together before anything runs. That compile-time checking is a quiet ally of testing: many mistakes never even reach a test.

**History.** C# was designed by Anders Hejlsberg — who had previously created Turbo Pascal and Delphi — and shipped with the first version of .NET in February 2002. The language has evolved continuously: generics (C# 2), LINQ (C# 3, 2007), `async`/`await` (C# 5, 2012), nullable reference types (C# 8), records (C# 9, 2020), and file-scoped namespaces (C# 10). It is standardized through Ecma International (ECMA-334) and ISO/IEC, and it is open source.

**What it offers you.** Strong typing that catches mistakes early; classes and interfaces for structuring behavior; records for data; LINQ for querying collections; `async`/`await` for asynchronous work without callback spaghetti; and attributes — the metadata mechanism that test frameworks hang everything on.

**Why use it here.** It is the language of the .NET ecosystem this guide teaches, and its features map directly onto testing needs: interfaces become seams, attributes become test markers, generics become typed collections of test data.

## 6.2 .NET and the .NET SDK

**Introduction.** .NET is the platform beneath C#: the runtime that executes your code, the base class library (thousands of ready types like `List<T>`, `decimal`, `Task`), the compilers, and the SDK — the *software development kit* that contains the compiler, project templates, package restore, and the `dotnet` command-line interface. Install the SDK, not merely a runtime: the runtime only *runs* programs; the SDK also *builds* them.

**History.** .NET Framework 1.0 arrived in February 2002, Windows-only. The Mono project (2004) brought .NET to other platforms. In 2016 Microsoft released .NET Core 1.0: a modular, cross-platform, open-source rewrite, together with the `dotnet` CLI. In November 2020, ".NET 5" dropped the "Core" name and unified the platform. Since then a major version ships every November: .NET 6 (2021, long-term support), .NET 7 (2022), .NET 8 (2023, long-term support), .NET 9 (2024), .NET 10 (2025, long-term support). Even-numbered releases are LTS — that is why this guide defaults to `net8.0` and happily runs on `net10.0`.

**What it offers you.** One command (`dotnet`) to create, build, test, and publish anything; a single, huge base library; cross-platform execution; and a mature package ecosystem (NuGet).

**Why use it here.** The .NET CLI makes every step of this guide reproducible on your machine, in CI, and on a colleague's machine — which is itself a testing value.

## 6.3 ASP.NET Core

**Introduction.** ASP.NET Core is .NET's framework for web applications and HTTP APIs. In this guide you will use its *minimal API* style: entire endpoints declared in a few lines of `Program.cs`, with routing, dependency injection, and JSON serialization provided by the framework.

**History.** The original ASP.NET shipped with .NET Framework in 2002. ASP.NET MVC (2009) introduced the model-view-controller pattern to the platform. ASP.NET Core 1.0 (2016) was the cross-platform rewrite. Minimal APIs arrived with .NET 6 (2021), cutting the ceremony of defining HTTP endpoints to its essentials.

**What it offers you.** A composable HTTP pipeline (routing, middleware, filters), built-in dependency injection, configuration and logging out of the box, and — critically for us — testability designed in from the start: `WebApplicationFactory` (covered in Chapter 18) can host your whole application in memory.

**Why use it here.** Because "does my HTTP API actually answer with the right status code?" is a question unit tests cannot answer, and ASP.NET Core is the standard .NET way to build the API that raises that question.

## 6.4 xUnit.net

**Introduction.** xUnit.net is the test framework this guide uses. You mark test methods with `[Fact]` (fixed scenario) or `[Theory]` (data-driven scenario), call the system under test, and judge with `Assert.*` methods.

**History.** Chapter 2 told the story: created in 2008 by James Newkirk and Brad Wilson as a rethink of the .NET testing frameworks that came before, with per-test class instantiation and parallel-by-default execution as design decisions. It is open source, community-driven, and works on all modern .NET versions; the current major version, xUnit v3, targets .NET 8 and later and runs on Microsoft.Testing.Platform.[3]

**What it offers you.** Concise syntax; theories with `InlineData` for data-driven tests; a rich assertion library with precise failure messages; fixtures with explicit lifetimes; traits for filtering; parallel test execution.

**Why use it here.** Its concepts are the modern mainstream of .NET testing, and everything you learn in it maps one-to-one onto NUnit, MSTest, and TUnit — attribute names differ, thinking does not.

## 6.5 The supporting cast

- **Moq** — the most-downloaded mocking library on NuGet. Chapter 15 uses it briefly so you can read mock-based tests in the wild, and warns about over-using it.
- **Coverlet** — the open-source coverage collector that ships inside the standard xUnit test template (`coverlet.collector`). It instruments your assemblies and records which lines and branches your tests executed. Chapter 22.
- **ReportGenerator** — a global tool that turns Coverlet's XML output into a browsable HTML report. Chapter 22.
- **GitHub Actions** — GitHub's automation system, generally available since November 2019. A small YAML file in your repository runs your build and tests on GitHub's machines on every push and pull request. Chapter 25.

## 6.6 How the pieces fit together

```text
        your terminal
             |
         dotnet test            (CLI)
             |
        test platform           (VSTest or Microsoft.Testing.Platform)
             |
        test adapter            (xunit.runner.visualstudio)
             |
        xUnit framework         ([Fact], [Theory], Assert, fixtures)
             |
   your test projects  --->  your production projects
   (Store.*.UnitTests /         (Store.Domain, Store.Application,
    Store.Api.IntegrationTests)  Store.Api on ASP.NET Core)
```

One command enters at the top; your verified behavior comes out at the bottom. Every layer is swappable in principle — and knowing the layers is what lets you stay calm when one of them misbehaves.

---

# Chapter 7: Installing and checking the tools

## 7.1 Install the .NET SDK

Download and install the **.NET SDK** from the official page: <https://dotnet.microsoft.com/download>.

Choose the SDK installer for your operating system. Remember the distinction from Chapter 6: a **runtime** only runs .NET programs; the **SDK** contains the compiler (`csc`/`Roslyn`), project templates, the NuGet restore tooling, and the `dotnet` CLI. You need the SDK for everything in this guide.

After installation, **open a new terminal window** (on Windows: a fresh PowerShell window). The `dotnet` command is added to the system path during setup, and existing terminals frequently do not see it.

## 7.2 Verify the installation

Run these four commands and read what they print:

```powershell
dotnet --version
dotnet --info
dotnet --list-sdks
dotnet --list-runtimes
```

Word by word:

| Command piece | Meaning |
|---|---|
| `dotnet` | Invokes the .NET command-line driver. Everything in this guide starts with this word. |
| `--version` | Prints the version of the SDK that `dotnet` will use in the current directory. |
| `--info` | Prints a summary: operating system, installed SDKs, installed runtimes, and environment. |
| `--list-sdks` | Lists every installed SDK with its install folder. |
| `--list-runtimes` | Lists every installed runtime (ASP.NET Core, .NET base). |

If the terminal answers `dotnet: command not found` (or "is not recognized"), the SDK is not installed or your terminal predates the installation. Install, reopen the terminal, try again. If it still fails, Chapter 29, section 29.1.

## 7.3 Choose a target framework

A **target framework** (written in each `.csproj`) tells the compiler and runtime which .NET API surface the project expects.

For this guide:

- Use `net8.0` for the broadest compatibility with the examples. .NET 8 is a long-term-support release.
- Use `net10.0` if your installed SDK and all packages you need support it — .NET 10 is also long-term support.
- Do not mix target frameworks accidentally across projects in one solution; the compiler will warn or error when a project references an incompatible one.

Check what templates your SDK offers (also a nice preview of everything the SDK can create):

```powershell
dotnet new list
```

## 7.4 Choose an editor

Any of these work; the guide is editor-agnostic:

- **Visual Studio Code** with the C# Dev Kit extension — free, lightweight, all platforms.
- **Visual Studio** (Windows, or Visual Studio for Mac historically) — the full IDE with Test Explorer.
- **JetBrains Rider** — cross-platform IDE with strong test tooling.

The terminal commands shown here work identically regardless of editor. Learning them means your knowledge never depends on one tool.

## 7.5 Choose a test framework (and why this guide chose xUnit)

| Framework | Test marker | Assertion style | Notes |
|---|---|---|---|
| MSTest | `[TestMethod]` | `Assert.AreEqual` | Microsoft's own; ships with Visual Studio heritage. |
| NUnit | `[Test]` | `Assert.That` / classic | Mature xUnit-family framework; initially ported from JUnit.[1] |
| **xUnit.net** | `[Fact]` / `[Theory]` | `Assert.Equal` | Fresh class per test; parallel by default. **This guide.** |
| TUnit | `[Test]` | fluent | Newer; built for Microsoft.Testing.Platform. |

This guide uses **xUnit.net** because its syntax is concise and its concepts are representative of modern .NET testing; Chapter 4 of its history explained why. The framework changes attribute names and assertion spellings — the testing principles do not change at all.

## 7.6 Create a clean learning folder

Create one folder that will hold everything you build in this guide, and step inside it:

```powershell
New-Item -ItemType Directory -Name TestingMastery
Set-Location TestingMastery
```

Word by word:

| Command piece | Meaning |
|---|---|
| `New-Item` | PowerShell command that creates something. |
| `-ItemType Directory` | The something is a folder. |
| `-Name TestingMastery` | The folder's name. |
| `Set-Location` | Changes the current working directory (same idea as `cd`). |

On macOS or Linux the equivalent is `mkdir TestingMastery` then `cd TestingMastery`.

## 7.7 If anything goes wrong

Installation problems, path problems, and permission problems are all normal and all solvable. Note what happened, and bring it to Chapter 29 (Troubleshooting), which collects the failures learners actually hit, in the order they usually hit them.

---

# Part III: Your first test from absolute zero

Everything so far was preparation. In this part you write real code, produce a real green test, deliberately turn it red, and learn the C# foundations that every later test assumes.

# Chapter 8: The Calculator walkthrough

## 8.0 What we will do in this chapter

From an empty `TestingMastery` folder you will, in order: create a class library named `Calculator`, create an xUnit test project named `Calculator.Tests`, connect them with a project reference, understand every file the templates generated, write four lines of production code, write one test, run it, break it on purpose, and fix it. Total time: about 30 minutes. Total understanding gained: the entire shape of .NET testing.

## 8.1 Create a class library

A **class library** is a project that produces reusable code — an assembly — instead of a directly launched application. Your business logic will live in one; tests will live in another.

From the `TestingMastery` folder, run:

```powershell
dotnet new classlib -n Calculator -f net8.0
```

Word by word:

| Piece | Kind | Meaning |
|---|---|---|
| `dotnet` | command | Invoke the .NET SDK driver. |
| `new` | verb | Create something from a template. |
| `classlib` | template name | The class library template. |
| `-n Calculator` | option | Name the project `Calculator` (a folder `Calculator` is created containing it). |
| `-f net8.0` | option | Set the target framework to .NET 8 (Chapter 7.3). |

This creates `Calculator/Calculator.csproj` and a placeholder source file `Calculator/Class1.cs`.

## 8.2 Create the test project

```powershell
dotnet new xunit -n Calculator.Tests -f net8.0
```

Only one piece is new: the template name **`xunit`** creates a test project preconfigured for the xUnit.net framework — it references the framework packages and contains a sample test file `UnitTest1.cs`.

> **Note on xUnit v3.** If your SDK provides the xUnit v3 templates and you want the native v3 setup, the xUnit documentation shows this alternative:[3]
> ```powershell
> dotnet new install xunit.v3.templates
> dotnet new xunit3 -n Calculator.Tests -f net8.0
> ```
> The v3 guide states that xUnit v3 targets .NET 8 or later and that generated templates may vary by installed SDK and template version.[3] For beginner continuity, the rest of this guide uses the broadly compatible `[Fact]`, `[Theory]`, and `Assert` syntax, which is the same in v2 and v3 for everything we do.

## 8.3 Connect the two projects

```powershell
dotnet add Calculator.Tests reference Calculator
```

Word by word: `dotnet add` modifies a project; `Calculator.Tests` is the project being modified; the `reference` keyword says we are adding a reference; `Calculator` is the project to reference.

This gives the test project **permission to compile against the public types of `Calculator`**. Mechanically, the command inserts this into `Calculator.Tests/Calculator.Tests.csproj`:

```xml
<ItemGroup>
  <ProjectReference Include="..\Calculator\Calculator.csproj" />
</ItemGroup>
```

Line by line: `<ItemGroup>` groups build items; `<ProjectReference ...>` declares "this project needs that one to build first, and I want to see its public types"; `Include="..\Calculator\Calculator.csproj"` is the relative path to the other project file. **Direction matters**: tests reference production, never the other way around. Production code must not know tests exist.

## 8.4 Understand what the template created

Open `Calculator.Tests/Calculator.Tests.csproj`. It looks like this (version numbers in your file may be newer; the template evolves — the names are what matter):

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <IsPackable>false</IsPackable>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.8.0" />
    <PackageReference Include="xunit" Version="2.5.3" />
    <PackageReference Include="xunit.runner.visualstudio" Version="2.5.3" />
    <PackageReference Include="coverlet.collector" Version="6.0.0" />
  </ItemGroup>

</Project>
```

Line by line, every element:

- `<Project Sdk="Microsoft.NET.Sdk">` — the root of every SDK-style project; `Sdk="Microsoft.NET.Sdk"` pulls in the standard build rules.
- `<PropertyGroup>` — settings, i.e. name/value facts about the build.
- `<TargetFramework>net8.0</TargetFramework>` — the API surface this project compiles against.
- `<ImplicitUsings>enable</ImplicitUsings>` — the compiler silently adds common `using` directives (like `using System;`), which is why small files need so few usings.
- `<Nullable>enable</Nullable>` — turns on nullable reference types: the compiler warns when a `string` might be `null` but is used as if it cannot be. You will meet `null!` in Chapter 15.
- `<IsPackable>false</IsPackable>` — this project will never be published as a NuGet package; it exists to run.
- `<PackageReference Include="Microsoft.NET.Test.Sdk" ... />` — the test-SDK layer that lets `dotnet test` build and run this project as tests.
- `<PackageReference Include="xunit" ... />` — the xUnit framework itself: `[Fact]`, `[Theory]`, `Assert`.
- `<PackageReference Include="xunit.runner.visualstudio" ... />` — the adapter connecting xUnit to the VSTest platform (Chapter 6.6's diagram, made real).
- `<PackageReference Include="coverlet.collector" ... />` — the Coverlet coverage collector, preinstalled for Chapter 22.

The production project's `.csproj` is the same file minus `IsPackable` and minus the four test packages. You now understand both files completely.

## 8.5 Write the production code

Open `Calculator/Class1.cs`, delete its contents, and type (type, do not paste):

```csharp
namespace Calculator;

public sealed class BasicCalculator
{
    public int Add(int left, int right)
    {
        return left + right;
    }
}
```

Rename the file to `BasicCalculator.cs` in your editor so the file name matches the type it contains (.NET convention).

Line by line:

**Line 1: `namespace Calculator;`**

| Word | Kind | Meaning |
|---|---|---|
| `namespace` | keyword | Declares a named grouping for related types. Purely organizational — it prevents name collisions, it is not security. |
| `Calculator` | identifier | The namespace's name; by convention it matches the project name. |
| `;` | punctuation | With a semicolon this is a **file-scoped namespace** (C# 10+): every type in the file belongs to it, without extra braces and indentation. |

**Line 3: `public sealed class BasicCalculator`**

| Word | Kind | Meaning |
|---|---|---|
| `public` | access modifier | Any assembly that references this project can see and use the type. Remove it and the test project could not even see the class. |
| `sealed` | inheritance modifier | No other class may inherit from `BasicCalculator`. Sealing types you do not design for inheritance is a modern .NET habit: smaller surface, fewer surprises. |
| `class` | keyword | Declares a reference type: a named bundle of state and behavior. |
| `BasicCalculator` | identifier | The type's name. PascalCase is the .NET convention for type names. |
| `{` | punctuation | Opens the class body. |

**Line 4: `public int Add(int left, int right)`**

| Word | Kind | Meaning |
|---|---|---|
| `public` | access modifier | The method is callable from outside — specifically, from your test. |
| `int` | return type | The method returns a 32-bit integer. |
| `Add` | identifier | The method's name. |
| `(int left, int right)` | parameter list | Two inputs, both integers, named `left` and `right` inside the method body. |
| `left`, `right` | parameter names | You will use these names when reading the body. |

**Line 6: `return left + right;`**

| Word | Kind | Meaning |
|---|---|---|
| `return` | keyword | Sends a value back to the caller and ends the method. |
| `left + right` | expression | Integer addition: the two parameters combined with the `+` operator. |
| `;` | punctuation | Ends the statement. |

The closing braces on lines 7 and 8 end the method body and the class body. You have now written every category of thing you will write in production code this entire guide: a namespace, a type, a method, and a statement.

## 8.6 Write the first test

Open `Calculator.Tests/UnitTest1.cs`, delete its contents, and type:

```csharp
using Calculator;

namespace Calculator.Tests;

public sealed class BasicCalculatorTests
{
    [Fact]
    public void Add_TwoNumbers_ReturnsTheirSum()
    {
        // Arrange
        var calculator = new BasicCalculator();

        // Act
        var actual = calculator.Add(2, 3);

        // Assert
        Assert.Equal(5, actual);
    }
}
```

Rename the file to `BasicCalculatorTests.cs`.

Line by line:

**`using Calculator;`** — imports the production namespace so this file can say `BasicCalculator` instead of the full `Calculator.BasicCalculator`. The test project can see it at all because of the project reference from section 8.3.

**`namespace Calculator.Tests;`** — the test type gets its own namespace. A common and useful convention: the test namespace mirrors the production namespace plus `.Tests`, which keeps parallel structure obvious.

**`public sealed class BasicCalculatorTests`** — the test class. It is a plain class; nothing about it is special until an attribute says otherwise. The name ends in `Tests` by convention: one test class per tested class.

**`[Fact]`** — an **attribute**: metadata attached to the method below it. xUnit scans your compiled test assembly, finds methods marked with `[Fact]`, and treats each one as a test with exactly one fixed scenario and no inputs. The square brackets are the syntax for applying an attribute; `Fact` is its class name from the xUnit framework.

**`public void Add_TwoNumbers_ReturnsTheirSum()`** — the test method, in pieces:

| Word | Kind | Meaning |
|---|---|---|
| `public` | access modifier | The runner must be able to invoke the method from outside. |
| `void` | return type | The test returns no value; it communicates success by not throwing, and failure by throwing (the assertion throws on mismatch). |
| `Add_TwoNumbers_ReturnsTheirSum` | identifier | The test's name — read as *method, scenario, expected result*. Chapter 12 is devoted to this naming pattern. |
| `()` | parameter list | Empty: a fact takes no inputs. (A `[Theory]` takes inputs; Chapter 14.) |

**`var calculator = new BasicCalculator();`** — the Arrange step.

| Word | Kind | Meaning |
|---|---|---|
| `var` | keyword | "Compiler, infer the variable's type from the right-hand side." The variable is still statically typed — it *is* a `BasicCalculator`. |
| `calculator` | identifier | Local variable name; camelCase by convention. |
| `=` | operator | Assigns the right side's value to the left side's name. |
| `new BasicCalculator()` | expression | Constructs a `BasicCalculator` object: allocates it and runs its constructor. |
| `;` | punctuation | Ends the statement. |

**`var actual = calculator.Add(2, 3);`** — the Act step. Calls the SUT with the inputs `2` and `3`, and stores the **observed** result in `actual`. The name `actual` is a deliberate vocabulary word: in every failure message you will ever read, "actual" means *what the code produced*.

**`Assert.Equal(5, actual);`** — the Assert step. `Assert` is xUnit's judge class; `Equal` compares its two arguments; the **expected** value comes first, the **actual** second. If they differ, `Equal` throws an exception, xUnit catches it, and the test is reported as failed with both values shown. If they match, nothing happens — and a test where nothing happened is a **passed** test.

The three comments — `// Arrange`, `// Act`, `// Assert` — divide the test into the three phases you will meet properly in Chapter 12. They are training wheels; many professionals keep them for life because they make tests scannable.

## 8.7 Run the test

From the `TestingMastery` folder:

```powershell
dotnet test
```

One command, five stages, in this order:

1. **Find**: locate test projects below the current directory.
2. **Restore**: download any NuGet packages that are not yet on disk (first run only; that is why the first run is slower).
3. **Build**: compile the projects.
4. **Discover**: load the test assemblies, find `[Fact]`/`[Theory]` methods via the adapter.
5. **Execute**: run them, collect results, print the summary.

The exact execution engine (VSTest or Microsoft.Testing.Platform) depends on your SDK and project configuration — current .NET documentation describes both modes of `dotnet test`.[5] For you, nothing changes at the keyboard.

You should see output ending with a summary line like `Passed! - Failed: 0, Passed: 1, Skipped: 0`. To run just the one project instead of everything below the folder:

```powershell
dotnet test .\Calculator.Tests\Calculator.Tests.csproj
```

(On macOS/Linux, use forward slashes: `dotnet test ./Calculator.Tests/Calculator.Tests.csproj`.)

## 8.8 Make the test fail on purpose

A test you have never seen fail is a test you cannot fully trust. Change one character — in the test, not the production code:

```csharp
Assert.Equal(6, actual);   // was: Assert.Equal(5, actual);
```

Run `dotnet test` again. Read the failure deliberately; every part of it is vocabulary:

- **Test name** — which behavior broke: `Add_TwoNumbers_ReturnsTheirSum`.
- **Expected** — the value the test demands: `6`.
- **Actual** — the value the code produced: `5`.
- **Stack trace** — the chain of method calls that led to the failing assertion.
- **File and line** — exactly where the assertion lives.

Now change it back to `5` and rerun. Green again.

> **Note.** A failed test is not a disaster. It is **evidence that the test can detect a mismatch** — proof the safety net has holes small enough to catch bugs. Chapter 13 will ask "which side is wrong, the code or the expectation?" every single time; here the expectation was deliberately wrong.

## 8.9 The loop you will repeat forever

This chapter's real lesson is a loop. Use it for every future change, all the way through the guide and through your career:

1. Write or change **one small behavior**.
2. Run the **smallest relevant test**.
3. **Read the output** — both green and red.
4. Fix the production code or the test — but **know which one is wrong** before touching either.
5. Rerun until green.
6. Run the **broader suite** before committing.

## 8.10 What you have truly learned

- Projects, references, and what a `.csproj` contains.
- How a test framework finds your tests (attributes) and judges them (assertions).
- How `dotnet test` works end to end.
- How to read a failure.
- The working loop of test-first-adjacent development.

The Calculator did its job. Everything from here is the real application — and you are ready for it.

---

# Chapter 9: C# language foundations for test code

You do not need to master all of C# before testing. You need the load-bearing subset below, each piece shown the way you will meet it in tests. Return to this chapter whenever later code reads unclearly.

## 9.1 Variables and types

```csharp
int quantity = 3;
string sku = "COFFEE";
var price = 12.50m;
```

- `int` — a whole number (32-bit). Counting things.
- `string` — text. `sku` holds a stock-keeping-unit code.
- `var price = 12.50m;` — the compiler infers `decimal`.
- The `m` suffix marks a **decimal literal** (think *m* for money): a base-10, 128-bit number that represents values like `12.50` exactly. Binary floating point (`double`, `float`) cannot: in `double`, `0.1 + 0.2` is `0.30000000000000004`. Money code uses `decimal`; this guide's prices always do.
- `=` assigns; `;` ends the statement. `var` is not "no type" — the type is fixed at compile time, just inferred.

## 9.2 Methods

```csharp
public decimal ApplyDiscount(decimal price, decimal rate)
{
    return price * (1 - rate);
}
```

A method's signature, left to right: access (`public`), return type (`decimal`), name (`ApplyDiscount`), parameter list (`(decimal price, decimal rate)`). The body computes and `return`s. Tests call public behavior through methods like this one; if it were `private`, only the class's own code could call it.

## 9.3 Objects and constructors

```csharp
var cart = new ShoppingCart();
```

`new` allocates an object and runs its **constructor** — the special method that initializes it. In tests, construction is usually the first line of Arrange: create the SUT, ready to be exercised.

## 9.4 Collections

```csharp
var names = new List<string>();
names.Add("Ana");
names.Add("Mina");

var availability = new Dictionary<string, bool>();
availability["COFFEE"] = true;
```

- `List<string>` — a growable, ordered collection of strings. `Add` appends.
- `Dictionary<string, bool>` — key/value pairs: look up a `bool` by a `string` key.
- The `<...>` angle brackets denote **generics**: typed containers, so the compiler knows exactly what goes in and comes out. Test data lives in lists, arrays, and theory attributes (Chapter 14).

## 9.5 Conditions and throwing

```csharp
if (quantity <= 0)
{
    throw new ArgumentOutOfRangeException(nameof(quantity));
}
```

- `if (condition)` — runs the braced block only when the condition is true.
- `<=` — less than or equal to.
- `throw` — stops normal execution immediately and raises an exception object describing the problem.
- `nameof(quantity)` — a compile-time expression that produces the string `"quantity"`. If the parameter is ever renamed, `nameof` renames with it, while a hand-typed `"quantity"` string would silently rot.
- This `if` + `throw` shape is a **guard clause**: reject invalid input at the boundary. Testing guard clauses is a major category of unit testing.

## 9.6 Exceptions

An **exception** is an object representing abnormal execution: invalid input, a broken precondition, a failed operation. `throw` launches it; it unwinds the call stack until something catches it. A test that *expects* an exception must let the assertion do the catching:

```csharp
var exception = Assert.Throws<ArgumentOutOfRangeException>(
    () => cart.Add("COFFEE", "Coffee", 10m, 0));

Assert.Equal("quantity", exception.ParamName);
```

- `Assert.Throws<T>(...)` — runs the lambda; fails the test if it does **not** throw exactly a `T`; returns the exception if it does, so you can assert on its details.
- `() => cart.Add(...)` — a **lambda**: the `() =>` syntax packages the call as a value the assertion can execute when *it* chooses. You hand over "how to provoke the exception", not the exception itself.
- `exception.ParamName` — the argument the exception blames; asserting it verifies *which* rule rejected the input.

## 9.7 Interfaces

```csharp
public interface IDiscountPolicy
{
    decimal GetDiscountRate(ShoppingCart cart);
}
```

An **interface** is a pure contract: method and property signatures with no implementation. Any class may promise to fulfill it (`class FixedDiscountPolicy : IDiscountPolicy`), and any code holding an `IDiscountPolicy` reference neither knows nor cares which class is behind it. Interfaces are the hinge of Part V: production code depends on the contract; tests install a controlled implementation behind it.

## 9.8 Tasks and asynchronous methods

```csharp
public async Task<bool> IsAvailableAsync(string sku)
{
    await Task.Delay(10);
    return true;
}
```

- `Task<bool>` — a *promise of a `bool` later*. The method returns immediately with a task; the bool arrives when the work completes.
- `async` — permits `await` inside the method body; the compiler rewrites the method into a state machine.
- `await` — "pause here without blocking the thread until the task finishes, then continue."
- A test of such a method should itself be `async Task` and `await` the call (Chapter 16 covers the rules and the traps: `async void`, `.Result`, `.Wait()`).

## 9.9 Attributes

```csharp
[Fact]
public void SomethingWorks()
{
}
```

`[Fact]` applies an attribute — an object carrying metadata about the method. Test frameworks are attribute-driven: xUnit's discovery step (Chapter 8.7) is literally "scan the assembly for methods with my attributes." `Trait`, `InlineData`, and `Theory` later in this guide are just more attributes.

## 9.10 The modifiers you will meet

| Modifier | Applies to | Meaning |
|---|---|---|
| `public` | types, members | Visible to any referencing assembly. |
| `private` | members | Visible only inside the declaring type. The default for members. |
| `internal` | types | Visible only within this assembly (project). |
| `sealed` | classes | Cannot be inherited. |
| `readonly` | fields | Cannot be reassigned after construction. |
| `static` | members | Belongs to the type itself, not to an instance (`Assert.Equal` is static — no `new Assert()` needed). |
| `const` | local/field | Compile-time constant value baked into the code. |

## 9.11 Members you will meet

- **Property** — a value exposed on a type: `cart.Subtotal` looks like a field but runs code (a getter).
- **Expression-bodied member** — `public decimal Subtotal => _items.Sum(...)`; the `=>` says "the member *is* this expression," a compact one-line form.
- **Auto-property** — `public string Sku { get; init; }` — compiler-generated storage; `init` means settable only during construction.
- **Primary constructor** (records) — parameters written directly after the type name; Chapter 11 uses it.

That is the entire grammatical vocabulary of this guide. From the next chapter on, these building blocks assemble into a real application.

---

# Part IV: The Store domain and its unit tests

The Calculator proved the machinery. Now you build the real thing: a solution with separated production and test projects, a domain model with genuine business rules, and a test suite that protects those rules. This part also teaches the two habits that make tests *readable* for years: disciplined naming and the Arrange-Act-Assert structure.

# Chapter 10: Creating the Store solution

## 10.1 Why a solution, and why this shape

A **solution** is a container for related projects, stored in a `.sln` file that lists them so that one command — `dotnet build Store.sln`, `dotnet test Store.sln` — can act on all of them. The shape below is the standard professional layout: production code under `src`, tests under `tests`, one test project per production project.

```text
Store.sln
├── src
│   ├── Store.Domain          (business rules, no dependencies)
│   └── Store.Application     (services that coordinate business rules)
└── tests
    ├── Store.Domain.UnitTests
    └── Store.Application.UnitTests
```

(Chapter 18 adds `src/Store.Api` and `tests/Store.Api.IntegrationTests` in the same spirit.) The separation is the whole point: `Store.Domain` has zero knowledge of databases, HTTP, or test frameworks, which is exactly what will make it perfectly testable.

## 10.2 The commands

From the `TestingMastery` folder, run each line and watch the output:

```powershell
dotnet new sln -n Store

dotnet new classlib -n Store.Domain -o src/Store.Domain -f net8.0
dotnet new classlib -n Store.Application -o src/Store.Application -f net8.0
dotnet new xunit -n Store.Domain.UnitTests -o tests/Store.Domain.UnitTests -f net8.0
dotnet new xunit -n Store.Application.UnitTests -o tests/Store.Application.UnitTests -f net8.0

dotnet add tests/Store.Domain.UnitTests reference src/Store.Domain
dotnet add tests/Store.Application.UnitTests reference src/Store.Application
dotnet add tests/Store.Application.UnitTests reference src/Store.Domain

dotnet sln Store.sln add src/Store.Domain/Store.Domain.csproj
dotnet sln Store.sln add src/Store.Application/Store.Application.csproj
dotnet sln Store.sln add tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj
dotnet sln Store.sln add tests/Store.Application.UnitTests/Store.Application.UnitTests.csproj
```

Word by word, the new pieces only:

- `dotnet new sln -n Store` — `sln` is the solution template; `-n Store` names the solution, producing `Store.sln` in the current folder.
- `-o src/Store.Domain` — the `-o` (output) option places the new project in that subfolder instead of a folder named after the project, giving you the `src/` and `tests/` layout.
- `dotnet add <A> reference <B>` — you know this from Chapter 8; here it is used three times.
- `dotnet sln Store.sln add <path-to-csproj>` — registers an existing project in the solution. (The solution file itself is a small structured text listing each project's path and a unique ID; you rarely read or edit it by hand.)

## 10.3 The reference graph, and its one rule

After the commands, the projects relate like this:

```text
Store.Domain.UnitTests        ──> Store.Domain

Store.Application.UnitTests   ──> Store.Application ──> Store.Domain
```

Arrows read "references". The one rule: **tests reference production; production never references tests.** Production code must be shippable and must not know that tests exist. The `Store.Application` → `Store.Domain` reference says application services build on domain rules. And `Store.Domain` references nothing — check its `.csproj`: no project references at all. That emptiness is a design feature.

## 10.4 Build it

```powershell
dotnet build Store.sln
```

Watch it compile all four projects in dependency order. A **build failure is not a test failure**: it means the compiler could not produce assemblies at all (a typo, a wrong type name, a missing reference). Fix compilation first; tests cannot even run on broken builds.

Delete the placeholder `Class1.cs` files the templates left in `Store.Domain` and `Store.Application` (and the placeholder `UnitTest1.cs` in each test project when you replace them in the coming chapters). Empty folders, clean start.

---

# Chapter 11: Building the Store domain

## 11.1 `CartItem`: a record for data

Create `src/Store.Domain/CartItem.cs` (replacing `Class1.cs`) with exactly this:

```csharp
namespace Store.Domain;

public sealed record CartItem(
    string Sku,
    string Name,
    decimal UnitPrice,
    int Quantity);
```

Four lines, and every word is deliberate.

**Line 1** `namespace Store.Domain;` — you know file-scoped namespaces from Chapter 8; this file's types belong to `Store.Domain`.

**Line 3** — the declaration, word by word:

| Word | Kind | Meaning |
|---|---|---|
| `public` | access modifier | Other projects (the application, the tests) may use this type. |
| `sealed` | modifier | No inheritance; this data type is complete as written. |
| `record` | keyword | Declares a **record**: a reference type designed for *data* with value-like semantics. |
| `CartItem` | identifier | The type's name. |
| `(` ... `)` | punctuation | Opens the **primary constructor** — the list of values every `CartItem` must have. |
| `string Sku` | parameter | A text property named `Sku` — the stock-keeping unit, the unique product code. |
| `string Name` | parameter | The human-readable product name. |
| `decimal UnitPrice` | parameter | One unit's price, in exact base-10 decimal (Chapter 9.1's money rule). |
| `int Quantity` | parameter | How many units. |
| `);` | punctuation | Ends the primary constructor **and the whole type declaration** — no body braces needed when a record declares only data. |

What a `record` actually buys you, concretely:

- **Public read-only properties for free.** `Sku`, `Name`, `UnitPrice`, `Quantity` each become a gettable property, settable only during construction.
- **Value equality.** Two separately constructed `CartItem`s with the same values compare as **equal** (`==`, `Equals`, and `GetHashCode` are generated by the compiler). For plain classes, equality would be reference-based: two equal-looking items would be "different".
- **A readable `ToString()`**, which shows up inside test failure messages — records make failures easier to read.

A `CartItem` is pure data. It does not decide anything; it is what the cart's rules operate on.

## 11.2 `ShoppingCart`: a class for behavior

Create `src/Store.Domain/ShoppingCart.cs`:

```csharp
namespace Store.Domain;

public sealed class ShoppingCart
{
    private readonly List<CartItem> _items = new();

    public IReadOnlyList<CartItem> Items => _items.AsReadOnly();

    public decimal Subtotal => _items.Sum(item => item.UnitPrice * item.Quantity);

    public void Add(string sku, string name, decimal unitPrice, int quantity = 1)
    {
        if (string.IsNullOrWhiteSpace(sku))
        {
            throw new ArgumentException("A SKU is required.", nameof(sku));
        }

        if (string.IsNullOrWhiteSpace(name))
        {
            throw new ArgumentException("A product name is required.", nameof(name));
        }

        if (unitPrice < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(unitPrice));
        }

        if (quantity <= 0)
        {
            throw new ArgumentOutOfRangeException(nameof(quantity));
        }

        _items.Add(new CartItem(sku, name, unitPrice, quantity));
    }

    public decimal Total(decimal taxRate)
    {
        if (taxRate < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(taxRate));
        }

        var total = Subtotal * (1 + taxRate);
        return decimal.Round(total, 2, MidpointRounding.AwayFromZero);
    }
}
```

Line by line, with full attention on the load-bearing lines:

**`private readonly List<CartItem> _items = new();`**

| Word | Kind | Meaning |
|---|---|---|
| `private` | access modifier | Only code inside `ShoppingCart` can touch this field — callers cannot bypass the rules. |
| `readonly` | modifier | The field cannot be reassigned after construction: the *variable* is fixed (the list's contents may still change). |
| `List<CartItem>` | type | A mutable, growable list of items. |
| `_items` | identifier | Private field naming convention: underscore prefix. |
| `new()` | expression | Target-typed `new`: the compiler knows the type from the left side, so you need not repeat `new List<CartItem>()`. |

**`public IReadOnlyList<CartItem> Items => _items.AsReadOnly();`**

- Exposes the items **read-only**: the public type `IReadOnlyList<CartItem>` has no `Add` or `Remove` for callers to abuse.
- `=>` — an expression-bodied property (Chapter 9.11): the property *is* the expression.
- `_items.AsReadOnly()` — returns a live read-only **wrapper** around the same list (not a copy): callers see current contents, but through a window they cannot open.

**`public decimal Subtotal => _items.Sum(item => item.UnitPrice * item.Quantity);`**

- A computed property: nothing is stored; every read recalculates.
- `Sum(...)` is LINQ (the `System.Linq` namespace, available because of `ImplicitUsings`). It walks the list and accumulates.
- `item => item.UnitPrice * item.Quantity` — a lambda given to `Sum`: "for each `item`, here is its contribution: price times quantity." LINQ plus lambdas is the everyday grammar of C# business code.

**`public void Add(string sku, string name, decimal unitPrice, int quantity = 1)`**

- The public operation for adding a product to the cart.
- `quantity = 1` — a **default parameter value**: callers may omit `quantity`, and it means one unit. `cart.Add("TEA", "Tea", 4m)` is legal and means quantity 1.
- The four `if` blocks are **guard clauses**, one business rule each, in plain words: SKU must not be blank; name must not be blank; price must not be negative; quantity must be positive.
- `string.IsNullOrWhiteSpace(sku)` — true for `null`, for `""`, and for `"   "` — the three ways "blank" happens.
- `ArgumentException` versus `ArgumentOutOfRangeException` — different exceptions for different sins: an *unusable* argument (blank text) versus a *numerically out-of-range* one. The second constructor argument, `nameof(sku)`, becomes the exception's `ParamName` — and your tests will assert exactly that (Chapter 13).
- Only after all four guards pass: `_items.Add(new CartItem(sku, name, unitPrice, quantity));` — construct the record, store it. Invalid states cannot even get in the door.

**`public decimal Total(decimal taxRate)`**

- Guard: `taxRate` must not be negative.
- `var total = Subtotal * (1 + taxRate);` — subtotal plus tax: a `0.10m` rate multiplies by `1.10`.
- `decimal.Round(total, 2, MidpointRounding.AwayFromZero)` — round to 2 decimal places, and when a value sits exactly halfway (`.005`), round **away from zero**: `2.345` becomes `2.35`. The alternative, `MidpointRounding.ToEven` ("banker's rounding", the default of `Math.Round`), would give `2.34` by moving to the even digit. For customer-facing money, half-up is what people expect; the point for you is deeper: **rounding policy is a business decision, written explicitly, and therefore testable.**

## 11.3 Why this design is testable by construction

Notice what the class does *not* contain:

- no `DateTime.Now` anywhere (time is an input you would need to control);
- no `Random`, no file system, no network, no database;
- no `static` mutable state shared between instances;
- no hidden construction of collaborators.

Every input arrives through a method parameter; every output leaves through a return value or an exception. That is why, in the next chapters, testing `ShoppingCart` will require zero tricks: create one, call methods, assert.

## 11.4 The behavior contract in words

Before writing tests, write the contract you believe the class implements. This list becomes your test plan:

1. A new cart is empty and its subtotal is zero.
2. Adding one item makes the subtotal its unit price.
3. Quantity multiplies: 3 × 4.00 gives a subtotal of 12.00.
4. `Total` applies the tax rate and rounds to two decimals.
5. Blank SKU or name is rejected with `ArgumentException` naming the culprit.
6. Negative price, zero/negative quantity, negative tax rate are rejected with `ArgumentOutOfRangeException` naming the culprit.

Chapter 13 turns each line of this contract into a test. Chapter 12 first gives you the two craft skills that keep those tests readable.

---

---

# Chapter 12: Test naming and the Arrange Act Assert pattern

## 12.1 Test names are documentation

A test suite is read far more often than it is written — in code review, in CI failure lists, at 2 a.m. during an incident. The name is the only part most readers will ever see. A useful name contains three pieces:

```text
MethodOrBehavior_Scenario_ExpectedResult
```

Real examples from the chapters ahead:

- `Add_OneItem_SubtotalIsTheItemPrice`
- `Total_WithTenPercentTax_RoundsToTwoDecimalPlaces`
- `Add_NegativePrice_ThrowsArgumentOutOfRangeException`

Read each one aloud: it is a complete sentence about the system, subject-predicate-object. Now feel the contrast with `Test1`, `Works`, `ShouldPass` — names that force the reader to open the body to learn anything, and that turn a CI failure list into a wall of noise. When the build server reports `Add_ZeroQuantity_ThrowsArgumentOutOfRangeException` failed, you know the broken behavior *before opening a single file*. Microsoft's best-practices guidance recommends exactly this method-scenario-behavior naming because tests serve as executable documentation.[4]

The three parts, dissected:

| Part | Question it answers | Example value |
|---|---|---|
| `MethodOrBehavior` | What is being exercised? | `Add`, `Total`, `CalculateTotal` |
| `Scenario` | Under what conditions? | `OneItem`, `WithTenPercentTax`, `NegativePrice` |
| `ExpectedResult` | What must then be true? | `SubtotalIsTheItemPrice`, `ThrowsArgumentOutOfRangeException` |

The underscores are a deliberate test-code convention; production C# uses PascalCase without underscores. Test readability beats production naming style here.

## 12.2 Arrange: build the situation

**Arrange** creates and configures everything the scenario needs — the SUT, its inputs, any collaborators. Nothing is asserted, nothing is judged yet:

```csharp
var cart = new ShoppingCart();
cart.Add("COFFEE", "Coffee", 10m);
```

If Arrange grows long, that is information: your SUT may have too many collaborators (a design smell), or a small setup helper is warranted (section 12.6).

## 12.3 Act: perform the one operation under examination

**Act** is the moment of the test — the single operation whose behavior is being verified:

```csharp
var actual = cart.Total(0.10m);
```

Store the result in a variable named, by strong convention, `actual` — the same word the failure messages use, so the test and its failure output speak one language.

## 12.4 Assert: compare observed with expected

**Assert** judges: the observed value versus the value you previously decided is correct:

```csharp
Assert.Equal(11m, actual);
```

Expected first, actual second — matching `Assert.Equal`'s argument order so the test and the failure message agree (Chapter 14.5 expands this).

## 12.5 One Act per test

Aim for **one meaningful Act per test**. Watch a test that violates the rule:

```csharp
var subtotal = cart.Subtotal;
var total = cart.Total(0.10m);
var count = cart.Items.Count;

Assert.Equal(10m, subtotal);
Assert.Equal(11m, total);
Assert.Equal(1, count);
```

When this test fails, your first question is *which* behavior broke — but the test mumbles three answers at once. Split by contract:

```csharp
[Fact]
public void Subtotal_OfOneCoffeeItem_IsTheUnitPrice() { /* one Act: read Subtotal */ }

[Fact]
public void Total_WithTenPercentTax_IsSubtotalPlusTax() { /* one Act: call Total */ }

[Fact]
public void Add_OneItem_CartContainsOneItem() { /* one Act: read Items */ }
```

Multiple assertions about *one* returned object are fine — asserting a product's `Sku`, `Name`, and `Price` after a single lookup is one behavior with three observations. The rule targets multiple *behaviors* per test, not multiple assertions per behavior.

## 12.6 Keep Arrange visible

Do not bury setup in large shared fixtures that the reader cannot see (Chapter 20 covers fixtures' proper role). A reader of a single test should see *why this scenario exists* and *which input matters*. When several tests repeat identical setup, a small, intention-revealing helper earns its place:

```csharp
private static ShoppingCart CreateCartWithCoffee(decimal price = 10m)
{
    var cart = new ShoppingCart();
    cart.Add("COFFEE", "Coffee", price);
    return cart;
}
```

Word by word: `private static` — internal to the test class, callable without an instance; `decimal price = 10m` — a default parameter so most callers say nothing and one curious test overrides it.

Use helpers only when they make several tests *clearer*. If a helper would hide a critical difference between two tests, keep the setup inline. A helper named `CreateCart` that builds a cart with three items, a discount, and a tax rate is not a helper; it is an obstacle.

## 12.7 The AAA comments: training wheels you may keep

The `// Arrange`, `// Act`, `// Assert` comments first appeared in your Calculator test. Some professionals drop them once the rhythm is instinctive; many keep them because they make a test scannable in seconds. Both are correct positions. What is not optional is the *structure*: every test you write in this guide has one visible Arrange, one Act, and at least one meaningful Assert.

---

# Chapter 13: Writing useful unit tests

## 13.1 The test file skeleton

Replace `tests/Store.Domain.UnitTests/UnitTest1.cs` with `ShoppingCartTests.cs`:

```csharp
using Store.Domain;

namespace Store.Domain.UnitTests;

public sealed class ShoppingCartTests
{
    // the tests from this chapter live here
}
```

Line by line: `using Store.Domain;` imports the production namespace (possible because of the project reference from Chapter 10); `namespace Store.Domain.UnitTests;` mirrors the production namespace plus `.Tests`; the class name `ShoppingCartTests` states its subject. One test class per tested class, one file per test class.

## 13.2 The happy-path tests

Type these four tests inside the class:

```csharp
[Fact]
public void NewCart_SubtotalIsZero()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    var actual = cart.Subtotal;

    // Assert
    Assert.Equal(0m, actual);
}

[Fact]
public void Add_OneItem_SubtotalIsTheItemPrice()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    cart.Add("COFFEE", "Coffee", 12.50m);

    // Assert
    Assert.Equal(12.50m, cart.Subtotal);
}

[Fact]
public void Add_ItemWithQuantityThree_SubtotalUsesQuantity()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    cart.Add("TEA", "Tea", 4m, quantity: 3);

    // Assert
    Assert.Equal(12m, cart.Subtotal);
}

[Fact]
public void Total_WithTenPercentTax_RoundsToTwoDecimalPlaces()
{
    // Arrange
    var cart = new ShoppingCart();
    cart.Add("COFFEE", "Coffee", 10m);

    // Act
    var actual = cart.Total(0.10m);

    // Assert
    Assert.Equal(11m, actual);
}
```

Notice two things a careful reader should catch:

- In tests two and three, the **Act and Assert appear in different orders of obviousness**: test two's Act *is* the add, and the assertion observes the resulting state. There is no rule that Act must capture a return value; the *operation under test* is the Act.
- `quantity: 3` uses a **named argument** — writing the parameter's name at the call site. With `Add(sku, name, price, quantity)` it is optional here, but it documents *which* of two trailing numbers is which, and it lets you skip defaulted parameters in between.

Each test protects exactly one line of the Chapter 11.4 contract: a new cart starts empty; one item sets the subtotal; quantity multiplies; tax applies and rounds.

## 13.3 Why these tests are valuable (and what they deliberately never touch)

The tests verify **public behavior only**. They never:

- read the private `_items` field (impossible by design, and that design is the point of `private`);
- call private methods;
- re-implement the LINQ expression from `Subtotal` inside the test and compare the two.

That last one deserves a moment, because beginners write it constantly: a test that mirrors the implementation tests nothing — if the implementation is wrong, the mirrored expectation is wrong the same way, and the test passes. Assert the *business answer* (12.50, 12, 11), not the *mechanism*.

## 13.4 The invalid-input tests

Append these to the same class:

```csharp
[Fact]
public void Add_EmptySku_ThrowsArgumentException()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    var exception = Assert.Throws<ArgumentException>(
        () => cart.Add("", "Coffee", 10m));

    // Assert
    Assert.Equal("sku", exception.ParamName);
}

[Fact]
public void Add_NegativePrice_ThrowsArgumentOutOfRangeException()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    var exception = Assert.Throws<ArgumentOutOfRangeException>(
        () => cart.Add("COFFEE", "Coffee", -1m));

    // Assert
    Assert.Equal("unitPrice", exception.ParamName);
}

[Fact]
public void Add_ZeroQuantity_ThrowsArgumentOutOfRangeException()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    var exception = Assert.Throws<ArgumentOutOfRangeException>(
        () => cart.Add("COFFEE", "Coffee", 10m, quantity: 0));

    // Assert
    Assert.Equal("quantity", exception.ParamName);
}

[Fact]
public void Total_NegativeTaxRate_ThrowsArgumentOutOfRangeException()
{
    // Arrange
    var cart = new ShoppingCart();

    // Act
    var exception = Assert.Throws<ArgumentOutOfRangeException>(
        () => cart.Total(-0.01m));

    // Assert
    Assert.Equal("taxRate", exception.ParamName);
}
```

Anatomy of the pattern, once, slowly — `Assert.Throws<ArgumentException>(() => cart.Add("", "Coffee", 10m))`:

1. `Assert.Throws<T>` (Chapter 9.6) takes a **lambda**: the packaged call, not yet executed.
2. It executes the lambda *itself*, inside its own try/catch.
3. If the lambda throws exactly `ArgumentException`, the assertion catches it and **returns it**.
4. If it throws nothing, or a different exception type, the assertion **fails the test** and tells you what actually happened.
5. The returned exception is stored in `exception`, and the follow-up assertion checks `ParamName` — *which argument* was blamed.

Why assert both the **type** and the **ParamName**? The type protects the public contract ("this is how invalid input is reported"). The parameter name sharpens it into a diagnosis: `Add_EmptySku_ThrowsArgumentException` failing with "ParamName was `quantity`" tells you the guards fired in the wrong order — a bug the type alone would never reveal.

Note also what Arrange means here: for invalid-input tests, Arrange is just "a cart exists." The *input that matters* is baked into the Act's arguments — empty string, negative price, zero quantity — which is exactly why those argument positions are where your eye goes.

## 13.5 Run, scoped then broad

```powershell
dotnet test tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj
dotnet test Store.sln
```

The first runs one project; the second builds and runs the whole solution. You should have eight passing domain tests. (The application test project still holds its template `UnitTest1.cs`; replacing it comes in Chapter 15 — for now its single placeholder test also passes, or delete the file if you dislike the noise.)

## 13.6 When a test fails: which side is wrong?

When one of these tests goes red, you have exactly two suspects, and you must consciously choose between them:

1. **The production behavior is wrong** — the code does not do what the business needs. Fix the code.
2. **The expectation is wrong** — the test demands something nobody actually requires. Fix the test.

Tests contain bugs too; they are code. What you must never do is weaken an assertion to make red turn green. Chapter 21.7 turns this discipline into a systematic procedure.

---

---

# Chapter 14: Facts, theories, assertions, and exceptions

## 14.1 Facts: one fixed scenario

A **fact** is a test with one fixed scenario and no inputs. If the behavior is valid, it passes every time it runs, forever. You have written several already. One more, minimal:

```csharp
[Fact]
public void EmptyCart_HasNoItems()
{
    var cart = new ShoppingCart();

    Assert.Empty(cart.Items);
}
```

Use `[Fact]` when the scenario needs no data matrix: the cart is simply empty, and one assertion says so.

## 14.2 Theories: one rule, many rows

A **theory** is a parameterized test: one rule, executed once per row of data. Where you would otherwise write near-identical facts, write one theory:

```csharp
[Theory]
[InlineData(0, 0)]
[InlineData(10, 11)]
[InlineData(100, 110)]
public void Total_WithTenPercentTax_ReturnsExpectedValue(
    decimal subtotal,
    decimal expected)
{
    // Arrange
    var cart = new ShoppingCart();
    cart.Add("ITEM", "Item", subtotal);

    // Act
    var actual = cart.Total(0.10m);

    // Assert
    Assert.Equal(expected, actual);
}
```

Line by line:

- `[Theory]` marks the method as parameterized — it *cannot* run until each parameter has a value.
- `[InlineData(0, 0)]` supplies one row: the first value `0` flows into the first parameter `subtotal`; the second `0` flows into `expected`. Each additional `[InlineData]` is one more row: this theory is **three tests**, reported separately by the runner (a failure in the `100 → 110` row names that row precisely).
- The parameters `(decimal subtotal, decimal expected)` are ordinary typed parameters; the runner calls the method once per row, with the row's values as arguments.

When to use a theory: when examples **share one behavior and differ only by data**. When not to: to avoid writing readable tests. If each row would need a different comment to explain itself, they are different behaviors and deserve separate facts.

## 14.3 The assertion toolbox

The assertions you will use constantly, with what each one means and roughly what its failure tells you:

| Assertion | Passes when... | A failure tells you... |
|---|---|---|
| `Assert.Equal(expected, actual)` | the two values are equal | both values, e.g. "Expected: 11 / Actual: 10.9" |
| `Assert.NotEqual(unexpected, actual)` | they differ | that they were equal when they should not be |
| `Assert.True(condition)` | the Boolean is true | the expression was false |
| `Assert.False(condition)` | the Boolean is false | the expression was true |
| `Assert.Null(value)` / `Assert.NotNull(value)` | the reference is null / not null | the opposite |
| `Assert.Empty(collection)` / `Assert.NotEmpty(...)` | the collection has 0 / at least 1 item | the actual count |
| `Assert.Single(collection)` | the collection has exactly 1 item | the actual count |
| `Assert.Contains(item, collection)` | the item is present | that it was missing |
| `Assert.DoesNotContain(item, collection)` | the item is absent | that it was present |
| `Assert.IsType<SpecificType>(value)` | the runtime type matches exactly | the actual runtime type |

## 14.4 Prefer the most specific assertion

The toolbox is ranked by informativeness. Compare:

```csharp
// Poor: technically works, communicates badly
Assert.True(cart.Items.Count == 1);

// Better: says what you mean, and fails better
Assert.Single(cart.Items);
```

The first, on failure, tells you "Expected: True / Actual: False" — you learn nothing about the cart. The second tells you the collection held 0 items, or 3. Prefer the assertion that states your actual intent: `Assert.Single` over `Count == 1`, `Assert.Contains` over a hand-rolled loop, `Assert.Equal` over `Assert.True(a == b)`. The failure messages are documentation written by your past self for your future self.

## 14.5 Expected first, actual second

Most xUnit assertions take expected before actual:

```csharp
Assert.Equal(expected, actual);
```

The order is not decoration: it is why failure output reads as a correct sentence — "Expected 11, Actual 10.90" — matching how humans describe a mismatch (the requirement, then the observation). Your tests and your failure messages share one grammar.

## 14.6 Testing exceptions properly

The correct shape, which you know from Chapter 13.4:

```csharp
var exception = Assert.Throws<ArgumentException>(
    () => cart.Add("", "Coffee", 10m));
```

The anti-pattern, so you can recognize it in older codebases:

```csharp
cart.Add("", "Coffee", 10m);   // if this throws, the test fails outright
Assert.True(false);            // if it does not throw, we "reach" this and fail
```

The second form cannot state which exception it expected, cannot capture the exception for `ParamName` checking, and conflates "threw something" with success. Never write it; gently replace it when you meet it.

## 14.7 Testing asynchronous exceptions

For `async` code, the awaitable sibling is `Assert.ThrowsAsync`:

```csharp
var exception = await Assert.ThrowsAsync<InvalidOperationException>(
    () => service.ProcessAsync());
```

The lambda returns a `Task`; `ThrowsAsync` awaits it, catches what it throws, and hands the exception back — the test method itself must be `async Task` (Chapter 16 covers the full rules).

## 14.8 Skipping a test

A skipped test is **not** a passing test. It is a parked one. Use skipping only with a clear, temporary reason — an external environment that is currently unavailable — and track the work needed to bring it back. A suite full of permanent skips is a suite quietly lying about its guarantees.

---

# Part V: Dependencies and isolation

Real applications do not just compute — they talk: to pricing policies, inventory systems, payment gateways, clocks. Part V is where your tests learn to control those conversations. You will write an application service that depends on an interface, replace the dependency with hand-made test doubles, meet a mocking library, test asynchronous code, and finally learn to tame time and randomness — the two classic sources of flaky tests.

# Chapter 15: Testing dependencies with fakes, stubs, and mocks

## 15.1 Why dependencies complicate tests

A class often collaborates with other classes: a pricing policy, an email sender, a payment gateway, a clock, a repository, a message bus, a feature-flag provider. If a *unit* test uses the real versions of these, it stops being a unit test: it becomes slow (network), fragile (third-party downtime), unsafe (real charges), and non-deterministic (the collaborator's answer changes between runs).

The move that keeps the test a unit test: **give the SUT a controlled replacement instead of the real collaborator.** For that, the SUT must accept its collaborator from outside rather than building it inside — which is exactly what the service below does.

## 15.2 The test double family, precisely

| Double | Its job | It answers the question |
|---|---|---|
| **Stub** | Supplies controlled data when called | "What if the policy returns 10%?" |
| **Fake** | A lightweight *working* implementation (e.g., in-memory repository) | "How does the service behave against a controllable store?" |
| **Mock** | Records interactions; the test verifies them | "Did the service consult the policy at all?" |
| **Spy** | Records calls for later inspection | "What did the service send to the gateway?" |

Terminology warning, again, because you will hit it in the wild: Microsoft explicitly notes that *fake, stub, and mock* are used differently across testing literature.[4] The functional distinction worth burning in: **supplying data** versus **verifying interaction**.

## 15.3 The collaborator: `IDiscountPolicy`

Create `src/Store.Application/IDiscountPolicy.cs`:

```csharp
using Store.Domain;

namespace Store.Application;

public interface IDiscountPolicy
{
    decimal GetDiscountRate(ShoppingCart cart);
}
```

Line by line: the `using` imports the domain namespace (the interface's method mentions `ShoppingCart`); `public interface` declares the pure contract (Chapter 9.7): any implementation must expose `GetDiscountRate`, taking a cart and returning a `decimal` rate. Note what the interface does *not* say: where the rate comes from, how it is calculated, whether it hits a database. That freedom is precisely what tests will exploit.

## 15.4 The SUT: `CheckoutService`

Create `src/Store.Application/CheckoutService.cs`:

```csharp
using Store.Domain;

namespace Store.Application;

public sealed class CheckoutService
{
    private readonly IDiscountPolicy _discountPolicy;

    public CheckoutService(IDiscountPolicy discountPolicy)
    {
        _discountPolicy = discountPolicy
            ?? throw new ArgumentNullException(nameof(discountPolicy));
    }

    public decimal CalculateTotal(ShoppingCart cart, decimal taxRate)
    {
        ArgumentNullException.ThrowIfNull(cart);

        var discountRate = _discountPolicy.GetDiscountRate(cart);

        if (discountRate is < 0 or > 1)
        {
            throw new InvalidOperationException("The discount rate must be between 0 and 1.");
        }

        var discountedSubtotal = cart.Subtotal * (1 - discountRate);
        var total = discountedSubtotal * (1 + taxRate);

        return decimal.Round(total, 2, MidpointRounding.AwayFromZero);
    }
}
```

The new C# machinery, line by line:

**`private readonly IDiscountPolicy _discountPolicy;`** — a field holding *the contract type*, not a concrete class. The field's declared type is the seam.

**The constructor** — this is **constructor injection**: the service receives its collaborator from whoever creates it.

| Word | Meaning |
|---|---|
| `_discountPolicy = discountPolicy` | Store the received collaborator in the field. |
| `??` | Null-coalescing operator: "if the left side is null, use the right side." |
| `throw new ArgumentNullException(nameof(discountPolicy))` | The right side: throw if null. So the full line reads "store it, unless it is null, in which case reject construction." |
| `nameof(discountPolicy)` | The string `"discountPolicy"`, refactor-proof (Chapter 9.5). |

**`ArgumentNullException.ThrowIfNull(cart);`** — a static helper (added in .NET 6) doing the same check for the method's argument in one readable line: if `cart` is null, it throws `ArgumentNullException` blaming `cart`.

**`if (discountRate is < 0 or > 1)`** — a **relational pattern** (C# 9): "if the value is less than 0 **or** greater than 1." Equivalent to `discountRate < 0 || discountRate > 1`, but reads like the sentence you would say aloud. Note the business rule it encodes: a rate outside `[0, 1]` is not merely wrong — it is *invalid state from a collaborator*, so it throws `InvalidOperationException` (the operation cannot proceed) rather than an argument exception (this method's own inputs were fine).

**The arithmetic** — `cart.Subtotal * (1 - discountRate)` applies the discount; `* (1 + taxRate)` applies tax; `decimal.Round(total, 2, MidpointRounding.AwayFromZero)` is the same explicit money-rounding policy from Chapter 11.2.

## 15.5 A hand-made fake: `FixedDiscountPolicy`

Create `tests/Store.Application.UnitTests/Fakes/FixedDiscountPolicy.cs`:

```csharp
using Store.Domain;
using Store.Application;

namespace Store.Application.UnitTests.Fakes;

internal sealed class FixedDiscountPolicy : IDiscountPolicy
{
    private readonly decimal _rate;

    public FixedDiscountPolicy(decimal rate)
    {
        _rate = rate;
    }

    public decimal GetDiscountRate(ShoppingCart cart)
    {
        return _rate;
    }
}
```

Word by word, the new pieces:

- `internal sealed class FixedDiscountPolicy : IDiscountPolicy` — `internal` (Chapter 9.10) keeps this helper visible inside the test assembly only; `: IDiscountPolicy` announces "I fulfill that contract," and the compiler enforces that `GetDiscountRate` exists with the exact signature.
- The constructor captures one `rate` and the method returns it, unchanged, for any cart.

Behaviorally: this double **always answers with the chosen rate**. It is deterministic, instant, offline, and reads like its own documentation — `new FixedDiscountPolicy(0.10m)` *is* the test scenario "the policy grants 10%."

## 15.6 Testing the service with the fake

Replace `tests/Store.Application.UnitTests/UnitTest1.cs` with `CheckoutServiceTests.cs`:

```csharp
using Store.Application;
using Store.Application.UnitTests.Fakes;
using Store.Domain;

namespace Store.Application.UnitTests;

public sealed class CheckoutServiceTests
{
    [Fact]
    public void CalculateTotal_WithTenPercentDiscountAndTax_ReturnsExpectedTotal()
    {
        // Arrange
        var cart = new ShoppingCart();
        cart.Add("COFFEE", "Coffee", 100m);
        var discountPolicy = new FixedDiscountPolicy(0.10m);
        var service = new CheckoutService(discountPolicy);

        // Act
        var actual = service.CalculateTotal(cart, taxRate: 0.20m);

        // Assert
        Assert.Equal(108m, actual);
    }

    [Fact]
    public void Constructor_WithNullPolicy_ThrowsArgumentNullException()
    {
        // Act
        var action = () => new CheckoutService(null!);

        // Assert
        Assert.Throws<ArgumentNullException>(action);
    }
}
```

The arithmetic of the first test, in words: subtotal 100, minus 10% discount → 90, plus 20% tax → 108. The test **proves the service uses the collaborator's rate** — if the service ignored the policy, the answer would be 120 and the test would fail. One line of Arrange (`new FixedDiscountPolicy(0.10m)`) controlled the entire scenario.

The second test meets a new token: `null!`. Under nullable reference types (Chapter 8.4), the compiler warns when you pass literal `null` where a non-null reference is expected. The `!` is the **null-forgiving operator**: "compiler, I know; this test *intends* to pass null." The `Assert.Throws<ArgumentNullException>(action)` overload takes the lambda stored in `action` — same judgment as before, arranged in two statements.

## 15.7 Stub versus mock, and when each earns its keep

In this test the double only *supplied data* — by the Chapter 15.2 table, that is a **stub** usage. If a test also inspects *whether and how* the SUT called the double, that is a **mock/spy** usage. The same class can serve either role; what matters is what the test does with it.

Prefer a hand-made fake when the interface is small, the behavior stable, the fake readable, and reuse plausible. A mocking library earns its dependency when a collaborator has many members or the interaction rules are complex. Do **not** mock every class on principle: mocking implementation details produces tests that break on every harmless refactor (Chapter 26 returns to this as the over-mocking smell).

## 15.8 Optional: the same test with Moq

If you want to read mock-library fluency, install Moq into the test project:

```powershell
dotnet add tests/Store.Application.UnitTests package Moq
```

Then this test becomes possible:

```csharp
using Moq;
using Store.Application;
using Store.Domain;

[Fact]
public void CalculateTotal_UsesDiscountPolicy()
{
    // Arrange
    var policy = new Mock<IDiscountPolicy>();
    policy.Setup(x => x.GetDiscountRate(It.IsAny<ShoppingCart>()))
        .Returns(0.10m);

    var cart = new ShoppingCart();
    cart.Add("COFFEE", "Coffee", 100m);
    var service = new CheckoutService(policy.Object);

    // Act
    var actual = service.CalculateTotal(cart, 0m);

    // Assert
    Assert.Equal(90m, actual);
    policy.Verify(
        x => x.GetDiscountRate(It.IsAny<ShoppingCart>()),
        Times.Once);
}
```

Word by word:

| Piece | Meaning |
|---|---|
| `new Mock<IDiscountPolicy>()` | Moq generates a stand-in implementing the interface. |
| `policy.Setup(x => x.GetDiscountRate(...))` | Configures what one method call returns. |
| `It.IsAny<ShoppingCart>()` | An argument matcher: match any cart argument. |
| `.Returns(0.10m)` | ...and return `0.10m` when matched. |
| `policy.Object` | The actual `IDiscountPolicy`-typed object to hand the SUT. |
| `policy.Verify(x => x.GetDiscountRate(...), Times.Once)` | The mock-side assertion: the method must have been called exactly once. |

Then the closing judgment, which is the whole chapter in one sentence: **verify the interaction only when the interaction itself is the business rule.** Here the total already proves the policy was consulted (90 ≠ 100); the `Verify` line adds real value only if "the service must ask the policy exactly once" is itself a requirement. If only the result matters, assert the result and skip the call-count ceremony.

---

# Chapter 16: Testing asynchronous C# code

## 16.1 Why async exists

Slow operations — network calls, disk I/O, database queries — should not freeze the thread that starts them. C#'s `async`/`await` lets a method start such work, return a `Task` immediately, and resume when the result arrives, with the compiler doing the rewriting (Chapter 9.8). Our Store now gains an asynchronous collaborator: an inventory gateway whose answers arrive as `Task<bool>`.

## 16.2 The async SUT: `OrderService`

Create `src/Store.Application/IInventoryGateway.cs`:

```csharp
namespace Store.Application;

public interface IInventoryGateway
{
    Task<bool> IsAvailableAsync(
        string sku,
        int quantity,
        CancellationToken cancellationToken);
}
```

One new type in the contract: `CancellationToken` — a cooperative "please stop" signal that flows through async call chains; production code passes it to every I/O operation so the system can cancel work efficiently. It is part of the contract, so it appears here.

Create `src/Store.Application/OrderService.cs`:

```csharp
using Store.Domain;

namespace Store.Application;

public sealed class OrderService
{
    private readonly IInventoryGateway _inventory;

    public OrderService(IInventoryGateway inventory)
    {
        _inventory = inventory
            ?? throw new ArgumentNullException(nameof(inventory));
    }

    public async Task<bool> CanPlaceOrderAsync(
        ShoppingCart cart,
        CancellationToken cancellationToken = default)
    {
        ArgumentNullException.ThrowIfNull(cart);

        foreach (var item in cart.Items)
        {
            var available = await _inventory.IsAvailableAsync(
                item.Sku,
                item.Quantity,
                cancellationToken);

            if (!available)
            {
                return false;
            }
        }

        return true;
    }
}
```

Line by line, the new parts:

- `public async Task<bool> CanPlaceOrderAsync(...)` — an async method returning `Task<bool>`: callers `await` it to learn whether the order may proceed.
- `CancellationToken cancellationToken = default` — an optional parameter; `default` is the "no cancellation requested" token. Most call sites pass nothing.
- `foreach (var item in cart.Items)` — walks every item in the read-only view from Chapter 11.2.
- `await _inventory.IsAvailableAsync(...)` — awaits the gateway's answer **one item at a time**, forwarding the cancellation token.
- `if (!available) return false;` — the business rule: one unavailable item stops everything, immediately (an early return, not a flag and a break).
- After the loop: `return true;` — all items were available.

## 16.3 An async fake: `FakeInventoryGateway`

Create `tests/Store.Application.UnitTests/Fakes/FakeInventoryGateway.cs`:

```csharp
using Store.Application;

namespace Store.Application.UnitTests.Fakes;

internal sealed class FakeInventoryGateway : IInventoryGateway
{
    private readonly Dictionary<string, bool> _availability = new();

    public void SetAvailability(string sku, bool available)
    {
        _availability[sku] = available;
    }

    public Task<bool> IsAvailableAsync(
        string sku,
        int quantity,
        CancellationToken cancellationToken)
    {
        var available = _availability.GetValueOrDefault(sku, false);
        return Task.FromResult(available);
    }
}
```

Word by word, the interesting lines:

- `Dictionary<string, bool> _availability` — the fake's private store of answers, keyed by SKU.
- `SetAvailability("COFFEE", true)` — the test-facing control: the scenario is written in plain sight before the Act.
- `_availability.GetValueOrDefault(sku, false)` — looks the key up; returns `false` when the key is absent (unknown products count as unavailable).
- `Task.FromResult(available)` — the heart of the fake: it wraps an already-known value in an **already-completed** `Task<bool>`. The signature stays async (honoring the contract); the answer costs no delay, no thread, no network. The test controls asynchronous behavior *deterministically and instantly* — no `Task.Delay`, no sleeping.

## 16.4 The async tests

Create `tests/Store.Application.UnitTests/OrderServiceTests.cs`:

```csharp
using Store.Application;
using Store.Application.UnitTests.Fakes;
using Store.Domain;

namespace Store.Application.UnitTests;

public sealed class OrderServiceTests
{
    [Fact]
    public async Task CanPlaceOrder_AllItemsAvailable_ReturnsTrue()
    {
        // Arrange
        var cart = new ShoppingCart();
        cart.Add("COFFEE", "Coffee", 10m, quantity: 2);

        var inventory = new FakeInventoryGateway();
        inventory.SetAvailability("COFFEE", true);

        var service = new OrderService(inventory);

        // Act
        var actual = await service.CanPlaceOrderAsync(cart);

        // Assert
        Assert.True(actual);
    }

    [Fact]
    public async Task CanPlaceOrder_OneItemUnavailable_ReturnsFalse()
    {
        // Arrange
        var cart = new ShoppingCart();
        cart.Add("COFFEE", "Coffee", 10m);

        var inventory = new FakeInventoryGateway();
        inventory.SetAvailability("COFFEE", false);

        var service = new OrderService(inventory);

        // Act
        var actual = await service.CanPlaceOrderAsync(cart);

        // Assert
        Assert.False(actual);
    }
}
```

The one difference from every test you have written so far: the test method signature is `public async Task ...` instead of `public void ...`, and the Act line `await`s the SUT. The test itself becomes asynchronous so the runner can await *it*. Arrange is otherwise familiar: build the cart, script the fake's answers, construct the SUT.

## 16.5 The six rules of async tests

1. **Return `Task`, never `void`.** An `async void` test cannot be awaited by the runner; its exceptions can vanish, leaving tests that "pass" while hiding failures. The runner awaits `Task`; give it one.
2. **Use `await`; do not use `.Result` or `.Wait()`.** Blocking on a task ("sync-over-async") risks deadlocks in some synchronization contexts and always wastes a thread. You have no reason to pay that price in a test.
3. **No arbitrary `Task.Delay` to "wait long enough."** A sleep-based test is a future flaky test (it passes while the machine is fast and fails when the machine is busy). Control the *dependency*, not the clock.
4. **Control timing with fakes, `TaskCompletionSource`, or a deterministic scheduler** when timing genuinely matters. `TaskCompletionSource<T>` lets a test decide, in its own time, when a pending task completes — deterministic control over "later."
5. **Test cancellation when cancellation is part of the contract.** If a method accepts a `CancellationToken`, at least one test should pass an already-cancelled token and assert the documented reaction.
6. **Test exceptions with `Assert.ThrowsAsync`** (Chapter 14.7), awaited like everything else.

---

# Chapter 17: Dependency injection, time, randomness, and seams

## 17.1 The problem with hidden time

Consider this innocent-looking method:

```csharp
public bool IsWeekend()
{
    return DateTime.UtcNow.DayOfWeek is DayOfWeek.Saturday or DayOfWeek.Sunday;
}
```

`DateTime.UtcNow` reads the machine's clock *inside* the method. The test cannot choose the date, so the same test run passes on Saturday and fails on Monday. This is non-determinism (Chapter 5.3), and it is the most common birth defect of flaky tests. The same disease comes from `Random`, from environment variables read invisibly, and from file paths resolved against the current directory.

## 17.2 The cure: a seam

A **seam** is a controlled place where production code and test code can provide different implementations — a joint in the machinery you can open. For time, the seam is a clock interface:

```csharp
public interface IClock
{
    DateTimeOffset UtcNow { get; }
}

public sealed class WeekendChecker
{
    private readonly IClock _clock;

    public WeekendChecker(IClock clock)
    {
        _clock = clock;
    }

    public bool IsWeekend()
    {
        var day = _clock.UtcNow.DayOfWeek;
        return day is DayOfWeek.Saturday or DayOfWeek.Sunday;
    }
}
```

Line by line: `IClock` exposes one property, `UtcNow`, of type `DateTimeOffset` (a date-time plus its offset from UTC — the sane default for instants in time). `WeekendChecker` receives the clock through constructor injection, exactly like `CheckoutService` received its policy, and asks *it* for the time. In production you inject a `SystemClock` that reads `DateTimeOffset.UtcNow`; in tests you inject whatever moment you need. The calculation became pure: same clock value in, same answer out, any day of the week.

## 17.3 A fixed clock in a test

```csharp
private sealed class FixedClock : IClock
{
    public FixedClock(DateTimeOffset utcNow)
    {
        UtcNow = utcNow;
    }

    public DateTimeOffset UtcNow { get; }
}

[Theory]
[InlineData(DayOfWeek.Monday, false)]
[InlineData(DayOfWeek.Saturday, true)]
public void IsWeekend_ReturnsExpectedValue(
    DayOfWeek day,
    bool expected)
{
    // Arrange
    var date = new DateTimeOffset(
        2026, 10, 12, 0, 0, 0, TimeSpan.Zero);
    while (date.DayOfWeek != day)
    {
        date = date.AddDays(1);
    }

    var checker = new WeekendChecker(new FixedClock(date));

    // Act
    var actual = checker.IsWeekend();

    // Assert
    Assert.Equal(expected, actual);
}
```

Line by line, the Arrange is the star:

- `FixedClock` — a nested private test double whose `UtcNow` is set once, in its constructor, and never changes: `public DateTimeOffset UtcNow { get; }` is a get-only auto-property assigned during construction.
- `new DateTimeOffset(2026, 10, 12, 0, 0, 0, TimeSpan.Zero)` — October 12, 2026, midnight, with **zero offset**: that date is a Monday, which is why the loop below is the anchor.
- The `while` loop nudges the date forward one day at a time until it lands on the theory row's target day — Monday takes zero steps, Saturday takes five. (Once you know the calendar dates, writing two explicit dates is even simpler; the loop keeps the example honest about *deriving* the day rather than trusting memory.)
- The theory rows now assert the rule itself: Monday → not weekend; Saturday → weekend. **The same test passes on every real-world day**, because the test, not the machine, owns the clock.

## 17.4 Randomness

The same treatment applies to chance. Do not let correctness depend on an uncontrolled generator:

```csharp
var number = Random.Shared.Next();   // the test cannot choose this
```

Instead, inject a generator (an interface of your own, or a seeded `Random` passed as a parameter). Production injects the real source of randomness; tests inject a deterministic one — a known seed, or a sequence you choose. A promotion code generator, a shuffled deck, an A/B assignment: all become testable the moment the randomness becomes an input.

## 17.5 Environment variables and file paths

Environment access and current-directory resolution are hidden inputs of the same family. Wrap environment reads behind a small interface or a configuration object; pass file paths *into* methods instead of letting methods resolve them invisibly. The test then states its inputs explicitly — and a reader of the test sees everything that matters.

## 17.6 Dependency injection is not only a testing trick

Zoom out. **Dependency injection** means a type receives the collaborators it needs rather than constructing them internally. Yes, it gives tests control — and it *also* improves production composition: responsibilities separate cleanly, wiring lives in one place, and replacing an implementation (a new payment provider, a faster inventory client) stops being surgery. Testability and good design converge on the same move.

## 17.7 When not to add an interface

Do not stamp an interface on every class reflexively — that is ceremony, not design. Add a seam where the code crosses a boundary you genuinely need to control or replace: time, randomness, the network, the database, the filesystem, external services. `ShoppingCart` needed no interface; `IClock`, `IDiscountPolicy`, and `IInventoryGateway` each guard a real boundary. The craft is choosing *where* the joints go.

---

# Part VI: Integration and the whole system

Unit tests protect your rules. But users do not call your rules — they call your *system*. Part VI moves the tests outward: you will publish a real HTTP API with ASP.NET Core, exercise it end-to-end through the pipeline with `WebApplicationFactory` (without opening a network port), choose a strategy for testing databases, and learn the fixture and parallelism model that keeps larger suites fast and trustworthy.

# Chapter 18: Integration testing an ASP.NET Core API

## 18.1 What a unit test cannot see

A controller action or a minimal API endpoint is a meeting of many components:

- **routing** — which URL reaches this code;
- **model binding** — how the URL, query, and body become C# values;
- **dependency injection** — which implementations get constructed and injected;
- **serialization** — how C# objects become JSON and back;
- **authorization** — who may call this;
- **filters and middleware** — cross-cutting steps before and after;
- **HTTP status codes** — the boundary contract with callers.

A unit test of one service proves none of that wiring. An **integration test** checks the collaboration: it sends a real HTTP request through the whole pipeline and asserts the real response. Microsoft's ASP.NET Core guidance describes exactly this shape — a test project, a test web host, a client for requests, a test runner, and the normal Arrange-Act-Assert sequence — built on `WebApplicationFactory` and `TestServer` from the `Microsoft.AspNetCore.Mvc.Testing` package.[6]

## 18.2 Create the API project

```powershell
dotnet new webapi -n Store.Api -o src/Store.Api -f net8.0 --no-https
dotnet add src/Store.Api reference src/Store.Application
dotnet add src/Store.Api reference src/Store.Domain
dotnet sln Store.sln add src/Store.Api/Store.Api.csproj
```

Word by word, the new pieces:

- `webapi` — the ASP.NET Core Web API template; it creates a runnable HTTP project (depending on SDK version it may include sample "weather" files — delete those sample files if your template created them).
- `--no-https` — skips HTTPS/redirect configuration: convenient for a local learning API; real deployments configure TLS deliberately.
- The two `reference` commands give the API visibility of your domain and application projects, so endpoints can use them.

## 18.3 The minimal API: `Program.cs`

Replace the whole content of `src/Store.Api/Program.cs` with:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/health", () => Results.Ok(new { status = "ok" }));

app.MapGet("/products/{sku}", (string sku) =>
{
    if (sku == "COFFEE")
    {
        return Results.Ok(new
        {
            sku,
            name = "Coffee",
            price = 10m
        });
    }

    return Results.NotFound();
});

app.Run();

public partial class Program { }
```

Line by line — this file is dense with framework concepts, so take it slowly:

**`var builder = WebApplication.CreateBuilder(args);`** — creates the application *builder*: the object that collects configuration, logging, and dependency registrations. `args` are the command-line arguments.

**`var app = builder.Build();`** — compiles those registrations into an application: the middleware pipeline that will receive every request.

**`app.MapGet("/health", () => Results.Ok(new { status = "ok" }));`** — maps one route:

| Piece | Meaning |
|---|---|
| `app.MapGet` | Register a handler for the HTTP **GET** method. |
| `"/health"` | A literal route: requests to `/health` arrive here. |
| `() => ...` | The handler: a lambda receiving nothing. |
| `Results.Ok(...)` | Produces an HTTP **200 OK** response. |
| `new { status = "ok" }` | An **anonymous type** — a compiler-generated object with one property. ASP.NET Core serializes it to JSON automatically: `{"status":"ok"}`. |

**`app.MapGet("/products/{sku}", (string sku) => { ... });`** — a route with a **route parameter**:

- `{sku}` in the template is a placeholder; a request to `/products/COFFEE` binds `sku = "COFFEE"`.
- The lambda's `(string sku)` parameter is bound *from the route* by model binding — the framework hands the handler the extracted value.
- Known product (`COFFEE`): `Results.Ok(new { sku, name = "Coffee", price = 10m })` — `sku` uses **projection syntax**: inside an anonymous type initializer, a bare name means `sku = sku`. 200 OK with a JSON body.
- Anything else: `Results.NotFound()` — HTTP **404 Not Found**, no body.

**`app.Run();`** — starts the application: it now listens for requests until stopped.

**`public partial class Program { }`** — the odd-looking key that unlocks testing. With *top-level statements* (statements directly in the file, like everything above), the compiler generates a hidden `Program` class to host them — and that generated class is not visible to other projects. Declaring `public partial class Program { }` merges your (empty, public) declaration with the generated part, making the type name `Program` usable from the integration test project, where `WebApplicationFactory<Program>` needs it as the entry point. Remember the phrase: **partial class, empty body, whole point.**

## 18.4 Create the integration test project

```powershell
dotnet new xunit -n Store.Api.IntegrationTests -o tests/Store.Api.IntegrationTests -f net8.0
dotnet add tests/Store.Api.IntegrationTests reference src/Store.Api
dotnet add tests/Store.Api.IntegrationTests package Microsoft.AspNetCore.Mvc.Testing
dotnet sln Store.sln add tests/Store.Api.IntegrationTests/Store.Api.IntegrationTests.csproj
```

The new command form: `dotnet add <project> package <name>` adds a **NuGet package** reference (a library from the package feed) rather than a project reference. `Microsoft.AspNetCore.Mvc.Testing` is the package providing `WebApplicationFactory`. If the build later reports a missing web SDK or framework reference, open the `.csproj` and confirm the expected ASP.NET Core references are present for your target framework — template output varies slightly by SDK version.

## 18.5 The first integration test

Create `tests/Store.Api.IntegrationTests/HealthEndpointTests.cs`:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc.Testing;

namespace Store.Api.IntegrationTests;

public sealed class HealthEndpointTests
    : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public HealthEndpointTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHealth_ReturnsOk()
    {
        // Act
        using var response = await _client.GetAsync("/health");

        // Assert
        Assert.Equal(HttpStatusCode.OK, response.StatusCode);
    }
}
```

Line by line — several ideas working together:

- `using System.Net;` — imports `HttpStatusCode` (the named constants for HTTP status numbers).
- `using Microsoft.AspNetCore.Mvc.Testing;` — imports `WebApplicationFactory<T>`.
- `: IClassFixture<WebApplicationFactory<Program>>` — xUnit's **class fixture** mechanism (full treatment in Chapter 20): create **one** shared `WebApplicationFactory<Program>` for the whole test class instead of one per test. Booting an application is expensive; sharing it keeps the suite fast.
- `WebApplicationFactory<Program>` — the generic type parameter (`Program`) tells the factory *which application* to host: your `Program.cs`. It locates the entry point and runs it.
- The **constructor** receives the fixture and calls `factory.CreateClient()`: an `HttpClient` whose requests are routed **directly into the in-memory application** — no socket, no port, no network.
- `using var response = ...` — a `using` declaration disposes `response` when the block ends; `HttpClient` responses hold resources and should be released.
- `await _client.GetAsync("/health")` — sends a real HTTP GET through the *real* pipeline: routing, binding, the handler, serialization. The Act of an integration test is an HTTP call.
- `Assert.Equal(HttpStatusCode.OK, response.StatusCode)` — asserts the boundary contract: 200. Expected first, actual second, as always.

Notice what the test does **not** do: start Kestrel on a port, wait for it to listen, or tear it down. `WebApplicationFactory` hosts the application on an in-memory `TestServer`; the client's requests travel through the complete ASP.NET Core pipeline and come back as real `HttpResponseMessage` objects. Everything between your browser and your code is exercised — everything below the network is faked.

## 18.6 The not-found boundary

Add the second test inside the same class:

```csharp
[Fact]
public async Task GetUnknownProduct_ReturnsNotFound()
{
    // Act
    using var response = await _client.GetAsync("/products/UNKNOWN");

    // Assert
    Assert.Equal(HttpStatusCode.NotFound, response.StatusCode);
}
```

Same shape, different boundary: an unknown SKU must produce 404. An integration test should assert what the *contract* promises — status, meaningful headers, and the important parts of the body — and not every byte of serialization detail unless that detail is itself the contract.

## 18.7 Unit or integration? The decision table

| Use a **unit test** for | Use an **integration test** for |
|---|---|
| a price or tax calculation | routing — does the URL reach the handler |
| a validation rule | model binding — do parameters arrive correctly |
| a state transition | DI registration — is the right service wired |
| a mapping function | middleware behavior |
| a service decision with fake collaborators | serialization — the JSON shape |
| | HTTP status codes and headers |
| | a chosen, real database interaction |

Keep the two categories in **separate projects** — which is exactly why you created `Store.Api.IntegrationTests` apart from the unit-test projects. Microsoft recommends the separation so slow infrastructure never leaks into the fast unit suite, and so each category can be run (and failed, and fixed) independently.[6]

---

# Chapter 19: Testing persistence and databases

## 19.1 The database question, asked properly

Before writing any database test, answer one question: **what am I trying to prove?**

- Testing **business logic** (rules, calculations, decisions)? Unit test, fake repository. The database is irrelevant to the rule.
- Testing a **query, transaction, constraint, mapping, migration, or provider-specific behavior**? Integration test, against a database that behaves like the production one. Only the real thing proves the real thing.

## 19.2 Strategy A: the fake repository

For application logic, hide persistence behind a seam you already know how to build:

```csharp
public interface IProductRepository
{
    Task<Product?> FindAsync(string sku, CancellationToken cancellationToken);
}
```

Line by line: `Task<Product?>` — an asynchronous result that may be `null`: the `?` says "maybe no product was found." A fake stores products in a `Dictionary<string, Product>` (the pattern of `FakeInventoryGateway` from Chapter 16) and answers instantly.

It proves how your *service* behaves when the repository answers in a given way. It proves nothing about SQL — which is fine, because that is not the question it is answering.

## 19.3 Strategy B: SQLite in-memory

Swap the real provider for SQLite running entirely in memory: a real relational engine — real SQL translation, real transactions — with zero setup and millisecond speed. The honest caveat: SQLite is not SQL Server or PostgreSQL; dialects, types, and features differ, so it proves *relational* behavior, not *your production engine's* behavior. It is a strong middle gear when your production behavior is compatible.

## 19.4 Strategy C: a real database in a container

Run the actual production engine (SQL Server, PostgreSQL, ...) in a container for the test run. Maximum realism: provider-specific SQL, constraints, indexes, concurrency. Costs: slower runs, container infrastructure, and — the real design problem — **isolation between tests**. The standard options, strongest first:

- a **fresh database per test class** (or per test, if speed allows);
- a **unique schema** per test class inside one database;
- **transaction-per-test** with rollback — each test opens a transaction, works, and rolls back, leaving no trace;
- a cleanup step that **resets known tables** between tests.

Choose based on how much your tests would otherwise interfere, and keep the choice documented — future-you will want to know why.

## 19.5 Why a purely in-memory *object* provider can mislead

Some ORMs offer an in-memory provider that is just an object graph with a database-like API. It may not reproduce:

- relational constraints (unique keys, foreign keys);
- SQL translation and its errors;
- transaction semantics;
- indexes and their effects;
- provider-specific functions;
- concurrency behavior.

Use it only when its limitations are acceptable for the behavior under test — and consciously, never by accident of "it was the default."

## 19.6 The integration-test lifecycle

A database integration test follows this sequence, every time:

1. Create or connect to the test database.
2. Apply schema or migrations.
3. Seed **only** the data this test needs.
4. Execute **one** business scenario.
5. Assert returned data **and** persisted data (the query result *and* what the tables now hold).
6. Roll back or clean up.
7. Dispose connections and contexts.

Do not share mutable data between tests unless isolation is deliberately controlled. Two tests both editing the `COFFEE` row are two tests scheduled for a fight.

## 19.7 Contract tests for repositories

When several implementations must honor the same promise (a fake for unit tests, an EF Core repository for production), a **contract test** runs the same checks against each:

```csharp
public abstract class ProductRepositoryContractTests
{
    protected abstract Task<IProductRepository> CreateRepositoryAsync();

    [Fact]
    public async Task FindAsync_ExistingSku_ReturnsProduct()
    {
        // Arrange
        var repository = await CreateRepositoryAsync();

        // Act
        var product = await repository.FindAsync("COFFEE", default);

        // Assert
        Assert.NotNull(product);
        Assert.Equal("COFFEE", product.Sku);
    }
}
```

Line by line: the class is `abstract` — it is a template, never run directly; `protected abstract Task<IProductRepository> CreateRepositoryAsync()` is the hook each implementation-specific subclass fills in ("build me a repository against *your* store, seeded for this test"); the tests then express the *contract* — any implementation must return the product for an existing SKU. One suite of rules, run against every implementation, including the real database one. Keep contracts small; they are an advanced pattern that pays off exactly when multiple implementations must stay aligned.

---

# Chapter 20: Fixtures, lifetime, cleanup, and parallel execution

## 20.1 Why lifecycle matters

A test framework must answer questions your tests silently depend on:

- When is the test class constructed — once, or per test?
- Does each test get fresh state?
- Where do expensive shared resources get created, and when are they destroyed?
- Do tests run at the same time?

Misunderstand here, and tests accidentally share state — the root of the mysterious "passes alone, fails in the suite" failure you will eventually meet and then diagnose with Chapter 29.10.

## 20.2 xUnit constructs a fresh instance per test

**xUnit creates a new instance of your test class for every single test method.** The constructor therefore runs once per test, making it the natural home for per-test setup:

```csharp
public sealed class CartTests
{
    private readonly ShoppingCart _cart;

    public CartTests()
    {
        _cart = new ShoppingCart();
    }

    [Fact]
    public void NewCart_IsEmpty()
    {
        Assert.Empty(_cart.Items);
    }
}
```

Here `NewCart_IsEmpty` and any sibling test each get their *own* `_cart`. Field values mutated by one test do **not** leak into the next — not because of your discipline, but because the next test runs in a brand-new object. This is the xUnit design decision from Chapter 6.4, now experienced first-hand. (Corollary: do not assume anything you stored in a field survives to the next test. It does not.)

## 20.3 Per-test cleanup with `IDisposable`

When a test acquires a resource that must be released even if the test fails, implement `IDisposable`:

```csharp
public sealed class FileTests : IDisposable
{
    private readonly string _path;

    public FileTests()
    {
        _path = Path.GetTempFileName();
    }

    public void Dispose()
    {
        File.Delete(_path);
    }

    [Fact]
    public void FileExistsAfterCreation()
    {
        Assert.True(File.Exists(_path));
    }
}
```

Line by line: `: IDisposable` — the standard .NET disposal interface; xUnit calls `Dispose()` after each test, pass or fail. `Path.GetTempFileName()` creates a uniquely named empty temporary file — unique paths keep parallel tests from colliding (section 20.7). The rules: cleanup must run even on failure; use unique temporary paths; never delete files you did not create.

## 20.4 Class fixtures: share the expensive

When setup is expensive and safely shareable **within one class**, declare a **class fixture**:

```csharp
public sealed class SharedResourceFixture : IDisposable
{
    public SharedResourceFixture()
    {
        // Create the resource once.
    }

    public void Dispose()
    {
        // Destroy the resource once.
    }
}

public sealed class ResourceTests
    : IClassFixture<SharedResourceFixture>
{
    private readonly SharedResourceFixture _fixture;

    public ResourceTests(SharedResourceFixture fixture)
    {
        _fixture = fixture;
    }
}
```

Word by word: `SharedResourceFixture` is an ordinary class implementing `IDisposable`; `IClassFixture<SharedResourceFixture>` on the test class tells xUnit to construct the fixture **once** for the class and dispose it when the class is finished; the constructor *receives* the fixture — the same constructor-injection idea you have used for services since Chapter 15. This is precisely the mechanism behind `IClassFixture<WebApplicationFactory<Program>>` in Chapter 18: the whole in-memory application is one shared, expensive resource. Share only what is safe to share — a shared *mutable* database makes tests order-dependent (section 20.8).

## 20.5 Wider sharing: collection fixtures (in brief)

To share one resource across **several** test classes, xUnit has collection fixtures: define a fixture class, define a named collection definition using `ICollectionFixture<T>`, and mark classes `[Collection("name")]`. The shape is the same; the scope is wider. Reach for it when, for example, several integration-test classes should share one database boot.

## 20.6 Async fixtures

For asynchronous setup and teardown, xUnit's lifecycle support extends to `Task`-returning methods:

```csharp
public sealed class AsyncResourceFixture : IAsyncLifetime
{
    public Task InitializeAsync()
    {
        return Task.CompletedTask;
    }

    public Task DisposeAsync()
    {
        return Task.CompletedTask;
    }
}
```

`IAsyncLifetime` runs `InitializeAsync` before the fixture's consumers and `DisposeAsync` after; `Task.CompletedTask` is an already-finished task (the async sibling of "do nothing"). Verify the exact lifecycle interfaces supported by your installed xUnit version — v2 and v3 differ in their async disposal options, as xUnit's shared-context documentation describes.[9]

## 20.7 Parallel execution

Parallel tests finish faster — but only if they do not collide. xUnit runs tests **in parallel by default across test collections** (each test class is its own collection by default), while tests **within** one collection run sequentially; its documentation explains collections, modes, thread limits, and how to disable parallelization.[10]

Parallel-safe tests must not depend on:

- a shared static list another test mutates;
- a fixed file name another test opens;
- a single database row another test edits;
- execution order;
- the current time (Chapter 17.1's disease);
- a global environment variable another test changes.

The mature response is **isolation**, not switching parallelism off: unique files, per-test data, injected clocks. Disable parallelism only when a shared resource truly cannot be isolated — and write down why, next to the switch.

## 20.8 Order dependence is a defect

If test B passes only when test A ran first, the suite contains hidden shared state. The wrong fix is ordering the tests — you would be painting over damp. The right fix is the state boundary: make B's setup produce everything B needs. Chapter 29.10 walks the diagnosis.

---

# Part VII: Professional workflows

You can write tests; now learn to *run them like a professional*: select exactly the tests you need, label them into categories, read failures systematically, measure what your suite actually touches, drive design with tests in the TDD rhythm, place every test type in a whole-system strategy, and let a machine re-verify everything on every push.

# Chapter 21: Selecting, categorizing, and debugging tests

## 21.1 Run exactly what you need

```powershell
# One project
dotnet test tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj

# One class, by name fragment
dotnet test --filter "FullyQualifiedName~ShoppingCartTests"

# One test, by name fragment
dotnet test --filter "FullyQualifiedName~Add_ZeroQuantity"
```

Word by word: `--filter` selects a subset; `FullyQualifiedName` is the test's namespace-qualified name; `~` means "contains" (the equality operator `=` and the contains operator `~` are both supported, combinable with `|` for OR and `&` for AND). Supported property names differ slightly between MSTest, xUnit, and NUnit — the .NET selective-testing document lists them per framework.[8]

## 21.2 Traits: labels that mean execution decisions

Traits attach key-value metadata to tests, at class or method level:

```csharp
[Trait("Category", "Unit")]
public sealed class ShoppingCartTests
{
    [Fact]
    [Trait("Speed", "Fast")]
    public void NewCart_SubtotalIsZero()
    {
        var cart = new ShoppingCart();
        Assert.Equal(0m, cart.Subtotal);
    }
}
```

Line by line: `[Trait("Category", "Unit")]` on the class labels every test inside it; the inner `[Trait("Speed", "Fast")]` adds a second label to one test. Then run by trait:

```powershell
dotnet test --filter "Category=Unit"
```

`Category` is the conventional trait for test *kind*, and it composes with everything else. Choose categories that represent real execution decisions — `Unit`, `Integration`, `Slow`, `External` — not a taxonomy of hopes. A dozen unused labels is taxonomy clutter; four labels that map to "what do I run before every commit" are leverage.

## 21.3 Build and test, separately

```powershell
# Compile only, in Release configuration
dotnet build Store.sln --configuration Release

# Run tests against already-built binaries
dotnet test Store.sln --no-build --configuration Release
```

`--configuration Release` selects the optimized build configuration (the default is `Debug`). `--no-build` skips rebuilding — a real speedup for repeated runs, and safe **only** when you know the binaries are current. After editing anything, build again before trusting `--no-build`.

## 21.4 Verbosity: making the tool talk

```powershell
dotnet test Store.sln --verbosity normal
dotnet test Store.sln --verbosity detailed
```

`--verbosity` (aliases `-v`) sets output detail: `quiet`, `minimal`, `normal`, `detailed`, `diagnostic`. Use `normal` to see which projects and tests run; reach for `detailed` only when debugging a runner or restore problem — it prints a lot.

## 21.5 Printing from inside a test: `ITestOutputHelper`

`Console.WriteLine` does not reliably reach xUnit's per-test output. The supported channel is injection, in the constructor:

```csharp
public sealed class DiagnosticTests
{
    private readonly ITestOutputHelper _output;

    public DiagnosticTests(ITestOutputHelper output)
    {
        _output = output;
    }

    [Fact]
    public void SomethingWorks()
    {
        _output.WriteLine("Reached the interesting branch");
        // ...
    }
}
```

Line by line: `ITestOutputHelper` is xUnit's per-test output writer; the runner injects it into your constructor (the same mechanism as fixtures); `_output.WriteLine(...)` text appears attached to that one test's result — visible when it fails, invisible noise when it passes. This is the sanctioned way to leave breadcrumbs mid-test.

## 21.6 Reading a failure systematically

When a test fails, resist the urge to fix *something*. Read, in this order:

1. **Test name** — which behavior is accused?
2. **Expected value** — what does the test demand?
3. **Actual value** — what really happened?
4. **Exception type and message** — how did it fail: assertion mismatch, null reference, timeout?
5. **Source line** — where exactly?
6. **Inner exception** — is there a deeper cause beneath the thrown one?
7. **Logs or captured output** — what breadcrumbs exist?
8. **Recent changes** — what moved last, in production *and* test code?

Only after those eight questions do you touch code — and then you already know which side is wrong (Chapter 13.6).

## 21.7 Never weaken an assertion to get green

The one forbidden move: making an assertion vaguer so the red goes away (`Equal(5)` → `True(actual > 0)`). Each such edit converts a documented guarantee into a shrug. If the expectation was wrong, *correct* it deliberately and say why. If the behavior is wrong, fix the behavior. The suite's value is exactly the honesty of its assertions.

---

# Chapter 22: Code coverage

## 22.1 What coverage measures

**Code coverage** measures how much of your code was *executed* while tests ran — most commonly:

- **line coverage** — fraction of executable lines that ran;
- **branch coverage** — fraction of decision outcomes (`if` taken *and* skipped, both sides of `?:`) that were exercised;
- **method coverage** — fraction of methods entered at all.

Coverage is evidence about which code ran. It is **not** evidence that the code is correct — see 22.2, then never forget it. Microsoft's best-practices guidance explicitly warns that an ambitious percentage goal can be counterproductive, because high coverage does not automatically mean high-quality tests.[4]

## 22.2 Executed is not verified

```csharp
cart.Total(0.10m);
Assert.True(true);
```

This test executes `Total` — line coverage goes up — and verifies absolutely nothing. Any bug in `Total` sails through. A smaller suite with real assertions beats a large suite of line-walkers: coverage is a *question-generator*, not a quality score.

## 22.3 Collect coverage

The standard xUnit template already ships the Coverlet collector (Chapter 8.4's `coverlet.collector`). Run:

```powershell
dotnet test Store.sln --collect:"XPlat Code Coverage"
```

Word by word: `--collect:"XPlat Code Coverage"` activates the cross-platform (XPlat) coverage collector during the test run. Results land under a `TestResults` directory as `coverage.cobertura.xml` — Cobertura being a standard coverage-XML format that many tools understand. Microsoft documents Coverlet and the built-in coverage collector as the two collection options for .NET test runs.[7]

## 22.4 Generate an HTML report

Install the report generator once (a *global tool*, installed into your user account, not the project):

```powershell
dotnet tool install -g dotnet-reportgenerator-globaltool
```

Then, after a coverage run, generate the browsable report (PowerShell; the backtick at line ends continues the command):

```powershell
reportgenerator `
  -reports:"**/TestResults/**/coverage.cobertura.xml" `
  -targetdir:"coverage-report" `
  -reporttypes:Html
```

Word by word: `-reports:` points at every Cobertura file under any `TestResults` folder (the `**` glob spans directories); `-targetdir:` chooses the output folder; `-reporttypes:Html` selects clickable HTML. Open `coverage-report/index.html` and browse your code colored by execution.

## 22.5 Interpreting coverage: ask questions, not percentages

When coverage is low, do not mechanically write filler tests. Ask:

- Which **business rule** has no test?
- Which **error path** is untested?
- Is this uncovered code **dead**, or deliberately excluded?
- Is a branch hard to reach because the design **hides a dependency** (a seam waiting to happen — Chapter 17)?
- Would an **integration test** be the honest test here rather than a unit-test contortion?

When coverage is high, remember 22.2 and ask what the assertions actually assert. Coverage tells you where your tests are silent; it cannot tell you where they are wrong.

---

# Chapter 23: Test driven development

## 23.1 The cycle: red, green, refactor

**Test-driven development** (Chapter 2.6 told its history) is a working rhythm:

1. **Red.** Write one small test for behavior that does not exist yet. Run it. Watch it fail. (A red you have *seen* is a test you can trust.)
2. **Green.** Write the *simplest* production code that makes it pass. Not the most general, not the most elegant — the simplest that satisfies the test you just wrote.
3. **Refactor.** With the safety net green, improve the design of production *and* test code. Rerun. Stay green.

Repeat in small steps, minutes per loop. The test is written *before* the implementation, and the sequence is a discipline, not a ceremony.

## 23.2 TDD in practice: a new discount rule

The business says: *orders of at least 100 receive a 10% discount rate; smaller orders receive nothing.*

**Red.** The test first — for a class `StandardDiscountPolicy` that does not exist yet. Add it to the application test project:

```csharp
using Store.Application;
using Store.Domain;

namespace Store.Application.UnitTests;

public sealed class StandardDiscountPolicyTests
{
    [Theory]
    [InlineData(99m, 0m)]
    [InlineData(100m, 0.10m)]
    public void GetDiscountRate_DependsOnSubtotal(
        decimal subtotal,
        decimal expectedRate)
    {
        // Arrange
        var cart = new ShoppingCart();
        cart.Add("ITEM", "Item", subtotal);
        var policy = new StandardDiscountPolicy();

        // Act
        var actual = policy.GetDiscountRate(cart);

        // Assert
        Assert.Equal(expectedRate, actual);
    }
}
```

Run it: **it does not compile** — `StandardDiscountPolicy` does not exist. That is the reddest red. (When a test cannot compile, some practitioners create the class with a throwing method body to see the test *fail* rather than not-build; both readings count as red.)

**Green.** The smallest implementation that passes both rows:

```csharp
using Store.Domain;

namespace Store.Application;

public sealed class StandardDiscountPolicy : IDiscountPolicy
{
    public decimal GetDiscountRate(ShoppingCart cart)
    {
        return cart.Subtotal >= 100m ? 0.10m : 0m;
    }
}
```

One line of logic, read word by word: `cart.Subtotal >= 100m` — the threshold test, `>=` because "at least 100" includes exactly 100 (the `100 → 0.10` row exists to pin this); `? 0.10m : 0m` — the conditional operator: if the condition holds, `0.10m`, else `0m`. Run: green, both rows.

**Refactor.** Now, protected by the theory, improve if needed: name the magic values (`private const decimal DiscountThreshold = 100m;`), split the method, adjust naming. After each move, `dotnet test` — green stays green. Refactoring without a green suite is guessing; with one, it is engineering.

## 23.3 Why write the test first?

Writing the test first is not about proving more; it changes what gets built:

- It forces the design question **"how will this be called?"** before "how will this work inside?" — which produces testable, caller-shaped APIs by construction.
- It prevents the silent debt of "I'll add tests later," which experience says arrives approximately never.
- It keeps steps small: a failing test defines a precise, finishable next move.

## 23.4 TDD is not dogma

Use the cycle when the feedback loop helps clarify a rule or a design — which is most of the time. In genuinely exploratory work (learning an API, spiking an algorithm), prototype freely, then *immediately* pin the learned behavior with tests once it stabilizes. The outcome that matters is fast, trustworthy feedback and clearly expressed behavior — not ritual compliance.

## 23.5 Beginner mistakes, pre-listed

- **Big steps:** writing five tests before any implementation. Return to one-at-a-time.
- **Skipping the red:** never seeing the new test fail, so never knowing it can fail.
- **Testing the implementation** rather than the behavior (Chapter 26.2) — the greenest suite that breaks on every refactor.
- **Refactoring in the red phase** — fix green first; refactor only on green.

---

# Chapter 24: Testing the whole system

## 24.1 The test pyramid

The **test pyramid** (Chapter 2.6: Mike Cohn, 2009; Ham Vocke's "The Practical Test Pyramid," 2018) is a portfolio heuristic:

```text
        /  \          few end-to-end tests
       /----\         fewer integration tests
      /--------\      many fast unit tests
```

Read bottom-up: *many* fast, cheap, precise unit tests; *fewer* integration tests at real boundaries; *few* end-to-end tests exercising whole workflows. The proportions are a heuristic, not law — the correct mix depends on architecture and risk — but the gradient always points the same way: as tests get more realistic, they get slower, flakier, and harder to diagnose, so buy realism only where it pays.

## 24.2 Unit tests — best for

Deterministic business rules: calculations, validation, state transitions, mappings, service decisions with fake collaborators. Quick and isolated; they are your development-time heartbeat.

## 24.3 Integration tests — best for

Boundaries between components: the HTTP pipeline (Chapter 18), database queries (Chapter 19), serialization, DI registration, middleware, file or message infrastructure selected for integration coverage.

## 24.4 Contract tests

A **contract test** verifies that a producer and a consumer agree on a boundary: an HTTP response shape, a message schema, a repository interface (Chapter 19.7's pattern), an external API adapter. Where an integration test asks "do these parts work together?", a contract test asks the sharper question "do both sides still promise the same thing?" — which is exactly what breaks silently when one team ships a rename. Focus contract tests on the *agreement*, not on either side's internals.

## 24.5 End-to-end tests

An end-to-end scenario drives the system through its outermost boundary, like a user would:

1. Create a customer through HTTP.
2. Add a product to a cart.
3. Submit an order.
4. Check the response.
5. Verify the persisted order.

Keep these few and deliberately chosen — they are the most expensive to run and the most painful to diagnose. A failing end-to-end test tells you *that* something broke somewhere in the whole chain; a failing unit test usually tells you *what* and *where*. Prefer the precision of the lower layers; reserve the top for journeys whose business value justifies the cost.

## 24.6 Performance tests

Performance testing needs a defined workload, a controlled environment, and a measurement plan. It is **not** this:

```csharp
[Fact]
public void Total_IsFastEnough()
{
    var stopwatch = Stopwatch.StartNew();
    cart.Total(0.10m);
    stopwatch.Stop();
    Assert.True(stopwatch.ElapsedMilliseconds < 10);   // do not do this
}
```

Wall-clock assertions on shared hardware are a flakiness factory: CI runners vary, machines are busy, garbage collection happens. Use real performance tooling, measure **distributions** (median, tail latency) over many iterations, and compare against an environment you control.

## 24.7 Security tests

Test what is genuinely testable: authorization decisions (this caller may not reach that endpoint — assert 401/403), input validation, secret handling (secrets never in logs or responses), and unsafe serialization settings. Pair these with automated scanning and threat-model-driven review. A green unit suite proves nothing about security by itself — security is a property of design and configuration, and tests are one of its witnesses, not its source.

---

# Chapter 25: Continuous integration with GitHub Actions

## 25.1 Why CI

A test suite that runs only on one developer's machine protects exactly one developer's machine. **Continuous integration (CI)** runs the same build and test commands on every push and pull request, on a clean machine, owned by no one — so "it works on my laptop" stops being an argument. GitHub Actions (Chapter 6.5's history: generally available since November 2019) is the automation system built into this repository's host, so it is the natural first CI.

## 25.2 The workflow file

Create the file `.github/workflows/dotnet.yml` in your repository (the exact path matters: GitHub looks in `.github/workflows/`):

```yaml
name: .NET build and test

on:
  push:
  pull_request:

permissions:
  contents: read

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Check out source
        uses: actions/checkout@v4

      - name: Install .NET SDK
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: 8.0.x

      - name: Restore dependencies
        run: dotnet restore Store.sln

      - name: Build
        run: dotnet build Store.sln --configuration Release --no-restore

      - name: Run tests
        run: dotnet test Store.sln --configuration Release --no-build --logger "trx;LogFileName=test-results.trx"
```

Line by line — YAML is indentation-sensitive, so type it exactly:

| Line piece | Meaning |
|---|---|
| `name: .NET build and test` | The workflow's human-readable name, shown on the Actions tab. |
| `on:` | **When** the workflow runs. |
| `push:` / `pull_request:` | On every push to any branch, and on every pull request. |
| `permissions:` | The workflow's token rights. |
| `contents: read` | Grant only read access to the repository — least privilege, a security habit. |
| `jobs:` | The work, in named jobs. |
| `build-and-test:` | This job's ID (one job here; more complex setups run unit and integration jobs separately). |
| `runs-on: ubuntu-latest` | The virtual machine the job executes on (a stock GitHub-hosted Linux runner). |
| `steps:` | The ordered steps of the job. |
| `- name: Check out source` | A step's display name. |
| `uses: actions/checkout@v4` | Run the official checkout **action** (a reusable step) to download your repository onto the runner. |
| `uses: actions/setup-dotnet@v4` | Run the official action that installs the .NET SDK. |
| `with:` / `dotnet-version: 8.0.x` | Configuration for that action: install the latest 8.0 patch (`8.0.x` accepts patch updates, not minor jumps). |
| `run: dotnet restore Store.sln` | Run a shell command: download NuGet dependencies. |
| `run: dotnet build ... --no-restore` | Compile the solution; `--no-restore` skips re-restoring, because the previous step already did (Chapter 21.3's separation, automated). |
| `run: dotnet test ... --no-build` | Run the tests against the just-built binaries; `--logger "trx;LogFileName=test-results.trx"` writes results in TRX format — the Visual Studio test-result XML — so CI keeps a per-run record of every test outcome. |

If your projects target `net10.0`, set `dotnet-version: 10.0.x` (and any other needed versions) — keep workflow SDK and project targets compatible.

## 25.3 CI principles worth internalizing

- Run the **same commands** developers run locally; CI should never be a mystery pipeline.
- **Fail when tests fail** — a CI that tolerates red is a random number generator with a logo.
- Keep unit tests **fast**, so the feedback loop stays tight.
- Run integration tests in a **separate job** (with its services — database containers, and so on) so their setup cost does not tax every push.
- **Publish artifacts** — the TRX results, coverage files — when diagnosis requires them.
- Do not hide flaky tests: **quarantine** them with an owner and a removal date, and fix the root cause (Chapter 26.7).
- **Pin or centrally manage tool versions** when reproducibility matters.

## 25.4 What you now have

Push to your repository and open the **Actions** tab: you should see the workflow run, the four steps execute, and your entire suite verified on a machine you have never seen, by commands you have typed a hundred times. That — not the code — is the moment your project became an engineering artifact.

---

# Part VIII: Mastery

The final part turns competent technique into judgment: how to design tests that stay cheap to maintain, how to recognize the smells that predict a decaying suite, which advanced techniques pay for themselves and where, and finally a capstone, a troubleshooting manual, and an honest self-assessment.

# Chapter 26: Test design and maintainability

## 26.1 The five characteristics of a good unit test

Memorable as **F.I.R.S.T.** — and identical to Microsoft's best-practices guidance:[4]

- **Fast** — finishes in milliseconds. Slow unit tests get skipped, and skipped tests protect nothing.
- **Isolated** — depends on no unrelated external state: no leftover files, no shared rows, no yesterday's clock.
- **Repeatable** — same code, same inputs, same verdict, every time, every machine.
- **Self-checking** — the runner decides pass/fail; no human reads output.
- **Timely** — economical to write relative to the behavior it protects; written close to the code it guards.

Grade any struggling test against these five; the failing dimension names the fix.

## 26.2 Test behavior, not implementation

The single most important design rule. Compare:

```csharp
// Fragile: asserts a private mechanism's fingerprint
Assert.Equal("System.Linq.Enumerable+WhereListIterator`1[System.Int32]",
    value.GetType().FullName);

// Useful: asserts what callers need
Assert.Equal(3, service.GetAvailableCount());
```

The fragile test encodes *how* the answer is produced (which internal iterator type LINQ happens to return). The next refactor — harmless, behavior-preserving — breaks it, and maintainers learn to fear the suite. The useful test protects the *contract*; implementations come and go underneath it. A practical detector: if you can describe the test's assertion without mentioning a method's internals, it tests behavior.

## 26.3 Do not test private methods directly

A `private` method is an implementation detail. Test it *through the public behavior that uses it*. If a private method is so complex that you itch to test it directly, that is a design message: extract the logic into its own small class with a public, well-named method — complexity promoted to a tested unit instead of a hidden tangle.

## 26.4 Keep logic out of tests

Tests should not contain loops, branching, or — worst of all — a re-implementation of the production algorithm (Chapter 13.3's mirror bug: the same mistake in both sides passes forever). Data belongs in explicit rows:

```csharp
[Theory]
[InlineData("", 0)]
[InlineData("1", 1)]
[InlineData("1,2", 3)]
public void Add_ReturnsExpectedSum(string input, int expected)
{
    var actual = calculator.Add(input);
    Assert.Equal(expected, actual);
}
```

Three visible scenarios beat one clever loop any week — visible data makes the missing case obvious at a glance.

## 26.5 Name your magic values

```csharp
const decimal FreeShippingThreshold = 50m;
const string CoffeeSku = "COFFEE";
```

`cart.Add(CoffeeSku, "Coffee", 10m)` tells the reader *which* product matters and why; `("X1", ...)` tells them nothing. A named constant carries its reason in its name, and when the threshold changes, one edit updates every test that cites it.

## 26.6 The test smell catalog

A **test smell** is a warning sign that a test or suite is becoming hard to trust or expensive to maintain. Learn the names — they are diagnostic shorthand:

- **Mystery guest** — hidden files, databases, or environment state silently affect the result; the test's inputs are not visible in the test.
- **General fixture** — setup builds a small city when the test needs one street.
- **Test code duplication** — copy-pasted setup that must be edited in a dozen places when reality shifts.
- **Assertion roulette** — a wall of assertions with no clear meaning; any failure could mean anything.
- **Conditional test logic** — `if` statements inside the test: the test is now a program, and programs have bugs.
- **Slow test** — a "unit" test waiting on real infrastructure.
- **Brittle test** — harmless refactors break it (usually implementation testing, 26.2).
- **Test coupling** — one test depends on another's side effects (Chapter 20.8).
- **Over-mocking** — the test verifies internal call sequences instead of outcomes (Chapter 15.8).
- **False confidence** — high coverage, weak assertions (Chapter 22.2).
- **Flaky test** — the verdict changes without a relevant code change.

Smells are signals, not commandments: investigate before amputating.

## 26.7 The flaky test protocol

When a test is flaky, do not rerun until green and walk away — that trains the team to ignore red. Follow the protocol:

1. **Record** when it fails, and under what environment, load, and parallelism.
2. **Hunt** in the usual lairs: time, randomness, concurrency, network, file paths, shared state (Chapter 17 is your field guide).
3. **Reproduce** in isolation and in parallel — both directions.
4. **Expose** the dependency: make it an explicit input or a test double.
5. **Fix the root cause.**
6. **Keep a regression test** that would have caught it.

If the root cause cannot be fixed today, quarantine the test — excluded from the main run, with an owner and a removal date — and say so honestly in the suite's documentation.

---

# Chapter 27: Advanced and mastery topics

## 27.1 Testing architecture boundaries

At mastery level, the question stops being "how do I test this class?" and becomes "**does my structure make the right tests natural?**" A healthy boundary looks like:

```text
API endpoint
    -> application service
        -> domain rules
        -> repository or gateway interface
            -> infrastructure implementation
```

Then each test type finds its natural home:

- domain rules → fast unit tests;
- application services → unit tests with fakes for gateways;
- API endpoints → integration tests at the HTTP boundary (Chapter 18);
- infrastructure → provider-specific integration tests (Chapter 19);
- the complete path → a few end-to-end tests (Chapter 24.5).

When a test is hard to write, the architecture is telling you where a seam is missing (Chapter 17.7).

## 27.2 Property-based testing

Example-based testing (all you have done so far) says: *for input A, expect result B.* **Property-based testing** says: *for many generated inputs, some general property must always hold.* Properties are rules that transcend specific examples:

- sorting a list does not change its length;
- encoding then decoding returns the original value;
- a cart's subtotal is never negative when all prices and quantities are valid;
- adding zero items never changes a total.

Libraries such as FsCheck generate inputs for .NET, hunting for a counterexample. Two disciplines make it usable: start from ordinary examples and graduate to properties only for rules that truly generalize; and demand **reproducible failures** — from a seed or a *shrunk* (minimized) counterexample — or a red property test is just a riddle. A generated failure you cannot reproduce is a flaky test with a diploma.

## 27.3 Mutation testing

Mutation testing deliberately breaks your production code in small, plausible ways — swap `>` for `>=`, delete a guard, flip a Boolean — and checks whether **any test notices**. Each unnoticed change is a *surviving mutant*: concrete evidence that your suite does not actually pin that behavior. Coverage asks "did this line run?"; mutation testing asks the better question "would my suite catch a plausible defect here?" It is expensive — run it on your most important domain components, not on generated code. Surviving mutants are a to-do list for missing assertions.

## 27.4 Approval and snapshot testing

An approval (snapshot) test serializes a large output — JSON, a report, UI markup — and compares it byte-for-byte with a stored approved file. Effective when *the full representation is the contract*: a stable document format, a complex payload. The risks are social and subtle: reviewers approve diffs they did not read; formatting noise drowns meaningful change; the snapshot quietly becomes the spec. Use snapshots when the whole shape matters; otherwise assert focused properties and let readers see *why* each value matters.

## 27.5 Testing messages and event-driven systems

Message-driven code adds a testing axis: the same message may arrive twice, out of order, or never. Test:

- **event shape** — the payload and its required headers;
- **idempotency** — processing the same message twice must not double its effect;
- **retry behavior** — what happens when handling fails partway;
- **duplicate delivery** — the transport *will* redeliver eventually;
- **ordering assumptions** — document and test what you actually rely on;
- **poison messages** — malformed input must quarantine, not crash, the consumer;
- **observability** — failures must leave traceable evidence.

A consumer that is safe to run more than once is not paranoia; it is the delivery model's contract.

## 27.6 Testing concurrency

Concurrency defeats timing-based tests — thread scheduling is not an assertion target. Prefer designs that expose **synchronization points you control**:

- inject a clock or scheduler (Chapter 17's seams, extended to time itself);
- use `TaskCompletionSource` as a remote control for "this step has (not) happened yet" — the test decides when pending work completes, deterministically;
- protect shared state with explicit locks or concurrent collections;
- assert **invariants** ("the counter equals the number of completed operations"), never exact thread interleavings.

Stress tests (many iterations under load) supplement deterministic tests; they never replace them — a stress test that fails once in ten thousand runs is a clue, not a check.

## 27.7 Native AOT and test execution platforms

Modern .NET applications may publish with trimming or **Native AOT** (ahead-of-time compilation), which restricts reflection — and test discovery leans on reflection. This is where the Chapter 6.5 platform story matters: Microsoft.Testing.Platform has scenarios where it is preferable to VSTest (AOT, specialized extensions, large repos), while VSTest remains valuable for established integrations. Microsoft's guidance: choose one platform consistently per repository and follow your framework's current documentation.[2] When you reach AOT territory, treat the test-platform choice as a real decision, not a default.

## 27.8 Test observability

A mature suite produces evidence, not just verdicts:

- test names that explain the scenario (Chapter 12);
- failures that include expected and actual values;
- logs identifying the boundary and a correlation ID;
- integration tests that preserve response bodies on failure;
- CI uploading test results and coverage artifacts (Chapter 25);
- timing data that names slow tests.

The standard: **a test that fails should explain itself.** A failure that creates more questions than it answers has not finished its job.

---

# Chapter 28: The capstone challenge

Build the Store application to this specification **without following the guide line by line**. Start from an empty folder if you want the full experience. Every requirement below has been taught; the challenge is assembling them unassisted.

## 28.1 Domain requirements

1. A cart contains products with SKU, name, price, and quantity.
2. SKU and name cannot be blank.
3. Price cannot be negative.
4. Quantity must be positive.
5. Subtotal is the sum of price multiplied by quantity.
6. Tax is applied using a supplied rate.
7. Totals are rounded to two decimal places using an explicit rounding policy.
8. A discount policy can change the subtotal.
9. Inventory availability is asynchronous.
10. An unavailable item prevents order placement.

## 28.2 API requirements

1. `GET /health` returns 200 with a JSON status.
2. `GET /products/{sku}` returns 200 for a known product.
3. Unknown products return 404.
4. Invalid request data returns a useful 400 response.
5. The API uses dependency injection rather than constructing infrastructure inside endpoints.

## 28.3 Test requirements

Deliver:

- at least one fact for each basic invariant;
- theories for equivalent input classes;
- exception tests for invalid arguments;
- unit tests using a fake discount policy;
- async tests using a fake inventory gateway;
- integration tests for routes and status codes;
- persistence integration tests for at least one real query;
- a coverage report;
- one CI workflow;
- a README section explaining how to run each test category.

## 28.4 Mastery questions

Before declaring the project complete, answer in your own words:

1. What is the SUT in each test?
2. Which dependencies are real, and which are test doubles?
3. Why is each test a unit test or an integration test?
4. What behavior would fail if the tax calculation changed?
5. What behavior would fail if routing changed?
6. Which tests can run in parallel safely?
7. How do you run only the fast tests?
8. What does your coverage report **not** prove?
9. Where would a contract test add value?
10. What production design change would make a difficult test simpler?

If you can implement the project and answer all ten questions, you have moved from learner to practitioner. The checklist follows.

---

# Chapter 29: Troubleshooting

The failures below are the ones learners actually hit, in roughly the order they hit them. Diagnose by symptom; each entry ends in a fix.

## 29.1 `dotnet` is not recognized

The SDK is not installed, or the terminal predates the installation. Install the SDK (Chapter 7.1), open a **new** terminal, and confirm with `dotnet --info`.

## 29.2 No tests are discovered

`dotnet test` builds, runs, and reports **zero tests**. Check, in order:

- the test project references a test framework package (`xunit`);
- each test method has `[Fact]`, `[Theory]`, or your framework's marker;
- the method is `public` when the framework version requires it;
- the project targets a supported framework;
- the test adapter package (`xunit.runner.visualstudio`) is present;
- the project actually builds — a broken build reports no tests, differently.

## 29.3 The test project cannot see production types

"The type or namespace 'Store' could not be found." The project reference is missing or mistyped:

```powershell
dotnet add tests/Store.Domain.UnitTests reference src/Store.Domain
```

Also check the type's access modifier and namespace: an `internal` type is invisible from another assembly unless that assembly is granted `InternalsVisibleTo` — prefer making test-relevant types `public` in the production project (the test double of Chapter 15.5 was `internal` *inside the test assembly*, which is different).

## 29.4 Restore fails

```powershell
dotnet restore Store.sln --verbosity detailed
dotnet nuget locals all --clear
```

Word by word: the first re-downloads dependencies with full logging; the second empties the local NuGet caches (`locals all --clear`) so corrupted caches are rebuilt. Then check network access, package-source configuration, and target-framework compatibility of the packages you reference.

## 29.5 An async test "passes" without testing anything

Symptom: an async test is green instantly, or exceptions vanish. Cause: the test is `async void` or blocks with `.Result`. Fix: make the test `async Task`, `await` the operation, assert on the result (Chapter 16.5's rules).

## 29.6 A test is flaky

It fails sometimes, on some machines, under load. Search the lairs (Chapter 26.7): uncontrolled time, randomness, parallel-shared state, fixed file names, environment variables, network calls, shared database rows. Replace hidden dependencies with explicit inputs or test doubles. Never "fix" it with a retry loop.

## 29.7 Integration tests cannot find `Program`

The compiler (or runtime) does not know the type `Program` in `WebApplicationFactory<Program>`. Ensure the web project's `Program.cs` ends with:

```csharp
public partial class Program { }
```

and that the integration test project references the web project and the `Microsoft.AspNetCore.Mvc.Testing` package (Chapter 18.4).

## 29.8 Works locally, fails in CI

The environment differs. Compare, item by item:

- SDK version (Chapter 25's `dotnet-version`);
- operating system and path separators (`\` versus `/`);
- culture and timezone (formatting, parsing, week-ends!);
- file permissions;
- environment variables;
- database availability;
- network assumptions;
- test parallelism settings;
- stale build artifacts (`--no-build` on outdated binaries).

Then either remove the environment assumption from the test, or make it explicit setup the CI provides.

## 29.9 Coverage is unexpectedly low

Confirm that:

- the test project actually references the production assembly under measurement;
- the coverage collector ran (`--collect:"XPlat Code Coverage"`, and the template's `coverlet.collector` is intact);
- the tests ran and passed;
- you ran the command from the directory you think you did;
- the "uncovered" files are not generated or filtered-out code.

Low coverage is a question (Chapter 22.5), not always a defect.

## 29.10 A test only passes in a particular order

Test B needs test A's leftovers: shared-state coupling. Fix the state boundary — local setup, unique data, per-test resources, isolated fixtures (Chapter 20.8). Depending on execution order is depending on a scheduler's mood; the fix is never "run them in this order," however tempting the `.ordered` file looks.

---

# Chapter 30: A practical mastery checklist

Grade yourself honestly. The boxes you cannot yet check are your curriculum.

## Beginner

- [ ] I can explain what an assertion is.
- [ ] I can create a class library and an xUnit test project.
- [ ] I can add a project reference.
- [ ] I can run `dotnet test` and run a single project.
- [ ] I can read expected versus actual in a failure.
- [ ] I understand `Fact`, `Theory`, and `InlineData`.
- [ ] I can write Arrange, Act, and Assert sections.
- [ ] I can explain what a target framework is.

## Intermediate

- [ ] I name tests by behavior, scenario, and result.
- [ ] I test valid, invalid, boundary, and exception cases.
- [ ] I can test an async method without blocking.
- [ ] I can write a fake or stub for an interface.
- [ ] I can inject time, randomness, and external services.
- [ ] I know whether a test should be a unit test or an integration test.
- [ ] I can test an ASP.NET Core endpoint with `WebApplicationFactory`.
- [ ] I can filter tests by name and by trait.

## Advanced

- [ ] I can choose a database test strategy based on the behavior being proved.
- [ ] I understand fixture lifetime and cleanup.
- [ ] I can diagnose parallel-test failures.
- [ ] I can collect and interpret coverage without worshipping it.
- [ ] I can configure CI and preserve test results.
- [ ] I recognize test smells by name.
- [ ] I can refactor production code to create a useful seam.
- [ ] I can test contracts at system boundaries.

## Mastery

- [ ] I design application boundaries that support fast, focused tests.
- [ ] I use unit, integration, end-to-end, performance, and security tests for their different risks.
- [ ] I can evaluate whether a mock verifies behavior or merely implementation details.
- [ ] I can reduce flakiness without disabling all parallelism.
- [ ] I can use mutation or property-based testing where it adds value.
- [ ] I can explain what my test suite proves — and what it does not.
- [ ] I can improve a test suite while preserving its trust, speed, and diagnostic quality.

---

# Appendix A: Glossary

Alphabetical, for quick reference. Longer introductions live in Chapter 5.

- **AAA:** Arrange, Act, Assert — the three-phase structure of a test (Chapter 12).
- **Adapter:** the layer connecting a test framework to a test platform, e.g. `xunit.runner.visualstudio`.
- **Assertion:** an executable condition that must be true for the test to pass.
- **CI (Continuous Integration):** automated build and test execution after every code change.
- **Class library:** a project producing a reusable assembly rather than a launched application.
- **Code coverage:** a measurement of which code the tests executed (line, branch, method).
- **Contract test:** a test of an agreement at a system boundary between producer and consumer.
- **Deterministic:** producing the same result for the same controlled inputs.
- **End-to-end test:** a test of a broad user- or system-level workflow.
- **Fake:** a lightweight working replacement, such as an in-memory repository.
- **Fixture:** shared test setup or a shared resource with a defined lifetime (class fixture, collection fixture).
- **Framework (test framework):** the authoring API and conventions — xUnit, NUnit, MSTest, TUnit.
- **Flaky test:** a test whose result changes without a relevant code change.
- **Guard clause:** an early `if` + `throw` rejecting invalid input at a method's boundary.
- **Integration test:** a test that multiple components work together, possibly through a database or HTTP pipeline.
- **InlineData:** xUnit attribute supplying one row of data to a `[Theory]`.
- **Mock:** a test double used to verify interactions, in the terminology common to many .NET tools.
- **Mutation testing:** deliberately changing code in small ways to measure whether tests detect plausible defects.
- **Named argument:** calling `Add("TEA", "TEA", 4m, quantity: 3)` — the parameter named at the call site.
- **Namespace:** a naming mechanism grouping types; organizational, not security.
- **NuGet package:** the .NET ecosystem's unit of shared code, restored by the CLI.
- **Nullable reference types:** compiler warnings about possibly-null references; enabled with `<Nullable>enable</Nullable>`.
- **Primary constructor:** parameters written directly after a record or class name (Chapter 11.1).
- **Project:** the unit of compilation, described by a `.csproj` file.
- **Project reference:** a declaration that one project compiles against another's public types.
- **Property-based testing:** testing a general property over many generated inputs.
- **Red, green, refactor:** the TDD cycle (Chapter 23).
- **Regression:** previously working behavior broken by a change.
- **Runner:** the program that discovers and executes tests and reports results.
- **Seam:** a controlled point where a dependency can be replaced (Chapter 17).
- **Snapshot/approval test:** comparing a large serialized output with a stored approved file.
- **Solution:** a container (`.sln`) for related projects.
- **SUT (System Under Test):** the object, method, service, or application whose behavior a test examines.
- **Stub:** a replacement that supplies controlled answers.
- **Target framework:** the .NET API surface a project expects (`net8.0`, `net10.0`).
- **Test case:** one specific scenario; a parameterized test can create several from one method.
- **Test double:** the umbrella name for fake, stub, mock, spy.
- **Test platform:** the execution engine beneath test frameworks — VSTest or Microsoft.Testing.Platform.
- **Test suite:** a collection of tests — in a project, a solution, or a selected group.
- **Theory:** a parameterized test that runs once per data row.
- **Trait:** a key-value label on a test usable as a filter (`Category=Unit`).
- **Unit test:** a focused test of a small behavior with infrastructure isolated.
- **xUnit:** the family pattern of test frameworks descending from SUnit/JUnit; also short for xUnit.net.

# Appendix B: References

The documentation this guide's statements about .NET tooling are anchored to. All were the current Microsoft and xUnit documentation pages at the time of writing.

[1] Testing in .NET — <https://learn.microsoft.com/en-us/dotnet/core/testing/>
[2] Microsoft.Testing.Platform versus VSTest — <https://learn.microsoft.com/en-us/dotnet/core/testing/test-platforms-overview>
[3] Getting Started with xUnit.net v3 — <https://xunit.net/docs/getting-started/netcore/cmdline>
[4] Unit testing best practices for .NET — <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices>
[5] Testing with dotnet test — <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test>
[6] Integration tests in ASP.NET Core — <https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests>
[7] Use code coverage for unit testing — <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-code-coverage>
[8] Run selected unit tests (filter syntax) — <https://learn.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests>
[9] Sharing Context between Tests (xUnit) — <https://xunit.net/docs/shared-context>
[10] Running Tests in Parallel (xUnit) — <https://xunit.net/docs/running-tests-in-parallel>

# Appendix C: Quick command reference

Every command used in this guide, on one page. All run from the repository root unless a path says otherwise.

| Goal | Command |
|---|---|
| Check the SDK | `dotnet --version` / `dotnet --info` / `dotnet --list-sdks` |
| List templates | `dotnet new list` |
| Create a solution | `dotnet new sln -n Store` |
| Create a class library | `dotnet new classlib -n Name -o src/Name -f net8.0` |
| Create a test project | `dotnet new xunit -n Name.Tests -o tests/Name.Tests -f net8.0` |
| Add a project reference | `dotnet add tests/Name.Tests reference src/Name` |
| Add a NuGet package | `dotnet add tests/Name.Tests package Moq` |
| Add a project to the solution | `dotnet sln Store.sln add src/Name/Name.csproj` |
| Build | `dotnet build Store.sln` |
| Run all tests | `dotnet test` or `dotnet test Store.sln` |
| Run one project's tests | `dotnet test tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj` |
| Filter by name | `dotnet test --filter "FullyQualifiedName~ShoppingCartTests"` |
| Filter by trait | `dotnet test --filter "Category=Unit"` |
| Skip rebuild | `dotnet test Store.sln --no-build --configuration Release` |
| More output | `dotnet test --verbosity normal` (or `detailed`) |
| Collect coverage | `dotnet test Store.sln --collect:"XPlat Code Coverage"` |
| Install report tool (once) | `dotnet tool install -g dotnet-reportgenerator-globaltool` |
| Render HTML report | `reportgenerator -reports:"**/TestResults/**/coverage.cobertura.xml" -targetdir:"coverage-report" -reporttypes:Html` |
| Clear NuGet caches | `dotnet nuget locals all --clear` |
| Restore only | `dotnet restore Store.sln` |

**Where the files live**

| Path | Contents |
|---|---|
| `Store.sln` | The solution: lists all projects. |
| `src/Store.Domain` | Business rules: `CartItem`, `ShoppingCart`. |
| `src/Store.Application` | Services: `IDiscountPolicy`, `CheckoutService`, `IInventoryGateway`, `OrderService`. |
| `src/Store.Api` | ASP.NET Core minimal API (`Program.cs`). |
| `tests/Store.Domain.UnitTests` | Domain unit tests. |
| `tests/Store.Application.UnitTests` | Application tests plus the `Fakes/` folder. |
| `tests/Store.Api.IntegrationTests` | HTTP integration tests. |
| `.github/workflows/dotnet.yml` | The CI pipeline (Chapter 25). |
| `TestResults/` | Coverage and TRX output (created by runs). |
