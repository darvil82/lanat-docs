---
switcher-label: Build System
---

# Acquire Lanat

The Lanat package available on a Repsy repository and on GitHub.
You may get it from either of these sources.

## Adding the dependency to your project

Here's the repository and dependency information you need to add to your project
build file to use Lanat.

> Lanat is only supported on Java 17 and above.
{style="note"}

{ type="narrow" }
Repository
: `https://repsy.io/mvn/darvil/java`

Dependency
: `io.repsy.darvil:lanat:%latest_version%`


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
        <id>repsy</id>
        <url>https://repsy.io/mvn/darvil/java</url>
    </repository>
</repositories>
````
{switcher-key="Maven"}

Then, add the dependency to the ``dependencies`` block:

````Kotlin
dependencies {
    implementation("io.repsy.darvil:lanat:%latest_version%")
}
````
{switcher-key="Gradle"}

````XML
<dependencies>
    <dependency>
        <groupId>io.repsy.darvil</groupId>
        <artifactId>lanat</artifactId>
        <version>%latest_version%</version>
    </dependency>
</dependencies>
````
{switcher-key="Maven"}

## Manually downloading the JAR

You may also download the JAR file from [Github packages](https://github.com/darvil82/Lanat/packages).