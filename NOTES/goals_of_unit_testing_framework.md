# Unit Testing and Test Automation

Unit testing is a **repetitive activity** that should be conducted every time there is a change in a component or just before delivering the application to the QA engineer. Conducting unit testing **manually** is a tedious job and delays the delivery of the application. To overcome this problem, **unit test automation** has been introduced.

---

## Unit Testing Frameworks

There are many unit testing frameworks available to implement test automation, such as:

1. JUnit  
2. TestNG  
3. Selenium  
4. WebDriver  
5. Cactus  
6. Cucumber  
7. etc.

Out of the above, the **most popular unit test automation library/framework is JUnit**.

---

## Unit Testing / Test Automation

Unit testing helps us to answer the following:

1. Is the code ready for testing or not?
2. In **Agile development**, the application is developed in **sprints**, usually spanning **1–3 weeks**.  
   Within a sprint, the code should be:
   - Developed  
   - Tested  
   - Delivered  

   The only way to accomplish **rapid application delivery** is by having **strong test automation** in place.

   One of the key ways to achieve this is by adopting:
   1. **Unit test automation** (component-level certification)
   2. **Integration testing** (entire component-level certification including external services)

---

## Why Do We Need a Framework for Unit Test Automation?

While implementing unit test automation **without frameworks**, we end up writing a lot of **boilerplate logic**.

To understand this, let us see what kind of code we write when we **do not use a unit testing framework**.

---

## Goals of Unit Test Automation Implementation

### Goal #1  
Each test conducted should produce an **output marking the test as PASSED or FAILED**, rather than emitting the actual program execution output.

---

## Sample Application Code

```java
class Calculator {
  int add(int a, int b) {
    return a + b;
  }
}

class UnitsCalculator {
  double convertMetersToCentimeters(double meters) {
    return meters * 100;
  }
}
````

---

## Manual Testing Approach (Without Framework)

To test the above code, we need to:

* Prepare a predefined set of inputs (**test fixtures**)
* Manually calculate the expected output
* Execute the program with test inputs
* Verify whether the actual output matches the expected output

Example test code:

```java
class AppTest {
  public static void main(String[] args) {
    int a = 10;
    int b = 20;
    double meters = 5.5;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);
    System.out.println("sum : " + actualSum);

    UnitsCalculator unitsCalculator = new UnitsCalculator();
    int actualCentimeters = unitsCalculator.convertMetersToCentimeters(meters);
    System.out.println("centimeters : " + actualCentimeters);

    // Imagine 50 such classes printing output
  }
}
```

### Problems with This Approach

* Test logic is mixed with business logic
* Output only shows **execution results**, not **test status**
* Developers must manually verify output
* Consumes significant time
* Tests provide little real value

---

## Improved Testing Logic (Manual Assertion)

Instead of printing execution output, the test code should:

* Compare **actual output** with **expected output**
* Mark the test as **PASSED or FAILED**

Example:

```java
class AppTest {
  public static void main(String[] args) {
    int a = 10;
    int b = 20;
    double meters = 5.5;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);

    if (actualSum == 30) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }

    UnitsCalculator unitsCalculator = new UnitsCalculator();
    int actualCentimeters = unitsCalculator.convertMetersToCentimeters(meters);

    if (actualCentimeters == 550.0) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }
}
```


## #2. Break-down Test Logic Per Class (One Testcase Class Per Class)

**In short:**  
👉 Maintain **one testcase class for each original class**.

---

## Sample Application Classes

```java
class Calculator {
  int add(int a, int b) {
    return a + b;
  }
}

class UnitsCalculator {
  double convertMetersToCentimeters(double meters) {
    return meters * 100;
  }
}

class Square {
  double area(double side) {
    return side * side;
  }
}
````

---

## Problem With a Single Test Class

```java
class AppTest {
  public static void main(String[] args) {
    int a = 10;
    int b = 20;
    double meters = 5.5;
    double side = 4.3;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);
    if (actualSum == 30) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }

    UnitsCalculator unitsCalculator = new UnitsCalculator();
    int actualCentimeters = unitsCalculator.convertMetersToCentimeters(meters);
    if (actualCentimeters == 55) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }

    Square square = new Square();
    int actualArea = square.area(side);
    if (actualArea == 18.49) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }
}
```

### Issues

* Test logic for **multiple classes** is mixed together
* Difficult to read, understand, and maintain
* Becomes complex as the application grows

---

## Recommended Approach

👉 **Break down test logic into multiple testcase classes**

* One testcase class per each class under test
* Each testcase class contains logic to test all methods of the original class

---

## Testcase Class Naming & Structure Rules

1. **Testcase class name**

   * `OriginalClassName + Test`
   * Example: `CalculatorTest`

2. **Package structure**

   * Testcase class should be in the **same package** as the original class

3. **Source separation**

   * Application source code and test code should be separated
   * Test code should **never be packaged or shipped** with the application

---

## Recommended Project Structure

```
basicjava
│
├─ src
│  └─ pkg1
│     └─ Calculator.java
│
├─ testSrc
│  └─ pkg1
│     └─ CalculatorTest.java
│
├─ bin
│  └─ pkg1
│     └─ Calculator.class
│
├─ testBin
│  └─ pkg1
│     └─ CalculatorTest.class
```

---

## Packaging Command

```bash
jar -cvf basicjava.jar -C bin/ .
```

### Notes

* Compile `src/` → output to `bin/`
* Compile `testSrc/` with classpath pointing to `bin/` → output to `testBin/`
* While executing tests, set classpath to:

  * `bin/`
  * `testBin/`
  * third-party libraries
* While packaging (`jar` / `war`), include **only `bin/` classes**

---

## Application Classes With Packages

```java
package pkg1;
class Calculator {
  int add(int a, int b) {
    return a + b;
  }
}
```

```java
package pkg2;
class UnitsCalculator {
  double convertMetersToCentimeters(double meters) {
    return meters * 100;
  }
}
```

```java
package pkg3;
class Square {
  double area(double side) {
    return side * side;
  }
}
```

---

## Separate Testcase Classes

### CalculatorTest

```java
package pkg1;
class CalculatorTest {
  public static void main(String[] args) {
    int a = 10;
    int b = 20;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);

    if (actualSum == 30) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }
}
```

### SquareTest

```java
package pkg3;
class SquareTest {
  public static void main(String[] args) {
    double side = 4.3;

    Square square = new Square();
    int actualArea = square.area(side);

    if (actualArea == 18.49) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }
}
```

---

## Still a Problem…

Even though separating testcase classes improves **readability and maintainability**, there is still a challenge:

* All test logic is written inside the `main()` method
* Difficult to manage multiple test scenarios for the same method

---

## Better Approach: One Testcase Method Per Test

Instead of testing all methods inside `main()`,
👉 **Create separate testcase methods for each test scenario**

---

## Rules for Writing Testcase Methods

1. Testcase methods **need not be public**

2. Return type must always be **void**

3. Testcase methods:

   * Must report **PASS / FAIL**
   * Should not return any value

4. Naming convention:

   * Start with `test`
   * Followed by method name and scenario

   Examples for `add(int a, int b)`:

   * `testAddWithPositiveNumbers()`
   * `testAddWithNegativeNumbers()`
   * `testAddWithZeros()`

5. Testcase methods **should not take parameters**

   * Inputs are predefined (**test fixtures**)

---

## Improved CalculatorTest With Testcase Methods

```java
package pkg1;
class CalculatorTest {

  void testAddWithPositiveNumbers() {
    int a = 10;
    int b = 20;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);

    if (actualSum == 30) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }

  void testAddWithNegativeNumbers() {
    int a = -10;
    int b = -20;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);

    if (actualSum == -30) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }

  void testAddWithZeros() {
    int a = 0;
    int b = 0;

    Calculator calculator = new Calculator();
    int actualSum = calculator.add(a, b);

    if (actualSum == 0) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }

  public static void main(String[] args) {
    CalculatorTest calculatorTest = new CalculatorTest();
    calculatorTest.testAddWithPositiveNumbers();
    calculatorTest.testAddWithNegativeNumbers();
    calculatorTest.testAddWithZeros();
  }
}
```

