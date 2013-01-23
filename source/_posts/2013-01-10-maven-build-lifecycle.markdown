---
layout: post
title: "Maven的生命周期和默认绑定的Plugin"
date: 2013-01-10 13:01
comments: true
styles: data-table
categories: Maven
---

## 三套构建生命周期

Maven有三套独立的Lifecycle：

* clean阶段: 项目清理阶段
* default阶段: 默认阶段，大部分的编译、测试、打包和部署都在这个阶段完成
* site阶段: 发布项目网站和文档阶段

每个Lifecycle还定义了按顺序执行的Phases，即从命令行指定执行的Phase，必然会先依次执行其前面的所有的Phase：

* 如果你执行 `mvn clean` Maven实际上会依次执行 `pre-clean` 和 `clean` 两个Phase
* 如果执行 `mvn install` Maven实际上会依次执行Default阶段前面的所有的22个Phase
* 如果执行 `mvn clean package` Maven首先会依次执行Clean阶段的 `pre-clean`, `clean`，然后再依次执行Default阶段的 `validate`, `initialize`, … 一直到 `package` 等17个Phase

三套Lifecycle相互独立，所以在执行 `mvn install` 的时候，是不会触发任何的Clean阶段的动作的。

** Clean阶段 **

1. pre-clean
1. clean
1. post-clean

** Default阶段 **

1. validate
1. initialize
1. generate-sources
1. process-sources
1. generate-resources
1. process-resources
1. compile
1. process-classes
1. generate-test-sources
1. process-test-sources
1. generate-test-resources
1. process-test-resources
1. test-compile
1. process-test-classes
1. test
1. prepare-package
1. package
1. pre-integration-test
1. integration-test
1. post-integration-test
1. verify
1. install
1. deploy

### Site阶段

1. pre-site
1. site
1. post-site
1. site-deploy

## Lifecycle和Maven Plugin

Maven的核心非常精简，除了核心的Lifecycle、Phase等过程的定义之外，所有的实际操作都是以Plugin的方式提供的。为了减少用户的配置，提高易用性，Maven在发布的时候，已经默认绑定了一些基本的plugin，可以完成大部分的编译、测试、打包和发布等操作。

### Clean Lifecycle Bindings

<table><tr class="a"><td align="left"><tt>clean</tt></td><td align="left"><tt>clean:clean</tt></td></tr></table>

### Default Lifecycle Bindings - Packaging `ejb` / `ejb3` / `jar` / `par` / `rar` / `war`

<table border="1" class="bodyTable"><tr class="a"><td align="left"><tt>process-resources</tt></td><td align="left"><tt>resources:resources</tt></td></tr><tr class="b"><td align="left"><tt>compile</tt></td><td align="left"><tt>compiler:compile</tt></td></tr><tr class="a"><td align="left"><tt>process-test-resources</tt></td><td align="left"><tt>resources:testResources</tt></td></tr><tr class="b"><td align="left"><tt>test-compile</tt></td><td align="left"><tt>compiler:testCompile</tt></td></tr><tr class="a"><td align="left"><tt>test</tt></td><td align="left"><tt>surefire:test</tt></td></tr><tr class="b"><td align="left"><tt>package</tt></td><td align="left"><tt>ejb:ejb</tt> <i>or</i> <tt>ejb3:ejb3</tt> <i>or</i> <tt>jar:jar</tt> <i>or</i> <tt>par:par</tt> <i>or</i> <tt>rar:rar</tt> <i>or</i> <tt>war:war</tt></td></tr><tr class="a"><td align="left"><tt>install</tt></td><td align="left"><tt>install:install</tt></td></tr><tr class="b"><td align="left"><tt>deploy</tt></td><td align="left"><tt>deploy:deploy</tt></td></tr></table>

### Default Lifecycle Bindings - Packaging `ear`

<table border="1" class="bodyTable"><tr class="a"><td align="left"><tt>generate-resources</tt></td><td align="left"><tt>ear:generateApplicationXml</tt></td></tr><tr class="b"><td align="left"><tt>process-resources</tt></td><td align="left"><tt>resources:resources</tt></td></tr><tr class="a"><td align="left"><tt>package</tt></td><td align="left"><tt>ear:ear</tt></td></tr><tr class="b"><td align="left"><tt>install</tt></td><td align="left"><tt>install:install</tt></td></tr><tr class="a"><td align="left"><tt>deploy</tt></td><td align="left"><tt>deploy:deploy</tt></td></tr></table>

### Default Lifecycle Bindings - Packaging `maven-plugin`

<table border="1" class="bodyTable"><tr class="a"><td align="left"><tt>generate-resources</tt></td><td align="left"><tt>plugin:descriptor</tt></td></tr><tr class="b"><td align="left"><tt>process-resources</tt></td><td align="left"><tt>resources:resources</tt></td></tr><tr class="a"><td align="left"><tt>compile</tt></td><td align="left"><tt>compiler:compile</tt></td></tr><tr class="b"><td align="left"><tt>process-test-resources</tt></td><td align="left"><tt>resources:testResources</tt></td></tr><tr class="a"><td align="left"><tt>test-compile</tt></td><td align="left"><tt>compiler:testCompile</tt></td></tr><tr class="b"><td align="left"><tt>test</tt></td><td align="left"><tt>surefire:test</tt></td></tr><tr class="a"><td align="left"><tt>package</tt></td><td align="left"><tt>jar:jar</tt> <i>and</i> <tt>plugin:addPluginArtifactMetadata</tt></td></tr><tr class="b"><td align="left"><tt>install</tt></td><td align="left"><tt>install:install</tt> <i>and</i> <tt>plugin:updateRegistry</tt></td></tr><tr class="a"><td align="left"><tt>deploy</tt></td><td align="left"><tt>deploy:deploy</tt></td></tr></table>

### Default Lifecycle Bindings - Packaging `pom`

<table border="1" class="bodyTable"><tr class="a"><td align="left"><tt>package</tt></td><td align="left"><tt>site:attach-descriptor</tt></td></tr><tr class="b"><td align="left"><tt>install</tt></td><td align="left"><tt>install:install</tt></td></tr><tr class="a"><td align="left"><tt>deploy</tt></td><td align="left"><tt>deploy:deploy</tt></td></tr></table>

### Site Lifecycle Bindings

<table border="1" class="bodyTable"><tr class="a"><td align="left"><tt>site</tt></td><td align="left"><tt>site:site</tt></td></tr><tr class="b"><td align="left"><tt>site-deploy</tt></td><td align="left"><tt>site:deploy</tt></td></tr></table>