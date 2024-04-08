# Default tags

Lanat provides a few tags that are ready to be used in descriptions:


<deflist>

<def title="link">
Gets the representation of the target object specified by the route.
</def>

<def title="desc" id="desc">
Gets the description of the target object specified by the route.

> Note that targeting the user itself is not allowed, as it would create an infinitely recursive description. Keep in
mind that it is still possible to infinitely recurse by implicitly targeting the user, for example, by making an
argument show the description of another argument, which in turn shows the description of the first argument too.
This would eventually cause a ``StackOverflowError``.
> {style="warning"}
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

</def>

<def title="format">

Changes the format of the text. (e.g. `<format=bold>`). The format value is case-insensitive.

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

> The tag can receive multiple format values, separated by commas. (e.g. `<format=bold,italic>`).
If the format value is preceded by an exclamation mark, the format of that kind will be reset. (e.g. `<format=!bold>`).
Both can be used together. For example, in `<format=bold,!italic>`), the text will be bold and the italic format will be reset.
> {style="note"}

</def>

<def title="default">

Gets the default value of an argument.

Returns `Default value: <value>`.

If a value is given, it is used. Otherwise, the default value of the argument is used.

> This tag can only be used on descriptions belonging to arguments.
> {style="warning"}

</def>

</deflist>