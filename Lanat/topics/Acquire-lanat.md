---
switcher-label: Build System
---

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

````Kotlin
repositories {
    maven("https://repsy.io/mvn/darvil/java")
}
````
{switcher-key="Gradle"}

````XML
<repositories>
    <repository>
        <id>darvil</id>
        <url>%repo%</url>
    </repository>
</repositories>
````
{switcher-key="Maven"}

Then, add the dependency to the ``dependencies`` block:

````Kotlin
dependencies {
    implementation("%group%:lanat:%latest_version%")
}
````
{switcher-key="Gradle"}

````XML
<dependencies>
    <dependency>
        <groupId>%group%</groupId>
        <artifactId>lanat</artifactId>
        <version>%latest_version%</version>
    </dependency>
</dependencies>
````
{switcher-key="Maven"}

## Manually downloading the JAR

You may also download the JAR file from [Github packages](https://github.com/darvil82/Lanat/packages).