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

Now let's create a Command template, which will define the structure of our command.

````Java
@Command.Define(name = "my-program")
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
	var result = ArgumentParser.parseFromInto(MyProgram.class, CLInput.from(args));

	System.out.println(result.op.execute(result.a, result.b));
}
````

> It doesn't matter where the ``main`` method is located. It can be in the command template, or in
> any other class. In this tutorial, we will put it in the command template class we created.
{style="note"}


## An issue in the way

Attempting to run the program at this point will result in an error.

````Console
Exception in thread "main" java.lang.IllegalStateException: The argument must have a type defined.
	at lanat.ArgumentBuilder.build(ArgumentBuilder.java:230)
	at lanat.utils.ArgumentAdder.addArgument(ArgumentAdder.java:34)
	at java.base/java.lang.Iterable.forEach(Iterable.java:75)
	at lanat.Command.from$recursive(Command.java:371)
	at lanat.Command.<init>(Command.java:108)
	at lanat.ArgumentParser.<init>(ArgumentParser.java:54)
	at lanat.ArgumentParser.from(ArgumentParser.java:75)
	at lanat.ArgumentParser.parseFromInto(ArgumentParser.java:123)
	at lanat.ArgumentParser.parseFromInto(ArgumentParser.java:143)
	at MyProgram.main(MyProgram.java:18)
`````
{collapsible="true" collapsed-title="Exception: The argument must have a type defined."}

Why does this occur?

Well, this issue is being caused by the ``op`` argument. The ``op`` argument is an enum, and enums
cannot be automatically inferred by Lanat. We need to define the type of the argument manually.

Lanat can infer the type of many different types of arguments, including primitive types, boxed
primitive types, and even some classes. Enums cannot be inferred as they are more complex types.

## Fixing the issue

In order to fix this issue, let's head back to the ``MyProgram`` class and define the type of the
``op`` argument. In order to do this, we will define the ``beforeInit`` static method.

````Java
@InitDef
public static void beforeInit(CommandBuildHelper helper) {
	helper.<EnumArgumentType<Operation>, Operation>arg("op")
		.withArgType(new EnumArgumentType<>(Operation.ADD))
		.onOk(value -> System.out.println("Operation explicitly set to " + value));
}
````

Running the program now will result in the following output:

````Console
$ lanat-example 5 2
7.0

$ lanat-example 5 2 --op subtract
Operation explicitly set to SUBTRACT
3.0
````

## Conclusion

That's it! You've just created your first argument parser with Lanat. You can now go ahead and
experiment with the different features of Lanat.

We've just scratched the surface of what Lanat can do. You can learn more about Lanat by reading
the rest of the documentation. Have fun!

### Complete example source code {collapsible="true" default-state="collapsed"}

````Java
@Command.Define(names = "my-program")
public class MyProgram extends CommandTemplate {
	@Argument.Define(required = true, positional = true)
	public double a;
	
	@Argument.Define(required = true, positional = true)
	public double b;
	
	@Argument.Define
	public Operation op;
	
	@InitDef
	public static void beforeInit(CommandBuildHelper helper) {
		helper.<EnumArgumentType<Operation>, Operation>arg("op")
			.withArgType(new EnumArgumentType<>(Operation.ADD))
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
