---
title: Apache项目Maven官方文档学习及翻译（译）
date: 2018-01-29 14:34:24
update: 2018-01-29 14:34:24
categories: 后端
tags: java
---

从理解pom.xml探析Maven

<!--more-->

### pom是什么

POM表示“Project Object Model(工程对象模型)”，同其他对象模型一样，pom用于表示一个工程的结构。

在Maven工程中以xml文件形式呈现，核心文件pom.xml

官网的形容是：pom是关乎工程的“一站式商店（one-stop-store）”，所以对工程的配置仅需一个pom.xml文件

### 概览

```Maven
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- The Basics -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>

  <!-- Build Settings -->
  <build>...</build>
  <reporting>...</reporting>

  <!-- More Project Information -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>

  <!-- Environment Settings -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```

![](http://trigolds.com/maven_lifecircle.png)

modelVersion是必填参数，4.0.0当前仅支持Maven2及Maven3版本

通览整个pom文件，分为一下几部分配置：

1. 基本配置（The Basics）

2. 构建配置（Build Settings）

3. 关于工程的更多信息（More Project Information）

4. 环境配置（Environment Settings）

### 基本配置

pom包含了关于工程的所有必要信息，包括构建过程中所用的插件配置。

它有效地声明了“who”、“what”和“where”（谁在哪里做了什么）

与此同时，构建生命周期是“when”和“how”（何时做以及怎么做）

#### 特别之处

上述的构建生命周期去决定“何时以及如何去做”并不意味着pom不能**干涉（影响）**生命周期流

取而代之的是，它可以

例如：通过配置maven-antrun-plugin，可以在pom中有效地嵌入ant任务

这最终是一个声明，然而正如build.xml在运行时告诉ant（流程上）准确去做什么，pom陈述了它（声明上）的配置

但如果某种外部力量迫使生命周期跳过ant插件的执行，Maven的魔法使得它并不会停止被执行的插件

这不像build.xml的任务几乎总是依赖于执行流上前一个任务的执行

### Maven坐标

示例：
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0</version>
</project>
```

以上是Maven2和Maven3允许的最小配置

groupId:artifactId:version是都被要求配置的参数（如果继承自父pom时，groupId和version则不需要准确定义）

这三个域对于一个工程扮演了地址和时间戳的角色，这在仓库中标记出一个特定的位置，像是扮演了Maven工程中的坐标系统这一角色

#### groupId

这通常对于一个组织或者一个工程是唯一的。例如Maven所有的核心组件都存在于org.apache.maven这一groupId下

groupId不是必须添加“.”符号，比如junit工程

包含“.”符的groupId不必和工程所包含的包结构保持一致，但这二者保持一致是一种推荐遵守的最佳实践

但存储在仓库时，组扮演了操作系统中java包结构的角色，“.”符将被操作系统替换为特定的目录分隔符（例如Unix中的斜杠符“/”），从而变成一个相对于仓库根目录的相对目录结构。

在所给例子中，org.codehaus.mojo组存在于$M2_REPO/org/codehaus/mojo中

#### artifactId

artifactId通常是工程所被知晓的名称

虽然groupId很重要，但是人们在组（group）内的讨论很少提及groupId（通常都是在相同ID的组内，例如Codehaus Mojo工程的groupId:org.codehaus.mojo）

artifactId在groupId之下对于将这个工程区别于所有其他工程创建了一个键，完整定义了组件在仓库中的住所

在上例中，即是my-project居住在$M2_REPO/org/codehaus/mojo/my-project

#### version

这是命名谜题中的最后一块

groupId:artifactId声明了一个单一的工程，但它没有描述清我们讨论的那个工程是哪一个化身

我们讨论的是今天的junit:junit，还是四年前的junit:junit（第二版）

简而言之，代码改变了，那些改动应该被版本化

version这个元素在组件线上定义了那些版本改动

-----------

上述这三个元素指出了一个特定的工程版本，使得Maven知晓我们在处理哪一个工程，还有何时我们需要它们

#### packaging

知道了地址结构groupId:artifactId:version之后，还有一个标准标签提供真正完整的地址，那就是工程的组件类型

上述示例pom中定义的org.codehaus.mojo:my-project:1.0将被以jar格式打包

当没有packaging被声明时，Maven的默认处理方式是以jar包形式打包

有效类型有：

1. pom

2. jar

3. maven-plugin

4. ejb

5. war

6. ear

7. rar

8. par

对于一个指定的包结构，这些定义了和构建声明周期阶段所一致的某个默认目标列表
（这句话稍显晦涩，原文：These define the default list of goals which execute to each corresponding build lifecycle stage for a particular package structure）

你有时会看到Maven打印出如下的工程坐标：

groupId:artifactId:packaging:version

#### classfier

你可能会在坐标中碰巧发现第五个元素，那就是classifier

### POM关系

Maven强大的一个方面在于它对工程关系的处理，包括**依赖**和**可传递依赖**、继承、聚合（多模块工程）

Maven在处理依赖管理这一老生常谈的问题上通过一个本地公共仓库正确处理工程联系及版本问题

#### 依赖

POM的基石是其依赖列表，几乎每一个工程都依赖于其他包或工程去正确地实现构建和运行

当你需要时，Maven会为你复杂的目标任务下载并关联依赖

##### groupId,artifactId,version

你会经常看到这些元素，这个三元组合用来及时计算针对某个特定工程的Maven坐标

计算的目的是为了选择一个匹配所有依赖声明的版本（由于可传递依赖的存在，对于相同的artifactId可以有多依赖声明）

- groupId,artifactId：直接对应的坐标的依赖

- version：一个被特指的依赖版本，将被用来计算依赖的有效版本

因为依赖由Maven坐标所描述，你可能会想“这意味着我的工程只能依靠artifacts”，答案是“当然，但那是好事”，这迫使你去依靠于Maven可管理的依赖

有时可能很不幸，当一个工程不能从中央仓库下载到。比如一个工程依赖于一个遵守闭源协议的jar文件，使得你无法从中央仓库下载所得。下面三种方法可以处理：

1. 使用安装插件进行本地安装这个依赖，这个方法是最简单的推荐方法

```
mvn install:install-file -Dfile=non-maven-proj.jar -DgroupId=some.group -DartifactId=non-maven-proj -Dversion=1 -Dpackaging=jar
```

注意这仍需要一个地址，只有这个时候你使用命令行和安装插件会通过你给出的地址创建一个POM

2. 创建你自己的仓库并把包部署上去。这对于公司在内部网络是一个令人喜欢的方法，并且需要保持每一个人同步。有一个Maven目标叫做deploy:deploy-file，和install:install-file类似

3. 设置依赖域为system并定义一个systemPath。这不被推荐，然而引出了接下来要解释的元素。

##### classfier

classfier用来区分由相同POM构建，但内容不同的artifacts。这是可选项，并且是任意的字符串，当它呈现时，它附加于artfact名称的版本后面

这个元素的动机是考虑到例如一个工程提供了一个针对JRE1.5的artifact，但与此同时另一个artifact仍支持JRE1.4。这样第一个artifact可以用classfier“jdk15”装配，第二个artifact用“jdk14”装配，这样客户端便可以使用哪一个

另一个classfiers的通用用例是对于工程主artifact所附加的第二artifacts。如果你浏览Maven中央仓库，会注意到在打包的文件中sources分类器和javadoc分类器被用于部署工程源码和API文档

##### type

和所依赖的artifact的packaging类型保持一致。默认是jar。

它常常展示于依赖文件的名称后缀，但并不总是这样

类型可以被映射到一个不同的拓展名和分类器

类型经常和打包所用方式一致，虽然这也不总是这样

可以通过插件定义新的类型，设置extensions为true，所以这不是一个完整的列表

##### scope

这个元素涉及到即将到来的（编译时、运行时、测试时）任务的类路径（classpath），同时也涉及如何限制可传递依赖，共有一下五个域变量：

![](http://trigolds.com/maven_scope.png)

1. compile：默认域，如果没特别指定，则使用compile。compile依赖将在所有的类路径可用，更甚，那些依赖会被传播到从属工程（compile、runtime、test）

2. provided：和compile很像，但指明了你想用的JDK或者在运行时所提供的容器。仅在编译和测试的类路径可用，并且不可传递（compile、test）

3. runtime：这个域指明了这个依赖在编译时不被需要，但被需要于执行时。它存在于运行时和测试的类路径中，不存在与编译类路径（runtime、test）

4. test：这个域指明了依赖在应用的常规使用时不被需要，而仅在测试编译和执行时可用。不可传递（test-compile、test-runtime）

5. system：这个域和provided类似，除非你不得不明确提供所包含的JAR

##### systemPath

仅被用来当scope是system时，否则如果这个设置被配置，构建将会失败

路径必须是绝对路径，所以推荐使用一个property去设置指定机器的路径，例如${java.home}/lib

假定系统域依赖安装一个priori，Maven不会为这个工程从仓库检查，取而代之的是去检查那个文件是否存在。如果不存在，，Maven将会构建失败并建议你手动下载并安装它

##### optional

当工程本身是一个依赖时标记可选配置为一个依赖。这困惑吗？举个例子，想象一个工程A依赖于工程B去编译一部分在运行时不会被使用的代码，然后我们可能没必要对于所有工程都需要工程B。所以如果工程X添加了工程A作为它自己的依赖，然后Maven将根本不必安装工程B。用象征性的符号表示，如果=>表示一个需要的依赖，-->表示可选，虽然情况是A=>B可能当构建A时，但情况是X=>A-->B当构建X时

简言之，optional让其他工程知道这样一点，就是当你使用这个工程，你不必需要这个依赖以正确工作

#### 依赖版本需求指定

依赖的版本元素定义了版本的需求，用来计算有效的依赖版本

版本需求有如下的语法：

- 1.0：软需求，需要1.0版本（仅是推荐，如果它匹配了所有其他的范围也可以，例如1.1或0.9）

- [1.0]：硬需求，指定1.0版本

- (,1.0)：x <= 1.0

- [1.2,1.3]：1.2 <= x <= 1.3

- [1.0,2.0)：1.0 <= x < 2.0

- [1.5,)：x >= 1.5

- (,1.0],[1.2,)：x <= 1.0 or x >= 1.2（多个范围以逗号隔开）

- (,1.1),(1.1,)：这排除了1.1（例如知道1.1这个版本不能正常工作）

#### 版本顺序指定

Maven坐标在符号之间以点符(.)和连线符(-)分割，在数字和字符之间以

分隔符会被记录下来并对顺序产生影响

数字和字符之间的等同于连字符

空字符会被替换为0

这通过.和-前缀给版本号（数值符）和版本限定符（非数值符号）一个顺序

实例：

1-1.foo-bar1baz-.1 -> 1-1.foo-bar-1-baz-0.1

#### 排除项

exclusions明确告诉Maven你在某个特定工程中某个依赖中不想包含的依赖（换言之，它的传递依赖）

举个例子，maven-embedder需要maven-core，并且我们不想使用它或者它的依赖，随后我们添加一个exclusion配置

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>2.0</version>
      <exclusions>
        <exclusion>
          <groupId>org.apache.maven</groupId>
          <artifactId>maven-core</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

这有时对于修剪依赖的传递依赖有帮助

一个依赖可能在某些特定域范围中不正确，或者依赖会与你工程中的其他依赖冲突

使用通配符排除法可以使得排除一个依赖的所有传递依赖变得简单

在下面的示例中你可能需要maven-embedder，并且你想自己管理依赖，所以你修剪了所有的传递依赖

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <dependencies>
    <dependency>
      <groupId>org.apache.maven</groupId>
      <artifactId>maven-embedder</artifactId>
      <version>3.1.0</version>
      <exclusions>
        <exclusion>
          <groupId>*</groupId>
          <artifactId>*</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    ...
  </dependencies>
  ...
</project>
```

exclusions包含了一个或多个exclusion元素，每一个都包含groupId和artifactId来指明一个排除的依赖

不像optional会可能或者可能不需要安装和使用，exclusions将其完全移除出依赖树

### 继承

Maven为构建管理带来的另一个强大力量是工程继承概念

虽然在构建系统例如Ant，继承当然可以模拟，Maven在使得工程继承对于工程对象模型明确上移除了额外的步骤

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>
</project>
```

对父工程和聚合工程（多模块）来说，packaging类型需要设为pom

这些类型定义了绑定到一系列生命周期阶段的目标

例如，如果packaging是jar，那么package将会执行jar:jar目标

如果packaging是pom，被执行的目标将会是site:attach-descriptor

现在我们可以向父pom中添加值，这些会被其子对象继承，大多数元素会从父pom继承到子，包括：

```
groupId
version
description
url
inceptionYear
organization
licenses
developers
contributors
mailingLists
scm
issueManagement
ciManagement
properties
dependencyManagement
dependencies
repositories
pluginRepositories
build
  plugin executions with matching ids
  plugin configuration
  etc.
reporting
profiles
```

特别注意一下元素不被继承：
```
artifactId
name
prerequisites
```

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>my-parent</artifactId>
    <version>2.0</version>
    <relativePath>../my-parent</relativePath>
  </parent>

  <artifactId>my-project</artifactId>
</project>
```

注意relativePath元素，是不必须的，但可能会用来作为提供给Maven搜索父工程路径的信号，在搜索本地仓库然后远程仓库之前（relativePath -> local repository -> remote repository）

#### 超类POM

和面向编程中对象都继承自跟对象类似，POMs都继承自一个根POM，此外正如java对象最终都继承自java.lang.Object，所有工程对象模型都继承自一个基类POM，下面的片段这Maven3.0.4的超类POM

```
<project>
  <modelVersion>4.0.0</modelVersion>

  <repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>

  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
  </pluginRepositories>

  <build>
    <directory>${project.basedir}/target</directory>
    <outputDirectory>${project.build.directory}/classes</outputDirectory>
    <finalName>${project.artifactId}-${project.version}</finalName>
    <testOutputDirectory>${project.build.directory}/test-classes</testOutputDirectory>
    <sourceDirectory>${project.basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory>src/main/scripts</scriptSourceDirectory>
    <testSourceDirectory>${project.basedir}/src/test/java</testSourceDirectory>
    <resources>
      <resource>
        <directory>${project.basedir}/src/main/resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>${project.basedir}/src/test/resources</directory>
      </testResource>
    </testResources>
    <pluginManagement>
      <!-- NOTE: These plugins will be removed from future versions of the super POM -->
      <!-- They are kept for the moment as they are very unlikely to conflict with lifecycle mappings (MNG-4453) -->
      <plugins>
        <plugin>
          <artifactId>maven-antrun-plugin</artifactId>
          <version>1.3</version>
        </plugin>
        <plugin>
          <artifactId>maven-assembly-plugin</artifactId>
          <version>2.2-beta-5</version>
        </plugin>
        <plugin>
          <artifactId>maven-dependency-plugin</artifactId>
          <version>2.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-release-plugin</artifactId>
          <version>2.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>

  <reporting>
    <outputDirectory>${project.build.directory}/site</outputDirectory>
  </reporting>

  <profiles>
    <!-- NOTE: The release profile will be removed from future versions of the super POM -->
    <profile>
      <id>release-profile</id>

      <activation>
        <property>
          <name>performRelease</name>
          <value>true</value>
        </property>
      </activation>

      <build>
        <plugins>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-source-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-sources</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-javadoc-plugin</artifactId>
            <executions>
              <execution>
                <id>attach-javadocs</id>
                <goals>
                  <goal>jar</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <inherited>true</inherited>
            <artifactId>maven-deploy-plugin</artifactId>
            <configuration>
              <updateReleaseInfo>true</updateReleaseInfo>
            </configuration>
          </plugin>
        </plugins>
      </build>
    </profile>
  </profiles>

</project>
```

你可以创建一个最小pom.xml来看一下超类POM是如何影响你的工程对象模型，并且通过在命令行执行mvn help:effective-pom

#### 依赖管理

除了对于顶级元素的继承，父pom还有一些元素配置用于子POMs和可传递依赖，其中之一是dependenceManagement

##### dependencyManagement

dependencyManagement被POMs用来管理贯穿其所有子pom的依赖信息

如果my-parent工程使用dependenceManagement来定义一个依赖junit:junit:4.0，然后POMs继承自这个pom，可以设置它们的依赖，仅设置groupId=junit，artifactId=junit，随后Maven会通过父工程为其填充version配置。这个方法的收益是显然的。依赖细节可以被设置在一个中央位置，会传播到继承其的POMs

注意artifact的version和scope是从传递依赖而组成的，这也被依赖管理部分控制着版本，这会带来意想不到的结果。考虑一个情况，在你的工程中使用两个依赖，dep1和dep2。dep2反过来又使用到dep1，并需要一个特定的最小版本去运作。如果你使用dependenceManagement去指定一个旧版本，dep2将会被迫使使用旧版本，并失败。

所以你必须小心检查整个依赖树去避免这种问题

mvn:dependence:tree会派上用场

### 聚合（或说多模块）

一个包含模块的工程被认为是一个多模块，或说聚合工程。

模块是这个POM列出的工程，同时会被以组的形式执行。一个以pom方式进行打包的工程可能聚合构建一组工程清单作为模块，这是相对路径或者那些工程的POM文件（原文： An pom packaged project may aggregate the build of a set of projects by listing them as modules, which are relative paths to the directories or the POM files of those projects.）

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.codehaus.mojo</groupId>
  <artifactId>my-parent</artifactId>
  <version>2.0</version>
  <packaging>pom</packaging>

  <modules>
    <module>my-project</module>
    <module>another-project</module>
    <module>third-project/pom-example.xml</module>
  </modules>
</project>
```

当列出模式时，你不必亲自考虑内部模块的依赖，例如POM给出的模块次序是不重要的。Maven会拓扑地排序模块以使依赖模块总会在依赖其的模块之前被构建（被依赖者总会先构建）

#### 最后需要注意关于继承和聚合的一点

继承和聚合创造了一个良好的动态操作去控制构建一个单一的、高级的POM。你将经常看到工程既是父工程，又是聚合工程。例如，整个Maven核心运行于一个简单的基POM——org.apache.maven:maven，所以构建Maven工程可以通过一个简单的命令mvn compile去执行。

然而虽然POM工程、聚合工程还有父工程都不同并且不应该令人困惑。一个POM工程可能会被继承——但不必需要——任何它聚合的模块。相反，一个POM工程可能聚合工程而并没有继承自它的工程

### 属性

属性是理解POM基本元素的最后一块。Maven属性是占位值，像Ant中的属性。它们的值可以被POM中任何位置通过${X}所获取到，X是属性值。或者它们可被用于作为插件默认值，例如：

```
<project>
  ...
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  </properties>
  ...
</project>
```

它们以五种方式表示：

1. env.X：伴随env前缀变量会返回shell的环境变量。例如${env.PATH}包含了PATH环境变量

  > 注意：Windows本身环境变量大小写不敏感，但属性是大小写敏感的。换句话说，当在Windows的shell对于%PATH%和%Path%返回相同的值时，Maven会区分${env.PATH}和${env.Path}。谈及Maven2.1.0，环境变量名称被规范化为全大写以保证可靠性

2. project.x：POM中点符表示的路径会包含和元素值一致的值。例如：<project><version>1.0</version></project>可通过${project.version}达到

3. setting.x：setting.xml中的点符表示的路径包含和元素值相一致的值。例如：<settings><offline>false</offline></settings>可通过${settings.offline}达到

4. java系统属性：所有的属性可通过java.lang.System.getProperties()达到作为POM属性的变量，例如${java.home}

5. x：通过<properties />在POM中设置的元素。<properties><someVar>value</someVar></properties>的值可通过${someVar}获得

### 构建设置

在上述给出的Maven基本设置之上，有两个元素必须在声明完整的POM之前被理解。它们就是build元素，就是像声明你工程目录结构和管理插件一样的处理器。还有reporting元素，能最大化反映构建元素的报告

#### 构建

根据POM 4.0.0 XSD，build元素被概念上拆分成两部分：一个BaseBuild类型包含了build元素（project层级下的顶级构建元素和profiles下的构建元素）的通用元素集；还有一个Build类型，包含了BaseBuild集同时还有更多用于顶级定义的元素。让我们开始分析这二者的通用元素

注意：这些不同的build元素会被以project build和profile build指明

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...
  <!-- "Project Build" contains more elements than just the BaseBuild set -->
  <build>...</build>

  <profiles>
    <profile>
      <!-- "Profile Build" contains a subset of "Project Build"s elements -->
      <build>...</build>
    </profile>
  </profiles>
</project>
```

##### BaseBuild元素集

BaseBuild恰如其名：在POM文件中两个build元素之间的基本元素集

```
<build>
  <defaultGoal>install</defaultGoal>
  <directory>${basedir}/target</directory>
  <finalName>${artifactId}-${version}</finalName>
  <filters>
    <filter>filters/filter1.properties</filter>
  </filters>
  ...
</build>
```

1. defaultGoal：如果未给定，则执行的默认目标或时期。如果goal被给定，那应该被定义如命令行中的样子（例如jar:jar）。如果时期被定义也如此（例如install）

2. directory：这是构建将会输出其文件的目录，或者Maven的说法——构建目标物，这适当地默认设置为${basedir}/target

3. finalName：这是捆绑项目的名称当其最终构建（无文件拓展名，例如my-project-1.0.jar，此时finalName就是my-project-1.0）。默认是${artifactId}-${version}

finalName这个术语有点用词不当，然而，正如插件构建捆绑项目拥有每一个权利去忽略或修改这个名称（但它们通常不会这么做）。例如：如果maven-jar-plugins被配置用于生成一个jar并添加一个test的classifier，然后上述定义的实际jar将会被构建为my-project-1.0-test.jar

4. filter：定义*.properties文件，该文件包含一个属性列表，这个属性列表是用于作为其他接受设置的资源。换句话说，在filter文件中被定义的“键值对”在构建时会用实际资源替换掉${name}字符串。上述例子定义了filter/目录下的filter1.properties文件。Maven的默认过滤器目录是${basedir}/src/main/filters/

##### 资源

build元素的另一个特征是指定资源存在于你工程的哪里。资源不是代码。它们不被编译，但也是要被绑定进你项目的项或被用作其他理由，比如代码生成

举个例子：一个Plexus工程需要伴随一个存在于META-INF/plexus下的configuration.xml文件（用来指定对于容器的组件配置信息）去存在。虽然我们仅需要简单地放置这个文件在src/main/resources/META-INF/plexus，但我们想取而代之的是给予Plexus一个它自己的目录src/main/plexus。为了JAR插件能够正确地绑定资源，你需要指定资源类似如下：

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <resources>
      <resource>
        <targetPath>META-INF/plexus</targetPath>
        <filtering>false</filtering>
        <directory>${basedir}/src/main/plexus</directory>
        <includes>
          <include>configuration.xml</include>
        </includes>
        <excludes>
          <exclude>**/*.properties</exclude>
        </excludes>
      </resource>
    </resources>
    <testResources>
      ...
    </testResources>
    ...
  </build>
</project>
```

1. resources：是一个资源元素列表，其中每一个元素描述了去包含什么文件和去哪获得这个文件关联进这个工程

2. targetPath：指定放置构建出的资源所处目录结构。目标路径默认是根目录。对于将被打包进一个JAR的资源，一个通常指定的目标路径是META-INF

3. filtering：值是true或者false，指示出对于这个资源是否使用过滤器。注意，*.properties过滤器文件不必被定义去过滤——资源也可以使用在POM中被默认定义的属性（例如${project.version}），被传递到命令行，通过使用-D标识（例如-Dname=value）或者通过属性元素准确定义。

4. directory：这个元素的值定义了资源从哪里找。默认用于构建的目录是${basedir}/src/main/resources

5. includes：一组用于指定在特定目录下被包含进作为资源的文件模式，使用*作为通配符

6. excludes：和includes一样的结构，但指定哪些文件被忽略。当include和exclude产生矛盾时，exclude生效

7. testResources：这个元素块包含了testResource元素。它们的定义类似resource元素，但只在test时期使用。有一点不同的是一个工程的默认（超POM所定义的）测试资源目录是${basedir}/src/test/resources。测试资源不会被部署

##### 插件

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <build>
    ...
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.6</version>
        <extensions>false</extensions>
        <inherited>true</inherited>
        <configuration>
          <classifier>test</classifier>
        </configuration>
        <dependencies>...</dependencies>
        <executions>...</executions>
      </plugin>
    </plugins>
  </build>
</project>
```


### 总结

本文源自apache官网Maven项目的官方文档，本人初读此文档时感觉晦涩难度，难以完整通读。但苦于找不到其他关于Maven学习的高质有效资料，遂决定再次“品味”官方文档并作翻译，并借此对官方文档逐行理解，并做此译文，以便后人。部分字句由于鄙人才疏学浅，无法做到准确翻译，伴有原文。翻译不当之处还请指正。

参考链接：

<a href="https://maven.apache.org/pom.html">https://maven.apache.org/pom.html</a>
