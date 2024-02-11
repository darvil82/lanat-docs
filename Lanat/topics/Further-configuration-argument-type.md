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

</def>

</deflist>