# Python Tutorial

## Numbers

```py
>>> 17 / 3  # classic division returns a float
5.666666666666667
>>> 17 // 3  # floor division discards the fractional part
5
>>> 17 % 3  # the % operator returns the remainder of the division
2
>>> 5 * 3 + 2  # result * divisor + remainder
17
>>> 5 ** 2  # 5 squared
25
>>> 2 ** 7  # 2 to the power of 7
128
```

In interactive mode, the last printed expression is assigned to the variable _.

```py
>>> tax = 12.5 / 100
>>> price = 100.50
>>> price * tax
12.5625
>>> price + _
113.0625
>>> round(_, 2)
113.06
```

## Strings

Strings can be concatenated (glued together) with the + operator, and repeated with *:

```py
>>>
>>> # 3 times 'un', followed by 'ium'
>>> 3 * 'un' + 'ium'
'unununium'
```

Strings can be indexed (subscripted), with the first character having index 0.

```py
>>> word[0]  # character in position 0
'P'
>>> word[-1]  # last character
'n'
```

```py
>>> word[0:2]  # characters from position 0 (included) to 2 (excluded)
'Py'
>>> word[2:5]  # characters from position 2 (included) to 5 (excluded)
'tho'
```

Python strings cannot be changed — they are immutable. Therefore, assigning to an indexed position in the string results in an error:

```py
>>> word[0] = 'J'
  ...
TypeError: 'str' object does not support item assignment
>>> word[2:] = 'py'
  ...
TypeError: 'str' object does not support item assignment
```

If you need a different string, you should create a new one:

```py
>>> 'J' + word[1:]
'Jython'
>>> word[:2] + 'py'
'Pypy'
```

The built-in function len() returns the length of a string:

```py
>>> s = 'supercalifragilisticexpialidocious'
>>> len(s)
34
```

