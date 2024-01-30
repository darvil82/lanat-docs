# Creating an argument type

As an example, let's make a simple argument type that receives a website URL as input, and returns a ``URL`` instance.

```Java
class URLArgumentType extends ArgumentType<URL> {
	public URLArgumentType() { }

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