## Introduction

`sbt-tasks` is an `sbt` plugin that gives `sbt` project Rails/Rake like functionality to execute tasks directly in `sbt` with the full
classpath and dependencies loaded.  This means you can do awesome things like seed and reset databases, perform
migrations and other things that are project specific.

## Requirements

- [sbt](http://www.scala-sbt.org/) 0.13

## Installation

Add the following lines to ~/.sbt/0.13/plugins/build.sbt or PROJECT_DIR/project/plugins.sbt

    resolvers += "yobreifca.se repository" at "http://yobriefca.se/maven"

    addSbtPlugin("se.yobriefca" % "sbt-tasks" % "0.2.0")

> ** Version numbers may not be accurate **

## Usage

First of all create a `Task` in the `tasks` package (this is the default place and can be configured... see later).
A `Task` is simply a class that implements `Runnable` e.g.

    package tasks

    class Printy extends Runnable {
        def run = println("Useless Printy Task")
    }

Then in your `build.sbt` or `Build.scala` file (example shows `build.sbt`) import the plugin and `install` some tasks

    import se.yobriefca.SbtTasks._
    ...
    installTask("printy")
    ...

This allows you to do `sbt printy` on the command line to run the associated `Printy` task.

If you want to also add a bit of descriptive text for your task you can call `installTask` with an extra argument

    ...
    installTask("printy", "Prints a pointless message")
    ...

### Wait so how does `sbt-tasks` know what task to run?

Good question.  It uses the defined package name (defaulting to `tasks`) and the convention of
`taskname.captialize` - so `printy` will call `tasks.Printy`.

## Configuration

There is very little configuration involved in `sbt-tasks` but you can currently modify the default package that `sbt-tasks`
will look for `Tasks` in.  We can extend our example `build.sbt` to achieve this

    import se.yobriefca.SbtTasks._
    ...
    defaultTaskPackage := "errands"
    ...
    installTask("printy")
    ...

So in this case the task resolution on `printy` will look for `errands.Printy`

## Play! 2 Integration

With a simple trait it's possible to gain access to your Play! application within your tasks which allows you to gain
access to its plugins and configuration properties

    import play.core.StaticApplication

    trait PlayTask extends Runnable {
      val application = new StaticApplication(new java.io.File("."))
    }

    class PrintyTask extends PlayTask {
        def run() = {
            val configSetting = application.configuration... // get some Play! configuration
            println(configSetting)
        }
    }

## Changes

    sbt-task 0.2.0 (19 Nov 2013)
    - Added description argument to installTask task

## License

The MIT License (MIT)

Copyright (c) 2013 James Hughes (james@yobriefca.se)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.