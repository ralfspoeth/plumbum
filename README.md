# ´Εις έαυτόν

I am using this module in order to 
make publishing artefacts to maven central
easier. I'll keep the dependencies and plugins up to date.

Beginning with version 2.0.0 we'll include `jspecify` annotations
but no longer `JUnit` dependencies since these may easily 
be imported.

There is a [Sample POM](sample-pom.xml)

After making `plumbum` the parent of my library 

    <parent>
        <groupId>io.github.ralfspoeth</groupId>
        <artifactId>plumbum</artifactId>
        <version>2.0.5</version>
    </parent>

I'll have to provide the name of my project by adding the 
`plumbum.name` property:

    <!-- provide my project name -->
    <properties>
        <plumbum.name>my</project.name>
    </properties>

and a description, as in 
    
    <name>Brief name</name>
    <description>
        Oh what MY does to the greater good of society
    </description>

I then need to add these plugins in my `pom.xml`

    <!-- sonatype publishing -->
    <plugin>
        <groupId>org.sonatype.plugins</groupId>
        <artifactId>nexus-staging-maven-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-gpg-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-release-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-javadoc-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
    </plugin>

    <!-- for compilation and Junit Tests -->
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
    </plugin>
    
Furthermore, I may simply add these dependencies:

    <!-- jspecify -->
    <dependency>
        <groupId>org.jspecify</groupId>
        <artifactId>jspecify</artifactId>
    </dependency>

That's all there is.