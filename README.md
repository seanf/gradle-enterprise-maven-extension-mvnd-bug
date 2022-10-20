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

mvnd: https://github.com/apache/maven-mvnd/releases/tag/0.8.2
