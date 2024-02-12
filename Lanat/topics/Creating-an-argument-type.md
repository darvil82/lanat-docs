# Creating an argument type

As an example, let's make a simple argument type that receives a website URL as input, and returns a ``URL`` instance.


## ArgumentType subclass

In order to create an argument type, you can create a subclass of `ArgumentType`. This is an abstract class that
provides a way to parse user input into a specific type.

```Java
class URLArgumentType extends ArgumentType<URL> {
	@Override
	public @Nullable URL parseValues(@NotNull String[] args) {
		try {
			return URI.create(args[0]).toURL();
		} catch (Exception e) {
			this.addError("Invalid URL.");
			return null;
		}
	}
}
```

`ArgumentType` accepts a single type parameter, which is the type of value that the argument type will return.
There's a single abstract method that the inheritor must always implement, which is `parseValues`. This method
receives an array of strings, which are the user input, and returns a value of type `T`.

In the case of our `URLArgumentType`, it will only ever receive a single string, because it hasn't been configured
to be able to receive more than one value. We'll see how to do that later on.


### Handling errors

When the parsing fails, the `parseValues` method should return `null`. It may also append an error message which will
be shown to the user:

````Java
this.addError("Invalid URL.");
````

In our example, we will add an error message when the URL creation throws any kind of exception.

> `ArgumentType#addError` has multiple overloads, including one that accepts an `ErrorLevel` parameter. This allows
> you to specify the severity of the error. The default severity is `ErrorLevel.ERROR`.
> We'll see more about error levels later on.


## Other ways of creating an argument type

There are other ways to create an argument type, which may be more convenient in some cases.


### SimpleArgumentType class {collapsible="true" default-state="collapsed"}

You can use the `SimpleArgumentType` class to define the behavior of your argument type by using a builder pattern.

```Java
var myType = SimpleArgumentType.of((args, errorAdder) -> {
	try {
		return URI.create(args[0]).toURL();
	} catch (Exception e) {
		errorAdder.addError("Invalid URL.");
		return null;
	}
})
	.withName("url")
	.withDescription("An absolute URL");

Argument.create(myType, "my-arg");
```

Both `withName` and `withDescription` are optional. The only required element is the function that will parse the
values. This function receives the user input and an `ErrorAdder` instance, which is used to add errors, as we've seen
before.


### Parseable implementor {collapsible="true" default-state="collapsed"}

A class may implement the `Parseable` interface, which is the interface that `ArgumentType` implements. Most methods in
this interface have default implementations, so you only need to implement the `parseValues` method.

```Java
class URLArgumentType implements Parseable<URL> {
	@Override
	public @Nullable URL parseValues(@NotNull String[] args) {
		try {
			return URI.create(args[0]).toURL();
		} catch (Exception e) {
			return null;
		}
	}
}
```

Then, when defining an argument, you can use the `FromParseableArgumentType` argument type to create an argument type
from a `Parseable` instance:

```Java
Argument.create(
	new FromParseableArgumentType<>(new URLArgumentType(), "The URL specified is invalid."),
	"my-arg
);
```

> The second argument of the `FromParseableArgumentType` constructor is the error message that will be shown to the user
> when the parsing fails. (When the `parseValues` method returns `null`)
{style="note"}

> As you may see, the main limitation of this method is that you cannot show different error messages depending on the
> input.