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
	@EnumArgumentType.Default
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

> Notice the `@EnumArgumentType.Default` annotation. This annotation makes the `ADD` operation the default value, which
> is what we want in this case.
> {style="note"}

## Defining the Command template

Now let's create a [Command template](Command-templates.md), which will define the structure of our command.

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

Lastly, we'll write the main function that will parse the arguments and execute the operation.

````Java
public static void main(String[] args) {
	var result = ArgumentParser.parseFromInto(MyProgram.class, args);

	System.out.println(result.op.execute(result.a, result.b));
}
````

> It doesn't matter where the ``main`` method is located. It can be in the command template, or in
> any other class. In this tutorial, we will put it in the command template class we created.
{style="note"}


## Result

Running the program now will result in the following output:

![Result on the terminal](example-result.png){width="700"}

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
	
	public enum Operation {
		@EnumArgumentType.Default
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
		var result = ArgumentParser.parseFromInto(MyProgram.class, args);
	
		System.out.println(result.op.execute(result.a, result.b));
	}
}
````
