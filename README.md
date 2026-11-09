# Testing C# Applications with .NET

A complete, guided path from absolute beginner to confident test engineer.

> **How to use this guide:** Read the chapters in order. Type the commands yourself instead of only copying them. After every meaningful code change, run the tests. When a test fails, study the failure before changing the code.

This guide uses **C#**, the **.NET SDK**, the **.NET CLI**, and **xUnit** as the main test framework. The concepts transfer to MSTest, NUnit, and other .NET test frameworks. The main examples are written to work with .NET 8 or later. If you have the .NET 10 SDK installed, you can target `net10.0`; otherwise use `net8.0`.

The guide grows one small application called **Store**. We begin with a class library and unit tests. We then add dependencies, asynchronous code, an ASP.NET Core API, integration tests, persistence, code coverage, continuous integration, and advanced testing techniques.

---

## Table of contents

- [Chapter 0: What you will learn](#chapter-0-what-you-will-learn)
- [Chapter 1: What software testing means](#chapter-1-what-software-testing-means)
- [Chapter 2: A short history](#chapter-2-a-short-history)
- [Chapter 3: The words you must understand](#chapter-3-the-words-you-must-understand)
- [Chapter 4: Installing and checking the tools](#chapter-4-installing-and-checking-the-tools)
- [Chapter 5: Your first test from zero](#chapter-5-your-first-test-from-zero)
- [Chapter 6: C# language foundations for test code](#chapter-6-c-language-foundations-for-test-code)
- [Chapter 7: Building the Store domain](#chapter-7-building-the-store-domain)
- [Chapter 8: Writing useful unit tests](#chapter-8-writing-useful-unit-tests)
- [Chapter 9: Facts, theories, assertions, and exceptions](#chapter-9-facts-theories-assertions-and-exceptions)
- [Chapter 10: Test naming and the Arrange-Act-Assert pattern](#chapter-10-test-naming-and-the-arrange-act-assert-pattern)
- [Chapter 11: Testing dependencies with fakes, stubs, and mocks](#chapter-11-testing-dependencies-with-fakes-stubs-and-mocks)
- [Chapter 12: Testing asynchronous C# code](#chapter-12-testing-asynchronous-c-code)
- [Chapter 13: Dependency injection, time, randomness, and seams](#chapter-13-dependency-injection-time-randomness-and-seams)
- [Chapter 14: Integration testing an ASP.NET Core API](#chapter-14-integration-testing-an-aspnet-core-api)
- [Chapter 15: Testing persistence and databases](#chapter-15-testing-persistence-and-databases)
- [Chapter 16: Fixtures, lifetime, cleanup, and parallel execution](#chapter-16-fixtures-lifetime-cleanup-and-parallel-execution)
- [Chapter 17: Selecting, categorizing, and debugging tests](#chapter-17-selecting-categorizing-and-debugging-tests)
- [Chapter 18: Code coverage](#chapter-18-code-coverage)
- [Chapter 19: Test-driven development](#chapter-19-test-driven-development)
- [Chapter 20: Testing the whole system](#chapter-20-testing-the-whole-system)
- [Chapter 21: Continuous integration with GitHub Actions](#chapter-21-continuous-integration-with-github-actions)
- [Chapter 22: Test design and maintainability](#chapter-22-test-design-and-maintainability)
- [Chapter 23: Advanced and mastery topics](#chapter-23-advanced-and-mastery-topics)
- [Chapter 24: The capstone challenge](#chapter-24-the-capstone-challenge)
- [Chapter 25: Troubleshooting](#chapter-25-troubleshooting)
- [Chapter 26: A practical mastery checklist](#chapter-26-a-practical-mastery-checklist)
- [Glossary](#glossary)
- [References](#references)

---

# Chapter 0: What you will learn

## 0.1 The final ability

By the end, you should be able to:

1. Explain why automated tests exist and what risks they reduce.
2. Create a .NET solution containing production projects and test projects.
3. Write readable unit tests for normal behavior, edge cases, failures, and exceptions.
4. Test synchronous and asynchronous C# code.
5. Replace external dependencies with controlled test doubles.
6. Test an ASP.NET Core application through HTTP without starting a real production server.
7. Choose an appropriate database-testing strategy.
8. Run one test, a class, a category, a project, or an entire solution.
9. Measure code coverage without confusing coverage with correctness.
10. Design tests that remain useful during refactoring.
11. Put tests into a CI pipeline.
12. Recognize test smells and improve a test suite deliberately.

## 0.2 The learning order

The order is intentional:

- You first learn **what a test is**.
- You then learn the small amount of C# needed to read test code.
- You write a test against a tiny method.
- You build a real domain component and test its behavior.
- You add collaborators and learn isolation.
- You move outward from unit tests to integration tests.
- You learn execution, coverage, CI, and advanced design.

Do not skip the first chapters because the later chapters assume their vocabulary.

## 0.3 The rule for every code example

Every code block in this guide should be read in four passes:

1. **Shape:** What files, classes, methods, and namespaces exist?
2. **Syntax:** What does each keyword, symbol, attribute, and punctuation mark do?
3. **Behavior:** What input enters the code and what output or side effect leaves it?
4. **Test meaning:** Which behavior is protected by the test, and what failure would the test reveal?

---

# Chapter 1: What software testing means

## 1.1 A definition

A **software test** is an executable check that compares an observed result with an expected result.

In plain words:

- We prepare a situation.
- We run the code.
- We observe what happened.
- We decide whether what happened is acceptable.

A test is not merely a `Console.WriteLine`. A printed value still requires a human to inspect it. An automated test contains a rule that can report pass or fail by itself.

.NET documentation describes automated tests as a way to check that application code behaves as its authors intend. It distinguishes unit tests, integration tests, and load tests.[1]

## 1.2 What problem testing solves

Software changes create risk. A change can:

- break an existing feature;
- work for normal input but fail for an empty or extreme input;
- use a dependency incorrectly;
- return the wrong HTTP status code;
- save incorrect data;
- behave differently when operations run asynchronously;
- fail only in CI or on another operating system.

A test suite gives the team a repeatable way to discover some of these problems early.

A test does **not** prove that the application has no bugs. It proves only that the tested behaviors passed under the conditions represented by the tests.

## 1.3 Why use tests in .NET applications?

Well-designed tests provide:

- **Fast feedback:** small unit tests can run in milliseconds.
- **Regression protection:** old tests are rerun after new changes.
- **Executable documentation:** a well-named test describes a behavior.
- **Refactoring confidence:** the internal design can change while behavior remains protected.
- **Design feedback:** code that is difficult to test is often too tightly coupled.
- **Team communication:** the test expresses what the team currently considers correct.
- **Automation:** `dotnet test` can run in a local terminal, IDE, or CI pipeline.

Microsoft's best-practices guidance lists speed, isolation, repeatability, self-checking behavior, and timeliness as important characteristics of good unit tests.[4]

## 1.4 What testing does not give you automatically

Testing is not a substitute for:

- secure design;
- code review;
- logging and monitoring;
- performance measurement;
- threat modeling;
- manual usability testing;
- production observability;
- requirements analysis.

Testing is one engineering activity inside a larger quality process.

---

# Chapter 2: A short history

## 2.1 Before modern test frameworks

Early software teams relied heavily on manual checks and ad hoc programs. A developer might run an application, enter a few values, inspect output, and repeat the process after every change. This approach becomes slow and inconsistent as the application grows.

The central idea of unit testing developed with modular software design: if a program is divided into small units with clear responsibilities, each unit can be checked independently.

## 2.2 The xUnit family

Many test frameworks use a common family design called **xUnit**. The name is a pattern: `x` stands for a specific language or ecosystem, such as JUnit for Java or NUnit and xUnit.net for .NET.

Microsoft's .NET testing overview notes that NUnit was initially ported from JUnit and that xUnit.net was created by the original inventor of NUnit v2.[1] This history explains why frameworks often have similar concepts even when their attributes and assertion APIs differ.

## 2.3 .NET test tooling over time

The modern .NET testing stack has several layers:

1. A **test framework** defines the code you write, such as xUnit, NUnit, MSTest, or TUnit.
2. A **test adapter or integration layer** allows tooling to understand that framework.
3. A **test platform or runner** discovers and executes tests.
4. The **.NET CLI or IDE** provides the command or user interface.

Historically, VSTest was the long-established .NET test platform. The .NET 10 SDK also supports Microsoft.Testing.Platform (MTP), which is an alternative execution model. Microsoft's current guidance recommends choosing one platform consistently for a repository rather than mixing VSTest-based and MTP-based projects in one run configuration.[2]

For a beginner, the important point is not to memorize every platform detail. Start by writing framework-level tests and run them with the command supported by the template you created. Study the platform distinction when you configure CI, native AOT, specialized extensions, or a large repository.

## 2.4 Why the history matters

History explains why you may encounter several valid styles:

- `[Fact]` in xUnit;
- `[TestMethod]` in MSTest;
- `[Test]` in NUnit;
- `dotnet test` using a test adapter;
- an IDE test window using the same underlying test assembly.

The transferable skill is not memorizing one framework's decoration. The transferable skill is expressing behavior clearly and executing it reliably.

---

# Chapter 3: The words you must understand

## 3.1 Application

An **application** is software built to perform a purpose. A web API, desktop program, command-line tool, worker, and mobile backend are all applications.

## 3.2 .NET

**.NET** is Microsoft's open-source, cross-platform development platform. It includes runtimes, libraries, compilers, SDK tools, and application frameworks such as ASP.NET Core.

## 3.3 C#

**C#** is the programming language used in this guide. C# source code is compiled into an intermediate representation and executed by a .NET runtime.

## 3.4 Project

A **project** is usually a `.csproj` file that describes source files, target frameworks, package dependencies, and build settings.

## 3.5 Solution

A **solution** is a container for related projects. A typical application solution separates production code from test code:

```text
Store.sln
├── src
│   ├── Store.Domain
│   ├── Store.Application
│   └── Store.Api
└── tests
    ├── Store.Domain.UnitTests
    ├── Store.Application.UnitTests
    └── Store.Api.IntegrationTests
```

The solution is an organizational and build boundary. It is not the same thing as the application running in production.

## 3.6 System Under Test

The **System Under Test**, often abbreviated **SUT**, is the object, method, service, API, or application whose behavior a test examines.

If a test calls `ShoppingCart.Total()`, the `ShoppingCart` object is the SUT.

## 3.7 Unit test

A **unit test** checks a small unit of behavior, normally without real databases, file systems, networks, clocks, or other infrastructure. Microsoft defines a unit test as exercising an individual software component or method and recommends keeping infrastructure concerns outside the unit test.[1]

## 3.8 Integration test

An **integration test** checks whether two or more components work together. It may involve a database, HTTP pipeline, serialization, dependency injection, or a test web host.[1]

## 3.9 End-to-end test

An **end-to-end test** exercises a broad path through a system, often close to the way a real user or client uses it. It usually costs more to run and maintain than a unit test.

## 3.10 Load or performance test

A **load test** measures how a system behaves under a specified workload, such as many concurrent requests. It asks a different question from a unit test: not "is this result correct?" but "does the system remain responsive under this demand?"[1]

## 3.11 Test case

A **test case** is one specific scenario, such as:

> Given an empty cart, when the subtotal is calculated, then the subtotal is zero.

A parameterized test may create several test cases from one test method.

## 3.12 Test suite

A **test suite** is a collection of tests. It may mean all tests in a project, a solution, or a selected group.

## 3.13 Test runner

A **test runner** discovers tests, executes them, collects results, and reports failures. The runner is not necessarily the same thing as the test framework.

## 3.14 Test framework

A **test framework** provides attributes, assertions, lifecycle features, and conventions used by test authors. xUnit, NUnit, MSTest, and TUnit are examples.[1]

## 3.15 Assertion

An **assertion** is a statement that must be true for a test to pass.

```csharp
Assert.Equal(10, actual);
```

This means: compare `actual` with `10`; fail the test if they are not equal.

## 3.16 Regression

A **regression** is a defect introduced by a change that breaks behavior that previously worked.

## 3.17 Deterministic test

A **deterministic** test produces the same result when the code and test inputs have not changed. Tests that depend on the current time, random numbers, network availability, or shared mutable state are common sources of non-determinism.

## 3.18 Test double

A **test double** is a replacement for a real collaborator used to control a test. Common names are:

- **Stub:** supplies controlled data.
- **Fake:** a lightweight working implementation, such as an in-memory repository.
- **Mock:** records or verifies interactions, depending on the terminology used by the framework or literature.
- **Spy:** records calls so the test can inspect them.

The terminology is not perfectly consistent across books and tools. Microsoft explicitly warns that fake, stub, and mock are used differently in different testing literature.[4]

---

# Chapter 4: Installing and checking the tools

## 4.1 Install the .NET SDK

Install the .NET SDK from the official [.NET download page](https://dotnet.microsoft.com/download). Install the **SDK**, not only a runtime. The SDK contains the compiler, project templates, restore tools, and CLI commands.

Open a new PowerShell window and run:

```powershell
dotnet --version
dotnet --info
dotnet --list-sdks
dotnet --list-runtimes
```

### Meaning of each command

- `dotnet` selects the .NET command-line program.
- `--version` prints the selected SDK version.
- `--info` prints operating-system, runtime, SDK, and environment information.
- `--list-sdks` lists installed SDKs.
- `--list-runtimes` lists installed runtimes.

If `dotnet` is not recognized, install the SDK and open a new terminal.

## 4.2 Choose a target framework

A target framework tells the compiler and runtime which .NET API surface the project expects.

For this guide:

- Use `net8.0` for the broadest current compatibility with the examples.
- Use `net10.0` if your installed SDK and all referenced packages support it.
- Do not mix target frameworks accidentally across projects.

Check the SDK's available templates:

```powershell
dotnet new list
```

## 4.3 Choose a test framework

For this guide we use **xUnit** because its syntax is concise and its concepts are representative of modern .NET testing.

The common choices are:

- **MSTest:** Microsoft's framework.
- **NUnit:** a mature .NET framework in the xUnit family.
- **xUnit.net:** a community-focused framework with `Fact`, `Theory`, fixtures, assertions, and parallel execution.
- **TUnit:** a newer framework built for Microsoft.Testing.Platform.

The framework changes the attribute names and assertion methods. The testing principles remain the same.

## 4.4 Create a clean learning folder

```powershell
New-Item -ItemType Directory -Name TestingMastery
Set-Location TestingMastery
```

PowerShell meaning:

- `New-Item` creates an item.
- `-ItemType Directory` says the item is a folder.
- `-Name TestingMastery` chooses the folder name.
- `Set-Location` changes the current working directory.

---

# Chapter 5: Your first test from zero

## 5.1 Create a class library

A class library is a project that produces reusable code instead of a directly launched application.

```powershell
dotnet new classlib -n Calculator -f net8.0
dotnet new xunit -n Calculator.Tests -f net8.0
dotnet add Calculator.Tests reference Calculator
```

### Read the commands word by word

`dotnet new classlib -n Calculator -f net8.0`:

- `dotnet` invokes the SDK.
- `new` creates a project from a template.
- `classlib` selects the class-library template.
- `-n Calculator` assigns the project name.
- `-f net8.0` sets the target framework.

`dotnet new xunit -n Calculator.Tests -f net8.0` creates a test project.

`dotnet add Calculator.Tests reference Calculator` adds a **project reference**. It gives the test project permission to compile against types from the `Calculator` project.

If your SDK provides the xUnit v3 template and you want the native v3 setup, the xUnit documentation shows this alternative:

```powershell
dotnet new install xunit.v3.templates
dotnet new xunit3 -n Calculator.Tests -f net8.0
```

The v3 guide states that xUnit v3 targets .NET 8 or later and that generated templates may vary by installed SDK and template version.[3] For beginner continuity, the remainder of this guide uses the broadly compatible `[Fact]`, `[Theory]`, and `Assert` syntax.

## 5.2 Write the production code

Open `Calculator/Class1.cs` and replace it with:

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

### Line-by-line explanation

`namespace Calculator;`

- `namespace` groups related types under a name.
- `Calculator` is the namespace name.
- `;` ends the file-scoped namespace declaration.
- Every type below belongs to `Calculator`.

`public sealed class BasicCalculator`

- `public` means other projects may access the type.
- `sealed` means another class cannot inherit from this class.
- `class` declares a reference type.
- `BasicCalculator` is the type name.
- `{` starts the class body.

`public int Add(int left, int right)`

- `public` makes the method callable by the test project.
- `int` is the return type.
- `Add` is the method name.
- `(int left, int right)` declares two integer parameters.
- `left` and `right` receive input values.

`return left + right;`

- `return` sends a value back to the caller.
- `left + right` performs integer addition.
- `;` ends the statement.

The closing braces end the method and the class.

## 5.3 Write the first test

Open `Calculator.Tests/UnitTest1.cs` and replace it with:

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

### Line-by-line explanation

`using Calculator;` imports the namespace so the test can write `BasicCalculator` without its full name.

`namespace Calculator.Tests;` puts the test type in a separate namespace. A namespace is not security; it is a naming and organization mechanism.

`public sealed class BasicCalculatorTests` declares a test class.

`[Fact]` is an attribute. Attributes are metadata attached to code. xUnit uses `[Fact]` to discover a test that has one fixed scenario and no input parameters.

`public void Add_TwoNumbers_ReturnsTheirSum()` is the test method:

- `public` allows the runner to invoke it.
- `void` means it returns no ordinary value. The test communicates pass or fail through assertions or exceptions.
- The method name tells us the method, scenario, and expected result.

`var calculator = new BasicCalculator();`

- `var` asks the compiler to infer the static type from the right side.
- `new BasicCalculator()` constructs an object.
- `calculator` stores a reference to that object.

`var actual = calculator.Add(2, 3);` calls the SUT and stores the observed result.

`Assert.Equal(5, actual);` compares expected value `5` with actual value `actual`. If they differ, xUnit fails the test.

The comments divide the test into **Arrange**, **Act**, and **Assert**. We study that pattern in Chapter 10.

## 5.4 Run the test

From the `TestingMastery` folder:

```powershell
dotnet test
```

The command finds test projects below the current directory, restores packages if needed, builds the projects, discovers tests, executes them, and reports the result. The exact execution mode depends on the SDK and project configuration. `dotnet test` has both VSTest and Microsoft.Testing.Platform modes in current .NET documentation.[5]

Run only the test project:

```powershell
dotnet test .\Calculator.Tests\Calculator.Tests.csproj
```

A successful run should report one passed test.

## 5.5 Make the test fail intentionally

Change:

```csharp
Assert.Equal(5, actual);
```

to:

```csharp
Assert.Equal(6, actual);
```

Run the test again. Read the failure:

- **Expected:** the value the test says should occur.
- **Actual:** the value the program produced.
- **Stack trace:** the path of method calls that led to the failure.
- **File and line:** where the assertion or failure occurred.

Change it back to `5`. A failed test is not a disaster. It is evidence that the test can detect a mismatch.

## 5.6 The complete first-test loop

Use this loop for every future change:

1. Write or change one small behavior.
2. Build or run the smallest relevant test.
3. Read the output.
4. Fix the production code or the test, but know which one is wrong.
5. Run the test again.
6. Run the broader test suite before committing.

---

# Chapter 6: C# language foundations for test code

You do not need to master every part of C# before testing. You do need to understand the following building blocks.

## 6.1 Variables

```csharp
int quantity = 3;
string sku = "COFFEE";
var price = 12.50m;
```

- `int` stores a whole number.
- `string` stores text.
- `var` still has a compile-time type; the compiler infers it.
- `m` marks a decimal literal.
- `=` assigns a value.
- `;` terminates a statement.

## 6.2 Methods

```csharp
public decimal ApplyDiscount(decimal price, decimal rate)
{
    return price * (1 - rate);
}
```

A method has access, return type, name, parameters, and a body. Tests call public behavior through methods or other public entry points.

## 6.3 Objects and constructors

```csharp
var cart = new ShoppingCart();
```

`new` allocates and initializes an object by calling a constructor. In a test, object construction is commonly part of Arrange.

## 6.4 Collections

```csharp
var names = new List<string>();
names.Add("Ana");
names.Add("Mina");
```

A collection holds multiple values. Test data is frequently stored in arrays, lists, or theory attributes.

## 6.5 Conditions

```csharp
if (quantity <= 0)
{
    throw new ArgumentOutOfRangeException(nameof(quantity));
}
```

- `if` chooses a path when a condition is true.
- `<=` means less than or equal to.
- `throw` stops normal execution and raises an exception.
- `nameof(quantity)` produces the string `"quantity"` safely during compilation.

Tests should cover both sides of meaningful conditions.

## 6.6 Exceptions

An exception is an object representing an abnormal condition. A public method can use exceptions to reject invalid input.

```csharp
var exception = Assert.Throws<ArgumentOutOfRangeException>(
    () => cart.Add("COFFEE", "Coffee", 10m, 0));

Assert.Equal("quantity", exception.ParamName);
```

The `() => ...` expression is a lambda. It packages the call so the assertion can execute it and inspect the exception.

## 6.7 Interfaces

```csharp
public interface IDiscountPolicy
{
    decimal GetDiscountRate(ShoppingCart cart);
}
```

An interface describes a contract without choosing one implementation. Interfaces help production code depend on capabilities and help tests provide controlled collaborators.

## 6.8 Tasks and asynchronous methods

```csharp
public async Task<bool> IsAvailableAsync(string sku)
{
    await Task.Delay(10);
    return true;
}
```

- `Task<bool>` represents a future result that will eventually contain a Boolean.
- `async` allows the method to use `await`.
- `await` waits without blocking the current thread in the usual asynchronous way.
- A test for this method should itself be `async Task` and should `await` the call.

## 6.9 Attributes

```csharp
[Fact]
public void SomethingWorks()
{
}
```

`[Fact]` is an attribute instance written with short syntax. It tells xUnit that the method is a test.

---

# Chapter 7: Building the Store domain

We now stop testing a toy calculator and create a small domain component.

## 7.1 Create the solution structure

From the `TestingMastery` folder:

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

The project reference graph is:

```text
Store.Domain.UnitTests  ──> Store.Domain
Store.Application.UnitTests ──> Store.Application ──> Store.Domain
```

Tests reference production projects. Production projects should not reference their tests.

## 7.2 Write `CartItem.cs`

Replace `src/Store.Domain/Class1.cs` with:

```csharp
namespace Store.Domain;

public sealed record CartItem(
    string Sku,
    string Name,
    decimal UnitPrice,
    int Quantity);
```

### Read it carefully

- `record` creates a type designed for value-like data semantics.
- `sealed` prevents inheritance.
- The primary constructor parameters become public properties.
- `string Sku` means the item has text identifying its stock-keeping unit.
- `decimal UnitPrice` represents a currency-like numeric value without binary floating-point behavior.
- `int Quantity` represents the number of units.
- The trailing `;` ends the type declaration.

A `CartItem` is a data object. It does not decide how a cart behaves.

## 7.3 Write `ShoppingCart.cs`

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

### Line-by-line explanation

`private readonly List<CartItem> _items = new();`

- `private` hides the field from callers.
- `readonly` prevents replacing the list reference after construction.
- `List<CartItem>` is a mutable list of `CartItem` objects.
- `_items` follows a common private-field naming convention.
- `new()` constructs the list.

`public IReadOnlyList<CartItem> Items => _items.AsReadOnly();`

- The caller can read the items but the public type does not expose `Add` or `Remove`.
- `=>` introduces an expression-bodied member.
- `AsReadOnly()` returns a read-only wrapper.

`public decimal Subtotal => ...` calculates the current subtotal.

`_items.Sum(item => item.UnitPrice * item.Quantity)` uses LINQ:

- `Sum` adds values.
- `item => ...` is a lambda applied to each item.
- `UnitPrice * Quantity` calculates a line total.

`public void Add(...)` is the public operation for adding an item.

`quantity = 1` gives the parameter a default value. Calling `Add` with no quantity means one item.

Each `if` validates one business rule. Each invalid input throws an exception that identifies the invalid parameter.

`_items.Add(new CartItem(...))` creates a value object and stores it.

`Total(decimal taxRate)` validates the tax rate, calculates subtotal plus tax, and rounds to two decimal places using `MidpointRounding.AwayFromZero`.

## 7.4 Build before testing

```powershell
dotnet build Store.sln
```

A build failure is not a test failure. A build failure means the compiler could not produce the required assemblies. Fix compilation first.

---

# Chapter 8: Writing useful unit tests

## 8.1 The first domain test file

Replace `tests/Store.Domain.UnitTests/UnitTest1.cs` with:

```csharp
using Store.Domain;

namespace Store.Domain.UnitTests;

public sealed class ShoppingCartTests
{
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
}
```

## 8.2 Why the tests are valuable

Each test has one visible behavior:

- a new cart starts empty;
- adding one item changes the subtotal;
- quantity affects the subtotal;
- tax is applied and the final amount is rounded.

The tests do not inspect `_items`, call private methods, or reproduce the implementation's LINQ expression. They verify the public behavior.

## 8.3 Add invalid-input tests

Append these tests to the same class:

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

These tests check both the exception type and the parameter name. The type protects the public contract. The parameter name improves the diagnostic contract.

## 8.4 Run all domain tests

```powershell
dotnet test tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj
```

Then run the complete solution:

```powershell
dotnet test Store.sln
```

If tests fail, first ask: is the production behavior wrong, or is the expected behavior in the test wrong? A test can contain a bug too.

---

# Chapter 9: Facts, theories, assertions, and exceptions

## 9.1 Facts

A **fact** is a test with a fixed scenario. It should pass every time while the behavior remains valid.

```csharp
[Fact]
public void EmptyCart_HasNoItems()
{
    var cart = new ShoppingCart();

    Assert.Empty(cart.Items);
}
```

Use a fact when the scenario does not need a data matrix.

## 9.2 Theories

A **theory** is a parameterized test. It describes one rule and runs that rule with multiple inputs.

Replace several similar tests with:

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

`[InlineData(0, 0)]` supplies one row. The first value enters `subtotal`; the second enters `expected`.

Do not use a theory merely to avoid writing readable tests. Use one when the examples share the same behavior and differ only by data.

## 9.3 Common assertions

```csharp
Assert.Equal(expected, actual);
Assert.NotEqual(unexpected, actual);
Assert.True(condition);
Assert.False(condition);
Assert.Null(value);
Assert.NotNull(value);
Assert.Empty(collection);
Assert.NotEmpty(collection);
Assert.Single(collection);
Assert.Contains(expectedItem, collection);
Assert.DoesNotContain(unwantedItem, collection);
Assert.IsType<SpecificType>(value);
```

Prefer the most specific assertion that explains the failure.

Poor:

```csharp
Assert.True(cart.Items.Count == 1);
```

Better:

```csharp
Assert.Single(cart.Items);
```

The better assertion communicates intent and gives a clearer failure message.

## 9.4 Assertion order

Most assertion methods take expected first and actual second:

```csharp
Assert.Equal(expected, actual);
```

This makes failure output read naturally: expected X, actual Y.

## 9.5 Testing exceptions

Always execute the action inside the assertion:

```csharp
var exception = Assert.Throws<ArgumentException>(
    () => cart.Add("", "Coffee", 10m));
```

Do not write:

```csharp
cart.Add("", "Coffee", 10m);
Assert.True(false);
```

The second version is less precise and does not state which exception is expected.

## 9.6 Testing async exceptions

For asynchronous code use `Assert.ThrowsAsync`:

```csharp
var exception = await Assert.ThrowsAsync<InvalidOperationException>(
    () => service.ProcessAsync());
```

The test method must be `async Task`.

## 9.7 Skipping a test

A skipped test is not a passing test. Use skipping only for a clear temporary reason, such as an unavailable external environment, and track the work needed to re-enable it.

---

# Chapter 10: Test naming and the Arrange-Act-Assert pattern

## 10.1 Test names are documentation

A useful name contains three pieces:

```text
MethodOrBehavior_Scenario_ExpectedResult
```

Examples:

- `Add_OneItem_SubtotalIsTheItemPrice`
- `Total_WithTenPercentTax_RoundsToTwoDecimalPlaces`
- `Add_NegativePrice_ThrowsArgumentOutOfRangeException`

Avoid names like `Test1`, `Works`, or `ShouldPass`. When a CI run reports a failure, the name should explain the broken behavior without opening the source file.

Microsoft recommends naming tests with the method, scenario, and expected behavior because tests serve as executable documentation.[4]

## 10.2 Arrange

Arrange creates and configures the objects required by the scenario.

```csharp
var cart = new ShoppingCart();
cart.Add("COFFEE", "Coffee", 10m);
```

## 10.3 Act

Act performs the one operation being examined.

```csharp
var actual = cart.Total(0.10m);
```

## 10.4 Assert

Assert compares the observed behavior with the expected behavior.

```csharp
Assert.Equal(11m, actual);
```

## 10.5 One Act per test

Aim for one meaningful Act operation per test. If a test calls three unrelated behaviors, a failure does not immediately tell you which behavior is broken.

Instead of:

```csharp
var subtotal = cart.Subtotal;
var total = cart.Total(0.10m);
var count = cart.Items.Count;

Assert.Equal(10m, subtotal);
Assert.Equal(11m, total);
Assert.Equal(1, count);
```

split the behaviors when they describe different contracts. Multiple assertions about one returned object can be reasonable, but do not turn one test into a miniature end-to-end workflow.

## 10.6 Keep Arrange visible

Do not hide all setup in a large global fixture. A reader should be able to see why this scenario exists and what input matters.

Good setup helpers are small and intention-revealing:

```csharp
private static ShoppingCart CreateCartWithCoffee(decimal price = 10m)
{
    var cart = new ShoppingCart();
    cart.Add("COFFEE", "Coffee", price);
    return cart;
}
```

Use the helper only when it makes several tests clearer. If a helper hides a critical difference, keep the setup in the test.

---

# Chapter 11: Testing dependencies with fakes, stubs, and mocks

## 11.1 Why dependencies matter

A class often collaborates with another class:

- a pricing policy;
- an email sender;
- a payment gateway;
- a clock;
- a repository;
- a message bus;
- a feature flag provider.

If a unit test uses the real network, database, or payment gateway, it becomes slow, fragile, and unsafe. The unit test should control the collaborator.

## 11.2 Add an application service

Create `src/Store.Application/IDiscountPolicy.cs`:

```csharp
using Store.Domain;

namespace Store.Application;

public interface IDiscountPolicy
{
    decimal GetDiscountRate(ShoppingCart cart);
}
```

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

### Important C# details

- The private field stores a collaborator.
- The constructor receives that collaborator. This is constructor injection.
- `??` means "if the left side is null, use the right side".
- `ArgumentNullException.ThrowIfNull` rejects a missing SUT argument.
- `is < 0 or > 1` is a pattern-based range check.
- `1 - discountRate` calculates the remaining proportion after discount.

## 11.3 Write a hand-made fake

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

This fake always returns a chosen rate. It is deterministic and needs no network or database.

## 11.4 Test the application service

Replace `tests/Store.Application.UnitTests/UnitTest1.cs` with:

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

The first test proves the service uses the collaborator's discount rate. The second proves invalid construction is rejected.

## 11.5 Stub versus mock

This guide uses a fake as a **stub** because it supplies a result. If a test also inspects whether a method was called, it is using the replacement as a **mock** or **spy**, depending on the terminology.

Prefer a hand-made fake when:

- the interface is small;
- the behavior is stable;
- the fake is readable;
- the fake could be reused in several tests.

A mocking library may be useful when a collaborator has many members or when you need to verify complex interaction rules. Do not mock every class automatically. Mocking implementation details can make tests brittle.

## 11.6 Optional Moq example

If you choose Moq, add it to the test project:

```powershell
dotnet add tests/Store.Application.UnitTests package Moq
```

Then a test can create a configured replacement:

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

Meaning:

- `Mock<IDiscountPolicy>` creates a configurable test double.
- `Setup` describes the return value for a call.
- `It.IsAny<ShoppingCart>()` matches any cart argument.
- `.Object` exposes the implementation of the test double as `IDiscountPolicy`.
- `Verify` checks an interaction.
- `Times.Once` says the interaction should happen exactly once.

Test the interaction only when the interaction itself is a business rule. If the result is what matters, assert the result and avoid unnecessary call-count assertions.

---

# Chapter 12: Testing asynchronous C# code

## 12.1 Add an asynchronous collaborator

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

The method awaits inventory for every item. It returns `false` as soon as one item is unavailable.

## 12.2 Write an async fake

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

`Task.FromResult` creates an already completed task. It lets a unit test control asynchronous behavior without sleeping or using a real service.

## 12.3 Write async tests

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

### Rules for async tests

1. Return `Task`, not `void`, from an asynchronous test.
2. Use `await`; do not call `.Result` or `.Wait()` in normal tests.
3. Do not add arbitrary `Task.Delay` calls to make a test "wait long enough".
4. Control timing with a fake, `TaskCompletionSource`, or a deterministic scheduler when needed.
5. Test cancellation when cancellation is part of the contract.
6. Test exception behavior with `Assert.ThrowsAsync`.

An `async void` test can hide exceptions from the runner and should generally be avoided.

---

# Chapter 13: Dependency injection, time, randomness, and seams

## 13.1 The problem with hidden time

This code is difficult to test:

```csharp
public bool IsWeekend()
{
    return DateTime.UtcNow.DayOfWeek is DayOfWeek.Saturday or DayOfWeek.Sunday;
}
```

A test cannot choose the current date. It may pass on Saturday and fail on Monday.

## 13.2 Introduce a clock interface

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

The interface is a **seam**: a controlled place where production code and test code can provide different implementations.

## 13.3 Use a fixed clock in a test

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

For production-quality tests, prefer a simpler date factory or explicit known dates once you know the calendar day. The important idea is that the test controls the clock.

## 13.4 Randomness

Do not make correctness depend on an uncontrolled random generator.

Instead of:

```csharp
var number = Random.Shared.Next();
```

inject a generator or pass a seed. A production random implementation can be used in production, while a deterministic fake can be used in tests.

## 13.5 Environment variables and file paths

Wrap environment access behind an interface or configuration object. Pass paths into methods instead of reading the current directory invisibly. Tests become clearer when inputs are explicit.

## 13.6 Dependency injection is not a testing trick only

Dependency injection means a type receives the collaborators it needs rather than constructing them internally. It improves production composition, test control, and separation of responsibilities.

Do not add an interface to every class without a reason. Add a seam where the code crosses a boundary you need to control or replace.

---

# Chapter 14: Integration testing an ASP.NET Core API

## 14.1 Why unit-test an API separately?

A controller, minimal API endpoint, or middleware pipeline involves several components:

- routing;
- model binding;
- dependency injection;
- serialization;
- authorization;
- filters or middleware;
- application services;
- HTTP status codes.

A unit test of one service cannot prove that the entire HTTP pipeline is wired correctly. An integration test can check the collaboration.

Microsoft's ASP.NET Core guidance describes integration tests as using a test project, a test web host, a client for requests, a test runner, and the normal Arrange-Act-Assert sequence. `WebApplicationFactory` and `TestServer` are provided by `Microsoft.AspNetCore.Mvc.Testing`.[6]

## 14.2 Create the API project

```powershell
dotnet new webapi -n Store.Api -o src/Store.Api -f net8.0 --no-https
dotnet add src/Store.Api reference src/Store.Application
dotnet add src/Store.Api reference src/Store.Domain
dotnet sln Store.sln add src/Store.Api/Store.Api.csproj
```

Delete the generated sample weather files if your template created them.

## 14.3 Create a minimal endpoint

Replace `src/Store.Api/Program.cs` with:

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

public partial class Program
{
}
```

### Explanation

- `WebApplication.CreateBuilder(args)` creates the application builder.
- `builder.Build()` creates the application pipeline.
- `MapGet` maps an HTTP GET request to a delegate.
- `/health` is a literal route.
- `/products/{sku}` contains a route parameter.
- `Results.Ok` creates a successful HTTP result.
- `Results.NotFound` creates a 404 result.
- `app.Run()` starts the application.
- `public partial class Program` exposes a type that the integration test can use as the entry point. The class is `partial` because the compiler generates another part for top-level statements.

## 14.4 Create the integration test project

```powershell
dotnet new xunit -n Store.Api.IntegrationTests -o tests/Store.Api.IntegrationTests -f net8.0
dotnet add tests/Store.Api.IntegrationTests reference src/Store.Api
dotnet add tests/Store.Api.IntegrationTests package Microsoft.AspNetCore.Mvc.Testing
dotnet sln Store.sln add tests/Store.Api.IntegrationTests/Store.Api.IntegrationTests.csproj
```

The test project must use the web SDK or otherwise reference the web-testing package as required by the template and installed SDK. If the build reports a missing web SDK or framework reference, open the `.csproj` and verify it contains the expected ASP.NET Core references for your target framework.

## 14.5 Write an HTTP integration test

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

### Line-by-line explanation

`using System.Net;` imports `HttpStatusCode`.

`using Microsoft.AspNetCore.Mvc.Testing;` imports `WebApplicationFactory`.

`IClassFixture<WebApplicationFactory<Program>>` tells xUnit to create and share a web-application fixture for this test class.

`private readonly HttpClient _client;` stores a client that sends requests to the in-memory test server.

The constructor receives the fixture and calls `CreateClient()`.

The test method is `async Task` because HTTP calls are asynchronous.

`using var response` disposes the response after the test.

`GetAsync("/health")` sends a GET request through the application pipeline.

The assertion checks the boundary behavior: HTTP 200 OK.

## 14.6 Test the not-found boundary

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

An HTTP integration test should usually assert the response status, headers, and meaningful body content. Do not assert every serialization detail unless it is part of the API contract.

## 14.7 Unit tests versus integration tests

Use a unit test for:

- a price calculation;
- a validation rule;
- a state transition;
- a mapping function;
- a service decision with fake collaborators.

Use an integration test for:

- routing;
- dependency injection registration;
- middleware behavior;
- serialization;
- HTTP status and headers;
- a real database interaction selected for integration coverage.

Keep the two categories in separate projects. Microsoft recommends separating unit and integration tests so infrastructure does not accidentally enter the fast unit-test suite and so each set can be run independently.[6]

---

# Chapter 15: Testing persistence and databases

## 15.1 The database testing question

Ask what you are trying to prove.

If you are testing business logic, use a unit test and a fake repository.

If you are testing a query, transaction, constraint, mapping, migration, or database-specific behavior, use an integration test against a database that behaves like the production database.

## 15.2 Three common strategies

### Strategy A: Fake repository

Fast and isolated. Good for application logic.

```csharp
public interface IProductRepository
{
    Task<Product?> FindAsync(string sku, CancellationToken cancellationToken);
}
```

A fake can store products in a dictionary. It does not prove SQL correctness.

### Strategy B: SQLite in-memory

Fast and closer to relational behavior than a simple in-memory object store. It is useful when your production database behavior is compatible with SQLite, but it still differs from SQL Server, PostgreSQL, or another production engine.

### Strategy C: Real database in a container

Most realistic for provider-specific behavior. It costs more time and infrastructure. Test isolation requires a database or schema strategy per test or test class.

## 15.3 Why an object-only in-memory provider can mislead

An object-based in-memory provider may not reproduce:

- relational constraints;
- SQL translation;
- transactions;
- indexes;
- provider-specific functions;
- concurrency behavior.

Use it only when its limitations are acceptable for the behavior under test.

## 15.4 The integration-test lifecycle

A database integration test commonly follows this sequence:

1. Create or connect to a test database.
2. Apply schema or migrations.
3. Seed only the data needed by the test.
4. Execute one business scenario.
5. Assert returned data and persisted data.
6. Roll back or clean up.
7. Dispose connections and contexts.

Do not share mutable data between tests unless you deliberately control isolation.

## 15.5 Example of a repository contract test

A contract test can run the same behavior checks against multiple repository implementations:

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

This is an advanced pattern. It is useful when multiple implementations must honor the same contract. Keep the contract small and focused.

---

# Chapter 16: Fixtures, lifetime, cleanup, and parallel execution

## 16.1 Why lifecycle matters

A test framework must decide:

- when to construct a test class;
- whether each test gets a fresh instance;
- when to create expensive shared resources;
- when to dispose those resources;
- whether tests run at the same time.

If you do not understand the lifecycle, tests can accidentally share state.

## 16.2 xUnit creates a fresh test-class instance

xUnit creates a new instance of the test class for every test. The constructor is therefore a place for reusable setup that should be recreated for each test. xUnit's shared-context documentation describes this behavior and the class, collection, and assembly fixture scopes.[9]

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

Do not assume a field mutated by one test will be available to another test.

## 16.3 Dispose per-test resources

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

Use cleanup even when a test fails. Prefer unique temporary paths and avoid deleting user files.

## 16.4 Class fixtures

Use a class fixture when setup is expensive and can safely be shared by all tests in one class:

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

Share only what is safe to share. A shared mutable database can make tests order-dependent.

## 16.5 Async fixtures

For asynchronous setup and cleanup, use the async lifecycle support provided by your xUnit version. In xUnit v3, async disposal can use `IAsyncDisposable`; xUnit's shared-context guidance describes the version differences.[9]

A safe pattern is:

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

Verify the exact lifecycle interface supported by the installed xUnit version.

## 16.6 Parallel execution

Parallel tests are faster, but only when tests do not conflict. xUnit runs tests in parallel by default at defined collection scopes. Its documentation explains collections, modes, maximum threads, and ways to disable parallelization.[10]

Tests must not depend on:

- a shared static list;
- a fixed file name;
- a single database row that another test mutates;
- test execution order;
- the current time;
- a global environment variable that another test changes.

The best solution is isolation, not turning all parallelism off. Disable parallelism only when a shared resource truly cannot be isolated, and document why.

## 16.7 Test-order dependence is a defect

If test B passes only after test A, the suite contains hidden shared state. Do not fix this by ordering tests. Fix the state boundary.

---

# Chapter 17: Selecting, categorizing, and debugging tests

## 17.1 Run one project

```powershell
dotnet test tests/Store.Domain.UnitTests/Store.Domain.UnitTests.csproj
```

## 17.2 Run one class or test name

```powershell
dotnet test --filter "FullyQualifiedName~ShoppingCartTests"
dotnet test --filter "FullyQualifiedName~Add_ZeroQuantity"
```

The .NET filter syntax supports properties, equality or contains operators, and Boolean `|` and `&` combinations. Supported properties differ between MSTest, xUnit, and NUnit.[8]

## 17.3 Add xUnit traits

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

Run the category:

```powershell
dotnet test --filter "Category=Unit"
```

Use categories that represent useful execution decisions, such as `Unit`, `Integration`, `Slow`, or `External`. Avoid creating dozens of labels nobody uses.

## 17.4 Build without running tests

```powershell
dotnet build Store.sln --configuration Release
```

## 17.5 Run without rebuilding

```powershell
dotnet test Store.sln --no-build --configuration Release
```

Use `--no-build` only when you know the binaries are current.

## 17.6 Increase diagnostic output

```powershell
dotnet test Store.sln --verbosity normal
```

If you are debugging a runner or restore issue:

```powershell
dotnet test Store.sln --verbosity detailed
```

## 17.7 Read a failure systematically

When a test fails, read in this order:

1. Test name.
2. Expected value.
3. Actual value.
4. Exception type and message.
5. Source line.
6. Inner exception.
7. Logs or captured output.
8. Recent production and test changes.

Do not immediately make the assertion less strict. First decide whether the behavior or the expectation is wrong.

---

# Chapter 18: Code coverage

## 18.1 What coverage measures

Code coverage measures how much code was executed by tests. Common measures include:

- line coverage;
- branch coverage;
- method coverage.

Coverage is evidence about which code ran. It is not evidence that the code is correct. Microsoft's best-practices guidance warns that an ambitious percentage goal can be counterproductive, because high coverage does not automatically mean high-quality tests.[4]

## 18.2 Collect coverage

The standard xUnit template commonly includes a Coverlet collector. Run:

```powershell
dotnet test Store.sln --collect:"XPlat Code Coverage"
```

The results are normally written below a `TestResults` directory as a coverage report such as `coverage.cobertura.xml`. Microsoft documents Coverlet and the built-in coverage collector as alternatives for .NET test runs.[7]

## 18.3 Generate an HTML report

Install the report generator once:

```powershell
dotnet tool install -g dotnet-reportgenerator-globaltool
```

Then generate a report. PowerShell example:

```powershell
reportgenerator `
  -reports:"**/TestResults/**/coverage.cobertura.xml" `
  -targetdir:"coverage-report" `
  -reporttypes:Html
```

The backtick continues a PowerShell command onto the next line.

## 18.4 Interpret coverage correctly

A test can execute a line without checking its result:

```csharp
cart.Total(0.10m);
Assert.True(true);
```

This may increase line coverage while providing almost no protection. Prefer a smaller number of tests with meaningful assertions over a large number of tests designed only to execute lines.

## 18.5 Use coverage to ask questions

When coverage is low, ask:

- Which business rule has no test?
- Which error path is untested?
- Is this code dead or intentionally excluded?
- Is a branch hard to reach because the design hides a dependency?
- Would an integration test be more appropriate?

Do not mechanically write a test for every generated property or trivial line.

---

# Chapter 19: Test-driven development

## 19.1 What TDD is

**Test-driven development** is a development cycle:

1. **Red:** write a test for behavior that does not yet pass.
2. **Green:** implement the simplest code that makes it pass.
3. **Refactor:** improve design while keeping tests passing.

The test is written before the production implementation for that behavior. TDD is a design and feedback practice, not a requirement to predict the entire system before coding.

## 19.2 TDD a new discount rule

Suppose the business says: orders of at least `100m` receive a `0.10m` discount rate.

Start with a failing test:

```csharp
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
```

Now write the smallest implementation:

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

Then refactor only when the tests protect the behavior.

## 19.3 TDD is not test-first dogma

Use TDD when the feedback loop helps clarify a rule or design. In exploratory work, you may prototype first and add tests immediately after learning. The important outcome is not the ceremony; it is fast, trustworthy feedback and clear behavior.

---

# Chapter 20: Testing the whole system

## 20.1 The testing pyramid

A practical test portfolio usually contains:

- many fast unit tests;
- a smaller number of integration tests;
- a small number of end-to-end tests;
- separate performance, security, and operational checks.

This is a heuristic, not a fixed percentage. The correct mix depends on the architecture and risks.

## 20.2 Unit tests

Best for deterministic business rules. They should be quick and isolated.

## 20.3 Integration tests

Best for boundaries between components:

- HTTP pipeline;
- database queries;
- serialization;
- dependency injection;
- file or message infrastructure selected for integration coverage.

## 20.4 Contract tests

A contract test verifies that a producer and consumer agree on a boundary. Examples include:

- an HTTP response shape;
- a message schema;
- a repository interface;
- an external API adapter.

A contract test should focus on the agreement, not every internal implementation detail.

## 20.5 End-to-end tests

An end-to-end scenario might be:

1. Create a customer through HTTP.
2. Add a product to a cart.
3. Submit an order.
4. Check the response.
5. Verify the persisted order.

Keep these tests few and valuable. They are more expensive to diagnose.

## 20.6 Performance tests

Performance tests require a workload, environment, and measurement plan. Do not use unit-test timing assertions such as "this method must finish in 10 milliseconds" unless the environment and timing mechanism are controlled. Use a performance tool and measure distributions such as median and tail latency.

## 20.7 Security tests

Test authorization decisions, input validation, secret handling, and unsafe serialization. A passing unit test does not prove an endpoint is secure. Include threat-model-driven tests and automated scanning where appropriate.

---

# Chapter 21: Continuous integration with GitHub Actions

## 21.1 Why CI matters

A test that only runs on one developer's machine is weak protection. CI runs the same build and test commands on every pull request or push.

## 21.2 Create the workflow

Create `.github/workflows/dotnet.yml`:

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

### Read the YAML

- `name` gives the workflow a human-readable name.
- `on` says when it runs.
- `push` runs after pushes.
- `pull_request` runs for pull requests.
- `permissions` grants only the read permission required here.
- `jobs` contains named jobs.
- `runs-on` selects the virtual machine.
- `actions/checkout` downloads the repository.
- `actions/setup-dotnet` installs the requested SDK.
- `restore` downloads NuGet dependencies.
- `build` compiles the solution.
- `test` executes the tests.
- `--no-restore` and `--no-build` avoid repeating earlier work.

If your project targets `net10.0`, use a matching installed SDK such as `10.0.x`. Keep the workflow target and project target compatible.

## 21.3 CI principles

- Run the same commands developers can run locally.
- Fail when tests fail.
- Keep unit tests fast.
- Run integration tests in a separate job if they need services.
- Publish test and coverage artifacts when diagnosis requires them.
- Do not hide flaky tests; quarantine them with an owner and a removal date.
- Pin or centrally manage tool versions when reproducibility matters.

---

# Chapter 22: Test design and maintainability

## 22.1 The five characteristics of a good unit test

Use this checklist:

- **Fast:** it finishes quickly.
- **Isolated:** it does not depend on unrelated external state.
- **Repeatable:** it produces the same result under the same conditions.
- **Self-checking:** the runner can decide pass or fail.
- **Timely:** it is economical to write relative to the behavior.

These characteristics are part of Microsoft's unit-testing guidance.[4]

## 22.2 Test behavior, not implementation

Fragile test:

```csharp
Assert.Equal("System.Linq.Enumerable+WhereListIterator...", value.GetType().FullName);
```

Useful test:

```csharp
Assert.Equal(3, service.GetAvailableCount());
```

The useful test protects what callers need. A refactor can change the implementation without invalidating the behavior.

## 22.3 Avoid testing private methods directly

A private method is an implementation detail. Test the public behavior that uses it. If a private method is too complex to test indirectly, consider whether it should become a separate public-facing class with a clear responsibility.

## 22.4 Avoid logic in tests

Tests should not contain complicated loops, branching, or duplicated business algorithms. Logic in the test can contain the same bug as the production code.

Use explicit theory rows:

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

## 22.5 Avoid magic values

Name unusual values:

```csharp
const decimal FreeShippingThreshold = 50m;
const string CoffeeSku = "COFFEE";
```

The name explains why the value matters.

## 22.6 Test smells

A **test smell** is a warning sign that a test or suite may be difficult to trust or maintain.

Common smells:

- **Mystery guest:** hidden files, databases, or environment state affect the result.
- **General fixture:** setup creates far more data than the test needs.
- **Test code duplication:** many tests repeat hard-to-maintain setup.
- **Assertion roulette:** many assertions have no clear failure meaning.
- **Conditional test logic:** the test contains complex branches.
- **Slow test:** a unit test waits on real infrastructure.
- **Brittle test:** harmless refactors break the test.
- **Test coupling:** one test depends on another's state.
- **Over-mocking:** the test verifies implementation calls instead of behavior.
- **False confidence:** coverage is high but assertions are weak.
- **Flaky test:** the result changes without a relevant code change.

## 22.7 Flaky tests

When a test is flaky:

1. Record when it fails and under what environment.
2. Look for time, randomness, concurrency, network, file paths, and shared state.
3. Reproduce it in isolation and in parallel.
4. Make the dependency explicit and controllable.
5. Fix the root cause.
6. Keep a regression test for the failure.

Do not repeatedly rerun until it passes and then call it fixed.

---

# Chapter 23: Advanced and mastery topics

## 23.1 Testing architecture boundaries

At mastery level, ask whether your code structure makes the desired tests natural.

A healthy boundary often looks like:

```text
API endpoint
    -> application service
        -> domain rules
        -> repository or gateway interface
            -> infrastructure implementation
```

Then:

- domain rules receive fast unit tests;
- application services use fakes for gateways;
- API tests verify the HTTP boundary;
- infrastructure tests verify provider-specific behavior;
- a small number of end-to-end tests verify the complete path.

## 23.2 Property-based testing

Example-based testing says:

> For input A, the result should be B.

Property-based testing says:

> For many generated inputs, a general property should always hold.

Examples of properties:

- sorting a list does not change its length;
- encoding then decoding returns the original value;
- a cart subtotal is never negative when all prices and quantities are valid;
- adding zero items does not change a total.

Libraries such as FsCheck can generate values. Start with ordinary examples before adding generated data. A generated failure must be reproducible from its seed or shrunk counterexample.

## 23.3 Mutation testing

Mutation testing changes production code in small ways, such as replacing `>` with `>=`, and checks whether tests fail. If a mutation survives, the suite may not assert the affected behavior strongly enough.

Mutation testing is more expensive than coverage but asks a better question:

> Would this test suite notice a plausible defect?

Use it on important domain components, not necessarily every generated file.

## 23.4 Approval and snapshot testing

Approval or snapshot tests compare a large serialized result with a stored expected file. They can be useful for stable documents, complex JSON, or UI output.

Risks:

- a developer may accept a changed snapshot without understanding it;
- formatting changes may create noisy diffs;
- the snapshot can hide which behavior matters.

Use snapshots when the full representation is the contract. Otherwise assert focused properties.

## 23.5 Testing messages and event-driven systems

For messaging systems, test:

- event shape;
- required headers or metadata;
- idempotency;
- retry behavior;
- duplicate delivery;
- ordering assumptions;
- poison-message handling;
- observability on failure.

A message consumer should usually be safe to run more than once when the delivery system can redeliver.

## 23.6 Testing concurrency

Concurrency tests are difficult because timing is not a reliable assertion. Prefer designs that expose synchronization points:

- inject a clock or scheduler;
- use `TaskCompletionSource` to pause and release operations deterministically;
- protect shared state with explicit locks or concurrent types;
- assert invariants rather than exact thread order.

Use stress tests to supplement deterministic tests, not replace them.

## 23.7 Native AOT and test execution platforms

Modern .NET applications may use trimming or Native AOT. These scenarios can expose reflection and test-discovery assumptions. Microsoft.Testing.Platform has scenarios where it is preferable to VSTest, while VSTest remains valuable for established integrations. Choose one platform consistently and follow the framework's current documentation.[2]

## 23.8 Test observability

A mature test suite produces useful diagnostics:

- test names explain the scenario;
- failures include expected and actual values;
- logs identify the boundary and correlation ID;
- integration tests preserve response bodies when failures occur;
- CI uploads results and coverage artifacts;
- timing identifies slow tests.

A test that fails without explaining why creates more work than it saves.

---

# Chapter 24: The capstone challenge

Build the Store application to this target:

## 24.1 Domain requirements

1. A cart can contain products with SKU, name, price, and quantity.
2. SKU and name cannot be blank.
3. Price cannot be negative.
4. Quantity must be positive.
5. Subtotal is the sum of price multiplied by quantity.
6. Tax is applied using a supplied rate.
7. Totals are rounded to two decimal places using an explicit policy.
8. A discount policy can change the subtotal.
9. Inventory availability is asynchronous.
10. An unavailable item prevents order placement.

## 24.2 API requirements

1. `GET /health` returns 200 and a JSON status.
2. `GET /products/{sku}` returns 200 for a known product.
3. Unknown products return 404.
4. Invalid request data returns a useful 400 response.
5. The API uses dependency injection rather than constructing infrastructure inside endpoints.

## 24.3 Test requirements

Write:

- at least one fact for each basic invariant;
- theories for equivalent input classes;
- exception tests for invalid arguments;
- unit tests with a fake discount policy;
- async tests with a fake inventory gateway;
- integration tests for routes and status codes;
- persistence integration tests for at least one real query;
- a coverage report;
- one CI workflow;
- a test README section explaining how to run the categories.

## 24.4 Mastery questions

Before calling the project complete, answer these questions in your own words:

1. What is the SUT in each test?
2. Which dependencies are real and which are test doubles?
3. Why is each test a unit or integration test?
4. What behavior would fail if the tax calculation changed?
5. What behavior would fail if routing changed?
6. Which tests can run in parallel safely?
7. How do you run only the fast tests?
8. What does your coverage report not prove?
9. Where would a contract test add value?
10. What production design change would make a difficult test simpler?

If you can answer these questions and implement the project without following the guide line by line, you are moving from learner to practitioner.

---

# Chapter 25: Troubleshooting

## 25.1 `dotnet` is not recognized

Install the SDK, reopen the terminal, and run `dotnet --info`.

## 25.2 No tests are discovered

Check:

- the test project references the test framework package;
- the test method is `public` when required by the installed framework version;
- the method has `[Fact]`, `[Theory]`, `[Test]`, or the correct framework attribute;
- the test project targets a supported framework;
- the test adapter or SDK package is present if the template requires it;
- the project builds successfully.

## 25.3 The test project cannot see production types

Add a project reference:

```powershell
dotnet add tests/Store.Domain.UnitTests reference src/Store.Domain
```

Also check the namespace and access modifier. A type marked `internal` is not normally visible from another assembly unless you explicitly use `InternalsVisibleTo`.

## 25.4 Restore fails

Try:

```powershell
dotnet restore Store.sln --verbosity detailed
dotnet nuget locals all --clear
```

Then restore again. Check network access, package source configuration, and target framework compatibility.

## 25.5 An async test passes without testing the result

Make the test `async Task` and await the operation. Do not use `async void`.

## 25.6 A test is flaky

Search for uncontrolled time, randomness, parallel state, shared files, environment variables, network calls, and database data. Replace hidden dependencies with explicit inputs or test doubles.

## 25.7 Integration tests cannot find `Program`

Ensure the web project contains:

```csharp
public partial class Program
{
}
```

Ensure the integration test references the web project and `Microsoft.AspNetCore.Mvc.Testing`.

## 25.8 The test works locally but fails in CI

Compare:

- SDK version;
- OS and path separators;
- culture and timezone;
- file permissions;
- environment variables;
- database availability;
- network assumptions;
- test parallelism;
- generated build artifacts.

Make the environment assumptions explicit or remove them from unit tests.

## 25.9 Coverage is unexpectedly low

Confirm that:

- the test actually references the production assembly;
- the collector is installed or included by the test template;
- the test ran successfully;
- the coverage command was run from the expected directory;
- generated and excluded files are not the only files being measured.

## 25.10 A test only passes in a particular order

It has shared-state coupling. Make setup local, use unique data, reset state, or isolate the fixture. Do not depend on test order.

---

# Chapter 26: A practical mastery checklist

## Beginner

- [ ] I can explain what an assertion is.
- [ ] I can create a class library and an xUnit test project.
- [ ] I can add a project reference.
- [ ] I can run `dotnet test`.
- [ ] I can read expected versus actual output.
- [ ] I understand `Fact`, `Theory`, and `InlineData`.
- [ ] I can write Arrange, Act, and Assert sections.

## Intermediate

- [ ] I name tests by behavior, scenario, and result.
- [ ] I test valid, invalid, boundary, and exception cases.
- [ ] I can test an async method without blocking.
- [ ] I can write a fake or stub for an interface.
- [ ] I can inject time, randomness, and external services.
- [ ] I know when a test is a unit test or integration test.
- [ ] I can test an ASP.NET Core endpoint with `WebApplicationFactory`.
- [ ] I can filter tests by name and trait.

## Advanced

- [ ] I can choose a database test strategy based on the behavior being proved.
- [ ] I understand fixture lifetime and cleanup.
- [ ] I can diagnose parallel-test failures.
- [ ] I can collect and interpret coverage.
- [ ] I can configure CI and preserve test results.
- [ ] I recognize test smells.
- [ ] I can refactor production code to create a useful seam.
- [ ] I can test contracts at system boundaries.

## Mastery

- [ ] I design application boundaries that support fast, focused tests.
- [ ] I use unit, integration, end-to-end, performance, and security tests for different risks.
- [ ] I can evaluate whether a mock verifies behavior or merely implementation details.
- [ ] I can reduce flakiness without disabling all parallelism.
- [ ] I can use mutation or property-based testing where it adds value.
- [ ] I can explain what my test suite proves and what it does not prove.
- [ ] I can improve a test suite while preserving trust, speed, and diagnostic quality.

---

# Glossary

- **AAA:** Arrange, Act, Assert.
- **Adapter:** A layer that connects a test framework to a test platform.
- **Assertion:** An executable condition that must be true for a test to pass.
- **CI:** Continuous Integration; automated build and test execution after code changes.
- **Code coverage:** A measurement of code executed by tests.
- **Contract test:** A test of an agreement at a system boundary.
- **Deterministic:** Producing the same result for the same controlled inputs.
- **End-to-end test:** A test of a broad user or system workflow.
- **Fake:** A lightweight working replacement used in tests.
- **Fixture:** Shared test setup or resource with a defined lifetime.
- **Framework:** The test-authoring API and conventions, such as xUnit or NUnit.
- **Integration test:** A test of multiple components working together.
- **Mock:** A test double used to verify interactions in terminology common to many .NET tools.
- **Mutation testing:** Deliberately changing code to measure whether tests detect plausible defects.
- **Regression:** A previously working behavior broken by a change.
- **Runner:** The program that discovers and executes tests.
- **Seam:** A controlled point where a dependency can be replaced.
- **SUT:** System Under Test.
- **Stub:** A replacement that supplies controlled answers.
- **Test double:** A general name for a fake, stub, mock, spy, or related replacement.
- **Test platform:** The execution engine and integration layer beneath test frameworks.
- **Theory:** A parameterized test that runs against multiple input rows.
- **Unit test:** A focused test of a small behavior with infrastructure isolated.

---

# References

[1]: https://learn.microsoft.com/en-us/dotnet/core/testing/ "Testing in .NET"
[2]: https://learn.microsoft.com/en-us/dotnet/core/testing/test-platforms-overview "Microsoft.Testing.Platform versus VSTest"
[3]: https://xunit.net/docs/getting-started/netcore/cmdline "Getting Started with xUnit.net v3"
[4]: https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices "Unit testing best practices for .NET"
[5]: https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test "Testing with dotnet test"
[6]: https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-10.0 "Integration tests in ASP.NET Core"
[7]: https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-code-coverage "Use code coverage for unit testing"
[8]: https://learn.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests "Run selected unit tests"
[9]: https://xunit.net/docs/shared-context "Sharing Context between Tests"
[10]: https://xunit.net/docs/running-tests-in-parallel "Running Tests in Parallel"
