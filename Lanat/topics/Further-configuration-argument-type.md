# Further configuration

As previously mentioned, argument types can not just receive a single value. When creating a new argument type,
you can specify how many values it can receive, how many times it can be used, and more.

<deflist>

<def title="Value count bounds">

Specify how many values the argument type should receive.
This is done by overriding the `getValueCountBounds` method.

```Java
@Override
public Range getValueCountBounds() {
	return Range.from(2).to(10);
}
```

In this example, the argument type will require the user to provide at least 2 values, and at most 10.

This will impact the number of values that the argument type can receive when its `parseValues` method is called.

</def>


<def title="Usage count bounds">

Specify how many times the argument type should be used. This is done by overriding the `getUsageCountBounds` method.

```Java
@Override
public Range getUsageCountBounds() {
	return Range.AT_LEAST_ONE;
}
```

In this example, the argument type will require the user to use it at least once, but it can be used any number of times.

> The minimum number of times can not be zero. In order to allow the argument type to not be used, mark
> the argument that will use this type as optional.

This will impact the number of times the `parseValues` method will be called. For each time the argument type is used,
the `parseValues` method will be called once with the values provided by the user.

</def>


<def title="Initial Value">

An argument type may specify an initial value by using the proper constructor overload, or by calling
the `setInitialValue` method.

```Java
public MyArgumentType() {
	super(1500);
}
```

In this example, the argument type will have an initial value of `1500`.

> When setting it by calling the super constructor overload, the current value will also be set to the initial value.
> {style="note"}

The initial value is used in cases such as when an argument should always return a value, but the user did not provide one.
For instance, the `CounterArgumentType` always returns a value. If the user never used the argument, it will return the
initial value, which is `0`.

> The initial value can be retrieved at any time by calling the `getInitialValue` method.

> The initial value is also used when resetting the state of the argument type. When the argument type is reset, the
> current value will be set to the initial value.
> {style="note"}

</def>


<def title="Name">

A short and descriptive name for the argument type. By default, the name is taken from the class name, without the
`ArgumentType` suffix.

```Java
@Override
public String getName() {
	return "url";
}
```

</def>


<def title="Representation">

The representation is a formatted string that visually represents how the argument type behaves in a simple and concise way.
By default, the representation is just the name of the argument type.

```Java
@Override
public TextFormatter getRepresentation() {
	return TextFormatter.of("url/pointing/to.something", SimpleColor.BRIGHT_MAGENTA)
		.addFormat(FormatOption.BOLD);
}
```

> Use the ``TextFormatter`` utility class to (optionally) add color, formatting, and more to the representation.
> {style="note"}

</def>


<def title="Description">

A detailed description of the argument type. This may appear in the help message when the user requests help for a command.

```Java
@Override
public String getDescription() {
	return "A valid URL path pointing to a resource.";
}
```

> By default, defined argument type descriptions do not appear for the argument in the help message. This is because
> the argument itself also has its own description. If you want to show the argument type description, you can do so
> by using the [``desc``](Default-tags.md#desc) [description tag](Description-tags.md) in the argument's description.
> {style="note"}

</def>

</deflist>


## Registering an argument subtype

An argument type may have subtypes. This is useful when an argument type needs to rely on other argument types to parse
its values.

Let's look at the implementation of `TupleArgumentType`:

```Java
public class TupleArgumentType<T> extends ArgumentType<T[]> {
	private final Range valueCount;
	private final ArgumentType<T> argumentType;

	public TupleArgumentType(Range valueCount, ArgumentType<T> argumentType) {
		this.valueCount = valueCount;
		this.registerSubType(this.argumentType = argumentType);
	}

...
```
{collapsible="true" collapsed-title-line-number="7" default-state="collapsed"}

`TupleArgumentType` is a good example of an argument type that has subtypes. It is used to parse any number of values
into an array of a specific type. It relies on another argument type to parse the values.

This is how the `parseValues` method is implemented (simplified):

```Java
@Override
public T[] parseValues(String[] values) {
	var result = new Object[values.length];

	for (int i = 0; i < values.length; i++) {
		result[i] = this.argumentType.parseValues(values[i]);
	}

	return (T[])result;
}
```

> You can technically use a subtype without registering it, but it is recommended to do so. When a subtype is registered,
> any errors added by the subtype will be automatically dispatched to the parent argument type.