# Creating custom tags

Lanat allows you to create custom tags to use in descriptions. A tag is basically just a parser that returns a string
based on the value given to it.

Let's create a simple tag that all that does is return a string with the name of the user and a message if given.

<procedure title="Tutorial">
<step>

Create a new class inheriting ``Tag`` and implement the ``parse`` method.

```Java
public class MyTag extends Tag {
	@Override
	protected String parse(NamedWithDescription user, String value) {
		return "My name is " + user.getName()
			+ (value == null ? "" : " and I say " + value)
			+ "!";
	}
}
```

The ``value`` parameter is the value given to the tag in the description after the `=` sign.
If no value is given, it will be ``null``.

</step>

<step>

Next register the tag so that Lanat is aware of its existence.

```Java
Tag.register("mytag", MyTag.class);
```

The first parameter is the name the user will have to use in the description, and the second is the class of the tag.

</step>

<step>

And that's it! Now you can use the tag in descriptions.

```Java
@Command.Define(description = "<mytag=Hello there>")
class MyCommand extends CommandTemplate { }
```

The description of the command will be: ``My name is MyCommand and I say Hello there!``

</step>
</procedure>


## What is the user of a tag?

<tldr>
The user of a tag is the element that the description (that contains the tag) belongs to.
</tldr>

A tag can be present in a description of an element, which can be a command, an argument, group, etc. (Anything
implementing ``NamedWithDescription``)

> `NamedWithDescription` is an interface that represents an element that has a name and a description. (Duh!)
> It is implemented by commands, arguments, groups, and other elements in Lanat.

When a tag is parsed, it receives the user of the tag as the first parameter of the ``parse`` method. This allows the tag



When a description is parsed, the target it belongs to is passed to the tag when ``parse`` is called. This allows the
tag to access this element and its properties.


## Unregistering tags

You can unregister a tag by calling ``Tag.unregister`` with the name of the tag.

```Java
Tag.unregister("mytag");
```

This will remove the tag from the list of registered tags, and it will no longer be usable in descriptions. This can
be useful to override an already registered tag with a new implementation, for example.