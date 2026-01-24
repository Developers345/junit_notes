## Test Method Order
------------------

The order in which the test methods execute should not matter, because unit tests are written to be **isolated and independent** of each other.  
However, in some scenarios—such as **integration tests** or **functional tests**—we may want to **enforce a specific execution order**.

When using `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`, test methods can share state, and ordering may become important.

### Default Behavior
By default, JUnit 5 executes test methods **in the order they are declared** in the test class (top to bottom).

### Controlling Execution Order in JUnit 5
JUnit 5 allows controlling the execution order of test methods using:

```java
@TestMethodOrder(MethodOrderer.*)
````

This annotation is applied at the **test class level**, and you can choose one of the following strategies:

1. `MethodOrderer.DisplayName`
2. `MethodOrderer.MethodName` – Executes tests in sorted order of method names
3. `MethodOrderer.OrderAnnotation` – Executes tests based on `@Order` annotation
4. `MethodOrderer.Random` – Executes tests in random order

---

## Example

---

### Original Class

---

```java
package com.bu5.beans.orderer;

import java.util.Arrays;

public class ArrayHepler {

    public int bigNumber(int[] numbers) {
        int big = Arrays.stream(numbers)
                        .reduce(0, (int max, int b) -> max < b ? b : max);
        return big;
    }
}
```

---

### Test Class

---

```java
package com.bu5.beans.orderer;

import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.assertEquals;

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@TestMethodOrder(MethodOrderer.MethodName.class)
@TestMethodOrder(MethodOrderer.DisplayName.class)
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
public class ArrayHeplerTest {

    ArrayHepler arrayHepler;

    @BeforeEach
    public void setUp() {
        arrayHepler = new ArrayHepler();
    }

    @Test
    @DisplayName("big number with different integers")
    @Order(3)
    void test_Big_Number() {
        int[] numbers = new int[] {10, 83, 3, 8, 93, 27, 0};
        int expectedBigNumber = 93;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @Test
    @DisplayName("big number with same integers")
    @Order(2)
    void test_With_Same_Number() {
        int[] numbers = new int[] {10, 10, 10, 10, 10, 10, 10};
        int expectedBigNumber = 10;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @Test
    @DisplayName("big number with single integer")
    @Order(1)
    void test_With_Single_Number() {
        int[] numbers = new int[] {10};
        int expectedBigNumber = 10;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @AfterEach
    public void cleanUp() {
        arrayHepler = null;
    }
}
```

---

## Display Name Generators

---

Instead of manually defining display names for each test class or test method, JUnit 5 provides **display name generators** to generate them automatically.

You can configure a display name generation strategy using:

```java
@DisplayNameGeneration(...)
```

### Available Display Name Generation Strategies

1. **Standard**
2. **Simple**

   * Removes parentheses and parameter information
3. **ReplaceUnderscores**

   * Replaces underscores (`_`) with spaces
   * Example:
     `test_BigNumber()` → `test BigNumber`
4. **IndicativeSentences**

   * Generates complete sentences by combining test method and class names

---

## Example

---

### Original Class

---

```java
package com.bu5.beans.orderer;

import java.util.Arrays;

public class ArrayHepler {

    public int bigNumber(int[] numbers) {
        int big = Arrays.stream(numbers)
                        .reduce(0, (int max, int b) -> max < b ? b : max);
        return big;
    }
}
```

---

### Test Class with Display Name Generators

---

```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@DisplayNameGeneration(DisplayNameGeneration.Standard.class)
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
@DisplayNameGeneration(DisplayNameGenerator.IndicativeSentences.class)
public class ArrayHeplerTest {

    ArrayHepler arrayHepler;

    @BeforeEach
    public void setUp() {
        arrayHepler = new ArrayHepler();
    }

    @Test
    @Order(3)
    void test_Big_Number() {
        int[] numbers = new int[] {10, 83, 3, 8, 93, 27, 0};
        int expectedBigNumber = 93;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @Test
    @Order(2)
    void test_With_Same_Number() {
        int[] numbers = new int[] {10, 10, 10, 10, 10, 10, 10};
        int expectedBigNumber = 10;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @Test
    @Order(1)
    void test_With_Single_Number() {
        int[] numbers = new int[] {10};
        int expectedBigNumber = 10;

        int actualBigNumber = arrayHepler.bigNumber(numbers);

        assertEquals(expectedBigNumber, actualBigNumber);
    }

    @AfterEach
    public void cleanUp() {
        arrayHepler = null;
    }
}
```
