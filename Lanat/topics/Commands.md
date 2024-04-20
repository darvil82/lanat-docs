---
switcher-label: Command creation method
---

# Commands

Commands are an integral part of Lanat. They define the structure of arguments your program should accept as input
from the user.

A command can contain other commands, which can be used to separate different logical parts of your program, which can
have different arguments.


## Defining the command

<tldr>
See the two methods of defining commands and arguments in Lanat. One using reflection and the other using a more
traditional approach.
</tldr>

Lanat allows you to define commands in two ways. One uses the properties of a class as descriptors for the actual
arguments, which belong to a command that is represented by the class itself. The other method is more imperative and
defines the commands and arguments in a more traditional way.

Both methods have their advantages and disadvantages, which we will see in the following sections.

> Use the **switch option** at the top of the page to switch between the two methods and see the differences.
{style="note"}


### Define a Command Template { switcher-key="Templates" }

As mentioned above, this method uses the properties of a class to define the arguments of a command.
Let's take a look at an example:

````Java
@Command.Define
class MyCommand extends CommandTemplate { }
````

Here we have defined a new command called `MyCommand`. Commands defined by this method are called _Command Templates_.
They are called templates because what they do is define the structure of a command, but they are not
an actual command instance _yet_.

> A command template class must _always_ inherit from ``CommandTemplate``.
> 
> Note that a class can inherit from any other class as long as in the end of the inheritance chain there is a class that
> extends ``CommandTemplate``. We'll see why this can be useful in the next sections.
{style="note"}

In order to add an argument to a command, add a property to the class and annotate it with `@Argument.Define`:

````Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public String userName;
}
````

We just added a new argument to our command template. This argument is called `userName` and it's type is
``StringArgumentType``. This means that the user will have to provide a string as input for this argument.

#### Creating the command instance

To parse the command line arguments, you need to create an actual command instance from the command template.
In order to do this, you can use the ``ArgumentParser.from`` method:

````Java
var myCommand = ArgumentParser.from(MyCommand.class);
````

``myCommand`` is now an actual command instance which can be used to parse input from the user.


### Construct an ArgumentParser { switcher-key="Traditional" }

This method allows you to define commands and arguments in a more traditional way. It may allow you to define things
more dynamically, but it is also more verbose and less type-safe.

Let's take a look at an example:

````Java
var myCommand = new ArgumentParser("MyCommand");
myCommand.addArgument(Argument.create(new StringArgumentType(), "userName"));
````

If you compare this to the templates method, you will notice that this is a lot more verbose. However, it is also more
dynamic, as you can define arguments and commands at runtime, instead of compile time. (e.g. define an argument only
if a certain condition is met)

<tip>

You may also define the commands by subclassing ``ArgumentParser``:

```Java
var myCommand = new ArgumentParser("MyCommand") {{
	addArgument(Argument.create(new StringArgumentType(), "userName"));
}};
```

We will use this anonymous subclass syntax in the following sections.

</tip>

At this point, ``myCommand`` is now an actual command instance which can be used to parse input from the user.


## TL;DR

<tldr>
It is recommended to use the templates method to define commands and arguments.
</tldr>

The traditional method used to be the only way to define commands in Lanat. However, the templates method was added
later on to provide a more type-safe way of defining commands. In fact, the command templates system uses the traditional
approach under the hood.