# Type inference

Lanat is able to infer argument types from other regular types. As an example, take a look at this
[command template](Command-Templates.md):

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define(type = IntegerArgumentType.class)
	public int number;
}
```

As you can see, it is rather redundant to specify the type of the argument as `IntegerArgumentType` when the property
is of type `int`. Lanat can infer the type of the argument from the type of the property that it is assigned to.
With this feature, this code will work just as well:

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public int number;
}
```
{collapsible="true" default-state="collapsed" collapsed-title-line-number="3"}


## How it works

This feature relies on the ``ArgumentTypeInfer`` class. When an argument is defined in a command template, Lanat will
call ``ArgumentTypeInfer#get(Class<?>)`` to get a matching argument type for the property type.

Therefore, the code above is similar to this:

```Java
Argument.create(ArgumentTypeInfer.get(int.class), "number");
```


## Creating custom inferences

``ArgumentTypeInfer`` of course allows you to create custom type inferences. Let's say we have a `URLArgumentType` class
that we want to use for parsing URLs. We can create a custom inference for it like this:

```Java
ArgumentTypeInfer.register(URLArgumentType::new, URL.class);
```

Now, when an argument is defined with a property of type `URL`, Lanat will use `URLArgumentType` as the argument type:

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public URL url; // Lanat will use URLArgumentType for this argument
}
```

> It is not allowed to create inferences for the ``Optional`` type.
> {style="warning"}


### Overriding existing inferences

If you want to override an existing type inference, or simply want to remove it, you can do so by calling
``ArgumentTypeInfer#unregister(Class<?>)``.

#### Example

By default, a ``String[]`` type will be inferred as a ``TupleArgumentType`` accepting any number of values. Let's say
we want to instead only take up to 3 values. We can unregister the default inference and register our custom one:

```Java
// first unregister the default inference defined by Lanat
ArgumentTypeInfer.unregister(String[].class);

// now register our custom inference
ArgumentTypeInfer.register(
	() -> new TupleArgumentType<>(Range.from(1).to(3), new StringArgumentType()),
	String[].class
);
```

#### Another example

Let's say we want to remove the inference for `int` and use `CustomIntArgumentType` instead. We can do this:

```Java
ArgumentTypeInfer.unregisterWithPrimitive(Integer.class, int.class);
ArgumentTypeInfer.registerWithPrimitive(CustomIntArgumentType::new, Integer.class, int.class);
```

Lanat also defines inference for primitive types, so we need to unregister them as well.
This means that we could technically have a custom inference for `int` and a different one for `Integer`, but this is
not recommended as it can lead to confusion.

Use the ``withPrimitive`` variants of the methods to register/unregister inferences for primitive types.

> Note that in this example the inference ``Integer[].class`` has not been overridden, so it will still use the
> default inference made by Lanat.