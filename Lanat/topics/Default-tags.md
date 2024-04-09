# Default tags

Lanat provides a few tags that are ready to be used in descriptions:


<deflist>

<def title="link">

Gets the representation of the target object specified by the route.

Example:

```Java
@Command.Define(
  description = "This is a link to the argument: <link=args.name>"
)
class MyCommand extends CommandTemplate {
    @Argument.Define(required = true, positional = true)
    public String name;
}
```

Shows as:

![Representation of the example](tags-repr1.png)

</def>

<def title="desc" id="desc">
Gets the description of the target object specified by the route.

> Note that targeting the user itself is not allowed, as it would create an infinitely recursive description. Keep in
mind that it is still possible to infinitely recurse by implicitly targeting the user, for example, by making an
argument show the description of another argument, which in turn shows the description of the first argument too.
This would eventually cause a ``StackOverflowError``.
> {style="warning"}

Example:

```Java
@Command.Define(
  description = "This is a link to the argument: <link=args.name>"
)
class MyCommand extends CommandTemplate {
    @Argument.Define(
      required = true, positional = true,
      description = "Description of the command: <desc>"
    )
    public String name;
}
```

Shows as:

![Representation of the example](tags-repr2.png)


</def>

<def title="color">

Changes the color of the text.

The syntax for specifying colors is `foreground[:background]`, where ``foreground`` and ``background``
follow the syntax ``color_name|#rrggbb|r,g,b``.

* `#rrggbb` is the hexadecimal value of the color.
* `r,g,b` are the RGB values of the color.
* `color_name` is a case-insensitive color name. The values that may be used are:
  * black
  * red
  * green
  * yellow
  * blue
  * magenta
  * cyan
  * white
  * gray / grey
  * dark red
  * dark green
  * dark yellow
  * dark blue
  * dark magenta
  * dark cyan
  * dark white

Example:

```Java
"<color=red>This is red text, <color=#00ff7e:black>and this is green text on a black background."
```

Which shows as:

![Representation of the example](tags-repr3.png)

</def>

<def title="format">

Changes the format of the text. (e.g. `<format=bold>`). The format value is case-insensitive.

The syntax for specifying formats is `format[,format,...]` where `format` is `[!]format_name`.
`!` is used to reset the format of that kind.

The values that may be used are:
* reset
* bold / b
* italic / i
* underline / u
* strikethrough / s
* dim
* blink
* reverse
* hidden

Example:

```Java
"<format=bold,italic>This is bold and italic text, <format=!italic>and this is just bold text. <format=reset>And this is normal text."
```

Which shows as:

![Representation of the example](tags-repr4.png)

</def>

<def title="default">

Gets the default value of an argument.

Returns `Default value: <value>`.

If a value is given, it is used. Otherwise, the default value of the argument is used.

> This tag can only be used on descriptions belonging to arguments.
> {style="warning"}

</def>

</deflist>