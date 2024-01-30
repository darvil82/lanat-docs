# Argument Types

When creating arguments, these must know what kind of value they are expecting to receive. Not only that, but they
also have to know how to properly convert the user input into that value.

That's the job of an argument type. An argument type is a class that extends ``ArgumentType<T>``. It defines how
to parse a number of strings into a value of type ``T``, and what occurs when the parsing fails, among other things.


## Default Argument types

Lanat comes with a set of default argument types that you can use out of the box.

`StringArgumentType`
:
Parses a single string. Essentially just forwards the user input.

`StdinArgumentType`
:
Parses the standard input stream. This is useful for commands that receive input from a pipe.

`CounterArgumentType`
:
Receives no input. It's return value is the number of times it has been used.

`BooleanArgumentType`
:
Parses a boolean value. Accepts `true`, `false`, `yes`, `no`, `1` and `0`.

`ActionArgumentType`
:
Receives no input. It returns `true` if the argument was used. This type is mostly used for when you want to make
an argument execute some code when it is used, but you don't want it to receive any input.