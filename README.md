# Example sbt project that compiles using Dotty

[![Build Status](https://travis-ci.org/scala/scala3-example-project.svg?branch=master)](https://travis-ci.org/scala/scala3-example-project)

## Usage

This is a normal sbt project, you can compile code with `sbt compile` and run it
with `sbt run`, `sbt console` will start a Dotty REPL.

If compiling this example project fails, you probably have a global sbt plugin
that does not work with dotty, try to disable all plugins in
`~/.sbt/1.0/plugins` and `~/.sbt/1.0`.

### IDE support

Dotty comes built-in with IDE support, to try it out see
http://dotty.epfl.ch/docs/usage/ide-support.html

## Making a new Dotty project

The fastest way to start a new Dotty project is to use one of the following templates:

* [Simple Dotty project](https://github.com/scala/scala3.g8)
* [Dotty project that cross-compiles with Scala 2](https://github.com/scala/scala3-cross.g8)

## Using Dotty in an existing project

You will need to make the following adjustments to your build:

### project/plugins.sbt

```scala
addSbtPlugin("ch.epfl.lamp" % "sbt-dotty" % "0.5.2")
```

### project/build.properties

```scala
sbt.version=1.4.7
```

You must use sbt 1.4 or newer; older versions of sbt are not supported.

### build.sbt

Any version number that starts with `0.` is automatically recognized as Dotty by
the `sbt-dotty` plugin, you don't need to set up anything:

```scala
scalaVersion := "3.0.0-RC1"
```

#### Nightly builds

If the latest release of Dotty is missing a bugfix or feature you need, you may
wish to use a nightly build. Look at the bottom of
https://repo1.maven.org/maven2/org/scala-lang/scala3-compiler_3.0.0-RC1/ to find the version
number for the latest nightly build. Alternatively, you can set `scalaVersion :=
dottyLatestNightlyBuild.get` to always use the latest nightly build of dotty.

## Getting your project to compile with Dotty

When porting an existing project, it's a good idea to start out with the Scala 2
compatibility mode (note that this mode affects typechecking and thus may
prevent some valid Dotty code from compiling) by adding to your `build.sbt`:

```scala
scalacOptions ++= { if (isDotty.value) Seq("-source:3.0-migration") else Nil }
```

Using the `isDotty` setting ensures that this option will only be set when
compiling with Dotty. For more information on the `-source` flag, see
http://dotty.epfl.ch/docs/usage/language-versions.html, for more information on
migrating to Scala 3 see [the migration
guide](https://github.com/scalacenter/scala-3-migration-guide).

If your build contains dependencies that have only been published for Scala 2.x,
you may be able to get them to work on Dotty by replacing:

```scala
    libraryDependencies += "a" %% "b" % "c"
```

by:

```scala
    libraryDependencies += ("a" %% "b" % "c").withDottyCompat(scalaVersion.value)
```

This will have no effect when compiling with Scala 2.x, but when compiling
with Dotty this will change the cross-version to a Scala 2.x one. This
works because Dotty is currently retro-compatible with Scala 2.x.

Alternatively, to set this setting on all your dependencies, you can use:

```scala
    libraryDependencies := libraryDependencies.value.map(_.withDottyCompat(scalaVersion.value))
```

## Discuss

Feel free to come chat with us on the
[Dotty gitter](http://gitter.im/lampepfl/dotty)!
