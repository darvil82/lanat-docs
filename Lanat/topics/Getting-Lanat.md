# Getting Lanat

The Lanat package is available on [Maven Central](https://central.sonatype.com/artifact/io.github.darvil82/lanat).

> Lanat is only supported on Java 17 and above.
{style="note"}

Add the dependency to the ``dependencies`` block:

<tabs group="build-system">
<tab title="Gradle" group-key="gradle">

````Kotlin
dependencies {
    implementation("%group%:lanat:%latest_version%")
}
````

</tab>

<tab title="Maven" group-key="maven">

````XML
<dependencies>
    <dependency>
        <groupId>%group%</groupId>
        <artifactId>lanat</artifactId>
        <version>%latest_version%</version>
    </dependency>
</dependencies>
````

</tab>
</tabs>

And that's it! You can now start using Lanat in your project.