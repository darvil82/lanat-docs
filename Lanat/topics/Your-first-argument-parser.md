# Your first argument parser

Let's assume we are working on a sample application that will receive two number arguments, and an
operator argument. The application will then perform the operation on the two numbers and print the
result.

We will make the operation argument **optional**, and if it is not provided, the application will default
to addition.

<procedure title="Examples">

<step>

````Console
$ my-program 5 10.12
````
The application will print `15.12`.
</step>
<step>

````Console
$ my-program 5.2 10 --op subtract
````
The application will print `-4.8`.

</step>
</procedure>

> By default, the prefix of arguments is **system dependent**. On Windows, the prefix is `/`, while on
> Linux and macOS, the prefix is `-`. For the sake of simplicity, we will assume that the prefix is
> `-` in this tutorial.
{style="note"}

In this tutorial we will not dwell into the details of all the options used. You can learn more about
them in the documentation.

## Defining the operations

Let's create an enum that will make our job easier when defining the operations.

````Java
public enum Operation {
	ADD,
	SUBTRACT,
	MULTIPLY,
	DIVIDE;
	
	public double execute(double a, double b) {
		return switch (this) {
			case ADD -> a + b;
			case SUBTRACT -> a - b;
			case MULTIPLY -> a * b;
			case DIVIDE -> a / b;
		};
	}
}
````

## Defining the Command template

Now let's create a [Command template](Command-Templates.md), which will define the structure of our command.

````Java
@Command.Define
public class MyProgram extends CommandTemplate {
	@Argument.Define(required = true, positional = true)
	public double a;
	
	@Argument.Define(required = true, positional = true)
	public double b;
	
	@Argument.Define
	public Operation op;
}
````

## The main function

Lastly, we'll define the code needed to make the program perform the desired behavior.

````Java
public static void main(String[] args) {
	var result = ArgumentParser.parseFromInto(MyProgram.class, args);

	System.out.println(result.op.execute(result.a, result.b));
}
````

> It doesn't matter where the ``main`` method is located. It can be in the command template, or in
> any other class. In this tutorial, we will put it in the command template class we created.
{style="note"}


## An issue in the way

Attempting to run the program at this point will result in an error.

````Console
Exception in thread "main" CommandTemplateException: Could not infer the argument type from the field 'op' with type 'Operation'.
	at lanat.ArgumentBuilder.setTypeFromField(ArgumentBuilder.java:243)
	at lanat.Command.lambda$from$10(Command.java:437)
	at java.base/java.lang.Iterable.forEach(Iterable.java:75)
	at lanat.Command.from(Command.java:437)
	at lanat.Command.<init>(Command.java:107)
	at lanat.ArgumentParser.<init>(ArgumentParser.java:54)
	at lanat.ArgumentParser.from(ArgumentParser.java:75)
	at lanat.ArgumentParser.parseFromInto(ArgumentParser.java:123)
	at lanat.ArgumentParser.parseFromInto(ArgumentParser.java:143)
	at MyProgram.main(MyProgram.java:40)
`````
{collapsible="true" collapsed-title="Exception: Could not infer the argument type from the field 'op' with type 'Operation'."}

````Console
Could not infer the argument type from the field 'op' with type 'Operation'.
`````


Why does this occur?

Well, this issue is being caused by the ``op`` argument. The ``op`` argument is an enum, and enums
cannot be automatically inferred by Lanat. We need to define the type of the argument manually.

[Lanat can infer](Type-inference.md) the type of many different types of arguments, including primitive types, boxed
primitive types, and even some classes. Enums cannot be inferred as they are more complex types.


## Fixing the issue

In order to fix this issue, let's head back to the ``MyProgram`` class and define the type of the
``op`` argument. In order to do this, we will define the
[``beforeInit``](Command-Templates.md#the-beforeinit-method) static method.

````Java
@InitDef
public static void beforeInit(CommandBuildContext ctx) {
	ctx.argWithType("op", new EnumArgumentType<>(Operation.ADD))
		.onOk(value -> System.out.println("Operation explicitly set to " + value));
}
````

As you can see, we not only defined the type of the argument, but we also added a callback that will
be called when the argument is successfully parsed.

> The ``beforeInit`` method is called right before the command is initialized with all the arguments.
> It receives a ``CommandBuildContext`` instance, which can be used to alter the arguments before
> they are built.

Running the program now will result in the following output:

![example-result.png](example-result.png "Result on the terminal")

> Notice how Lanat shows an error message for you when the user incorrectly provides an unknown value.
> We will learn more about how errors are handled later.


## Conclusion

That's it! You've just created your first argument parser with Lanat.
We've just scratched the surface of what Lanat can do. You can learn more about Lanat by reading
the rest of the documentation. Have fun!

### Complete example source code {collapsible="true" default-state="collapsed"}

````Java
@Command.Define
public class MyProgram extends CommandTemplate {
	@Argument.Define(required = true, positional = true)
	public double a;
	
	@Argument.Define(required = true, positional = true)
	public double b;
	
	@Argument.Define
	public Operation op;
	
	@InitDef
	public static void beforeInit(CommandBuildContext ctx) {
		ctx.argWithType("op", new EnumArgumentType<>(Operation.ADD))
			.onOk(value -> System.out.println("Operation explicitly set to " + value));
	}
	
	public enum Operation {
		ADD,
		SUBTRACT,
		MULTIPLY,
		DIVIDE;
		
		public double execute(double a, double b) {
			return switch (this) {
				case ADD -> a + b;
				case SUBTRACT -> a - b;
				case MULTIPLY -> a * b;
				case DIVIDE -> a / b;
			};
		}
	}
	
	
	// entry point here
	public static void main(String[] args) {
		var result = ArgumentParser.parseFromInto(MyProgram.class, CLInput.from(args));
	
		System.out.println(result.op.execute(result.a, result.b));
	}
}
````
