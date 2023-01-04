# 用 Maven 构建前端:简单的资源

> 原文：<https://www.sitepoint.com/building-front-end-maven-simple-resources/>

### 目录 

*   [提供静态资源](#servingupstaticresources)
*   [普通的、未经处理的文件](#plainunmanipulatedfiles)
*   [模板化文件](#templatedfiles)
*   [Maven 资源插件](#mavenresourcesplugin)
*   [Maven 战争插件](#mavenwarplugin)
*   [CSS 文件](#cssfiles)
*   [小于](#less)
*   [SCSS](#scss)
*   [生成的 HTML 文件](#generatedhtmlfiles)
*   [降价文件](#markdownfiles)
*   [AsciiDoc 文件](#asciidocfiles)
*   [总结](#summary)
*   [评论](#comments)

每当您开发一个 web 应用程序时，您总是会有许多静态资源希望提供给最终用户。这些静态文件有多种不同的形式——HTML、CSS、LESS、SCSS、Javascript、纯文本、Markdown、Asciidoc 等。–并且有许多挑战需要最好地整合到您的 web 应用程序中，以实现最简单的开发过程。本文旨在展示一些使用 Maven 插件的简单技术，以简化这些静态资源在应用程序中的开发和合并。

## 提供静态资源

假设您已经能够从 web 应用程序中提供静态资源。一般来说，无论你用什么框架来构建你的应用程序，都有标准的支持方式——比如 Spring——使用`mvc:resources`标签。此外，假设您正在使用一个 Servlet 容器，比如 Tomcat，通常情况下，您可以提供出现在`src/main/webapp`目录中的任何内容，而不需要任何额外的配置。知道静态文件需要在生成的 WAR 文件中的什么位置结束是很重要的，因为这将在本文给出的例子中重复使用。

## 普通的、未经处理的文件

可以包含的最简单形式的静态资源是那些绝对不需要操作的资源。这些是您编写的文件，然后按原样包含在 webapp 中。包括这些真的很简单。你所需要做的就是将文件放入`src/main/webapp`或`src/main/resources`中，以适合你想要它们出现的位置。包含在`src/main/webapp`中的文件将被复制到你的 WAR 文件的根目录中，而包含在`src/main/resources`中的文件将被复制到`target/classes`中，然后在你的 webapp 的类路径中结束。

## 模板文件

有时您会发现您想要一些普通文件，但是在其中包含来自 Maven 构建的扩展属性。例如，工件的版本号是一个可能被包含的公共版本号。

这可以使用已经作为你的构建的一部分使用的标准 Maven 插件来实现——[Maven 资源插件](https://maven.apache.org/plugins/maven-resources-plugin/)和 [Maven WAR 插件](https://maven.apache.org/plugins/maven-war-plugin/)——所以让我们来看看它们。

### Maven 资源插件

不需要任何额外的配置，Maven Resources 插件已经用于将`src/main/resources`目录复制到结果 JAR 或 WAR 文件中。(注意，Maven Resources 插件也用于`src/test/resources`目录，这里提到的一切同样适用于该目录)。

默认情况下，它不会过滤，所以需要一些额外的配置来支持它。过滤是在将资源复制到 webapp 时，用正确的值替换静态资源中的特殊占位符的行为。

配置资源的过滤是一个简单的例子，将以下配置添加到您的`pom.xml`文件中:

```
<build>
    <resources>
        <resource>
            <directory>src/main/filteredResources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
</build> 
```

该块有两个作用:

*   添加将从中复制资源的新目录——在本例中，我们使用了`src/main/filteredResources`
*   对该目录中的所有资源启用筛选

如果可能的话，建议您将筛选的资源分开，这样您就不会意外地对您不想要的文件执行筛选。例如，Spring 上下文文件通常包含要扩展的属性，这些属性使用完全相同的语法，但是您不希望被这个过程扩展。

然后通过在文件中添加特殊的占位符来实现过滤。这些占位符都以`${`开始，以`}`结束。大括号之间是您希望替换到文件中的属性名。比方说，您有一个文件`src/main/filteredResources/details.txt`，它包含以下内容:

```
Group ID: ${project.groupId}
Artifact ID: ${project.artifactId}
Version: ${project.version} 
```

然后，在复制到构建的 webapp 中时，这将会自动地用组 ID、工件 ID 和当前正在构建的项目版本展开。

这里可以使用的属性是 Maven 反应器可以使用的任何属性。这些属性包括默认的 Maven 属性、POM 文件中定义的其他属性以及命令行中提供的系统属性。

### Maven 战争插件

Maven WAR 插件与构建过程的集成程度稍低，因此配置它需要使用配置插件的标准机制。

同样，默认情况下，这不会进行任何过滤。和以前一样，您可能希望对筛选的文件有所选择，以便不会过多地干扰其他来源。如果您希望使用与前面类似的设置，其中一个完整的目录用于过滤的资源，另一个用于未过滤的资源，那么下面的配置将起作用。

```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <configuration>
                <webResources>
                    <resource>
                        <directory>src/main/filteredWebapp</directory>
                        <filtering>true</filtering>
                    </resource>
                    <resource>
                        <directory>src/main/webapp</directory>
                        <filtering>false</filtering>
                    </resource>
                </webResources>
            </configuration>
        </plugin>
    </plugins>
</build> 
```

和以前一样，我们现在有两个目录，其中的文件将被包含到 WAR 文件中。`src/main/webapp`中的文件将按原样包含，而`src/main/filteredWebapp`中的文件将在被复制时被过滤。

或者，您可能希望将所有文件放在一起，并有选择地只过滤其中的一小部分。这可以通过以下方式实现:

```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <configuration>
                <webResources>
                    <resource>
                        <directory>src/main/webapp</directory>
                        <filtering>true</filtering>
                        <includes>
                            <include>version.html</include>
                        </includes>
                    </resource>
                    <resource>
                        <directory>src/main/webapp</directory>
                        <filtering>false</filtering>
                        <excludes>
                            <exclude>version.html</exclude>
                        </excludes>
                    </resource>
                </webResources>
            </configuration>
        </plugin>
    </plugins>
</build> 
```

这个版本将所有文件保存在`src/main/webapp`中，并选择性地只过滤指定的文件——在本例中为`version.html`。这使得跟踪哪些文件正在使用变得更加容易，但缺点是过滤文件列表需要指定两次——一次是包含在过滤文件集中，一次是从非过滤文件集中排除。这是需要使用不同的`resource`标签来表示过滤和未过滤资源的一个不幸的副作用。

![Using Maven to process your project's resources](img/972b4c4ef90544e3bd6b2a029db3be4b.png)

## CSS 文件

任何 webapp 都需要包含级联样式表(CSS)文件来描述页面的样式。这些 CSS 文件可以通过多种方式构建，从手工制作并作为简单资源包含(如上所述)到使用 CSS 预处理器，如 [LESS](http://lesscss.org/) 或 [SASS](http://sass-lang.com/) 。

### 较少的

使用 [LESS Maven 插件](https://github.com/marceloverdijk/lesscss-maven-plugin)可以支持更少的文件。LESS 是作为一个 Javascript 应用程序实现的，LESS Maven 插件通过在运行构建的 JVM 内部执行 Javascript 来将它合并到构建中。这个插件比 SASS 插件稍微简单一些，重量也轻一些，但是 LESS 也是一个稍微简单一些的预处理器，功能也比较少。如果您以前从未使用过 CSS 预处理器，那么这是一个很好的起点。

配置构建以自动将较少的文件构建到 CSS 文件中可以按如下方式完成:

```
<build>
    <plugins>
        <plugin>
            <groupId>org.lesscss</groupId>
            <artifactId>lesscss-maven-plugin</artifactId>
            <version>1.7.0.1.1</version>
            <configuration>
                <sourceDirectory>
                    ${project.basedir}/src/main/less
                </sourceDirectory>
                <outputDirectory>
                    ${project.build.directory}/${project.build.finalName}/css
                </outputDirectory>
            </configuration>
            <executions>
                <execution>
                    <id>compile-less</id>
                    <phase>generate-resources</phase>
                    <goals>
                        <goal>compile</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build> 
```

该配置将编译`src/main/less`目录中的每个`*.less`文件，并将生成的 CSS 文件放在 webapp 的`css`目录中，就像 CSS 文件本身被放入`src/main/webapp/css`中一样。如果您希望将文件包含在类路径中——就好像它们是用`src/main/resources`编写的一样，那么只需将`outputDirectory`改为使用`classes`而不是`${project.build.finalName}`。

### SCSS

使用 [SASS Maven 插件](http://www.geodienstencentrum.nl/sass-maven-plugin/plugin-info.html)可以支持 SCSS 文件。SASS 被实现为一个 Ruby 应用程序，SASS Maven 插件通过使用 Java 的 [JRuby](http://jruby.org/) 绑定来执行 Ruby 代码，从而将其合并到构建中。这使得这个过程比 LESS 插件稍微复杂一些，但是这种复杂性主要是由 Maven 插件处理的，用户不需要关心。

配置构建以自动将 SCSS 文件构建到 CSS 文件中可以按如下方式完成:

```
<build>
    <plugins>
        <plugin>
            <groupId>nl.geodienstencentrum.maven</groupId>
            <artifactId>sass-maven-plugin</artifactId>
            <version>2.23</version>
            <executions>
                <execution>
                    <id>lint</id>
                    <phase>validate</phase>
                    <goals>
                        <goal>scss-lint</goal>
                    </goals>
                </execution>
                <execution>
                    <id>build</id>
                    <phase>generate-resources</phase>
                    <goals>
                        <goal>update-stylesheets</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <sassSourceDirectory>
                    ${project.basedir}/src/main/scss
                </sassSourceDirectory>
                <destination>
                    ${project.build.directory}/${project.build.finalName}/css
                </destination>
            </configuration>
        </plugin>
    </plugins>
</build> 
```

该配置将编译`src/main/scss`目录中的每个`*.scss`文件，并将生成的 CSS 文件放在 webapp 的`css`目录中，就像 CSS 文件本身被放入`src/main/webapp/css`中一样。如果您希望将文件包含在类路径中——就好像它们是用`src/main/resources`编写的一样，那么只需将`destination`改为使用`classes`而不是`${project.build.finalName}`。

注意，SASS 插件提供给 LESS 插件的一个特性是支持样式表的林挺。上面的配置是在早期阶段执行，这将确保 SCSS 文件在语法上是有效的。这对于确保构建在构建生命周期的早期是有效的非常有用。

## 生成的 HTML 文件

有时，您可能需要从作为构建一部分的标记文件中生成 HTML。例如，您可能以 Markdown 或 AsciiDoc 格式维护 API 文档，并希望将此文档的 HTML 版本发布为已部署 webapp 的一部分。(甚至有可能从您的代码中生成这些内容，然后使用这个插件将其转换成 HTML。这样做是留给读者的一个练习。)

### 降价文件

通过使用[减价页面生成器插件](https://github.com/walokra/markdown-page-generator-plugin)可以将减价文件转换成 HTML 文件。这个插件将自动为所有被处理的降价文件生成 HTML 文件。它可以被配置为完全定制生成文件的风格，但开箱即用，它仍然可以生成完全可用的文件。

将构建配置为自动将降价文件构建为 HTML 文件可以按如下方式完成:

```
<build>
    <plugins>
        <plugin>
            <groupId>com.ruleoftech</groupId>
            <artifactId>markdown-page-generator-plugin</artifactId>
            <version>1.0.0</version>
            <executions>
                <execution>
                    <phase>process-sources</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <inputDirectory>
                    ${project.basedir}/src/main/markdown
                </inputDirectory>
                <outputDirectory>
                    ${project.build.directory}/${project.build.finalName}/docs
                </outputDirectory>
            </configuration>
        </plugin>
    </plugins>
</build> 
```

该配置将处理`src/main/markdown`中的每个`*.md`文件，并在 webapp 的`docs`目录中生成 HTML 文件，就像 HTML 文件本身被放入`src/main/webapp/docs`中一样。

### AsciiDoc 文件

使用[asciidor Maven 插件](https://github.com/asciidoctor/asciidoctor-maven-plugin)可以将 AsciiDoc 文件转换成 HTML 文件。这个插件将自动为所有被处理的 AsciiDoc 文件生成 HTML 文件。它可以被配置为完全定制生成文件的风格，但开箱即用，它仍然可以生成完全可用的文件。

配置 build 以自动将 AsciiDoc 文件构建为 HTML 文件可以按如下方式完成:

```
<build>
    <plugins>
        <plugin>
            <groupId>org.asciidoctor</groupId>
            <artifactId>asciidoctor-maven-plugin</artifactId>
            <version>1.5.5</version>
            <executions>
                <execution>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>process-asciidoc</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <sourceDirectory>
                    ${project.basedir}/src/main/asciidoc
                </sourceDirectory>
                <outputDirectory>
                    ${project.build.directory}/${project.build.finalName}/docs
                </outputDirectory>
                <preserveDirectories>true</preserveDirectories>
                <backend>html5</backend>
            </configuration>
        </plugin>
    </plugins>
</build> 
```

这种配置将处理`src/main/asciidoc`中的每个`*.ad`、`*.adoc`和`*.asciidoc`文件，并在 webapp 的`docs`目录中生成 HTML 文件，就像 HTML 文件本身被放入`src/main/webapp/docs`中一样。

## 摘要

无论是过滤一些模板化的资源文件来添加构建信息，复制 CSS 文件，处理 LESS 或 SCSS 文件，还是将 Markdown 一个 Asciidoc 转换成 HTML，都有一个 Maven 插件可以做任何事情。它们中的每一个都比这里所涵盖的要深入得多，所以如果你想用它们做更多的事情，可能需要一些配置工作。然而，上面的配置示例是现成可用的，可以作为构建的标准部分添加对这些技术的支持。

希望这已经给出了一些想法，可以简化 web 应用程序中前端资产的包含，而不需要复杂的多阶段构建。

## 分享这篇文章