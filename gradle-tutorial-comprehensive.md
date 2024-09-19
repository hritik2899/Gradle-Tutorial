# Comprehensive Gradle Tutorial

## Table of Contents
1. [Introduction to Gradle](#chapter-1-introduction-to-gradle)
2. [Understanding Gradle Build Scripts](#chapter-2-understanding-gradle-build-scripts)
3. [Dependency Management](#chapter-3-dependency-management)
4. [Gradle Plugins](#chapter-4-gradle-plugins)
5. [Multi-Project Builds](#chapter-5-multi-project-builds)
6. [Task Customization and Incremental Builds](#chapter-6-task-customization-and-incremental-builds)
7. [Gradle Wrapper](#chapter-7-gradle-wrapper)
8. [Testing with Gradle](#chapter-8-testing-with-gradle)
9. [Continuous Integration with Gradle](#chapter-9-continuous-integration-with-gradle)
10. [Advanced Gradle Features](#chapter-10-advanced-gradle-features)

## Chapter 1: Introduction to Gradle

### 1.1 Overview of build automation tools and where Gradle fits

Build automation tools are essential in modern software development, automating the process of compiling source code, packaging binary code, and running tests. Gradle is a powerful and flexible build automation tool that has gained significant popularity in recent years.

Gradle fits into the ecosystem of build tools by offering:
- A declarative DSL (Domain Specific Language) based on Groovy or Kotlin
- High performance and scalability
- Extensibility through plugins
- Support for multi-project builds
- Incremental builds to save time

### 1.2 Comparison with other build tools like Maven and Ant

Gradle vs Maven:
- Gradle uses a more concise and flexible build script compared to Maven's XML-based POM files
- Gradle offers better performance, especially for large projects
- Gradle provides more customization options and is easier to extend

Gradle vs Ant:
- Gradle provides a structured approach to builds, unlike Ant's free-form XML scripts
- Gradle includes built-in dependency management, which Ant lacks
- Gradle offers better performance and more modern features

### 1.3 Setting up Gradle on different operating systems (Windows, macOS, Linux)

To install Gradle:

Windows:
1. Download the latest Gradle binary from gradle.org
2. Extract the ZIP file to a location (e.g., C:\Gradle)
3. Add the bin folder to your PATH environment variable

macOS/Linux:
1. Use a package manager like Homebrew (macOS) or apt (Linux)
2. Run: `brew install gradle` (macOS) or `sudo apt-get install gradle` (Linux)

Alternatively, use SDKMAN! on any system:
1. Install SDKMAN!
2. Run: `sdk install gradle`

Verify installation by running `gradle -v` in your terminal.

### 1.4 Creating a basic Gradle project from scratch

To create a new Gradle project:

1. Create a new directory for your project
2. Open a terminal in that directory
3. Run: `gradle init`
4. Follow the prompts to select your project type, build script DSL, and other options

This will create a basic Gradle project structure.

### 1.5 Understanding Gradle's directory structure and basic commands

A typical Gradle project structure:

```
project-root/
├── build.gradle
├── settings.gradle
├── gradle/
│   └── wrapper/
├── src/
│   ├── main/
│   │   └── java/
│   └── test/
│       └── java/
└── build/
```

Basic Gradle commands:
- `gradle build`: Compiles and tests your code
- `gradle clean`: Deletes the build directory
- `gradle run`: Runs the application (if the application plugin is applied)
- `gradle test`: Runs the tests
- `gradle tasks`: Lists available tasks

## Chapter 2: Understanding Gradle Build Scripts

### 2.1 Introduction to Gradle build scripts (Groovy and Kotlin DSLs)

Gradle build scripts can be written in either Groovy or Kotlin DSL. Both offer a declarative way to define your build logic.

Groovy DSL example (build.gradle):
```groovy
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:2.5.0'
}
```

Kotlin DSL example (build.gradle.kts):
```kotlin
plugins {
    java
}

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web:2.5.0")
}
```

### 2.2 Understanding `build.gradle` and `settings.gradle`

- `build.gradle`: Defines the build configuration for a single project.
- `settings.gradle`: Configures which projects are included in the build. It's particularly important for multi-project builds.

### 2.3 Explanation of the key elements in a build script

- Plugins: Extend Gradle's core functionality
- Repositories: Specify where Gradle should look for dependencies
- Dependencies: Declare external libraries your project needs
- Tasks: Define units of work in a build

### 2.4 Example of creating a simple custom task using Gradle

```groovy
task hello {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

Run with: `gradle hello`

## Chapter 3: Dependency Management

### 3.1 Introduction to dependency management in Gradle

Gradle's dependency management system allows you to declare the libraries your project depends on. Gradle will automatically download and make these dependencies available to your project.

### 3.2 Understanding configurations

Gradle uses configurations to declare groups of dependencies:

- `implementation`: Dependencies required at compile and runtime
- `compileOnly`: Dependencies only needed at compile time
- `runtimeOnly`: Dependencies only needed at runtime
- `testImplementation`: Dependencies for compiling and running tests

### 3.3 How to declare dependencies

Local dependency:
```groovy
dependencies {
    implementation files('libs/local-library.jar')
}
```

Remote dependency:
```groovy
dependencies {
    implementation 'com.google.guava:guava:30.1-jre'
}
```

### 3.4 How Gradle resolves transitive dependencies

Gradle automatically resolves and downloads transitive dependencies (dependencies of your dependencies). It uses a sophisticated resolution strategy to handle version conflicts.

### 3.5 Example of managing third-party libraries in a project using Gradle

```groovy
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'com.google.guava:guava:30.1-jre'
    implementation 'org.apache.commons:commons-lang3:3.12.0'
    testImplementation 'junit:junit:4.13.2'
}
```

## Chapter 4: Gradle Plugins

### 4.1 Understanding the role of plugins in Gradle

Plugins add new tasks, configurations, dependencies, and more to your Gradle project. They encapsulate reusable build logic.

### 4.2 Overview of commonly used plugins

- Java Plugin: Adds Java compilation, testing, and bundling capabilities
- Application Plugin: Facilitates creating an executable JVM application
- Spring Boot Plugin: Provides Spring Boot support

### 4.3 How to apply and configure plugins in `build.gradle`

Using the plugins DSL:
```groovy
plugins {
    id 'java'
    id 'application'
}
```

Using the legacy method:
```groovy
apply plugin: 'java'
apply plugin: 'application'
```

### 4.4 Example of using the Java plugin to build a simple Java project

```groovy
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'junit:junit:4.13.2'
}

sourceCompatibility = '11'
targetCompatibility = '11'

jar {
    manifest {
        attributes 'Main-Class': 'com.example.Main'
    }
}
```

### 4.5 Example of using the Application plugin to create and run an executable

```groovy
plugins {
    id 'application'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'com.google.guava:guava:30.1-jre'
}

application {
    mainClass = 'com.example.Main'
}
```

Run with: `gradle run`

## Chapter 5: Multi-Project Builds

### 5.1 Introduction to multi-project builds in Gradle

Multi-project builds allow you to manage multiple related projects within a single build. This is useful for modular applications or when working with microservices.

### 5.2 Structuring a multi-project Gradle build

A typical multi-project structure:

```
root-project/
├── settings.gradle
├── build.gradle
├── project1/
│   └── build.gradle
├── project2/
│   └── build.gradle
└── project3/
    └── build.gradle
```

### 5.3 Defining dependencies between projects

In `settings.gradle`:
```groovy
rootProject.name = 'root-project'
include 'project1', 'project2', 'project3'
```

In `project2/build.gradle`:
```groovy
dependencies {
    implementation project(':project1')
}
```

### 5.4 How to configure the root project and subprojects

In root `build.gradle`:
```groovy
allprojects {
    repositories {
        mavenCentral()
    }
}

subprojects {
    apply plugin: 'java'
    
    dependencies {
        testImplementation 'junit:junit:4.13.2'
    }
}
```

### 5.5 Example of setting up a multi-module Gradle project

```groovy
// settings.gradle
rootProject.name = 'my-app'
include 'api', 'service', 'webapp'

// root build.gradle
allprojects {
    repositories {
        mavenCentral()
    }
}

subprojects {
    apply plugin: 'java'
    
    sourceCompatibility = '11'
    targetCompatibility = '11'
    
    dependencies {
        implementation 'org.slf4j:slf4j-api:1.7.30'
        testImplementation 'junit:junit:4.13.2'
    }
}

// api/build.gradle
dependencies {
    implementation 'com.google.guava:guava:30.1-jre'
}

// service/build.gradle
dependencies {
    implementation project(':api')
}

// webapp/build.gradle
plugins {
    id 'war'
}

dependencies {
    implementation project(':service')
}
```

## Chapter 6: Task Customization and Incremental Builds

### 6.1 How to define and configure custom Gradle tasks

```groovy
task copyDocs(type: Copy) {
    from 'src/main/doc'
    into 'build/target/doc'
}

task printVersion {
    doLast {
        println "Version: $version"
    }
}
```

### 6.2 Understanding task dependencies and task ordering

```groovy
task taskB {
    dependsOn taskA
    doLast {
        println 'taskB'
    }
}

taskA.finalizedBy taskC
```

### 6.3 Using up-to-date checks to enable incremental builds

```groovy
task generateReport {
    inputs.file('data.csv')
    outputs.file('report.pdf')
    doLast {
        // Generate report logic
    }
}
```

### 6.4 Example of creating a custom task that processes files incrementally

```groovy
task processFiles(type: IncrementalReverseTask) {
    inputDir = file('src/data')
    outputDir = file('build/processed')
}

class IncrementalReverseTask extends SourceTask {
    @OutputDirectory
    File outputDir

    @TaskAction
    void execute(IncrementalTaskInputs inputs) {
        inputs.outOfDate { change ->
            def targetFile = new File(outputDir, change.file.name)
            targetFile.text = change.file.text.reverse()
        }

        inputs.removed { change ->
            def targetFile = new File(outputDir, change.file.name)
            targetFile.delete()
        }
    }
}
```

## Chapter 7: Gradle Wrapper

### 7.1 What is the Gradle Wrapper and why it's important

The Gradle Wrapper is a script that invokes a declared version of Gradle, downloading it beforehand if necessary. It ensures that anyone can run the build without needing to install Gradle first.

### 7.2 How to set up and use the Gradle Wrapper

To set up the Wrapper:
```
gradle wrapper
```

This creates:
- `gradlew` (Unix) and `gradlew.bat` (Windows) scripts
- `gradle/wrapper/gradle-wrapper.jar`
- `gradle/wrapper/gradle-wrapper.properties`

Use the Wrapper instead of installed Gradle:
```
./gradlew build
```

### 7.3 Customizing the Gradle Wrapper for specific Gradle versions

In `build.gradle`:
```groovy
wrapper {
    gradleVersion = '7.0'
    distributionType = Wrapper.DistributionType.ALL
}
```

Then run:
```
gradle wrapper
```

### 7.4 Example of creating and distributing a project with the Gradle Wrapper

1. Create your project and `build.gradle`
2. Run `gradle wrapper`
3. Commit all files, including Wrapper files, to version control
4. Others can now clone and build your project using `./gradlew build` without installing Gradle

```
my-project/
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── src/
│   └── main/
│       └── java/
├── build.gradle
├── gradlew
└── gradlew.bat
```

## Chapter 8: Testing with Gradle

### 8.1 Overview of testing in Gradle

Gradle provides built-in support for testing, primarily through the Java plugin. It automatically adds a `test` task to your project and sets up a `test` source set.

### 8.2 Integrating JUnit, TestNG, and other testing frameworks

JUnit integration:
```groovy
dependencies {
    testImplementation 'junit:junit:4.13.2'
    // or for JUnit 5
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.2'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.2'
}

test {
    useJUnitPlatform()
}
```

TestNG integration:
```groovy
dependencies {
    testImplementation 'org.testng:testng:7.4.0'
}

test {
    useTestNG()
}
```

### 8.3 How to configure test tasks in Gradle

```groovy
test {
    maxHeapSize = '1G'
    systemProperty 'property', 'value'
    testLogging {
        events "passed", "skipped", "failed"
    }
}
```

### 8.4 Running and reporting tests

Run tests: `gradle test`
Generate HTML report: `build/reports/tests/test/index.html`

### 8.5 Example of setting up and running unit and integration tests using Gradle

```groovy
plugins {
    id 'java'
}

sourceSets {
    integrationTest {
        java {
            compileClasspath += main.output + test.output
            runtimeClasspath += main.output + test.output
            srcDir file('src/integrationTest/java')
        }
        resources.srcDir file('src/integrationTest/resources')
    }
}

configurations {
    integrationTest