# Tuples

Tuples are characters that basically define delimiters for a group of values. They usually help to make the values to
forward to the argument both more readable, and explicit.

````Console
$ my-program --values [5 2 3]
````

In this example, the values `5`, `2`, and `3` are grouped together by the square brackets `[` and `]`. The square
brackets are the tuple characters.

<procedure title="Changing the tuple characters" id="changing_the_tuple_characters_to_use">
<step>

Before parsing the arguments, you can change the tuple characters to use by setting the `TupleChar#current` constant.

````Java
TupleChar.current = TupleChar.PARENTHESIS;
````

</step>

<step>

Now, the tuple characters are set to the parenthesis characters (`(` and `)`):

````Console
$ my-program --values (5 2 3)
````

</step>
</procedure>

> Changing the tuple characters can interfere with how certain terminal shells parse them. Be careful when changing them.
> {style="warning"}


## Uses of tuples

Sometimes Lanat cannot tell exactly if some token is a value or another kind of token, like a sub-command. Lanat will always
prioritize (if possible) to assume that the token is any other kind of token than a value. This is where tuples can help.

Tuples also improve the readability of the input, making it easier to understand which values is each argument receiving.

<procedure title="Case" id="cases">
<step>

Assume there is a `values` argument that can take from 2 to 5 strings. There is also a sub-command with the name `example`:

````Console
$ my-program --values 5 2 3 example
````

In this case, Lanat will assume that `5`, `2` and `3` are values, and `example` a sub-command, since `values` is already
satisfied with 3 values.

In order for `example` to be considered a value, you can use tuples:

````Console
$ my-program --values [5 2 3 example]
````

> Wrapping values in quotes also forces Lanat to consider them as values.
> ````Console
> $ my-program --values 5 2 3 "example"
> ````
> {style="note"}
</step>
</procedure>