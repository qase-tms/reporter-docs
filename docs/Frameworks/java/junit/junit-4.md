---
title: Junit 4
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
**JUnit 4** is a widely-used Java testing framework, ideal for unit and integration testing. It simplifies writing and organising tests with annotations like `@Test`, `@Before`, and `@After`. Despite newer versions being available, JUnit 4 remains a popular choice, especially for existing projects.

The `qase-junit4-reporter` allows you to automatically publish your JUnit 4 test results to Qase using the public API. This makes it easy to centralize your testing data, track test outcomes, and improve overall project visibility.

<br />

## Installation

To integrate Qase into your project, follow these steps -

<br />

### Activate the JUnit4 App

1. To activate the app, go to the [Apps](https://app.qase.io/apps?app=junit4-reporter) section in your workspace, and click on ‘Activate’
2. Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token as we’ll need it for the next steps.

<br />

### Add the reporter to your project

#### For Maven Projects

To install and add the reporter as a dependency, add the following to your `pom.xml`:

```xml Maven
<properties>
  <aspectj.version>1.9.22</aspectj.version>
  <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
</properties>

<dependencies>
  <dependency>
     <groupId>io.qase</groupId>
     <artifactId>qase-junit4-reporter</artifactId>
     <version>4.1.5</version>
     <scope>test</scope>
  </dependency>
  <dependency>
     <groupId>org.junit</groupId>
     <artifactId>junit-bom</artifactId>
     <version>5.11.2</version>
     <type>pom</type>
     <scope>test</scope>
  </dependency>
  <dependency>
     <groupId>org.junit.jupiter</groupId>
     <artifactId>junit-jupiter-api</artifactId>
     <version>5.11.2</version>
     <scope>test</scope>
  </dependency>
  <dependency>
     <groupId>org.junit.jupiter</groupId>
     <artifactId>junit-jupiter-params</artifactId>
     <version>5.11.2</version>
     <scope>test</scope>
  </dependency>
</dependencies>

<build>
  <plugins>
     <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.0.0-M5</version>
        <configuration>
           <argLine>
              -javaagent:"${settings.localRepository}/org/aspectj/aspectjweaver/${aspectj.version}/aspectjweaver-${aspectj.version}.jar"
           </argLine>
           <!-- This section is required for the proper functioning of the reporter. -->
           <properties>
              <configurationParameters>
                 junit.jupiter.extensions.autodetection.enabled = true
              </configurationParameters>
           </properties>
        </configuration>
        <dependencies>
           <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>${aspectj.version}</version>
           </dependency>
        </dependencies>
     </plugin>
  </plugins>
</build>

```

<br />

#### For Gradle Projects

1. Include the following to your `build.gradle`:

```toml Gradle
configurations {
   aspectjweaver
}

tasks.withType(JavaCompile).configureEach {
   // Enables the adapter to accept real parameter names
   options.compilerArgs.add("-parameters")
}

dependencies {
   aspectjweaver "org.aspectj:aspectjweaver:1.9.22"
   testImplementation platform('org.junit:junit-bom:5.10.0')
   testImplementation 'org.junit.jupiter:junit-jupiter'
   testImplementation 'org.junit.jupiter:junit-jupiter-api'
   testImplementation 'org.junit.jupiter:junit-jupiter-params'
   testImplementation 'org.junit.jupiter:junit-jupiter-engine'
   testImplementation 'org.junit.platform:junit-platform-launcher'
   testImplementation('io.qase:qase-junit5-reporter:4.1.5')
}

test {
   systemProperties = System.properties
}

compileTestJava {
   options.getCompilerArgs().add("-parameters")
}

test.doFirst {
   useJUnitPlatform()
   // This line is required for the proper functioning of the reporter.
   systemProperty "junit.jupiter.extensions.autodetection.enabled", true
   def weaver = configurations.aspectjweaver.find { it.name.contains("aspectjweaver") }
   jvmArgs += "-javaagent:$weaver"
}


```

2. Create a `aop-ajc.xml` file in the `src/test/resources/META-INF` directory with the following content:
   ```xml
   <aspectj>
       <weaver options="-warn:none -Xlint:ignore"/>
       <aspects>
           <aspect name="io.qase.junit4.QaseJunit4Aspects"/>
       </aspects>
   </aspectj>
   ```
   <br />

Replace the file in `src/test/java/com/example/`directory (your path may differ) with the code below:

```java Java
 //IntegrationTest.java

import org.junit.Test;
import static org.junit.Assert.*;

public class IntegrationTest {

     A simple method to test
    public int add(int a, int b) {
        return a + b;
    }

    @Test
    public void testAddition() {
        AppTest app = new AppTest();
        assertEquals(5, app.add(2, 3));
    }
}

```

<br />

Now, let’s run the test, by executing the following command:

**Maven**

```Text Terminal
mvn clean test -DQASE_MODE=testops -DQASE_TESTOPS_PROJECT=<prj-code> -DQASE_TESTOPS_API_TOKEN=<app-token>
```

**Gradle**

```Text Terminal
gradle clean test -DQASE_MODE=testops -DQASE_TESTOPS_PROJECT=<prj-code> -DQASE_TESTOPS_API_TOKEN=<app-token>
```

Please refer to this [article](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) for guidance on how to find your Project code in Qase. In the above command, we’re setting the reporter’s mode with `QASE_MODE`.

<br />

You should see the following summary at the end of the execution:

```Text Terminal
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running com.example.IntegrationTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.262 s - in com.example.IntegrationTest
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.449 s
[INFO] Finished at: 2023-11-07T12:00:00Z
[INFO] ------------------------------------------------------------------------

```
