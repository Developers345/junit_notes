## 3. Each Test Should Be Isolated and Executed Independently

Each test case must be **isolated** and should be executed **independently** of other test cases.  
A failure in one test should **not impact** the execution of other tests.

---

## Initial Testcase Implementation

```java
package pkg1;
class CalculatorTest {

  void testAddWithPositiveNumbers() {
    int a = 10;
    int b = 20;

    try {
      Calculator calculator = new Calculator();
      int actualSum = calculator.add(a, b);

      if (actualSum == 30) {
        System.out.println(".");
      } else {
        System.out.println("f");
      }
    } catch (Throwable t) {
      System.out.println("e");
    }
  }

  void testAddWithNegativeNumbers() {
    int a = -10;
    int b = -20;

    try {
      Calculator calculator = new Calculator();
      int actualSum = calculator.add(a, b);

      if (actualSum == -30) {
        System.out.println(".");
      } else {
        System.out.println("f");
      }
    } catch (Throwable t) {
      System.out.println("e");
    }
  }

  void testAddWithZeros() {
    int a = 0;
    int b = 0;

    try {
      Calculator calculator = new Calculator();
      int actualSum = calculator.add(a, b);

      if (actualSum == 0) {
        System.out.println(".");
      } else {
        System.out.println("f");
      }
    } catch (Throwable t) {
      System.out.println("e");
    }
  }

  public static void main(String[] args) {
    CalculatorTest calculatorTest = new CalculatorTest();
    calculatorTest.testAddWithPositiveNumbers();
    calculatorTest.testAddWithNegativeNumbers();
    calculatorTest.testAddWithZeros();
  }
}
````

---

## Problems With the Above Approach

1. Success and failure are reported using `System.out.println()`, which does **not clearly differentiate** between failure and error.
2. Each test method contains repetitive `try-catch` blocks.
3. Test logic and error handling are tightly coupled.
4. No proper mechanism exists to consolidate test results.

👉 **Best Practice:**

* Failures should always be reported using **Exceptions**
* `try-catch` logic should be handled by a **TestRunner**, not inside test methods

---

## Improved Testcase Design

```java
package pkg1;
class CalculatorTest {

  Calculator calculator;

  // Test fixture initialization
  void setup() {
    calculator = new Calculator();
  }

  void testAddWithPositiveNumbers() {
    int actualSum = calculator.add(10, 20);

    if (actualSum == 30) {
      System.out.println(".");
    } else {
      throw new AssertionFailureException(
        "testAddWithPositiveNumbers expected: 30, actual: " + actualSum
      );
    }
  }

  void testAddWithNegativeNumbers() {
    int actualSum = calculator.add(-10, -20);

    if (actualSum == -30) {
      System.out.println(".");
    } else {
      throw new AssertionFailureException(
        "testAddWithNegativeNumbers expected: -30, actual: " + actualSum
      );
    }
  }

  void testAddWithZeros() {
    int actualSum = calculator.add(0, 0);

    if (actualSum == 0) {
      System.out.println(".");
    } else {
      throw new AssertionFailureException(
        "testAddWithZeros expected: 0, actual: " + actualSum
      );
    }
  }

  // Test fixture cleanup
  void cleanup() {
    calculator = null;
  }
}
```

---

## Issue With Per-Testcase `main()` Method

* Each testcase class has its own `main()` method
* To test the entire application, we must run **each testcase class individually**
* This approach is tedious and error-prone

👉 **Solution:** Introduce a centralized **TestRunner**

---

## TestResult Class

```java
class TestResult {
  int totalTests;
  int totalSuccess;
  int totalFailures;
  int totalErrors;

  public TestResult() {
    totalTests = 0;
    totalSuccess = 0;
    totalFailures = 0;
    totalErrors = 0;
  }

  void success() {
    totalTests++;
    totalSuccess++;
  }

  void failed() {
    totalTests++;
    totalFailures++;
  }

  void error() {
    totalTests++;
    totalErrors++;
  }
}
```

---

## Manual TestRunner Implementation

```java
package test;
public class TestRunner {
  public static void main(String[] args) {

    TestResult testResult = new TestResult();
    CalculatorTest calculatorTest = new CalculatorTest();

    try {
      calculatorTest.setup();
      calculatorTest.testAddWithPositiveNumbers();
      calculatorTest.cleanup();
      testResult.success();
    } catch (AssertionFailureException e) {
      testResult.failed();
    } catch (Throwable t) {
      testResult.error();
    }

    try {
      calculatorTest.setup();
      calculatorTest.testAddWithNegativeNumbers();
      calculatorTest.cleanup();
      testResult.success();
    } catch (AssertionFailureException e) {
      testResult.failed();
    } catch (Throwable t) {
      testResult.error();
    }

    // Output TestResult summary
  }
}
```

---

## Reflection-Based Dynamic TestRunner

Instead of manually invoking test methods, we can enforce **standard conventions** and execute tests dynamically using the **Reflection API**.

### Enforced Conventions

1. Test fixture initialization method → `setUp()`
2. Test fixture cleanup method → `cleanUp()`
3. Each test method must start with → `testXXX()`

---

## Dynamic TestRunner Using Reflection

```java
class TestRunner {
  public static void main(String[] args) throws Exception {

    TestResult testResult = new TestResult();

    Stream.of(args).forEach(testCaseClass -> {
      try {
        Class<?> clazz = Class.forName(testCaseClass);
        Object testCaseObject = clazz.getDeclaredConstructor().newInstance();

        Method setUpMethod = clazz.getDeclaredMethod("setUp");
        Method cleanUpMethod = clazz.getDeclaredMethod("cleanUp");

        Method[] methods = clazz.getDeclaredMethods();

        for (Method method : methods) {
          if (method.getName().startsWith("test")) {
            try {
              setUpMethod.invoke(testCaseObject);
              method.invoke(testCaseObject);
              cleanUpMethod.invoke(testCaseObject);
              testResult.success();
            } catch (InvocationTargetException e) {
              if (e.getCause() instanceof AssertionFailureException) {
                testResult.failed();
              } else {
                testResult.error();
              }
            }
          }
        }
      } catch (Throwable t) {
        testResult.error();
      }
    });

    // Output consolidated TestResult
  }
}
```

---

## Key Requirements Identified for Unit Test Automation

From the above implementation, we can clearly see the essential requirements:

1. Each test must produce a **final outcome**: PASSED / FAILED
2. Support for **test fixture initialization and cleanup**
3. Each test must be **isolated** and independent
4. Failures must be reported using **exceptions**
5. Proper **test reporting**:

   * Total tests
   * Passed tests
   * Failed tests
   * Errors
6. Centralized and **dynamic test execution** via a TestRunner

---

## Why Unit Testing Frameworks Exist

Since all projects require the same boilerplate components:

* `TestRunner`
* `TestResult`
* Assertion failure handling
* Reporting logic

👉 **Unit testing frameworks like JUnit and TestNG were introduced** to:

* Eliminate boilerplate code
* Provide standard conventions
* Offer powerful assertions
* Generate rich test reports
* Enable seamless automation and integration with build tools


