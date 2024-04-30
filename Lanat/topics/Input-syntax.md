# Input syntax

Lanat follows the syntax you would expect from a command-line interface, while also expanding it with some other features.
A user that is familiar with other command-line programs will have no trouble using a program built with Lanat.

Let's take a deep dive into the syntax Lanat expects to parse.

## Arguments

To refer to an argument, the syntax is: (Spaces shall not be present in the actual input)
````
prefix [prefix] name
````
- The `name` must be any of the names [defined in the argument](Argument-options.md#names).
- The `prefix` must be a single character matching the [prefix of the argument](Argument-options.md#prefix).


### Argument name lists

You can refer to multiple arguments at once by listing their names after a single prefix with the following syntax:
(Spaces shall not be present in the actual input)
````
prefix n1 [n2 ...]
````
- `nX` must be any of the **single character** names [defined in the argument](Argument-options.md#names).
- The `prefix` must be a single character matching any of the [prefixes of any of the arguments](Argument-options.md#prefix)
  specified in the list, or either `-` or `/`.


## Argument values

After specifying an argument, you can provide a value to it by using the syntax:
````
argument [ = ] value
````
- When the `=` is present, no spaces should be present between these tokens.
- `value` is:
  ```
  [ tuple_start ] str1 [ str2... ] [ tuple_end ]
  ```
  - `tuple_[start|end]` are the characters that define the start and end of a [tuple](Tuples.md). They are defined by `TupleChar#current`.
  - `strX` is a string containing a value to be forwarded to the argument type.

> For [argument name lists](#argument-name-lists), a value can only be provided to the last argument in the list.
> The rest of the arguments prior to the last one must allow to receive no values.
> {style="note"}

> For [argument name lists](#argument-name-lists), the space between the last argument name and the value is optional.
> {style="note"}


<procedure title="Examples">
<step>

Argument with value:
```
--name 12
```
Expanded into:
- Argument `name`:
  - Value: `12`
  - Prefix: `-`

</step>

<step>

Argument name list:
```
-axvz 56
```
Expanded into:
- Arguments: `a`, `x`, `v`, `z`
- Value **(for z)**: `56`
- Prefix: `-`

</step>

<step>

Multiple arguments with multiple values:
```
/values 12 13 14 +t56
```
Expanded into:
- Argument `values`:
  - Values: `12`, `13`, `14`
  - Prefix: `/`
- Argument `t`:
  - Value: `56`
  - Prefix: `+`

</step>

<step>

Value with tuple:
```
--users ["Alice" "Bob" "Charlie"] /t=[1]
```
Expanded into:
- Argument `users`:
  - Value: `{"Alice", "Bob", "Charlie"}`
  - Prefix: `-`
- Argument `t`:
  - Value: `1`
  - Prefix: `/`

</step>
</procedure>



## Forward value

In some cases it may be useful to allow the user to provide a generic value to the program without specifying an argument.
This is called a forward value. The syntax is (including the space):

````
-- value
````

- `value` is a string containing _anything_. Lanat will not attempt to parse this.

<procedure title="Example">
<step>

Simple program that would run another program (`cat`) with some provided arguments forwarded to it:

```Console
$ run-with --cmd "grep" -- -r "pattern" "file.txt"
```

Expanded into:
- Argument `cmd`:
  - Value: `grep`
  - Prefix: `-`
- Forward value: `-r "pattern" "file.txt"`

</step>
</procedure>