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

    if (actualCentimeters == 55) {
      System.out.println(".");
    } else {
      System.out.println("f");
    }
  }
}
```
