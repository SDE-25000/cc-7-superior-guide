# CC-7 Superior Guide for writing test

0. Git clone the standard cc-7 repo
1. Add chosen peer repo as git submodule
```bash
# Add the repositories as submodules in a workspace directory
mkdir workspace
git submodule add https://github.com/SDE-25000/cc-.git workspace/whatever
git submodule add https://github.com/SDE-25000/cc.git workspace/whatever2
```

2. Create YOUR test directories:

This is where unit code YOU write will be. Since we have 2 write code for 2 peer projects, we create 2 directories.

```bash
mkdir -p TEST/src/test/java
mkdir -p TEST2/src/test/java
```

Note: from now on, I'll use the following as an example. Remember, these are in my cc-7 repo root.

```bash
mkdir -p cc-4-1-TEST/src/test/java
mkdir -p cc-6-3-TEST/src/test/java
```

I'll use MAVEN for this setup, is you use different tool, good luck.

3. Create main pom.xml in root directory:

NOTE: replace my alias `jvlewis` and module names to match YOUR setup (i.e `cc-4-1-TEST`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jvlewis</groupId> //replace
    <artifactId>test-parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>cc-4-1-TEST</module> //replace
        <module>cc-6-3-TEST</module> //replace
    </modules>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>3.2.5</version>
                     <dependencies>
        <dependency>
            <groupId>me.fabriciorby</groupId>
            <artifactId>maven-surefire-junit5-tree-reporter</artifactId>
            <version>0.1.0</version>
        </dependency>
    </dependencies>
    <configuration>
        <reportFormat>plain</reportFormat>
        <consoleOutputReporter>
            <disable>true</disable>
        </consoleOutputReporter>
        <statelessTestsetInfoReporter implementation="org.apache.maven.plugin.surefire.extensions.junit5.JUnit5StatelessTestsetInfoTreeReporter"/>
    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
</project>
```

4. For each peer project, create pom.xml in YOUR directory created (i.e `cc-4-1-TEST`)

NOTE: there's things to replace with your paths/setup, keep reading...

4a. Created pom.xml in cc-4-1-TEST directory:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>test-parent</artifactId>
        <groupId>com.jvlewis</groupId> //replace
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cc-4-1-TEST</artifactId>

   <build>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>build-helper-maven-plugin</artifactId>
                <version>3.6.0</version>
                <executions>
                    <execution>
                        <id>add-source</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>add-source</goal>
                        </goals>
                        <configuration>
                            <sources>
                                <source>${project.basedir}/../workspace/cc-4-1-java-banking-system-app-challenge-[PEER-ALIAS]</source>
                            </sources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <includes>
                        <include>**/*.java</include>
                    </includes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

Ensure your source points to the `workspace` directory (or whatever one you created) which contains the git submodules.

Therefore, replace the below to match your project structure:
```
<sources>
    <source>${project.basedir}/../workspace/cc-4-1-java-banking-system-app-challenge-[PEER-ALIAS]</source>
</sources>
```

5. Created pom.xml in YOUR 2nd directory (i.e `cc-6-3-TEST`) directory 

This file is basically the same as the first POM file but with different source directory

6. Final project structure:
```
cc-7-0-java-testing-challenge-YOUR-ALIAS/
├── pom.xml
├── cc-4-1-TEST/
│   ├── pom.xml
│   └── src/
│       └── test/
│           └── java/
│               └── [test files]
├── cc-6-3-TEST/
│   ├── pom.xml
│   └── src/
│       └── test/
│           └── java/
│               └── [test files]
└── workspace/
    ├── cc-4-1-java-banking-system-app-challenge-[PEER-ALIAS]/
    └── cc-6-3-java-dsa3-code-challenge-[PEER-ALIAS]/
```

7. To run tests:
```bash
mvn clean install
mvn clean test
mvn test

# Run specific module tests
mvn test -pl cc-4-1-TEST
mvn test -pl <YOUR FOLDER/MODULE>
```

8. For someone else to clone and use this project:
```bash
# Clone with submodules
git clone --recursive [your-repository-url]

# If already cloned without submodules
git submodule update --init --recursive

# Run tests
mvn clean test
```

# How to push once you have test ready

1. git status

```
git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .gitmodules
	new file:   workspace/cc-4-1-java-banking-system-app-challenge-[PEER-ALIAS]
	new file:   workspace/cc-6-3-java-dsa3-code-challenge-[PEER-ALIAS]

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.gitignore
	cc-4-1-TEST/
	cc-6-3-TEST/
	pom.xml
```

2. Add your files

```
git add .gitignore .gitmodules pom.xml cc-4-1-TEST/ cc-6-3-TEST
```

3. Commit your changes

```
git commit -m "Add test setup with JUnit 5 tests for banking system"
```

4. Push without submodules
```
git push origin main --recurse-submodules=no
```