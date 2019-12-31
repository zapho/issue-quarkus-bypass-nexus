# issue-quarkus-bypass-nexus project

Issue reproducer: quarkus bypasses maven Nexus when running 'mvn test'.

This project has been built via `mvn io.quarkus:quarkus-maven-plugin:1.1.0.Final:create` and contains an artifact only available within an internal maven proxy (Nexus).

# Environment
```bash
mvn -v

Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-04T21:00:29+02:00)
Maven home: D:\dev\tools\maven
Java version: 1.8.0_221, vendor: Oracle Corporation, runtime: D:\dev\java\jdk8\jre
Default locale: fr_FR, platform encoding: Cp1252
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```
```bash
cat ./maven/conf/settings.xml

...
<mirror>
    <id>my-proxy</id>
    <mirrorOf>*,!staging</mirrorOf>
    <name>my-proxy</name>
    <url>https://my-proxy.host/nexus/repository/releases/</url>
</mirror>
...
```

# Reproducing the issue
**Compile-time resolution is ok**
```bash
 mvn compile -U                                                                                                                           /d/dev/SourceRepoAlt/sandbox/issue-quarkus-bypass-nexus 1
=======

...
<mirror>
    <id>my-proxy</id>
    <mirrorOf>*,!staging</mirrorOf>
    <name>my-proxy</name>
    <url>https://my-proxy.host/nexus/repository/releases/</url>
</mirror>
...
```

# Reproducing the issue
**Compile-time resolution is ok**
```bash
 mvn compile -U                                                                                                                           /d/dev/SourceRepoAlt/sandbox/issue-quarkus-bypass-nexus 1
[INFO] Scanning for projects...
[INFO]
[INFO] ------------< fr.zapho.sandbox:issue-quarkus-bypass-nexus >-------------
[INFO] Building issue-quarkus-bypass-nexus 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ issue-quarkus-bypass-nexus ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 2 resources
[INFO]
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ issue-quarkus-bypass-nexus ---
[INFO] Nothing to compile - all classes are up to date
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.759 s
[INFO] Finished at: 2019-12-31T10:14:17+01:00
[INFO] ------------------------------------------------------------------------
```

**Test are failing**
```bash
 mvn test                                                                                                                                   /d/dev/SourceRepoAlt/sandbox/issue-quarkus-bypass-nexus
[INFO] Scanning for projects...
[INFO]
[INFO] ------------< fr.zapho.sandbox:issue-quarkus-bypass-nexus >-------------
[INFO] Building issue-quarkus-bypass-nexus 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ issue-quarkus-bypass-nexus ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 2 resources
[INFO]
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ issue-quarkus-bypass-nexus ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ issue-quarkus-bypass-nexus ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory D:\dev\SourceRepoAlt\sandbox\issue-quarkus-bypass-nexus\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ issue-quarkus-bypass-nexus ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-surefire-plugin:2.22.1:test (default-test) @ issue-quarkus-bypass-nexus ---
[INFO]
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running fr.zapho.sandbox.HelloResourceTest
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 1.888 s <<< FAILURE! - in fr.zapho.sandbox.HelloResourceTest
[ERROR] testHelloEndpoint  Time elapsed: 0.009 s  <<< ERROR!
org.junit.jupiter.api.extension.TestInstantiationException: TestInstanceFactory [io.quarkus.test.junit.QuarkusTestExtension] failed to instantiate test class [fr.zapho.sandbox.HelloResourceTest]: Failed to create the boostrap class loader
Caused by: java.lang.IllegalStateException: Failed to create the boostrap class loader
Caused by: io.quarkus.bootstrap.BootstrapException: Failed to create the deployment classloader for fr.zapho.sandbox:issue-quarkus-bypass-nexus::jar:1.0-SNAPSHOT
Caused by: io.quarkus.bootstrap.resolver.AppModelResolverException: Failed to resolve dependencies for fr.zapho.sandbox:issue-quarkus-bypass-nexus:jar:1.0-SNAPSHOT
Caused by: org.eclipse.aether.resolution.DependencyResolutionException: Could not find artifact fr.zapho:chaos-proxy-quarkus:jar:1.2.0 in central (https://repo.maven.apache.org/maven2)
Caused by: org.eclipse.aether.resolution.ArtifactResolutionException: Could not find artifact fr.zapho:chaos-proxy-quarkus:jar:1.2.0 in central (https://repo.maven.apache.org/maven2)
Caused by: org.eclipse.aether.transfer.ArtifactNotFoundException: Could not find artifact fr.zapho:chaos-proxy-quarkus:jar:1.2.0 in central (https://repo.maven.apache.org/maven2)

[INFO]
[INFO] Results:
[INFO]
[ERROR] Errors:
[ERROR]   HelloResourceTest.testHelloEndpoint  TestInstantiation TestInstanceFactory [i...
[INFO]
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  5.590 s
[INFO] Finished at: 2019-12-31T10:11:27+01:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.22.1:test (default-test) on project issue-quarkus-bypass-nexus: There are test failures.
```
**Same issue using bundled mvnw**
```bash
./mvnw test -U                                                                                                                             /d/dev/SourceRepoAlt/sandbox/issue-quarkus-bypass-nexus 1
[INFO] Scanning for projects...
[INFO]
[INFO] ------------< fr.zapho.sandbox:issue-quarkus-bypass-nexus >-------------
[INFO] Building issue-quarkus-bypass-nexus 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
Downloading from central: https://repo.maven.apache.org/maven2/fr/zapho/chaos-proxy-quarkus/1.2.0/chaos-proxy-quarkus-1.2.0.pom
[WARNING] The POM for fr.zapho:chaos-proxy-quarkus:jar:1.2.0 is missing, no dependency information available
Downloading from central: https://repo.maven.apache.org/maven2/fr/zapho/chaos-proxy-quarkus/1.2.0/chaos-proxy-quarkus-1.2.0.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.810 s
[INFO] Finished at: 2019-12-31T11:04:54+01:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project issue-quarkus-bypass-nexus: Could not resolve dependencies for project fr.zapho.sandbox:issue-quarkus-bypass-nexus:jar:1.0-SNAPSHOT: Could not find artifact fr.zapho:chaos-proxy-quarkus:jar:1.2.0 in central (https://repo.maven.apache.org/maven2) -> [Help 1]
```

# Fixing
Adding a `<repository>` in pom.xml is fixing this. Not a good solution though.
