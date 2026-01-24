# Repeated Tests

Executing a test method repeatedly for a specified number of times is called **Repeated Tests**. Each repetition behaves like a regular test method execution with full support of the **Test Execution Lifecycle**.

By default, the JUnit Test Engine executes a test method only once. Sometimes, we want to run a test repeatedly to verify the behavior or consistency of a method. This can be achieved using **Repeated Tests**.

On the test method, we need to apply the `@RepeatedTest(nTimes)` annotation so that the Test Execution Engine repeats the execution for the specified number of times.

In addition, we can customize the `displayName` and extract repetition information using expressions or API classes provided by JUnit.

```java
@RepeatedTest(value = 5, name = "{displayName} {currentRepetition}/{totalRepetitions}")
@DisplayName("test isPalindrome with regular string")
void testIsPalindromeWithRegularString() {
    final String s = "Good Morning";
    Assertions.assertFalse(stringHelper.isPalindrome(s));
}
````

```java
@RepeatedTest(5)
void testIsPalindromeWithPalindromeString(RepetitionInfo repetitionInfo) {
    final String s = "madam";
    System.out.println(
        repetitionInfo.getCurrentRepetition() + " / " +
        repetitionInfo.getTotalRepetitions()
    );
    Assertions.assertTrue(stringHelper.isPalindrome(s));
}
```

---

# Disabled Test

Sometimes we want to skip a few test methods during test execution. One way is by commenting out the `@Test` annotation, but the drawback is that such methods are completely lost and appear as if they never existed.

Instead of commenting test methods, we can use the `@Disabled` annotation. The Test Engine will then skip execution of the test and also report the number of disabled tests in the test execution report.

```java
@Test
@Disabled("Known issue, will address")
void testIsPalindromeWithNullString() {
    final String s = null;
    Assertions.assertFalse(stringHelper.isPalindrome(s));
}
```

---

# Conditional Tests

Not all tests are applicable across all environments. Some tests may need to run only on specific platforms, JDK versions, or configurations.

JUnit 5 supports **Conditional Tests** to handle such scenarios.

## 1. Based on Operating System

```java
@EnabledOnOs(OS.MAC)
// Supported values:
// MAC
// WINDOWS
// LINUX
```

## 2. Based on Java Version

```java
@EnabledOnJre(JRE.JAVA_11)

@EnabledOnJreRange(min = JRE.JAVA_11, max = JRE.JAVA_17)
```

## 3. Based on System Property

```java
@EnableIfSystemProperty(named = "cache.enabled", matches = "true")
```

## 4. Based on Environment Variable

```java
@EnableIfEnvironmentVariable(named = "M2_HOME", matches = ".*")
```

## 5. Custom Conditions

```java
@Test
@EnabledIf("checkCondition")
public void testIsPalindrome() {
}
```

```java
static boolean checkCondition() {
    // Custom logic to decide test execution
    return true;
}
```

---

# Full Example for Repeated Tests, Disabled Tests, and Conditional Tests

## Original Class

```java
package com.bu5.beans.repetition;

public class StringHelper {

    public boolean isPalindrome(final String s) {
        boolean palindrome = false;
        StringBuffer reverse = new StringBuffer();

        for (int i = s.length() - 1; i >= 0; i--) {
            reverse.append(s.charAt(i));
        }

        if (reverse.toString().equals(s)) {
            palindrome = true;
        }

        return palindrome;
    }
}
```

---

## Test Class

```java
package com.bu5.beans.repetition;

import org.junit.jupiter.api.*;
import org.junit.jupiter.api.condition.EnabledIf;
import org.junit.jupiter.api.condition.EnabledOnOs;
import org.junit.jupiter.api.condition.OS;

import java.io.IOException;
import java.util.Properties;

import static org.junit.jupiter.api.Assertions.*;

public class StringHelperTest {

    StringHelper stringHelper;

    @BeforeEach
    public void setUp() {
        stringHelper = new StringHelper();
    }

    @RepeatedTest(value = 5, name = "{displayName} {currentRepetition}/{totalRepetitions}")
    @DisplayName("test isPalindrome with regular string")
    @EnabledIf("reverseEnabled")
    public void testIsPalindromeWithRegularString() {
        String s = "Good Morning";
        assertFalse(stringHelper.isPalindrome(s));
    }

    @RepeatedTest(5)
    @EnabledOnOs(OS.MAC)
    public void testIsPalindromeWithPalindromeString(RepetitionInfo repetitionInfo) {
        String s = "madam";
        System.out.println(
            repetitionInfo.getCurrentRepetition() + " / " +
            repetitionInfo.getTotalRepetitions()
        );
        assertTrue(stringHelper.isPalindrome(s));
    }

    @Test
    @Disabled("INTO1245 will address along with current working ticket")
    public void testIsPalindromeWithNullString() {
        String s = null;
        assertFalse(stringHelper.isPalindrome(s));
    }

    static boolean reverseEnabled() throws IOException {
        final Properties properties = new Properties();
        properties.load(
            StringHelperTest.class
                .getClassLoader()
                .getResourceAsStream("application.properties")
        );

        return "true".equals(properties.getProperty("reverseEnabled"));
    }

    @AfterEach
    public void cleanUp() {
        stringHelper = null;
    }
}
```

---

## application.properties

```properties
reverseEnabled=false
```

---

## Output

```
<img width="1718" height="603" alt="Screenshot 2026-01-24 101228" src="https://github.com/user-attachments/assets/78873ddf-9766-4d2b-9bc1-81e00247425b" />

```
