---
title: Gradle Wrapper
layout: post
categories:
- devops
---

### Gradle Wrapper
The Gradle wrapper allows you to run a Gradle task without requiring that Gradle is installed on your system.

### Creating Gradle Wrapper

```
task wrapper(type: Wrapper) {
    gradleVersion = '2.10' //we want gradle 2.10 to run this project
}
```

### Running Gradle Wrapper

`gradle wrapper`

Following files will be created:
```
|-gradle
  |--- wrapper
      |--- gradle-wrapper.jar
      |--- gradle-wrapper.properties
|-gradlew
|-gradlew.bat
```

Gradle wrapper are useful when you want to run gradle command without installing gradle
