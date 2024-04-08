# Description tags

Descriptions are not just limited to regular raw text. Lanat allows you to use special _tags_ in descriptions to create
richer help messages.

Tags are names enclosed in ``<`` and ``>`` characters. They may contain a value that can be supplied (if the tag
specified accepts values) by separating the tag name and the value with an equal sign. For example, ``<tag=value>``.


## Example

Let's use the ``<desc>`` tag. This tag is used to _embed_ the description of another element into the current one.

```Java
@Command.Define(description = "Description of arg1: <desc=args.arg1>")
class MyCommand extends CommandTemplate {
	@Argument.Define(description = "This is the description of arg1")
	public String arg1;
}
```

Note the ``args.arg1`` value in the ``<desc>`` tag. This is a _route_ which references the argument named ``arg1``
in the current command.

We can quickly check the result of this by using ``DescriptionParser#parse()``:

```Java
var cmd = ArgumentParser.from(MyCommand.class); // create a command instance from the template
System.out.println(DescriptionParser.parse(cmd));
```

> ``DescriptionParser`` is the class in charge of parsing descriptions with tags. It is used internally by Lanat to
> generate help messages.
> {style="note"}

The output of the code above will be:

```
Description of arg1: This is the description of arg1
```