[Content from previous chapters...]

## Chapter 8: Testing with Gradle (continued)

### 8.5 Example of setting up and running unit and integration tests using Gradle (continued)

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
    integrationTestImplementation.extendsFrom testImplementation
    integrationTestRuntimeOnly.extendsFrom testRuntimeOnly
}

dependencies {
    testImplementation 'junit:junit:4.13.2'
    integrationTestImplementation 'org.testcontainers:testcontainers:1.15.3'
}

task integrationTest(type: Test) {
    description = 'Runs integration tests.'
    group = 'verification'

    testClassesDirs = sourceSets.integrationTest.output.classesDirs
    classpath = sourceSets.integrationTest.runtimeClasspath
    shouldRunAfter test
}

check.dependsOn integrationTest
```

## Chapter 9: Continuous Integration with Gradle

### 9.1 Introduction to CI/CD pipelines

Continuous Integration (CI) and Continuous Deployment (CD) are practices that involve automatically building, testing, and deploying code changes.

### 9.2 How to configure Gradle for Jenkins, Travis CI, or GitHub Actions

Jenkins Pipeline:
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh './gradlew build'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
    }
}
```

Travis CI (.travis.yml):
```yaml
language: java
install: skip
jdk:
  - openjdk11
script:
  - ./gradlew build
  - ./gradlew test
```

GitHub Actions:
```yaml
name: Java CI with Gradle

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    - name: Build with Gradle
      run: ./gradlew build
```

### 9.3 Automating builds, tests, and deployments with Gradle

```groovy
task deployToStaging(type: Exec) {
    dependsOn build
    commandLine 'scp', '-r', 'build/libs/', 'user@staging-server:/path/to/deploy'
}
```

### 9.4 Example of setting up a CI/CD pipeline using Gradle

GitHub Actions workflow for a Spring Boot application:

```yaml
name: Spring Boot CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    - name: Build with Gradle
      run: ./gradlew build
    - name: Run tests
      run: ./gradlew test
    - name: Build Docker image
      run: docker build -t myapp .
    - name: Push to Docker Hub
      run: |
        echo ${{ secrets.DOCKER_HUB_TOKEN }} | docker login -u ${{ secrets.DOCKER_HUB_USERNAME }} --password-stdin
        docker push myapp:latest
```

## Chapter 10: Advanced Gradle Features

### 10.1 Introduction to Gradle Daemon, Build Caching, and Parallel Builds

- Gradle Daemon: A long-lived process that speeds up builds
- Build Cache: Reuses outputs from previous builds
- Parallel Builds: Executes independent tasks concurrently

### 10.2 Configuring and optimizing Gradle for performance

```groovy
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2048m
```

### 10.3 Writing custom plugins and extending Gradle's functionality

```groovy
class GreetingPlugin implements Plugin<Project> {
    void apply(Project project) {
        project.task('hello') {
            doLast {
                println 'Hello from the GreetingPlugin'
            }
        }
    }
}

apply plugin: GreetingPlugin
```

### 10.4 Example of creating and publishing a custom Gradle plugin

```groovy
plugins {
    id 'java-gradle-plugin'
    id 'maven-publish'
}

gradlePlugin {
    plugins {
        greetingPlugin {
            id = 'com.example.greeting'
            implementationClass = 'com.example.GreetingPlugin'
        }
    }
}

publishing {
    repositories {
        maven {
            url = uri("https://repo.example.com")
        }
    }
}
```

### 10.5 Integrating Gradle with Docker or Kubernetes for complex build environments

```groovy
plugins {
    id 'com.palantir.docker' version '0.25.0'
}

docker {
    name "${project.name}:${project.version}"
    files 'build/libs/app.jar'
}

task deployToKubernetes(type: Exec) {
    dependsOn docker
    commandLine 'kubectl', 'apply', '-f', 'kubernetes-deployment.yaml'
}
```

## Conclusion

This comprehensive Gradle tutorial has covered a wide range of topics, from basic concepts to advanced features. Gradle is a powerful build tool that can significantly improve your development workflow, especially for complex projects.

Key takeaways:
1. Gradle offers flexibility and performance advantages over other build tools.
2. The Gradle DSL (in Groovy or Kotlin) provides a concise way to define build logic.
3. Dependency management in Gradle is powerful and flexible.
4. Plugins extend Gradle's functionality and are crucial for most projects.
5. Multi-project builds in Gradle allow for modular development.
6. Task customization and incremental builds can significantly optimize your build process.
7. The Gradle Wrapper ensures build reproducibility across different environments.
8. Gradle has robust support for testing, including integration with popular testing frameworks.
9. Gradle integrates well with CI/CD pipelines, supporting modern development practices.
10. Advanced features like the Gradle Daemon, Build Cache, and custom plugins can further enhance your build process.

Remember that Gradle is continuously evolving, so it's a good idea to keep an eye on the official Gradle documentation for the most up-to-date information and best practices. As you become more comfortable with Gradle, you'll find it to be an invaluable tool in your development toolkit.

