# Coding Questions 63-91 Solutions

These answers are written in Java because the question set mentions `Stream API`, `Comparator`, and employee record filtering.

Assumptions used:

- For Fibonacci, `n` means the first `n` terms.
- For duplicate employee questions, "duplicate" is interpreted based on the field mentioned in the question, such as `designation`.
- Code snippets are method-focused so they are easy to revise in interviews.

Common imports if you want to run these snippets:

```java
import java.util.*;
import java.util.stream.Collectors;
```

---

## Common Employee Model Used in Some Questions

```java
class Employee {
    private int id;
    private String name;
    private String department;
    private String designation;

    public Employee(int id, String name, String department, String designation) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.designation = designation;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public String getDesignation() { return designation; }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "', department='" + department
                + "', designation='" + designation + "'}";
    }
}
```

---

## String Programs

### 63. Reverse a string

Short theory: Reversing means reading characters from the end back to the start.

Brute force:

- Copy characters from the last index to the first into a new string.
- Time: `O(n)`, Space: `O(n)`

```java
static String reverseBrute(String s) {
    String result = "";
    for (int i = s.length() - 1; i >= 0; i--) {
        result += s.charAt(i);
    }
    return result;
}
```

Better:

- Use `StringBuilder` because repeated `+` creates extra objects.
- Time: `O(n)`, Space: `O(n)`

```java
static String reverseBetter(String s) {
    StringBuilder sb = new StringBuilder();
    for (int i = s.length() - 1; i >= 0; i--) {
        sb.append(s.charAt(i));
    }
    return sb.toString();
}
```

Optimal:

- Use built-in `StringBuilder.reverse()`.
- Time: `O(n)`, Space: `O(n)`

```java
static String reverseOptimal(String s) {
    return new StringBuilder(s).reverse().toString();
}
```

### 64. Check if a string is a palindrome

Short theory: A palindrome reads the same from left to right and right to left.

Brute force:

- Reverse the string and compare.

```java
static boolean isPalindromeBrute(String s) {
    String reversed = new StringBuilder(s).reverse().toString();
    return s.equals(reversed);
}
```

Better:

- Use two pointers from both ends.
- Time: `O(n)`, Space: `O(1)`

```java
static boolean isPalindromeBetter(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

Optimal:

- Same two-pointer approach is the best practical solution.

```java
static boolean isPalindromeOptimal(String s) {
    return isPalindromeBetter(s);
}
```

### 65. Remove spaces from a string

Short theory: We skip every character that is a space and keep the rest.

Brute force:

```java
static String removeSpacesBrute(String s) {
    StringBuilder sb = new StringBuilder();
    for (char ch : s.toCharArray()) {
        if (ch != ' ') {
            sb.append(ch);
        }
    }
    return sb.toString();
}
```

Better:

- Use regex if only readability matters.

```java
static String removeSpacesBetter(String s) {
    return s.replaceAll("\\s+", "");
}
```

Optimal:

- Manual loop is usually better than regex for simple space removal.

```java
static String removeSpacesOptimal(String s) {
    return removeSpacesBrute(s);
}
```

### 66. Count the number of vowels in a string

Short theory: Check each character and count if it is `a, e, i, o, u`.

Brute force:

```java
static int countVowelsBrute(String s) {
    int count = 0;
    s = s.toLowerCase();
    for (char ch : s.toCharArray()) {
        if (ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u') {
            count++;
        }
    }
    return count;
}
```

Better:

- Store vowels in a `Set` for cleaner membership check.

```java
static int countVowelsBetter(String s) {
    Set<Character> vowels = Set.of('a', 'e', 'i', 'o', 'u');
    int count = 0;
    for (char ch : s.toLowerCase().toCharArray()) {
        if (vowels.contains(ch)) {
            count++;
        }
    }
    return count;
}
```

Optimal:

- Simple `if` checks are fastest and use constant space.

```java
static int countVowelsOptimal(String s) {
    return countVowelsBrute(s);
}
```

### 67. Compress a string: `aabbbrrrraadd -> a2b3r4a2d2`

Short theory: Count consecutive repeated characters and write `char + count`.

Brute force:

```java
static String compressBrute(String s) {
    StringBuilder sb = new StringBuilder();
    int i = 0;
    while (i < s.length()) {
        char current = s.charAt(i);
        int count = 1;
        while (i + 1 < s.length() && s.charAt(i) == s.charAt(i + 1)) {
            count++;
            i++;
        }
        sb.append(current).append(count);
        i++;
    }
    return sb.toString();
}
```

Better:

- Same logic, but return original string if compression is not smaller.

```java
static String compressBetter(String s) {
    String compressed = compressBrute(s);
    return compressed.length() < s.length() ? compressed : s;
}
```

Optimal:

- Run-length encoding with one pass is the best approach here.

```java
static String compressOptimal(String s) {
    return compressBrute(s);
}
```

### 68. Decompress a string: `a3f2k2s3 -> aaaffkksss`

Short theory: Read one character, then read its number, and repeat the character that many times.

Brute force:

```java
static String decompressBrute(String s) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < s.length(); i += 2) {
        char ch = s.charAt(i);
        int count = s.charAt(i + 1) - '0';
        for (int j = 0; j < count; j++) {
            sb.append(ch);
        }
    }
    return sb.toString();
}
```

Better:

- Support multi-digit counts like `a12`.

```java
static String decompressBetter(String s) {
    StringBuilder sb = new StringBuilder();
    int i = 0;
    while (i < s.length()) {
        char ch = s.charAt(i++);
        int count = 0;
        while (i < s.length() && Character.isDigit(s.charAt(i))) {
            count = count * 10 + (s.charAt(i) - '0');
            i++;
        }
        for (int j = 0; j < count; j++) {
            sb.append(ch);
        }
    }
    return sb.toString();
}
```

Optimal:

- The one-pass parser with multi-digit support is the preferred solution.

```java
static String decompressOptimal(String s) {
    return decompressBetter(s);
}
```

### 69. Find the first non-repeating character in a string

Short theory: Count all characters first, then scan again to find the first count of `1`.

Brute force:

- For each character, compare with all others.
- Time: `O(n^2)`

```java
static Character firstNonRepeatingBrute(String s) {
    for (int i = 0; i < s.length(); i++) {
        boolean unique = true;
        for (int j = 0; j < s.length(); j++) {
            if (i != j && s.charAt(i) == s.charAt(j)) {
                unique = false;
                break;
            }
        }
        if (unique) {
            return s.charAt(i);
        }
    }
    return null;
}
```

Better:

- Use `LinkedHashMap` to preserve insertion order.

```java
static Character firstNonRepeatingBetter(String s) {
    Map<Character, Integer> freq = new LinkedHashMap<>();
    for (char ch : s.toCharArray()) {
        freq.put(ch, freq.getOrDefault(ch, 0) + 1);
    }
    for (Map.Entry<Character, Integer> entry : freq.entrySet()) {
        if (entry.getValue() == 1) {
            return entry.getKey();
        }
    }
    return null;
}
```

Optimal:

- Two-pass frequency counting is the right approach.

```java
static Character firstNonRepeatingOptimal(String s) {
    int[] freq = new int[256];
    for (char ch : s.toCharArray()) {
        freq[ch]++;
    }
    for (char ch : s.toCharArray()) {
        if (freq[ch] == 1) {
            return ch;
        }
    }
    return null;
}
```

### 70. Check if two strings are anagrams

Short theory: Two strings are anagrams if they contain the same characters with the same frequency.

Brute force:

- Sort both strings and compare.

```java
static boolean isAnagramBrute(String s1, String s2) {
    if (s1.length() != s2.length()) {
        return false;
    }
    char[] a = s1.toCharArray();
    char[] b = s2.toCharArray();
    Arrays.sort(a);
    Arrays.sort(b);
    return Arrays.equals(a, b);
}
```

Better:

- Use one `HashMap` for frequency.

```java
static boolean isAnagramBetter(String s1, String s2) {
    if (s1.length() != s2.length()) {
        return false;
    }
    Map<Character, Integer> freq = new HashMap<>();
    for (char ch : s1.toCharArray()) {
        freq.put(ch, freq.getOrDefault(ch, 0) + 1);
    }
    for (char ch : s2.toCharArray()) {
        Integer count = freq.get(ch);
        if (count == null || count == 0) {
            return false;
        }
        freq.put(ch, count - 1);
    }
    return true;
}
```

Optimal:

- Use an array count for fixed charset.

```java
static boolean isAnagramOptimal(String s1, String s2) {
    if (s1.length() != s2.length()) {
        return false;
    }
    int[] freq = new int[256];
    for (char ch : s1.toCharArray()) {
        freq[ch]++;
    }
    for (char ch : s2.toCharArray()) {
        freq[ch]--;
        if (freq[ch] < 0) {
            return false;
        }
    }
    return true;
}
```

### 71. Remove duplicates from a string array

Short theory: Duplicates are removed by keeping only the first occurrence of each string.

Brute force:

```java
static List<String> removeDuplicatesBrute(String[] arr) {
    List<String> result = new ArrayList<>();
    for (String word : arr) {
        if (!result.contains(word)) {
            result.add(word);
        }
    }
    return result;
}
```

Better:

- Use `HashSet`.

```java
static Set<String> removeDuplicatesBetter(String[] arr) {
    return new HashSet<>(Arrays.asList(arr));
}
```

Optimal:

- Use `LinkedHashSet` to remove duplicates and keep original order.

```java
static List<String> removeDuplicatesOptimal(String[] arr) {
    return new ArrayList<>(new LinkedHashSet<>(Arrays.asList(arr)));
}
```

### 72. Print all words from a sentence

Short theory: Split the sentence at spaces and print each word.

Brute force:

```java
static void printWordsBrute(String sentence) {
    String[] words = sentence.split(" ");
    for (String word : words) {
        if (!word.isEmpty()) {
            System.out.println(word);
        }
    }
}
```

Better:

- Use regex to handle multiple spaces.

```java
static void printWordsBetter(String sentence) {
    String[] words = sentence.trim().split("\\s+");
    for (String word : words) {
        System.out.println(word);
    }
}
```

Optimal:

- Regex split with trim is the most practical interview answer.

```java
static void printWordsOptimal(String sentence) {
    printWordsBetter(sentence);
}
```

### 73. Concatenate two strings

Short theory: Concatenation means joining strings end-to-end.

Brute force:

```java
static String concatBrute(String a, String b) {
    return a + b;
}
```

Better:

- Use `StringBuilder` when many concatenations are involved.

```java
static String concatBetter(String a, String b) {
    return new StringBuilder().append(a).append(b).toString();
}
```

Optimal:

- For two strings, `+` is fully fine.

```java
static String concatOptimal(String a, String b) {
    return a + b;
}
```

### 74. Count frequency of a particular character in a string

Short theory: Scan the string and count how many times the target character appears.

Brute force:

```java
static int charFrequencyBrute(String s, char target) {
    int count = 0;
    for (char ch : s.toCharArray()) {
        if (ch == target) {
            count++;
        }
    }
    return count;
}
```

Better:

- Case-insensitive version if needed.

```java
static int charFrequencyBetter(String s, char target) {
    int count = 0;
    char t = Character.toLowerCase(target);
    for (char ch : s.toLowerCase().toCharArray()) {
        if (ch == t) {
            count++;
        }
    }
    return count;
}
```

Optimal:

- One linear scan is already optimal.

```java
static int charFrequencyOptimal(String s, char target) {
    return charFrequencyBrute(s, target);
}
```

---

## Array and Number Programs

### 75. Find the second largest element in an array

Short theory: Track the largest and second largest while scanning once.

Brute force:

- Sort and pick the second last unique value.

```java
static int secondLargestBrute(int[] arr) {
    int[] copy = Arrays.copyOf(arr, arr.length);
    Arrays.sort(copy);
    for (int i = copy.length - 2; i >= 0; i--) {
        if (copy[i] != copy[copy.length - 1]) {
            return copy[i];
        }
    }
    throw new IllegalArgumentException("No second largest element");
}
```

Better:

- Use a `TreeSet` to keep unique sorted values.

```java
static int secondLargestBetter(int[] arr) {
    TreeSet<Integer> set = new TreeSet<>();
    for (int num : arr) {
        set.add(num);
    }
    if (set.size() < 2) {
        throw new IllegalArgumentException("No second largest element");
    }
    set.pollLast();
    return set.last();
}
```

Optimal:

- One pass with two variables.

```java
static int secondLargestOptimal(int[] arr) {
    int largest = Integer.MIN_VALUE;
    int second = Integer.MIN_VALUE;
    for (int num : arr) {
        if (num > largest) {
            second = largest;
            largest = num;
        } else if (num > second && num != largest) {
            second = num;
        }
    }
    if (second == Integer.MIN_VALUE) {
        throw new IllegalArgumentException("No second largest element");
    }
    return second;
}
```

### 76. Find the largest element in an array

Short theory: Compare each element and keep the maximum seen so far.

Brute force:

```java
static int largestBrute(int[] arr) {
    Arrays.sort(arr);
    return arr[arr.length - 1];
}
```

Better:

```java
static int largestBetter(int[] arr) {
    int max = arr[0];
    for (int num : arr) {
        if (num > max) {
            max = num;
        }
    }
    return max;
}
```

Optimal:

- Linear scan is optimal.

```java
static int largestOptimal(int[] arr) {
    return largestBetter(arr);
}
```

### 77. Reverse an integer

Short theory: Take the last digit using `% 10`, append it to the reversed number, then remove the digit using `/ 10`.

Brute force:

```java
static int reverseIntegerBrute(int n) {
    String reversed = new StringBuilder(String.valueOf(n)).reverse().toString();
    return Integer.parseInt(reversed);
}
```

Better:

```java
static int reverseIntegerBetter(int n) {
    int rev = 0;
    while (n != 0) {
        rev = rev * 10 + n % 10;
        n /= 10;
    }
    return rev;
}
```

Optimal:

- Math-based reverse is best, but overflow should be handled.

```java
static int reverseIntegerOptimal(int n) {
    long rev = 0;
    while (n != 0) {
        rev = rev * 10 + n % 10;
        if (rev > Integer.MAX_VALUE || rev < Integer.MIN_VALUE) {
            throw new ArithmeticException("Integer overflow");
        }
        n /= 10;
    }
    return (int) rev;
}
```

### 78. Reverse an integer with exception handling

Short theory: Same logic as reverse integer, but safely handle invalid or overflowing values.

Brute force:

```java
static int reverseIntegerWithExceptionBrute(String input) {
    try {
        int n = Integer.parseInt(input);
        String reversed = new StringBuilder(String.valueOf(n)).reverse().toString();
        return Integer.parseInt(reversed);
    } catch (NumberFormatException e) {
        throw new IllegalArgumentException("Invalid integer input", e);
    }
}
```

Better:

```java
static int reverseIntegerWithExceptionBetter(String input) {
    try {
        int n = Integer.parseInt(input);
        return reverseIntegerOptimal(n);
    } catch (NumberFormatException e) {
        throw new IllegalArgumentException("Input is not a valid integer", e);
    } catch (ArithmeticException e) {
        throw new IllegalArgumentException("Reversed integer is out of range", e);
    }
}
```

Optimal:

- Parse safely, reverse mathematically, and fail with clear errors.

```java
static int reverseIntegerWithExceptionOptimal(String input) {
    return reverseIntegerWithExceptionBetter(input);
}
```

### 79. Check if a number is prime

Short theory: A prime number has exactly two factors: `1` and itself.

Brute force:

```java
static boolean isPrimeBrute(int n) {
    if (n <= 1) {
        return false;
    }
    int count = 0;
    for (int i = 1; i <= n; i++) {
        if (n % i == 0) {
            count++;
        }
    }
    return count == 2;
}
```

Better:

```java
static boolean isPrimeBetter(int n) {
    if (n <= 1) {
        return false;
    }
    for (int i = 2; i < n; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

Optimal:

- Check divisors only up to `sqrt(n)`.

```java
static boolean isPrimeOptimal(int n) {
    if (n <= 1) {
        return false;
    }
    if (n == 2) {
        return true;
    }
    if (n % 2 == 0) {
        return false;
    }
    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

### 80. Print the Fibonacci series for the nth number

Short theory: Each Fibonacci number is the sum of the previous two numbers.

Brute force:

- Use recursion for each term.

```java
static int fibBrute(int n) {
    if (n <= 1) {
        return n;
    }
    return fibBrute(n - 1) + fibBrute(n - 2);
}

static void printFibonacciBrute(int n) {
    for (int i = 0; i < n; i++) {
        System.out.print(fibBrute(i) + " ");
    }
}
```

Better:

- Use iterative approach.

```java
static void printFibonacciBetter(int n) {
    int a = 0, b = 1;
    for (int i = 0; i < n; i++) {
        System.out.print(a + " ");
        int c = a + b;
        a = b;
        b = c;
    }
}
```

Optimal:

- Iterative generation is the preferred interview solution.

```java
static void printFibonacciOptimal(int n) {
    printFibonacciBetter(n);
}
```

### 81. Print only even Fibonacci numbers

Short theory: Generate Fibonacci numbers and print only the even ones.

Brute force:

```java
static void printEvenFibonacciBrute(int n) {
    for (int i = 0; i < n; i++) {
        int value = fibBrute(i);
        if (value % 2 == 0) {
            System.out.print(value + " ");
        }
    }
}
```

Better:

```java
static void printEvenFibonacciBetter(int n) {
    int a = 0, b = 1;
    for (int i = 0; i < n; i++) {
        if (a % 2 == 0) {
            System.out.print(a + " ");
        }
        int c = a + b;
        a = b;
        b = c;
    }
}
```

Optimal:

- Generate once and filter by parity.

```java
static void printEvenFibonacciOptimal(int n) {
    printEvenFibonacciBetter(n);
}
```

### 82. Check if a number is an Armstrong number

Short theory: An Armstrong number is equal to the sum of its digits raised to the power of the number of digits.

Brute force:

```java
static boolean isArmstrongBrute(int n) {
    String str = String.valueOf(n);
    int power = str.length();
    int sum = 0;
    for (char ch : str.toCharArray()) {
        sum += Math.pow(ch - '0', power);
    }
    return sum == n;
}
```

Better:

```java
static boolean isArmstrongBetter(int n) {
    int original = n;
    int digits = String.valueOf(n).length();
    int sum = 0;
    while (n > 0) {
        int digit = n % 10;
        sum += (int) Math.pow(digit, digits);
        n /= 10;
    }
    return sum == original;
}
```

Optimal:

- Digit extraction with math is the usual answer.

```java
static boolean isArmstrongOptimal(int n) {
    return isArmstrongBetter(n);
}
```

### 83. Find the second highest number using Stream API

Short theory: Remove duplicates, sort in reverse order, then skip the first element.

Brute force:

```java
static int secondHighestStreamBrute(List<Integer> list) {
    return list.stream()
            .sorted(Comparator.reverseOrder())
            .skip(1)
            .findFirst()
            .orElseThrow();
}
```

Better:

- Remove duplicates first.

```java
static int secondHighestStreamBetter(List<Integer> list) {
    return list.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(1)
            .findFirst()
            .orElseThrow();
}
```

Optimal:

- If Stream API is mandatory, `distinct + reverse sort + skip(1)` is the clean answer.

```java
static int secondHighestStreamOptimal(List<Integer> list) {
    return secondHighestStreamBetter(list);
}
```

### 84. Print duplicate values from a list of employee records

Short theory: Track seen employee IDs or names and print repeated ones. In real projects, duplicate definition must be clear.

Brute force:

```java
static List<Employee> duplicateEmployeesBrute(List<Employee> employees) {
    List<Employee> duplicates = new ArrayList<>();
    for (int i = 0; i < employees.size(); i++) {
        for (int j = i + 1; j < employees.size(); j++) {
            if (employees.get(i).getId() == employees.get(j).getId()) {
                duplicates.add(employees.get(j));
            }
        }
    }
    return duplicates;
}
```

Better:

```java
static List<Employee> duplicateEmployeesBetter(List<Employee> employees) {
    Set<Integer> seen = new HashSet<>();
    List<Employee> duplicates = new ArrayList<>();
    for (Employee emp : employees) {
        if (!seen.add(emp.getId())) {
            duplicates.add(emp);
        }
    }
    return duplicates;
}
```

Optimal:

- Hash-based tracking is the best practical solution.

```java
static List<Employee> duplicateEmployeesOptimal(List<Employee> employees) {
    return duplicateEmployeesBetter(employees);
}
```

### 85. Print a left half pyramid pattern

Short theory: Outer loop controls rows, inner loop prints stars in each row.

Brute force:

```java
static void leftHalfPyramidBrute(int n) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            System.out.print("* ");
        }
        System.out.println();
    }
}
```

Better:

- Use `String.repeat()` for cleaner code.

```java
static void leftHalfPyramidBetter(int n) {
    for (int i = 1; i <= n; i++) {
        System.out.println("* ".repeat(i));
    }
}
```

Optimal:

- Nested loops are easiest to explain and most portable.

```java
static void leftHalfPyramidOptimal(int n) {
    leftHalfPyramidBrute(n);
}
```

### 86. Swap two numbers without using a third variable

Short theory: Swap values either with arithmetic or XOR.

Brute force:

- Use a temporary variable. This breaks the exact question but is the easiest baseline.

```java
static void swapBrute() {
    int a = 10, b = 20;
    int temp = a;
    a = b;
    b = temp;
    System.out.println(a + " " + b);
}
```

Better:

- Arithmetic swap.

```java
static void swapBetter() {
    int a = 10, b = 20;
    a = a + b;
    b = a - b;
    a = a - b;
    System.out.println(a + " " + b);
}
```

Optimal:

- XOR swap avoids overflow from addition.

```java
static void swapOptimal() {
    int a = 10, b = 20;
    a = a ^ b;
    b = a ^ b;
    a = a ^ b;
    System.out.println(a + " " + b);
}
```

---

## Collections and Stream Coding

### 87. Create `ArrayList -> Stream -> filter -> collect back to List`

Short theory: Stream lets us process list elements declaratively and collect the filtered result back into a list.

Brute force:

```java
static List<String> filterListBrute(List<String> names) {
    List<String> result = new ArrayList<>();
    for (String name : names) {
        if (name.startsWith("R")) {
            result.add(name);
        }
    }
    return result;
}
```

Better:

```java
static List<String> filterListBetter() {
    List<String> names = new ArrayList<>(List.of("Raj", "Amit", "Riya", "Kunal"));
    return names.stream()
            .filter(name -> name.startsWith("R"))
            .collect(Collectors.toList());
}
```

Optimal:

- Stream pipeline is the intended solution.

```java
static List<String> filterListOptimal(List<String> names) {
    return names.stream()
            .filter(name -> name.startsWith("R"))
            .toList();
}
```

### 88. Find the second highest number using Stream API

Short theory: Same as Question 83.

Brute force:

```java
static int secondHighestQ88Brute(List<Integer> nums) {
    return nums.stream()
            .sorted(Comparator.reverseOrder())
            .skip(1)
            .findFirst()
            .orElseThrow();
}
```

Better:

```java
static int secondHighestQ88Better(List<Integer> nums) {
    return nums.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(1)
            .findFirst()
            .orElseThrow();
}
```

Optimal:

```java
static int secondHighestQ88Optimal(List<Integer> nums) {
    return secondHighestQ88Better(nums);
}
```

### 89. Sort a list using Comparator

Short theory: `Comparator` gives custom sorting logic without modifying the class.

Brute force:

```java
static void sortStringsBrute(List<String> list) {
    Collections.sort(list);
}
```

Better:

```java
static void sortEmployeesBetter(List<Employee> employees) {
    employees.sort(Comparator.comparing(Employee::getName));
}
```

Optimal:

- Comparator chaining is more flexible.

```java
static void sortEmployeesOptimal(List<Employee> employees) {
    employees.sort(
            Comparator.comparing(Employee::getDepartment)
                    .thenComparing(Employee::getName)
    );
}
```

### 90. Print duplicate and unique values from employee records based on designation

Short theory: Count how many employees belong to each designation. Count `> 1` means duplicate designation, count `== 1` means unique designation.

Brute force:

```java
static void designationDuplicateUniqueBrute(List<Employee> employees) {
    for (Employee e1 : employees) {
        int count = 0;
        for (Employee e2 : employees) {
            if (e1.getDesignation().equals(e2.getDesignation())) {
                count++;
            }
        }
        if (count > 1) {
            System.out.println("Duplicate designation: " + e1.getDesignation());
        } else {
            System.out.println("Unique designation: " + e1.getDesignation());
        }
    }
}
```

Better:

```java
static void designationDuplicateUniqueBetter(List<Employee> employees) {
    Map<String, Long> counts = employees.stream()
            .collect(Collectors.groupingBy(Employee::getDesignation, Collectors.counting()));

    counts.forEach((designation, count) -> {
        if (count > 1) {
            System.out.println("Duplicate designation: " + designation);
        } else {
            System.out.println("Unique designation: " + designation);
        }
    });
}
```

Optimal:

- Grouping by designation is the cleanest Stream solution.

```java
static Map<Boolean, List<String>> designationDuplicateUniqueOptimal(List<Employee> employees) {
    Map<String, Long> counts = employees.stream()
            .collect(Collectors.groupingBy(Employee::getDesignation, Collectors.counting()));

    return counts.entrySet().stream()
            .collect(Collectors.partitioningBy(
                    entry -> entry.getValue() > 1,
                    Collectors.mapping(Map.Entry::getKey, Collectors.toList())
            ));
}
```

`true` key contains duplicate designations and `false` key contains unique designations.

### 91. Print employee details where department is HR and name is Raj

Short theory: Filter records using both conditions together with logical `AND`.

Brute force:

```java
static void findEmployeeBrute(List<Employee> employees) {
    for (Employee emp : employees) {
        if ("HR".equals(emp.getDepartment()) && "Raj".equals(emp.getName())) {
            System.out.println(emp);
        }
    }
}
```

Better:

```java
static List<Employee> findEmployeeBetter(List<Employee> employees) {
    return employees.stream()
            .filter(emp -> "HR".equals(emp.getDepartment()) && "Raj".equals(emp.getName()))
            .collect(Collectors.toList());
}
```

Optimal:

- Stream filtering is concise and readable.

```java
static List<Employee> findEmployeeOptimal(List<Employee> employees) {
    return employees.stream()
            .filter(emp -> "HR".equalsIgnoreCase(emp.getDepartment()))
            .filter(emp -> "Raj".equalsIgnoreCase(emp.getName()))
            .toList();
}
```

---

## Quick Revision Tips

- For strings, think in terms of `two pointers`, `frequency counting`, and `StringBuilder`.
- For arrays, many interview answers become optimal with `one pass` tracking.
- For numbers, `% 10` and `/ 10` are common digit tricks.
- For Stream API, the common pattern is `stream() -> filter/map/sort/group -> collect`.
- For duplicate problems, `HashSet` or `groupingBy(..., counting())` is usually the right direction.
