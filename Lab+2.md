
<h1>141B Lab 2: Pythonic things</h1>
In this lab, you will add python code to the empty cells according to the instructions.  Tests with the intended output are included so that you can check your progress.

Exercise 1. create a function that takes a character as an argument, if it is 'a' then return 'A' otherwise return the original character. f('a') = 'A' and f(x) = x for all other x. 


```python
def f(x):
    y = []
    for i in range(len(x)):
        if x[i] == 'a':
            y.append('A')
        else:
            y.append(x[i])
    return y

f('sdjixaajsdxoja')
```




    ['s', 'd', 'j', 'i', 'x', 'A', 'A', 'j', 's', 'd', 'x', 'o', 'j', 'A']



<ul>
<li>Using a list comprehension, apply f to each element of 'banana' and return a list of characters.</li>
<li>Try to do the same thing with the map function.</li>
<li>Using ''.join concatenate the characters in the list to make a string (the result should be 'bAnAnA').</li>
</ul>


```python
print f('banana')
print map(f,'banana')
print ''.join(f('banana'))

```

    ['b', 'A', 'n', 'A', 'n', 'A']
    [['b'], ['A'], ['n'], ['A'], ['n'], ['A']]
    bAnAnA


Exercise 2. Sum the first 100 numbers in a for loop.


```python
sum([x for x in range(1,101)])
```




    5050



Sum the first 100 numbers, but using a generator expression.


```python
sum(x for x in range(1,101))
```




    5050



Using a generator expression sum the square of the first 100 numbers.


```python
sum([x**2 for x in range(1,101)])
```




    338350



Exercise 3. Let's create the Fibonacci sequence.  Start a list $Fib$ of where the first elements are 1,1, and follow the relation that
$$Fib[i] = Fib[i-1] + Fib[i-2]$$
Generate the Fibonacci numbers less than 1000 and store it in a list.


```python
Fib = []
Fib.append(1)
Fib.append(1)
i = 2
while(True):
    x = Fib[i-1] + Fib[i-2]
    if x > 1000:
        break
    Fib.append(x)
    i = i + 1
print Fib
```

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987]


Exercise 4. Create a function called addone that does the following depending on the type of the input.  
<ul>
<li>Integer: add 1</li>
<li>Float: add 1.0</li>
<li>String: take the last character and increment the character to the next character in alphabetical order using ord and chr.</li>
</ul>


```python
def addone(x):
    if type(x) is int:
        x += 1
    elif type(x) is float:
        x += 1.0
    elif type(x) is str:
        s = list(x)
        s[-1] = chr(ord(x[-1])+1)
        return ''.join(s)
    return x
```




    'bananb'




```python
print addone('bonfire'), addone(1), addone(3.2)
```

    bonfirf 2 4.2


Exercise 5. Create a function called commarem that removes all commas from a string.  Try to make it so that it will run faster if there is no comma in it.  Below is a timing of these two different inputs.


```python
def commarem(x):
    for i in range(len(x)):
        if x.find(',') == False:
            return x
    s = x.split(',')
    return ''.join(s)
    
print commarem('1300'), commarem('1,300')
```

    1300 1300



```python
%timeit commarem('1300')
```

    100000 loops, best of 3: 6.51 µs per loop



```python
%timeit commarem('1,300')
```

    100000 loops, best of 3: 7.92 µs per loop


Exercise 6. Create a set of the 5 lower case vowels and the set of 21 lower case consonants by removing the set of vowels from the alphabet.


```python
def consonants():
    vowels = 'aeiou'
    alphabet = "abcdefghijklmnopqrstuvwxyz"
    alphabet = alphabet.replace(vowels[0], '').replace(vowels[1], '').replace(vowels[2],'').replace(vowels[3],'').replace(vowels[4],'')
    return alphabet
print consonants()
```

    bcdfghjklmnpqrstvwxyz


Create a function called newletters that takes a string, s, and returns a list of strings formed by appending a new character to s, with the provision that that character must be a vowel if the last letter of s is a consonant and must be a consonant if the last letter of s is a vowel.


```python
def newletters(x):
    con = consonants()
    vowels = 'aeiou'
    s = []
    x = list(x)
    if (x[-1] == vowels[0] or x[-1] == vowels[1] or x[-1] == vowels[2] or x[-1] == vowels[3] or x[-1] == vowels[4]):
        x.append(con[0])
        s.append(''.join(x))
        for i in range(1,len(con)):
            s.append(''.join(x).replace((''.join(x))[-1],con[i]))
        return s
    else:
        x.append('a')
        s.append(''.join(x))
        for i in range(1,len(vowels)):
            s.append(''.join(x).replace((''.join(x))[-1],vowels[i]))
        return s
```


```python
newletters('banan')
```




    ['banana', 'benene', 'binini', 'bonono', 'bununu']




```python
words = ['hamburge','chicke','hotdog','rubarb','tahini']
```

For the words list above, using a generator expression, create a dictionary with the keys being the elements of words and the items being newletters applied to the key.


```python
words = ['hamburge','chicke','hotdog','rubarb','tahini']
newwords = dict((i, newletters(i)) for i in words)
newwords['hotdog']
```




    ['hotdoga', 'hotdoge', 'hotdogi', 'hotdogo', 'hotdogu']




```python

```
