# Alua (Another Lua)

I created Alua from a frok of Lua 5.3.4 because I was not happy with some of its semantics and syntax.

The ALua syntax is not compatible with the Lua syntax. However, the ALua bytecode is the same as the Lua syntax, so *pre-compiled (using `aluac`) files are executable using the official `lua`**.

So, what has changed?

## 1. The development

[Lua is not openly developed.](http://lua-users.org/lists/lua-l/2008-06/msg00407.html) And that's an incontestable right of the Lua team - I'm not saying that it should accept external patches.

This repo is really open to development, and I really encourage pull requests.

## 2. The pointless `then` and `do` keywords

They have been removed. You can write conditions like this:
```lua
n = 5
if n > 100
	print("n is greater than 100")
elseif n > 0
	print("n is positive")
elseif n == 0
	print("n is null")
else
	print("n is negative")
end
```
```
n is positive
```

And you can write loops like this:
```lua
t = {a = 1, b = 2, c = 3}
a = 15
while a > 0
	for k, v in pairs(t)
		print(k, v)
		a = a - v
	end
end
```
Possible output (the order of keys returned by `pairs` may change):
```
b	2
c	3
a	1
b	2
c	3
a	1
b	2
c	3
a	1
```

## 3. The quotes

In Lua, you can create a string using both single quotes (`'`) and double quotes (`"`). Because of that, many scripts make inconsistent use of both of them. In Alua, this has been fixed by taking example on C:
* Double quotes (`"`) create *strings*.
* Single quotes (`'`) must enclose *one* character (or escape sequence), and return the integer code of that character.

## 4. Compound-assignment operators operators

Alua introduces C-like compound-assignment operators, which are `+=`, `-=` and `..=`.

For example:
```lua
a += 1
```
is exactly the same as:
```lua
a = a + 1
```

Compound-assignment operators are very useful! They are implemented by nearly every programming languages (including C, C++, Python, Ruby, Java...).

Note that unlike in C, compound-assignment operators can't be used as expressions. For example, you can't write:
```lua
local a = 2
print(a += 1)
```

Moreover, you can't make multiple assigments with compound-assignment operators. For example, the following is invalid:
```lua
a, b += 1
```

Compound-assignment operators statements are... well, statements, and must be used in the same context as the assignment (`=`) operator.

## 4. Some useful functions

### `list(...)`
Just a very little function, easy to implement, but very useful. It creates a list and sets the `table` library as its metatable.
```lua
l = list("a", "b", "c")
for i, v in ipairs(l)
	print(i, v)
end
l:insert("d")
for i, v in ipairs(l)
	print(i, v)
end
```
```
1	a
2	b
3	c
1	a
2	b
3	c
4	d
```
Creating lists using the `{}` syntax is still possible though, and is recommended if you don't need to use the `table` library on your list.

### `chartostring(char)`

This function comes with the single quotes syntax, it simply converts a character represented as an integer value to a string. It is especially useful to compare strings with characters and to print them.
```lua
c = 'A'
print(type(c), c)
print(type(tostring(c)), tostring(c))
print(type(chartostring(c)), chartostring(c))
```
```
number	65
string	65
string	A
```

### `table.copy(table)`

Returns a copy of `table`.
```lua
t = {a = 1, b = 2, c = 3}
t_ref = t
t_copy = table.copy(t)
t.a = 2
print(t_ref.a, t_copy.a)
```
```
2	1
```

### `table.find(table, value)`

This function returns the key corresponding to the first occurrence of the given value, or nil if it does not exist. This function is very useful to know if a list contains a value.

```lua
t = {a = "abc", b = 2, c = {}}
print(table.find(t, 2)) -- Prints 'b'
print(table.find(t, "abc")) -- Prints 'a'
print(table.find(t, 32)) -- Prints 'nil' because the value does not exist
print(table.find(t, {})) -- Prints 'nil' because tables are references and compared by their addresses
```
```
b
a
nil
nil
```
