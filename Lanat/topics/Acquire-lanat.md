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

### Gradle example { collapsible="true" default-state="collapsed" }

````Kotlin
repositories {
    maven("https://repsy.io/mvn/darvil/java")
}
````

````Kotlin
dependencies {
    implementation("io.repsy.darvil:lanat:%latest_version%")
}
````

### Maven example { collapsible="true" default-state="collapsed" }

````XML
<repositories>
    <repository>
        <id>repsy</id>
        <url>https://repsy.io/mvn/darvil/java</url>
    </repository>
</repositories>
````

````XML
<dependencies>
    <dependency>
        <groupId>io.repsy.darvil</groupId>
        <artifactId>lanat</artifactId>
        <version>%latest_version%</version>
    </dependency>
</dependencies>
````

## Manually downloading the JAR

You may also download the JAR file from [Github packages](https://github.com/darvil82/Lanat/packages).