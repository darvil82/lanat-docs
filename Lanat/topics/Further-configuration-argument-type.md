# Further configuration

As previously mentioned, argument types can not just receive a single value. When creating a new argument type,
you can specify how many values it can receive, how many times it can be used, and more.

<deflist>

<def title="Required arg value count">

Specify how many values the argument type should receive.
This is done by overriding the `getRequiredArgValueCount` method.

```Java
@Override
public @NotNull Range getRequiredArgValueCount() {
	return Range.from(2).to(10);
}
```

In this example, the argument type will require the user to provide at least 2 values, and at most 10.

<include from="Warnings.md" element-id="utils-lib">
	<var name="what" value="the Range class"/>
</include>

This will impact the number of values that the argument type can receive when its `parseValues` method is called.

</def>


<def title="Required Usage count">

Specify how many times the argument type should be used.

```Java
@Override
public @NotNull Range getRequiredUsageCount() {
	return Range.AT_LEAST_ONE;
}
```

In this example, the argument type will require the user to use it at least once, but it can be used any number of times.

> The minimum number of times can not be zero. In order to allow the argument type to not be used, mark
> the argument that will use this type as optional.

<include from="Warnings.md" element-id="utils-lib">
	<var name="what" value="the Range class"/>
</include>

This will impact the number of times the `parseValues` method will be called. For each time the argument type is used,
the `parseValues` method will be called once with the values provided by the user.

</def>

<def title="Initial Value">

When an argument type invokes the `super` constructor, it may provide an initial value for the argument type.

```Java
public MyArgumentType() {
	super(1500);
}
```

In this example, the argument type will have an initial value of `1500`. Setting the initial value also sets the current
value of the argument type.

The initial value is used in cases such as when an argument should always return a value, but the user did not provide one.
For instance, the `CounterArgumentType` always returns a value. If the user never used the argument, it will return the
initial value, which is `0`.

> The initial value can be retrieved at any time by calling the `getInitialValue` method.


</def>

</deflist>