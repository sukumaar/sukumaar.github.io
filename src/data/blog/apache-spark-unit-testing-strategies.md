---
title: "Apache Spark Unit Testing Strategies"
description: "#scala #programming #apachespark #bigdata"
pubDatetime: 2024-12-05T00:00:00Z
author: "Sukumaar"
image: "../../assets/images/apache-spark-unit-testing-strategies-01.jpg"
tags: ["tutorial","bigdata-cloud"]
draft: false
featured: false
---
> Recipe/Guide about writing unit tests for Apache Spark with Scala (mainly for beginners).

**Recipe complexity level:** ◼️◻️◻️◻️

**Recipe prerequisite:**
- Some knowledge of Big Data, Apache Spark, Scala, Java.

**Recipe ingredients:**
- Your favorite IDE : Intellij or VSCode (with Metals)
- sbt / maven installed (sbt is used in this tutorial)
- jdk 8
- scala 2.12 😎

### Unit testing ?
In computer programming, unit testing is a software testing method by which individual units of source code—sets of one or more computer program modules together with associated control data, usage procedures, and operating procedures—are tested to determine whether they are fit for use Wikipedia

Writing unit tests of the code before writing the actual code is a brilliant strategy used in TDD.

### TDD ?
Test-driven development (TDD) is a software development process relying on software requirements being converted to test cases before software is fully developed, and tracking all software development by repeatedly testing the software against all test cases. Wikipedia

I will skip writing about the advantages of writing unit tests or the advantages of TDD (because there are so many that I need to write a separate article for it.)

This code sample uses (super awesome) Scalatest 😎🤩 testing framework.

### ScalaTest:

- It is the most flexible and most popular testing tool in the Scala ecosystem.link
- With so many other features, it allows designing tests with multiple styles.
    - ScalaTest supports different styles of testing, each designed to address a particular set of needs. link
    - There are separate traits for these styles.
    - JUnit lovers can use the `AnyFunSuite` trait.

___
Project Creation:
Directory structure of my project:
```bash
.
├── build.sbt
└── src
    ├── main
    │   └── scala
    │       └── sukumaar
    │           └── App.scala #This doesn't have any imp code
    └── test
        └── scala
            └── sukumaar
                ├── AppTest.scala
                └── TraitSparkSessionTest.scala
```

`build.sbt` I used :
```scala
name := "sample-spark-scala-project"
version := "1.0"
scalaVersion := "2.12.13"

val sparkVersion = "2.4.0"

libraryDependencies += 
    "org.apache.spark" %% "spark-core" % sparkVersion
libraryDependencies += 
    "org.apache.spark" %% "spark-sql" % sparkVersion
libraryDependencies += 
    "org.scalatest" %% "scalatest" % "3.2.9" % Test

/*
// you can always use this dependency if you are 
// going to use only funsuite
libraryDependencies += 
    "org.scalatest" %% "scalatest-funsuite" % "3.2.11" % "test"
*/
```
Import this project to your favorite IDE.
If you prefer CLI (like a mature developer 😛) then use this command:
`sbt clean compile`

The steps I followed:

of course, you can change package name, if you do then you have to change directory name accordingly in previous step

Step 1: Add this to TraitSparkSessionTest.scala
```scala
package sukumaar
trait TraitSparkSessionTest {}
```
Step 2: Add this to TraitSparkSessionTest.scala
```
package sukumaar

import org.apache.spark.sql.SparkSession

trait TraitSparkSessionTest {

  protected val sparkSession = SparkSession
    .builder()
    .appName("sample-spark-scala-project")
    .master("local[2]")
    .getOrCreate()       
}
```
Step 3: Add this to AppTest.scala
```
package sukumaar
class AppTest {}
```
The trick is sparkSession object must be used in all the test classes wherever spark test cases are present, unless there is a use case to use more than one spark session.
As this object is a part of TraitSparkSessionTest trait, this trait can be easily used as a mixin to mix with the test classes.

Step 4: Add this to `AppTest.scala`

![Code Example](../../assets/images/apache-spark-unit-testing-strategies-01.jpg)

Done. Now go and run your tests 😇

Full source code link: [spark-scala-unit-test-example](https://github.com/sukumaar/spark-scala-unit-test-example)
