# App README

To compile the application and demonstrate the bug:

```bash
cd module1 && mvn flow:clean-frontend && cd ../module2 && mvn flow:clean-frontend && cd .. 
./mvnw clean compile -T8 -Djava.util.concurrent.ForkJoinPool.common.parallelism=1
```

`-Djava.util.concurrent.ForkJoinPool.common.parallelism=1` simulates what happens on a 1 or 2 core machine in Java 25,
where the common pool size defaults to 1.

This POC reproduces the issue reliably on a specific 8 core machine, however probably not with any guarantee. Error
should be something similar to:

```
[ERROR] Failed to read the command '[which, npm]' stdout/stderr
java.util.concurrent.TimeoutException
    at java.util.concurrent.CompletableFuture.timedGet (CompletableFuture.java:1981)
    at java.util.concurrent.CompletableFuture.get (CompletableFuture.java:2116)
    at com.vaadin.flow.server.frontend.FrontendToolsLocator.executeCommand (FrontendToolsLocator.java:176)
    at com.vaadin.flow.server.frontend.FrontendToolsLocator.tryLocateTool (FrontendToolsLocator.java:80)
    at com.vaadin.flow.server.frontend.FrontendTools.getNpmCliToolExecutable (FrontendTools.java:917)
    at com.vaadin.flow.server.frontend.FrontendTools.getNpmExecutable (FrontendTools.java:885)
    at com.vaadin.flow.server.frontend.FrontendTools.getNpmExecutable (FrontendTools.java:577)
    at com.vaadin.flow.server.frontend.TaskRunNpmInstall.runNpmInstall (TaskRunNpmInstall.java:259)
    at com.vaadin.flow.server.frontend.TaskRunNpmInstall.execute (TaskRunNpmInstall.java:115)
    at com.vaadin.flow.server.frontend.NodeTasks.execute (NodeTasks.java:354)
    at com.vaadin.flow.plugin.base.BuildFrontendUtil.runNodeUpdater (BuildFrontendUtil.java:378)
    at com.vaadin.flow.plugin.maven.BuildFrontendMojo.executeInternal (BuildFrontendMojo.java:163)
    at jdk.internal.reflect.DirectMethodHandleAccessor.invoke (DirectMethodHandleAccessor.java:104)
    at java.lang.reflect.Method.invoke (Method.java:565)
    at com.vaadin.flow.plugin.maven.FlowModeAbstractMojo.execute (FlowModeAbstractMojo.java:341)
    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:126)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:328)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
    at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
    at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
    at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
    at org.apache.maven.lifecycle.internal.builder.multithreaded.MultiThreadedBuilder$1.call (MultiThreadedBuilder.java:193)
    at org.apache.maven.lifecycle.internal.builder.multithreaded.MultiThreadedBuilder$1.call (MultiThreadedBuilder.java:180)
    at java.util.concurrent.FutureTask.run (FutureTask.java:328)
    at java.util.concurrent.Executors$RunnableAdapter.call (Executors.java:545)
    at java.util.concurrent.FutureTask.run (FutureTask.java:328)
    at java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1090)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:614)
    at java.lang.Thread.run (Thread.java:1474)```
