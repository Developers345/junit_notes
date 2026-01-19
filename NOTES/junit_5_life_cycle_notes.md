# JUnit 5
------

JUnit 5 is the latest generation of the JUnit testing framework. It is a **major upgrade from JUnit 4**, offering a more **modular and extensible architecture**.

JUnit 5 consists of **three main modules**:

## 1. JUnit Platform
- Acts as the **foundation** for launching testing frameworks on the JVM.
- Defines the **TestEngine API** for developing testing frameworks that run on the platform.
- Most popular **IDEs and build tools** use the JUnit Platform to integrate with JUnit.

## 2. JUnit Jupiter
- Provides the **JUnit 5 programming model and API** for writing test cases.
- Includes:
  - New annotations
  - Parameterized tests
  - Modern assertion APIs
- This is the module developers mainly use while writing tests.

## 3. JUnit Vintage
- Provides **backward compatibility**.
- Allows running **JUnit 3 and JUnit 4** tests on the JUnit 5 platform.

---

## 1. How do we write a Test Case in JUnit 5 and what is the test execution lifecycle?

### Sample Code
-------------

### Original Class

```java
package pkg1;

class Calculator {
    int add(int a, int b) {
        return a + b;
    }
}
````

### Test Class

```java
package pkg1;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    public void init() {
        calculator = new Calculator();
    }

    @Test
    void testAddPositiveNumbers() {
        int a = 10;
        int b = 20;
        int expectedSum = 30;

        int actualSum = calculator.add(a, b);
        assertEquals(expectedSum, actualSum);
    }

    @AfterEach
    public void cleanUp() {
        calculator = null;
    }
}
```

---

## Test Execution Lifecycle

**Test execution lifecycle** refers to how the test case class and its test methods are executed.

When a test case class is passed to the **TestEngine**, the following steps are performed:

1. Instantiate the **test case class object**
2. Execute the `@BeforeEach` method to initialize the **test fixture**
3. Execute the `@Test` methods **one at a time**
4. Execute the `@AfterEach` method to clean up the test fixture
5. Destroy the test case class object

This execution model is called:

### **OBJECT_PER_METHOD lifecycle**

* A **new test class object** is created for **each test method**
* Ensures **test isolation**

---

## Test Execution Lifecycles in JUnit 5

JUnit 5 supports **two test execution lifecycles**:

1. **OBJECT_PER_METHOD**
2. **OBJECT_PER_CLASS**

### Default Behavior

* JUnit 5 uses **OBJECT_PER_METHOD** by default
* Ensures test methods are isolated from each other

> **Note:**
> In JUnit 3 and JUnit 4, there is **no option** to change the lifecycle.
> It is always **OBJECT_PER_METHOD**.

---

## OBJECT_PER_CLASS Lifecycle

* Only **one object** of the test case class is created
* The same object is reused for executing **all test methods**

### When to Use Which?

* If the test class object holds **state**, use **OBJECT_PER_METHOD**
* If state sharing is safe or needed, **OBJECT_PER_CLASS** can be used

---

## Configuring Lifecycle using `@TestInstance`

Use the `@TestInstance` annotation on the test class:

```java
@TestInstance(TestInstance.Lifecycle.PER_METHOD)
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```

---

## Example Program for JUnit 5 Lifecycle

---

### Original Class

```java
package com.bu5.beans.lifecycle;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }
}
```

---

## Test Class with OBJECT_PER_METHOD

---

```java
package com.bu5.beans.lifecycle;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    public void setUp() {
        System.out.println("beforeEach() method");
        calculator = new Calculator();
    }

    @Test
    void testAddPositiveNumbers() {
        int a = 10;
        int b = 20;
        int expectedSum = 30;

        System.out.println("CalculatorTest Class Object: " + this.hashCode());

        int actualSum = calculator.add(a, b);
        assertEquals(expectedSum, actualSum);
    }

    @Test
    void testNegativeNumbers() {
        int a = -10;
        int b = -20;
        int expectedSum = -30;

        System.out.println("CalculatorTest Class Object: " + this.hashCode());

        int actualSum = calculator.add(a, b);
        assertEquals(expectedSum, actualSum);
    }

    @AfterEach
    public void cleanUp() {
        System.out.println("afterEach() method");
        calculator = null;
    }
}
```

### Output (OBJECT_PER_METHOD)

```text
beforeEach() method
CalculatorTest Class Object: 1371006431
afterEach() method
beforeEach() method
CalculatorTest Class Object: 604125138
afterEach() method
```

---

## Test Class with OBJECT_PER_CLASS

---

```java
package com.bu5.beans.lifecycle;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestInstance;

import static org.junit.jupiter.api.Assertions.assertEquals;

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    public void setUp() {
        System.out.println("beforeEach() method");
        calculator = new Calculator();
    }

    @Test
    void testAddPositiveNumbers() {
        int a = 10;
        int b = 20;
        int expectedSum = 30;

        System.out.println("CalculatorTest Class Object: " + this.hashCode());

        int actualSum = calculator.add(a, b);
        assertEquals(expectedSum, actualSum);
    }

    @Test
    void testNegativeNumbers() {
        int a = -10;
        int b = -20;
        int expectedSum = -30;

        System.out.println("CalculatorTest Class Object: " + this.hashCode());

        int actualSum = calculator.add(a, b);
        assertEquals(expectedSum, actualSum);
    }

    @AfterEach
    public void cleanUp() {
        System.out.println("afterEach() method");
        calculator = null;
    }
}
```

### Output (OBJECT_PER_CLASS)

```text
beforeEach() method
CalculatorTest Class Object: 1652149987
afterEach() method
beforeEach() method
CalculatorTest Class Object: 1652149987
afterEach() method


```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.junit</groupId>
      <artifactId>junit-bom</artifactId>
      <version>5.11.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
````

Let’s break it down **clearly and practically**.

---

## 🔹 What is `junit-bom`?

**BOM = Bill Of Materials**

JUnit 5 is **not a single JAR**. It is a **set of multiple modules**:

* `junit-jupiter-api`
* `junit-jupiter-engine`
* `junit-jupiter-params`
* `junit-platform-engine`
* `junit-platform-commons`
* etc.

👉 The **JUnit BOM defines compatible versions** for all of them.

---

## 🔹 Purpose of JUnit BOM in `pom.xml`

### ✅ 1. Centralized version management

You declare the **JUnit version only once**:

```xml
<version>5.11.0</version>
```

No need to repeat versions in every dependency.

---

### ✅ 2. Prevents version mismatch problems

Without BOM, you might accidentally do this:

```text
junit-jupiter-api     5.11.0
junit-jupiter-engine  5.9.3   ❌
```

This can cause:

* Tests not running
* `ClassNotFoundException`
* Test engine not discovered

✔ BOM guarantees **all JUnit modules are compatible**.

---

### ✅ 3. Cleaner dependency declarations

With BOM:

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter</artifactId>
  <scope>test</scope>
</dependency>
```

Without BOM:

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter</artifactId>
  <version>5.11.0</version>
  <scope>test</scope>
</dependency>
```

BOM removes duplication.

---

### ✅ 4. Industry best practice

* Used heavily in **Spring Boot**
* Used in **large multi-module projects**
* Makes upgrades easy (change version in one place)

---

## 🔹 What happens if you REMOVE the BOM?

Then you **must specify versions everywhere**:

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter-api</artifactId>
  <version>5.11.0</version>
</dependency>

<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter-engine</artifactId>
  <version>5.11.0</version>
</dependency>
```

❌ Error-prone
❌ Hard to maintain

---

## 🔹 Is BOM mandatory?

### ❌ No (Optional)

JUnit works **without** BOM.

### ✅ But strongly recommended when:

* Using **JUnit 5**
* Using **multiple JUnit artifacts**
* Working on **enterprise / multi-module projects**

---

## 🔹 How BOM + Dependency works together

### Step 1: Import BOM

```xml
<dependencyManagement>
  <dependency>
    <groupId>org.junit</groupId>
    <artifactId>junit-bom</artifactId>
    <version>5.11.0</version>
    <type>pom</type>
    <scope>import</scope>
  </dependency>
</dependencyManagement>
```

### Step 2: Declare dependencies (no versions)

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter</artifactId>
  <scope>test</scope>
</dependency>
```

✔ Maven resolves the version from BOM automatically.

---

## 🔹 When NOT to use JUnit BOM

### ❌ Spring Boot project

Spring Boot **already manages JUnit versions**.

So in a Spring Boot POM:

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

👉 **DO NOT add JUnit BOM manually**
Spring Boot already does it.

---

## 🔹 Summary (One-Line Answer)

> **JUnit BOM is used to manage and align all JUnit 5 module versions in one place, avoiding version conflicts and duplicate version declarations.**

---

### Quick Table

| Item                     | Purpose           |
| ------------------------ | ----------------- |
| `junit-bom`              | Version alignment |
| `<dependencyManagement>` | Central control   |
| `<scope>import</scope>`  | Import BOM        |
| Needed in Spring Boot?   | ❌ No              |
| Needed in plain Maven?   | ✅ Yes             |

---

