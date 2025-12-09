# Argument types

When creating arguments, these must know what kind of value they are expecting to receive. Not only that, but they
also have to know how to properly convert the user input into that value.

That's the job of an argument type. An argument type is a class that extends `ArgumentType<T>`. It defines how
to parse a number of strings into a value of type `T`, and what occurs when the parsing fails, among other things.


## Default argument types

Lanat comes with a set of default argument types that you can use out of the box.

<deflist>

<def title="StringArgumentType" id="StringArgumentType">
	Parses a single string. Essentially just forwards the user input.
</def>

<def title="StdinArgumentType" id="StdinArgumentType">
	Parses the standard input stream. This is useful for commands that receive input from a pipe.
</def>

<def title="CounterArgumentType" id="CounterArgumentType">
	Receives no input. Its return value is the number of times it has been used.
</def>

<def title="BooleanArgumentType" id="BooleanArgumentType">
	Parses a boolean value. Accepts <code>true</code>, <code>false</code>, <code>yes</code>, <code>no</code>, <code>1</code> and <code>0</code>.
</def>

<def title="ActionArgumentType" id="ActionArgumentType">
	Receives no input. It returns <code>true</code> if the argument was used. This type is mostly used for when you want
	to make an argument execute some code when it is used, but you don't want it to receive any input.
</def>

<def title="TupleArgumentType" id="TupleArgumentType">
	Parses multiple values received from the user by using the argument type provided in the constructor.
	Returns a <code>T[]</code>.
</def>

<def title="NumberRangeArgumentType" id="NumberRangeArgumentType">
	Parses a number within a given range. The range is given in the constructor.
</def>

<def title="KeyValuesArgumentType" id="KeyValuesArgumentType">
	Takes key-value pairs. The key is a string, and the value is of the argument type given in the
	constructor. The key-value pairs are separated by an equals sign (e.g. <code>key=value</code>).
</def>

<def title="EnumArgumentType" id="EnumArgumentType">
	An argument type that takes a valid enum value.
	<p>
		The user can specify any of the enum values by their names.
		The names are case-insensitive.
	</p>

	
> In the enum class, one of the values may be annotated with <code>@EnumArgumentType.Default</code>, which will mark
> the value to be used when the user doesn't provide any.

> If needed, you can change the name of an enum value that the user must provide by using the
> <code>@EnumArgumentType.WithName</code> annotation.

</def>

<def title="OptListArgumentType" id="OptListArgumentType">
	An argument type that restricts a possible value out of a list of values.
</def>

<def title="TryParseArgumentType" id="TryParseArgumentType">
	Attempts to parse a string into the type given in the constructor.
	The type given must have a static <code>valueOf(String)}</code>, <code>parse(String)}</code>, or
	<code>from(String)}</code> method, or a constructor that takes a string.
	If none of these are found, an exception will be thrown.
	<tip>
		This argument type will first attempt to infer the argument type from the type given in the constructor,
		and if it fails to do so, it will use the method approach.
	</tip>
</def>

</deflist>


### Numeric types

Lanat provides a set of argument types to parse all kinds of numeric values. Some of them are: <code>IntegerArgumentType</code>,
<code>LongArgumentType</code>, <code>FloatArgumentType</code>, <code>DoubleArgumentType</code>, etc.


### Definition helpers

These argument types provide ways to define new argument types.

<deflist>

<def title="FromParseableArgumentType" id="FromParseableArgumentType">
	An argument type that uses a <code>Parseable</code> to parse values. If the <code>parseValues(String[])</code>
	method of the given <code>Parseable</code> returns <code>null</code>, an error is added.
	The error message can be specified in the constructor.
</def>

<def title="SimpleArgumentType" id="SimpleArgumentType">
	An argument type that allows to define its behavior by using a builder pattern. This basically provides another
	way to define an argument type.
</def>

</deflist>

Check out [how to create argument types](Creating-an-argument-type.md#other-ways-of-creating-an-argument-type) for more 
information on how to create your own argument types.