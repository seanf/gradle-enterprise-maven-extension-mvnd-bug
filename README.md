# Incompatibilities between `gradle-enterprise-maven-extension` and `mvnd`

## Testing Requirements
Apache Maven Daemon v0.8.2: https://github.com/apache/maven-mvnd/releases/tag/0.8.2

```
❯ mvnd --version
mvnd 0.8.2 darwin-amd64 native client (2bba2d6a4d3a5012ddf9f1f42a22784cad4011e3)
Terminal: org.jline.terminal.impl.PosixSysTerminal with pty org.jline.terminal.impl.jansi.osx.OsXNativePty
[WARN] Failed to initialize spy com.gradle.maven.internal.GradleEnterpriseLifecycleManager: build operation notification valve already started
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /usr/local/Cellar/mvnd/0.8.2/libexec/mvn
Java version: 19, vendor: Homebrew, runtime: /usr/local/Cellar/openjdk/19/libexec/openjdk.jdk/Contents/Home
Default locale: en_AU, platform encoding: UTF-8
OS name: "mac os x", version: "12.4", arch: "x86_64", family: "mac"
```


## Bug: "MavenPluginManager was not overridden" error when `gradle-enterprise-maven-extension` is active with `mvnd`

```
❯ mvnd initialize
[WARN] Failed to initialize spy com.gradle.maven.internal.GradleEnterpriseLifecycleManager: build operation notification valve already started
[INFO] Processing build on daemon c31a048e
[ERROR] Internal error: java.lang.IllegalStateException: MavenPluginManager was not overridden -> [Help 1]
org.apache.maven.InternalErrorException: Internal error: java.lang.IllegalStateException: MavenPluginManager was not overridden
	at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:120)
	at org.apache.maven.cli.DaemonMavenCli.execute(DaemonMavenCli.java:687)
	at org.apache.maven.cli.DaemonMavenCli.doMain(DaemonMavenCli.java:239)
	at org.apache.maven.cli.DaemonMavenCli.main(DaemonMavenCli.java:215)
	at org.mvndaemon.mvnd.daemon.Server.handle(Server.java:572)
	at org.mvndaemon.mvnd.daemon.Server.client(Server.java:256)
	at org.mvndaemon.mvnd.daemon.Server.accept(Server.java:224)
	at java.base/java.lang.Thread.run(Thread.java:1589)
Caused by: java.lang.IllegalStateException: MavenPluginManager was not overridden
	at com.gradle.enterprise.b.a.a(SourceFile:146)
	at com.gradle.enterprise.b.a.a(SourceFile:39)
	at com.gradle.maven.internal.GradleEnterpriseLifecycleManager.afterSessionStart(SourceFile:138)
	at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:208)
	at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:192)
	at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:105)
	... 7 common frames omitted
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/InternalErrorException

```

Please note that the exact error shown above happens in versions 1.15.3 and 1.15.4, but not in 1.15.2.


## Bug: `NullPointerException` in `com.gradle.maven.cache.extension.b.h.a` when `gradle-enterprise-maven-extension` 1.15.2 is active with reused `mvnd`

If you edit `.mvn/extensions.xml` to use version 1.15.2, it is possible to run a build, as long as (a) the daemon is fresh and (b) the scan is disabled.

```
❯ mvnd --stop; mvnd initialize -Dgradle.scan.disabled
[INFO] Processing build on daemon 399c30d6
[INFO] Scanning for projects...
[INFO] BuildTimeEventSpy is registered.
[INFO]
[INFO] Using the SmartBuilder implementation with a thread count of 11
[INFO] Task segments : [initialize]
[INFO] Build maximum degree of concurrency is 11
[INFO] Total number of projects is 1
[INFO]
[INFO] ----------------------< com.mycompany.app:my-app >----------------------
[INFO] Building my-app 1
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] Segment walltime 0 s, segment projects service time 0 s, effective/maximum degree of concurrency 0.67/11
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.146 s (Wall Clock)
[INFO] Finished at: 2022-10-20T15:33:27+10:00
[INFO] ------------------------------------------------------------------------
[INFO] 0 goals, 0 executed
```

But if the daemon is reused:
```
❯ mvnd initialize -Dgradle.scan.disabled
[INFO] Processing build on daemon 399c30d6
[INFO] Scanning for projects...
[INFO] BuildTimeEventSpy is registered.
[INFO]
[INFO] Using the SmartBuilder implementation with a thread count of 11
[INFO] Task segments : [initialize]
[INFO] Build maximum degree of concurrency is 11
[INFO] Total number of projects is 1
[INFO]
[INFO] ----------------------< com.mycompany.app:my-app >----------------------
[INFO] Building my-app 1
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] Segment walltime 0 s, segment projects service time 0 s, effective/maximum degree of concurrency 0.77/11
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.010 s (Wall Clock)
[INFO] Finished at: 2022-10-20T15:33:49+10:00
[INFO] ------------------------------------------------------------------------
[ERROR] Internal error: java.lang.NullPointerException -> [Help 1]
org.apache.maven.InternalErrorException: Internal error: java.lang.NullPointerException
	at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:120)
	at org.apache.maven.cli.DaemonMavenCli.execute(DaemonMavenCli.java:687)
	at org.apache.maven.cli.DaemonMavenCli.doMain(DaemonMavenCli.java:239)
	at org.apache.maven.cli.DaemonMavenCli.main(DaemonMavenCli.java:215)
	at org.mvndaemon.mvnd.daemon.Server.handle(Server.java:572)
	at org.mvndaemon.mvnd.daemon.Server.client(Server.java:256)
	at org.mvndaemon.mvnd.daemon.Server.accept(Server.java:224)
	at java.base/java.lang.Thread.run(Thread.java:1589)
Caused by: java.lang.NullPointerException: null
	at java.base/java.util.Objects.requireNonNull(Objects.java:233)
	at com.gradle.maven.cache.extension.b.h.a(SourceFile:85)
	at com.gradle.maven.internal.GradleEnterpriseLifecycleManager.afterSessionEnd(SourceFile:173)
	at org.apache.maven.DefaultMaven.afterSessionEnd(DefaultMaven.java:354)
	at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:307)
	at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:192)
	at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:105)
	... 7 common frames omitted
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/InternalErrorException
```

## `ClassNotFoundException: org.slf4j.impl.SimpleLogger` if scan is enabled

[If you edit `.mvn/extensions.xml` to use version 1.15.2]
If the scan is enabled, this fails even with a fresh daemon:
```
❯ mvnd --stop; mvnd initialize
Stopping 1 running daemons
[INFO] Processing build on daemon 7576b7c0
[INFO] Scanning for projects...
[INFO] BuildTimeEventSpy is registered.
[INFO]
[INFO] Using the SmartBuilder implementation with a thread count of 11
[INFO] Task segments : [initialize]
[INFO] Build maximum degree of concurrency is 11
[INFO] Total number of projects is 1
[INFO]
[INFO] ----------------------< com.mycompany.app:my-app >----------------------
[INFO] Building my-app 1
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] Segment walltime 0 s, segment projects service time 0 s, effective/maximum degree of concurrency 0.59/11
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.143 s (Wall Clock)
[INFO] Finished at: 2022-10-20T15:35:32+10:00
[INFO] ------------------------------------------------------------------------
[INFO] 0 goals, 0 executed
[INFO]
[INFO] A build scan cannot be produced as an error occurred gathering build data.
[INFO] Please report this problem via https://gradle.com/help/plugin and include the following via copy/paste:
[INFO]
[INFO] ----------
[INFO] Maven version: 3.8.6
[INFO] Extension version: 1.15.2
[INFO]
[INFO] java.lang.RuntimeException: Cannot reinitialize SLF4J to reroute the System.out to capture the console log.
	at com.gradle.maven.scan.extension.internal.capture.k.a.a(SourceFile:218)
	at com.gradle.maven.scan.extension.internal.capture.k.a.a(SourceFile:108)
	at com.gradle.maven.scan.extension.internal.capture.c.d$a.a(SourceFile:67)
	at com.gradle.maven.scan.extension.internal.capture.c.d$a.a(SourceFile:74)
	at java.base/java.util.Optional.ifPresent(Optional.java:178)
	at com.gradle.maven.scan.extension.internal.capture.c.d$a.onEvent(SourceFile:71)
	at com.gradle.maven.scan.extension.internal.capture.c.d.a(SourceFile:28)
	at com.gradle.maven.scan.extension.internal.capture.c.i.a(SourceFile:22)
	at com.gradle.maven.scan.extension.internal.capture.c.k.a(SourceFile:17)
	at com.gradle.maven.scan.extension.internal.c.a(SourceFile:276)
	at com.gradle.maven.scan.extension.MvnBuildScanExtension.a(SourceFile:49)
	at com.gradle.maven.internal.GradleEnterpriseLifecycleManager.onEvent(SourceFile:193)
	at org.apache.maven.eventspy.internal.EventSpyDispatcher.onEvent(EventSpyDispatcher.java:104)
	at org.apache.maven.cli.DaemonMavenCli.execute(DaemonMavenCli.java:682)
	at org.apache.maven.cli.DaemonMavenCli.doMain(DaemonMavenCli.java:239)
	at org.apache.maven.cli.DaemonMavenCli.main(DaemonMavenCli.java:215)
	at org.mvndaemon.mvnd.daemon.Server.handle(Server.java:572)
	at org.mvndaemon.mvnd.daemon.Server.client(Server.java:256)
	at org.mvndaemon.mvnd.daemon.Server.accept(Server.java:224)
	at java.base/java.lang.Thread.run(Thread.java:1589)
Caused by: java.lang.ClassNotFoundException: org.slf4j.impl.SimpleLogger
	at org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy.loadClass(SelfFirstStrategy.java:50)
	at org.codehaus.plexus.classworlds.realm.ClassRealm.unsynchronizedLoadClass(ClassRealm.java:271)
	at org.codehaus.plexus.classworlds.realm.ClassRealm.loadClass(ClassRealm.java:247)
	at org.codehaus.plexus.classworlds.realm.ClassRealm.loadClass(ClassRealm.java:239)
	at com.gradle.maven.scan.extension.internal.capture.k.a.a(SourceFile:214)
	... 19 more
[INFO] ----------
[INFO]
```
