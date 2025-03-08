---
tags:
  - python
  - sorting
  - programming
  - real-python
---
# Python Strings and Character Data
- The `str()` function converts objects to their **string representation**.
- **String interpolation** in Python allows you to insert values into `{}` placeholders in strings using f-strings or the `.format()` method.
- You **access string elements** in Python using **indexing** with square brackets.
- You can **slice a string** in Python by using the syntax `string[start:end]` to extract a substring.
- You **concatenate strings** in Python using the `+` operator or by using the `.join()` method.

## Escape Sequences
|Escape Sequence|Escaped Interpretation|
|---|---|
|`\a`|ASCII Bell (`BEL`) character|
|`\b`|ASCII Backspace (`BS`) character|
|`\f`|ASCII Formfeed (`FF`) character|
|`\n`|ASCII Linefeed (`LF`) character|
|`\N{<name>}`|Character from Unicode database with given `<name>`|
|`\r`|ASCII Carriage return (`CR`) character|
|`\t`|ASCII Horizontal tab (`TAB`) character|
|`\uxxxx`|Unicode character with 16-bit hex value `xxxx`|
|`\Uxxxxxxxx`|Unicode character with 32-bit hex value `xxxxxxxx`|
|`\v`|ASCII Vertical tab (`VT`) character|
|`\ooo`|Character with octal value `ooo`|
|`\xhh`|Character with hex value `hh`|
Example:
```python
>>> # Tab
>>> print("a\tb")
a    b

>>> # Linefeed
>>> print("a\nb")
a
b

>>> # Octal
>>> print("\141")
a

>>> # Hex
>>> print("\x61")
a

>>> # Unicode by name
>>> print("\N{rightwards arrow}")
→
```
### Raw String Literals[](https://realpython.com/python-strings/#raw-string-literals "Permanent link")

With raw string literals, you can create strings that don’t translate escape sequences. Any backslash characters are left in the string.

**Note:** To learn more about raw strings, check out the [What Are Python Raw Strings?](https://realpython.com/python-raw-strings/) tutorial.

To create a raw string, you have to prepend the string literal with the letter `r` or `R`:

Python

`>>> print("Before\tAfter")  # Regular string Before    After  >>> print(r"Before\tAfter")  # Raw string Before\tAfter`

The raw string suppresses the meaning of the escape sequence, such as `\t`, and presents the characters as they are.

Raw strings are commonly used to create [regular expressions](https://realpython.com/regex-python/) because they allow you to use several different characters that may have special meanings without restrictions.

**Note:** To learn more about regular expressions in Python, check out the tutorials on regular expressions in Python [Part 1](https://realpython.com/regex-python/) and [Part 2](https://realpython.com/regex-python-part-2/).

For example, say that you want to create a regular expression to match email addresses. To do this, you can use a raw string to create the regular expression like in the code below:


```python
>>> import re  
>>> 
>>> pattern = r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b" >>> pattern '\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b'  >>> regex = re.compile(pattern)  
>>> 
>>> text = """ ...     Please contact us at support@example.com ...     or sales@example.com for further information. ... """  
>>> 
>>> regex.findall(text) ['support@example.com', 'sales@example.com']
```

The `pattern` variable holds a raw string that makes up a regular expression to match email addresses. Note how the string contains several backslashes that are escaped and inserted into the resulting string as they are. Then, you compile the regular expression and check for matches in a sample piece of text.

## Using Operators on Strings

### Repeating Strings: The `*` Operator[](https://realpython.com/python-strings/#repeating-strings-the-operator "Permanent link")

The `*` operator allows you to repeat a given string a certain number of times. In this context, this operator is known as the **repetition operator**. Its syntax is shown below:

```python
new_string = string * n new_string = n * string
```


The repetition operator takes two operands. One operand is the string that you want to repeat, while the other operand is an integer number representing the number of times you want to repeat the target string:

Python

`>>> "=" * 10 '=========='  >>> 10 * "Hi!" 'Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!'`

In the first example, you repeat the `=` character ten times. In the second example, you repeat the text `Hi!` ten times as well. Note that the order of the operands doesn’t affect the result.

The multiplier operand, `n`, is usually a positive integer. If it’s a zero or negative integer, the result is an empty string:

Python

`>>> "Hi!" * 0 ''  >>> "Hi!" * -8 ''`

You need to be aware of this behavior in situations where you compute `n` dynamically, and `0` or negative values can occur. Otherwise, this operator comes in handy when you need to present some output to your users in a table format.

For example, say that you have the following data about your company’s employees:

Python

`>>> data = [ ...    ["Alice", "25", "Python Developer"], ...    ["Bob", "30", "Web Designer"], ...    ["Charlie", "35", "Team Lead"], ... ]`

You want to create a function that takes this data, puts it into a table, and prints it to the screen. In this situation, you can write a function like the following:

Python

`>>> def display_table(data, headers): ...     max_len = max(len(header) for header in headers) ...     print(" | ".join(header.ljust(max_len) for header in headers)) ...     sep = "-" * max_len ...     print("-|-".join(sep for _ in headers)) ...     for row in data: ...         print(" | ".join(header.ljust(max_len) for header in row)) ...`

This function takes the employees’ data as an argument and the headers for the table. Then, it gets the maximum header length, prints the headers using the pipe character (`|`) as a separator, and justifies the headers to the left.

To separate the headers from the table’s content, you build a line of hyphens. To create the line, you use the repetition operator with the maximum header length as the multiplier operand. The effect of using the operator here is that you’ll get a visual line under each header that’s always as long as the longest header field. Finally, you print the employees’ data.

Here’s how the function works in practice:

Python

`>>> data = [ ...    ["Alice", "25", "Python Developer"], ...    ["Bob", "30", "Web Designer"], ...    ["Charlie", "35", "Team Lead"], ... ] >>> headers = ["Name", "Age", "Job Title"]  >>> display_table(data, headers) Name      | Age       | Job Title ----------|-----------|---------- Alice     | 25        | Python Developer Bob       | 30        | Web Designer Charlie   | 35        | Team Lead`

Again, creating tables like this one is a great use case for the repetition operator because it allows you to create separators dynamically.

The repetition operator also has an augmented variation that lets you do something like `string = string * n` in a shorter way:

Python

`>>> sep = "-"  >>> sep *= 10  >>> sep '----------'`

In this example, the `*=` operator repeats the string in `sep` ten times and assigns the result back to the `sep` variable.

https://realpython.com/python-strings/