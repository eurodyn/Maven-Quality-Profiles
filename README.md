This is a mini-project in which we try to package sensible
defaults for source code quality control profiles. The aim of this project is not
to define a global styleguide (although somehow indirectly it does), but
to provide you Maven-ready setups for some of the major
quality/style-checking tools.

Using the artifacts of this project you can quickly integrate checks in
the Maven build of your project without having to invest too much time
setting them up.

The tools we currently support are:  

* [PMD](https://pmd.github.io)
* [Checkstyle](http://checkstyle.sourceforge.net)
* [FindBugs](http://findbugs.sourceforge.net/)
* [OWASP Maven Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check)

__Opinion - everyone has one__:
You might have noticed on the above paragraphs we mentioned
"_sensible defaults_". We understand perfectly that what one finds
sensible may be totally insensible to someone else and, quite literally,
there is no one-size-fits-all approach here. If you find a rule to
restrictive (and can justify it), or if you find an ignored rule that
needs to be enforced (and can justify it), please use the
[Issues Tracking](https://github.com/eurodyn/Maven-Quality-Profiles/issues)
of the project to let us know.

The artifacts of this project are
[published in Maven Central](http://search.maven.org/#search%7Cga%7C1%7Ccom.eurodyn.qp),
so you can use it in your project as any other dependency:
[Maven Central - Quality Profiles]().

## PMD
For PMD we provide a `ed-qp-pmd-ruleset.xml` ruleset that can be
used in your project's build configuration. The ruleset comes with all
Java rules enabled except the ones documented in the ruleset file itself.

### Project integration
```
...
<plugins>
    ...
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-pmd-plugin</artifactId>
        <version>${maven-pmd-plugin.version}</version>
        <configuration>
            <rulesets>
                <ruleset>ed-qp-pmd-ruleset.xml</ruleset>
            </rulesets>
            <printFailingErrors>true</printFailingErrors>
            <linkXRef>false</linkXRef>
            <sourceEncoding>UTF-8</sourceEncoding>
        </configuration>
        <executions>
            <execution>
                <id>pmd</id>
                <phase>verify</phase>
                <goals>
                    <goal>check</goal>
                </goals>
            </execution>
        </executions>
        <dependencies>
            <dependency>
                <groupId>com.eurodyn.qp</groupId>
                <artifactId>ed-qp-pmd</artifactId>
                <version>${ed-qp.version}</version>
            </dependency>
        </dependencies>
    </plugin>
    ...
</plugins>
```

__Gotcha__: The above configuration will fail your build in case PMD errors exist,
which is a good thing on a new project. However, if you want to introduce
PMD checks on an existing project where you might not have the luxury of
time to correct all mistakes at once, you can add
on the `<configuration>` section `<failOnViolation>false</failOnViolation>`.

## Checkstyle
For Checkstyle we provide a `ed-qp-checkstyle.xml` configuration file
following the [Google Java Style Guide](http://google.github.io/styleguide/javaguide.html)
taken from [Checkstyle project](https://github.com/checkstyle/checkstyle/tree/master/src/main/resources).
Any rule that may be disabled on the above configuration template is
documented on the configuration file.

__Note to flame-warlords__: We understand there is an endless debate between
Google style vs Apache style, tabs vs spaces, etc. but, we believe, the
purpose of a Style Guide is not to declare "the best" styling for your
source code but to ensure that the source within your project (or entire
organisation) looks coherent. So, just pick a style guide you feel
comfortable with and stick with it; that is what we did here.

__Style as you type__: Are you using [IntelliJ IDEA](https://www.jetbrains.com/idea/)
 or [Eclipse](https://eclipse.org/ide/) IDE? You can set your default
 styling to Google's Style Guide [here](https://github.com/google/styleguide).

### Project integration
```
...
<plugins>
    ...
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <version>${maven-checkstyle-plugin.version}</version>
        <executions>
            <execution>
                <id>checkstyle</id>
                <phase>validate</phase>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <consoleOutput>true</consoleOutput>
                    <configLocation>ed-qp-checkstyle.xml</configLocation>
                </configuration>
                <goals>
                    <goal>check</goal>
                </goals>
            </execution>
        </executions>
        <dependencies>
            <dependency>
                <groupId>com.eurodyn.qp</groupId>
                <artifactId>ed-qp-checkstyle</artifactId>
                <version>${ed-qp.version}</version>
            </dependency>
        </dependencies>
    </plugin>
    ...
</plugins>
```

__Gotcha__: The above configuration will fail your build in case Checkstyle
 errors exist, which is a good thing on a new project. However, if you want
 to introduce checks on an existing project where you might not have the
 luxury of time to correct all mistakes at once, you can add on the
 `<configuration>` section `<failsOnError>false</failsOnError>`.

## FindBugs
[FindBugs](http://findbugs.sourceforge.net/) does not require a specially
crafted configuration (at least, for now). You can simply integrate it
as part of your Maven build following the instructions below.

#### Project integration
```
...
<plugins>
    ...
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>findbugs-maven-plugin</artifactId>
        <version>${findbugs-maven-plugin.version}</version>
        <executions>
            <execution>
                <id>findbugs</id>
                <goals>
                    <goal>check</goal>
                </goals>
                <configuration>
                    <includeTests>true</includeTests>
                    <sourceEncoding>UTF-8</sourceEncoding>
                </configuration>
            </execution>
        </executions>
    </plugin>
    ...
</plugins>
```

__Gotcha__: The above configuration will fail your build in case FindBugs
 errors exist, which is a good thing on a new project. However, if you want
 to introduce checks on an existing project where you might not have the
 luxury of time to correct all mistakes at once, you can add on the
 `<configuration>` section `<failOnError>false</failOnError>`.

## OWASP Maven Dependency Check
For OWASP MDC we provide a customised suppression list to exclude known
conflicts (such as the QLACK Fuse module).

#### Project integration
```
...
<plugins>
    ...
    <plugin>
      <groupId>org.owasp</groupId>
      <artifactId>dependency-check-maven</artifactId>
      <version>${dependency-check-maven.version}</version>
      <configuration>
        <suppressionFile>owasp-suppression.xml</suppressionFile>
      </configuration>
      <executions>
        <execution>
          <goals>
            <goal>check</goal>
          </goals>
        </execution>
      </executions>
      <dependencies>
        <dependency>
          <groupId>com.eurodyn.qp</groupId>
          <artifactId>ed-qp-owasp</artifactId>
          <version>${ed-qp.version}</version>
        </dependency>
      </dependencies>
    </plugin>
    ...
</plugins>
```