# ´Εις έαυτόν

I am using this module in order to 
make publishing artefacts to maven central
easier. I'll keep the dependencies and plugins up to date.

Beginning with version 2.0.0 we'll include `jspecify` annotations
but no longer `JUnit` dependencies since these may easily 
be imported.

After making `plumbum` the parent of my library 

    <parent>
        <groupId>io.github.ralfspoeth</groupId>
        <artifactId>plumbum</artifactId>
        <version>2.0.0</version>
    </parent>

I just need to add these plugins In my `pom.xml`

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