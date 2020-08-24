

# Gradle

## Installation

3 options
- Download the binary and set your env path to it in `~/.bash_profile`
- Eclipse can download and use a wrapper within itself
- Use gradlew script to manage project level gradle runtimes

## What is Gradle

- Opensource software
- DSL based instead of XML 
- DSL is groovy based but now Kotlin support also added
- This enables us to write direct groovy or kotlin scripts right into the build script
- Better Performance and Flexibility compared to plain old Maven
- Gradle is a command line tool kit and library (it comes with groovy compiler, etc)

## Components of Gradle

The most common components of a gradle build script are
- Projects
  Everything is a project. You would either have one or more of them. Each project is comprised of tasks pertaining to that project
- Tasks
  Task is nothing but a unit of work. It could be printing something on screen or it could be compiling the java source code
- Repository
  Gradle can point to any code repository for fetching artifacts including Maven public repos, JCenter, etc.
- Settings
  Gradle script comes with a set of settings or properties. It includes everything ranging from artifactIds, java source versions, etc.
- Its possible to migrate an existing maven project to gradle by running `gradle init` in the project location.  

## Maven Support

Gradle provides full support of maven repositories. You can specify URLs or aliases.

The most common way to specify a maven repo is 
```json
repositories {
	maven { url("some repo url") }
}
```

Note that Gradle provides certain aliases like

```json
//jCenter

repositories {
    jcenter()
}

is equivalent to

repositories {
	maven { url("http://jcenter.bintray.com/") }
}
```


```json
//mavenCenter

repositories {
    mavenCentral()
}

is equivalent to

repositories {
    maven { url("https://plugins.gradle.org/m2/") }
}	
```


```json
//mavenLocal

repositories {
	mavenLocal()
}

is equivalent to

repositories {
    maven { url '~/.m2' }
}
```


Most common way it is specified in build scripts are
```json
repositories {
	mavenLocal()
	jcenter()
	mavenCentral()
}
```

### Advantages of Jcenter over Maven

- Jcenter through the CDN service, using the https protocol, highly secured
- Jcenter is a superset of mavenCentral, including many additional jars
- Jcenter performance is better than mavenCentral

## Gradle Tasks

Gradle provides tasks. A task could be builtin ones or custom ones provided by 3rd parties or written by the developer.

Have a look at `verysimple` project.
It's build.gradle file creates a custom task called `hello`.

```json
task hello {
  // Code that goes here is *configuring* the task, and will 
    // get evaluated on *every* build invocation, no matter
    // which tasks Gradle eventually decides to execute.
    // Don't do anything time-consuming here.
  println 'task hello init'
  doLast {
    // `doLast` adds a so-called *task action* to the task.
          // The code inside the task action(s) defines the task's behavior.
          // It will only get evaluated if and when Gradle decides to 
          // execute the task.
    println 'task hello running'
    println 'hello world'
    ////
  }
}
```

You could ask gradle to show the projects involved and the tasks configured using the commands
`gradle projects`
`gradle tasks --all`

```shell
$ ls
build.gradle  settings.gradle
$ gradle projects
Starting a Gradle Daemon (subsequent builds will be faster)

> Configure project :
task hello init

> Task :projects

------------------------------------------------------------
Root project
------------------------------------------------------------

Root project 'verysimple'
No sub-projects

To see a list of the tasks of a project, run gradle <project-path>:tasks
For example, try running gradle :tasks

BUILD SUCCESSFUL in 4s
1 actionable task: 1 executed

$ gradle tasks --all

> Configure project :
task hello init

> Task :tasks

------------------------------------------------------------
Tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'verysimple'.
components - Displays the components produced by root project 'verysimple'. [incubating]
dependencies - Displays all dependencies declared in root project 'verysimple'.
dependencyInsight - Displays the insight into a specific dependency in root project 'verysimple'.
dependentComponents - Displays the dependent components of components in root project 'verysimple'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'verysimple'. [incubating]
projects - Displays the sub-projects of root project 'verysimple'.
properties - Displays the properties of root project 'verysimple'.
tasks - Displays the tasks runnable from root project 'verysimple'.

Other tasks
-----------
hello
prepareKotlinBuildScriptModel

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

## Extending tasks

Most of the time we will extending the existing tasks (knowingly or unknowingly) by changing its functionality or the 
default configurations.

## Plugins

Plugin is nothing but set of tasks. 95% of the times you need not write one, it will be out there in the open source community.
Applying a plugin to a project means that allows the plugin to extend the project’s capabilities.

### Plugin types: script and binary

There are two types of plugins in Gradle, script plugins and binary plugins. 

#### Script Plugins
Script plugins can be applied from a script on the local filesystem or at a remote location. 
Eg: In your build script,

`apply from: 'other.gradle'`

It is used to apply the other.gradle plugin to the build script.
Script plugins are rarely used. It is possible only if the source code of the plugin is available.

#### Binary Plugins
Each plugin is identified by plugin id or by it's short names.

Take a look into the following code snippet from build.gradle. It shows how to apply java plugin by using its type. Use this code in build.gradle file.
`apply plugin: JavaPlugin`

Take a look into the following code for applying core plugin using short name.
`plugins { id 'java' }`

Take a look into the following code for applying community plugin using short name.
```
plugins {
   id "com.jfrog.bintray" version "0.4.1"
}
```

Checkout `simple` project. 

- It is uses java plugin.
- The code is dead simple with JUnit 4 configured. You can uncomment and make it JUnit 5 compatible.
- There are some custom tasks defined

```
simple $ gradle myCustomTask

> Configure project :
WELCOME

> Task :test

simple.LibraryTest > testSomeLibraryMethod STANDARD_ERROR
    Aug 23, 2020 2:49:03 PM simple.LibraryTest testSomeLibraryMethod
    INFO: invoking testSomeLibraryMethod

simple.LibraryTest > testAnotherTestMethod STANDARD_ERROR
    Aug 23, 2020 2:49:03 PM simple.LibraryTest testAnotherTestMethod
    INFO: invoking testAnotherTestMethod

> Task :myCustomTask
task1 starting
task1 completed

BUILD SUCCESSFUL in 2s
4 actionable tasks: 4 executed
```

If you haven't noticed yet - notice that how the code inside doLast or doFirst is run as part of execution and everything else is part of configuration (check output of welcome task).

## BuildScript

BuildScript is used to specify whatever is needed to setup for the build script. It could range from setting certain properties or even providing endpoint for downloading & installing plugins.

```json
buildscript {
  ext {
    springBootVersion = '2.0.0.BUILD-SNAPSHOT'
  }
  repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/snapshot" }
    maven { url "https://repo.spring.io/milestone" }
  }
  dependencies {
    classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
  }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'com.siriuscom.dummy.demo'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

dependencies {
  configurations.all {
      exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
  }
  compile "org.springframework.boot:spring-boot-starter-log4j2:$springBootVersion"
  testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

In the above example we are setting certain configurations in buildscript to install spring boot gradle plugins.

## Dependencies

Dependencies are provided in the maven artifact cordinate form.


+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| Name               | Role                 | Consumable? | Resolveable? | Description                             |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| api                | Declaring            |      no     |      no      | This is where you should declare        |
|                    | API                  |             |              | dependencies which are transitively     |
|                    | dependencies         |             |              | exported to consumers, for compile.     |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| implementation     | Declaring            |      no     |      no      | This is where you should                |
|                    | implementation       |             |              | declare dependencies which are          |
|                    | dependencies         |             |              | purely internal and not                 |
|                    |                      |             |              | meant to be exposed to consumers.       |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| compileOnly        | Declaring compile    |     yes     |      yes     | This is where you should                |
|                    | only                 |             |              | declare dependencies                    |
|                    | dependencies         |             |              | which are only required                 |
|                    |                      |             |              | at compile time, but should             |
|                    |                      |             |              | not leak into the runtime.              |
|                    |                      |             |              | This typically includes dependencies    |
|                    |                      |             |              | which are shaded when found at runtime. |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| runtimeOnly        | Declaring            |      no     |      no      | This is where you should                |
|                    | runtime              |             |              | declare dependencies which              |
|                    | dependencies         |             |              | are only required at runtime,           |
|                    |                      |             |              | and not at compile time.                |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| testImplementation | Test dependencies    |      no     |      no      | This is where you                       |
|                    |                      |             |              | should declare dependencies             |
|                    |                      |             |              | which are used to compile tests.        |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| testCompileOnly    | Declaring test       |     yes     |      yes     | This is where you should                |
|                    | compile only         |             |              | declare dependencies                    |
|                    | dependencies         |             |              | which are only required                 |
|                    |                      |             |              | at test compile time,                   |
|                    |                      |             |              | but should not leak into the runtime.   |
|                    |                      |             |              | This typically includes dependencies    |
|                    |                      |             |              | which are shaded when found at runtime. |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+
| testRuntimeOnly    | Declaring test       |      no     |      no      | This is where you should                |
|                    | runtime dependencies |             |              | declare dependencies which              |
|                    |                      |             |              | are only required at test               |
|                    |                      |             |              | runtime, and not at test compile time.  |
+--------------------+----------------------+-------------+--------------+-----------------------------------------+

I always go with implementation and testImplementation for spring-boot projects. 
In case of traditional spring projects, artifacts like the servlet-3.x jars are provided by the runtime server hence only needed at compile time. Use compileOnly for those.

For Java developers familiar with Maven, compile-only dependencies function similarly to Maven’s provided scope, allowing you to declare non-transitive dependencies used only at compilation time.

Dependencies between your projects can be parent or sibiling.
An example is `advanced` project. Check it out.

```
Root project 'advanced'
+--- Project ':commons'
\--- Project ':web'
```

## Gradle Debug

`gradle run --debug-jvm`

## Performance

- Gradle runs a deamon if not up already for each project builds. The deamon has TTL of 4 hrs
- The deamon does a lot of things like caching
- It does everything incremental even your tests - it won’t run a test unless code has changed. This is exactly why you end up running clean sometimes.
- Gradle supports Groovy and Kotlin DSL
