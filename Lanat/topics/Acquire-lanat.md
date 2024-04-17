# Acquire Lanat

The Lanat package is available on a Repsy repository and on GitHub.
You may get it from either of these sources.

> **IMPORTANT**
> 
> This documentation refers to the version `1.0.0`, which is still in development and not yet released.
> {style="warning"}

## Adding the dependency to your project

Here's the repository and dependency information you need to add to your project
build file to use Lanat.

> Lanat is only supported on Java 17 and above.
{style="note"}

{ type="narrow" }
Repository
: `%repo%`

Dependency
: `%group%:lanat:%latest_version%`


### Example

Add the repository to the ``repositories`` block:

<tabs group="build-system">
<tab title="Gradle" group-key="gradle">

````Kotlin
repositories {
    maven("%repo%")
}
````

</tab>

<tab title="Maven" group-key="maven">

````XML
<repositories>
    <repository>
        <id>darvil</id>
        <url>%repo%</url>
    </repository>
</repositories>
````

</tab>
</tabs>



Then, add the dependency to the ``dependencies`` block:

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


## Manually downloading the JAR

You may also download the JAR file from [Github packages](https://github.com/darvil82/Lanat/packages).