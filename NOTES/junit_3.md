```md
# JUnit 3
------

**JUnit 3** is the initial release of the JUnit Framework and is currently **not actively used in the market**.  
In later versions of JUnit, many new features and improvements were introduced.  
Let us understand a simple example of writing **test cases using JUnit 3**.

---

## Project Structure

```

basicunittest3 (Java Project)
│
├─ src
│   └─ com.but3.beans
│       └─ Calculator.java
│
├─ testsrc
│   └─ com.but3.beans
│       └─ CalculatorTest.java
│
├─ bin
│   └─ com.but3.beans
│       └─ Calculator.class
│
├─ testbin
│   └─ com.but3.beans
│       └─ CalculatorTest.class
│
└─ lib
├─ junit3.jar
├─ hamcrest.jar
└─ objenesis.jar

````

---

## Test Source Code (JUnit 3)

**Location:** `testsrc/com/but3/beans/CalculatorTest.java`

```java
package com.but3.beans;

class CalculatorTest extends TestCase {

    Calculator calculator;

    public void setUp() {
        calculator = new Calculator();
    }

    void testAddWithPositiveNumbers() {
        int a = 10;
        int b = 20;
        int expectedSum = 30;

        int actualSum = calculator.add(a, b);
        Asserts.assertEquals(expectedSum, actualSum);
    }

    void testAddWithNegativeNumbers() {
        int a = -10;
        int b = -20;
        int expectedSum = -30;

        int actualSum = calculator.add(a, b);
        Asserts.assertEquals(expectedSum, actualSum);
    }

    public void tearDown() {
        calculator = null;
    }
}
````

---

## JUnit 3 Test Runner

JUnit 3 provides a **console-based TestRunner** called:

```
junit.textui.TestRunner
```

### How it works

* Accepts the **Fully Qualified Name (FQN)** of the test class
* Searches for methods starting with `testXXX()`
* Executes:

  * `setUp()` (before each test)
  * Test methods
  * `tearDown()` (after each test)
* Prints **textual test results** to the console

---

## Compile JUnit 3 Test

```bat
javac -cp ".\lib\junit-3.8.2.jar;.\lib\hamcrest-core-2.2.jar;.\out\production\basicunit3" -d ".\test\basicunit3" ".\testsrc\com\bu3\beans\CalculatorTest.java"
```

---

## Run JUnit 3 Test

```bat
java -cp ".\lib\junit-3.8.2.jar;.\lib\hamcrest-core-2.2.jar;.\out\production\basicunit3;.\out\test\basicunit3" ^
junit.textui.TestRunner com.bu3.beans.CalculatorTest
```

---

## Notes

* Test methods **must start with `test`**
* No annotations are used in JUnit 3
* Naming conventions drive test execution
* JUnit 3 is **deprecated** and mainly used for legacy projects

---

```
```
