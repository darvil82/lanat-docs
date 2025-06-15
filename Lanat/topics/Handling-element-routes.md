# Handling element routes

As we saw earlier, tags can receive [element routes](Element-routes.md) as values. Let's see how to handle these
when making a custom tag.

## The route parser

When a tag receives a route as a value, it may be forwarded to the `RouteParser` class, which is responsible for
parsing the route and returning the element it points to.

```java
var command = ArgumentParser.from(Program.class);

// element is the arugment `my-arg` of the command given
var element = RouteParser.parse(command, "args.my-arg");
```

And now nothing prevents us from using this in our tag parse method:

```java
	public class MyTag extends Tag {
		@Override
		protected String parse(NamedWithDescription user, String value) {
			return RouteParser.parse(user, value).getName();
		}
	}
```

> If `RouteParser.parse` receives a blank string or `null`, it will return the command the
> [user](Creating-custom-tags.md#what-is-the-user-of-a-tag) belongs to.
> {style="note"}


## Example

Here is a more complete example of a tag that uses the route parser to return information about some of the elements.

```java
public class MyTag extends Tag {
    @Override
    protected String parse(NamedWithDescription user, String value) {
        return switch (RouteParser.parse(user, value)) {
            case Argument<?, ?> arg -> "I'm an argument with a default value of " + arg.getDefaultValue() + ".";
            case Command cmd -> "I'm a command with " + cmd.getGroups().size()
                + " groups and " + cmd.getArguments().size() + " arguments.";
            case Group grp -> "I'm a" + (grp.isRestricted() ? " restricted" : "n unrestricted") + " group.";
            default -> "I am something else.";
        };
    }
}
```