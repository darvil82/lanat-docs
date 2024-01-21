---
switcher-label: Command creation method
---


# Arguments

An argument is a descriptor for a kind of value that the user can provide to the command. For example, in the command
`git commit -m "Hello World"`, the argument is `-m` and it's value is `"Hello World"`. It provides a way for the user
to provide input to the command.


## Defining Arguments

We are going to make an argument that takes an integer as input. We will call it `number`.

<chapter title="Using Argument Templates" switcher-key="Templates">

Add a new property to your command template class, and annotate it with ``@Argument.Define``.

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public int number;
}
```

</chapter>

<chapter title="Defining Arguments Imperatively" switcher-key="Traditional">

Add arguments to a command by using the ``Command#addArgument`` method:

```Java
var myCommand = new ArgumentParser("MyCommand") {{
	addArgument(Argument.create(new IntegerArgumentType(), "number"));
}};
```

</chapter>

Lanat will create an argument called `number` which will have an ``IntegerArgumentType``
as its type. This means that the user will have to provide a valid integer value as input for this argument.

In this case, the argument instance created by Lanat will have a type of ``IntegerArgumentType``, but, we never had
explicitly specified that. Lanat can infer the type of the argument from the type of the property that it is assigned to.
{switcher-key="Templates"}

We'll take a deeper look at argument types later on.