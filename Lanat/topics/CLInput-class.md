# CLInput class

``CLInput`` is a utility class that provides a few ways to indicate where to grab the user input from. It is used in the
``ArgumentParser#parse`` method to parse the input from the user.

## Usage

You use the ``from`` static methods to create a ``CLInput`` instance. The following methods are available:

<deflist>
<def title="from(String[])">

Reads the input from the given array of strings.

</def>

<def title="from(String)">

Reads the input from the given string.

</def>

<def title="fromSystemProperty()">

Reads the input from the system property ``sun.java.command``.

</def>

<def title="fromFile(File)">

Reads the input from the given file. The input will be the contents of the file. If the file does not exist,
the input will be an empty string.

</def>

<def title="fromStandardInput()">

Reads the input from the standard input. The input will be the first line read.

</def>

</deflist>