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

We'll now take a look at how to use this command template to create an actual command instance.


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

> Please note that you will need to have the [utils library](https://github.com/darvil82/java-utils) in your classpath
> in order to use this syntax. This is because subclasses of ``ArgumentParser`` need to have access to some utility
> classes from that library.
{style="warning"}

</tip>

At this point, ``myCommand`` is now an actual command instance which can be used to parse input from the user.
Let's take a look at how to do that.


## Using the command

Now that we have defined a command, we can use it to parse input from the user.


### Use the template { switcher-key="Templates" }

Lanat provides a utility method in ``ArgumentParser`` which allows you to construct a command instance from a command
template class.

```Java
void main(String[] args) {
	MyCommand input = ArgumentParser.parseFromInto(MyCommand.class, args);
}
```

Note how ``input`` is now an instance of ``MyCommand``. This means that you can access the values the arguments have
received from the user by naturally accessing the properties of the class.

```Java
...
System.out.println("Hello, " + input.userName + "!");
```

This provides a type-safe way of accessing the arguments, as you already have the type of the argument in the property
type.


### Parse the input { switcher-key="Traditional" }

In order to parse the input from the user, you can use the ``parse`` method of the ``ArgumentParser`` instance
you just made.

```Java
void main(String[] args) {
	var myCommand = new ArgumentParser("MyCommand");
	myCommand.addArgument(Argument.create(new StringArgumentType(), "userName"));
	
	// Parse the input
	ParseResultRoot input = myCommand.parse(CLInput.from(args)).getResult();
}
```

For now, we will only gathered the input by calling ``getResult`` on the `AfterParseOptions` instance returned by the
``parse`` method. This returns us a ``ParseResultRoot`` instance, which allows us to access the parsed values.

```Java
...
System.out.printf("Hello, %s!%n", input.get("userName").get());
```

As we previously mentioned, this method is not type-safe. ``get`` here is returning an ``Optional`` of ``Object`` since
at this point we don't know what the type of the argument is. Therefore, this can't work:

```Java
String userName = input.get("userName").get();
```

However, we can use the type parameter of ``get`` to prevent us from having to cast the value:

```Java
var userName = input.<String>get("userName").get();
```

> Note that this is still **not type-safe**, as you could have provided a value of a different type for the argument.
>
> If the type differs from the one you specified, a ``ClassCastException`` will be thrown.
{style="warning"}


## Conclusion

<tldr>
It is recommended to use the templates method to define commands and arguments.
</tldr>

The traditional method used to be the only way to define commands in Lanat. However, the templates method was added
later on to provide a more type-safe way of defining commands. In fact, the command templates system uses the traditional
approach under the hood.

<tip>

Something important to mention is that while we properly defined the ``userName`` argument, we don't _properly_
handle the case where the user doesn't provide a value for it.

We print the value of ``input.userName``, but if the user doesn't provide a value for it, the value of the property
will be ``null``. In this case, this will print ``Hello, null!``. In worse cases, this could cause a
``NullPointerException``. We'll see how to make this better in the next section.
{switcher-key="Templates"}

``input.get("userName")`` returns an ``Optional`` of ``Object``, then we call ``get`` on it to get the actual value.
If the user doesn't provide a value for the argument, the ``Optional`` will be empty, and calling ``get`` on it will
throw a ``NoSuchElementException``.
{switcher-key="Traditional"}

</tip>