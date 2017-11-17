# Maven - Dependency Management
 -- by Hao Hsiang Song

In the world of programming, we almost always rely on codes from outside world, say Apache libraries, Junit framework, Spring Boot, etc. We call them dependencies. [Maven](https://maven.apache.org/index.html) is such a role that it manages those for us so that we do not worry about downloading and moving them under classpath.

---

## Motivation
Traditionally, we put external jars under classpath: typically a lib folder. The work itself is such a hassle that we have no centralized way of managing those and developers hate manual managements. Apart from that, it also faciliates project building process.

#### 1. Lifecycle
There is a concept in software developement called "convention over configuration" which proposes that the needs are common throughout numerous projects, why not follow the conventions? Hence, there is a predefine structure of a Maven project, a predefined cycle of building a project, a hierachy of configuration inheritance. 

For the folder structure, this wiki image is really clear:
![alt text](https://upload.wikimedia.org/wikipedia/commons/c/cf/Maven_CoC.svg)
As can be seen, the test source are deferent from the main source. It makes packaging easier later on since we do not deploy tests to production servers.

#### 2. ClassNotFoundException when class is there!!! 
The problem arises when there are multiple version of the same jars in the lib folder. And mostly the newer version has classes that are introduced after the older version.
To understand why the version specified does not help, we have to think about the dependencies. There could be dependencies that uses a 1.1.1 version of Log4j internally. And we have our project depends both on that dependency and a 1.5.7 version of Log4j. Since Maven manages ALL the dependecies needed for the whole project. It downloads all the depenedencies needed for all our dependencies. That is where the versions clash one another. The problem is referred as Dependecy Hell or Jar Hell.
We might think that the version is specified explicitly in the pom.xml. However, reality is that Maven, at best, pack the project for us and its job stop from there. What would happen is the WAR file gets deployed to Tomcat. And the classloader reads one of the version of the JARs and uses that at runtime since we cannot have the same class name under the same package. That is why you only experience it at runtime.
There is no point pointing out a problem without having some direction of solving it. 
- One obvious and easy way is to force every duplicate dependencies to be the latest by explicitly exclude the order version in pom. It works most of the time because newer library usually supports downward compatibility.

```xml
<dependency>
  <groupId>sample.ProjectA</groupId>
  <artifactId>Project-A</artifactId>
  <version>1.0</version>
  <scope>compile</scope>
  <exclusions>
    <exclusion>
      <groupId>sample.ProjectB</groupId>
      <artifactId>Project-B</artifactId>
    </exclusion>
  </exclusions> 
</dependency>
```
- Were things that easy, ... I imagine providing a self-defined classloader might help since after all developers brutal: whatever does not meet our requirements, we override it. The solution is also mentioned on [stackoverflow](https://stackoverflow.com/questions/6909306/jar-hell-how-to-use-a-classloader-to-replace-one-jar-library-version-with-anoth)

- [Spotify Lab](https://labs.spotify.com/2015/09/01/java-linking/) has a more in-depth discussion on this topic.

#### 3. Centralized dependencies
Back to our first intuitive solution -- force an updated dependency, sometime it is just different projects across the teams that have messy dependencies. There is no certain reason why a project uses 2.1.3 while another uses 3.0.0. For this problem, Maven provides centralized managing means:

- Custom properties: we can define our versioning as followed.
```xml
<properties>
  <mybatis.version>3.3.1</mybatis.version>
  <mybatis.spring.version>1.2.4</mybatis.spring.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>${mybatis.version}</version>
  </dependency>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>${mybatis.spring.version}</version>
  </dependency>
</dependencies>
```
We link the version to our properties.

- Dependency Management:
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.3.1</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.2.4</version>
    </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
  </dependency>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
  </dependency>
</dependencies>
```
The versioning of dependencies in dependency management simply server as a look up place. Later on, when the dependencies are required. We simply specify the artifacts needed. 

- Pom inheritance:
```xml
<groupId>com.our.group</groupId>
<artifactId>our-project</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>pom</packaging>
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.3.5.RELEASE</version>
  <relativePath />
</parent>
```
Similar to java inheritance, pom files could be extended by specifying the parent and the hierachy is not limited to two levels. So what we would like to do here is to declare a parent pom that locks the versioning of common dependencies in dependency management. The is a common technique used everywhere.

- Mix and match: all three methods above could be mixed and match however we want.

This diagram from tutorial.jenkov.com shows the hierachy:

![alt text](http://tutorials.jenkov.com/images/maven/maven-super-pom.png)

#### 4. Assembly
When it comes to deployments, assembly get handy. It is a Maven plugin that emphasizes the packaging of the JAR. A descriptor xml serving as a recipe of our JAR tells what to pack and what not to pack and how we want them packed. It becomes especially useful when we want to have a self-contained zip file that has all the JARs needed for distribution. 

However, at Innova Solutions, we met a problem that we want to pack both a master and a slave project into one distributable zip file, no matter how we specify the descriptor, there is always some random JARs missing, which is not self-contained at all. I found this slick solution on the Internet that solves the problem without a complicated configuration: simply have a new project named distribution that depends on both master and the slave and pack the distribution. That's it! Pretty neat.


