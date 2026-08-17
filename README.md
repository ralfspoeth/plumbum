# ´Εις έαυτόν

I am using this module in order to make publishing artefacts to maven central easier. I'll keep the dependencies and
plugins up to date.

Beginning with version 2.0.0 we'll include `jspecify` annotations but no longer `JUnit` dependencies since these may
easily be imported.

There is a [Sample POM](sample-pom.xml)

After making `plumbum` the parent of my library

    <parent>
        <groupId>io.github.ralfspoeth</groupId>
        <artifactId>plumbum</artifactId>
        <version>3.0.3</version>
    </parent>

    <artifactId>my</artifactId>
    <version>1.0.0-SNAPSHOT</version>

The `scm` node is necessary as well:

    <scm>
        <url>scm:git:https://github.com/ralfspoeth/my</url>
        <developerConnection>scm:git:https://github.com/ralfspoeth/my.git</developerConnection>
        <tag>HEAD</tag>
    </scm>

I'll have to provide the name and a description, as in

    <name>Brief name</name>
    <description>
        Oh what MY does to the greater good of society
    </description>
    <url>https://github.com/ralfspoeth/my</url>

I then need to add these plugins in my `pom.xml`

    <!-- sonatype publishing (Central Portal) -->
    <plugin>
        <groupId>org.sonatype.central</groupId>
        <artifactId>central-publishing-maven-plugin</artifactId>
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

Furthermore, I may simply add this dependency:

    <!-- jspecify -->
    <dependency>
        <groupId>org.jspecify</groupId>
        <artifactId>jspecify</artifactId>
    </dependency>

That's all there is.

## Publishing to the Central Portal

Since version 2.1.0 publishing goes through the
[`central-publishing-maven-plugin`](https://central.sonatype.org/publish/publish-portal-maven/)
instead of the retired OSSRH / `nexus-staging` route. `mvn deploy` builds a bundle, uploads it to the Central Portal,
and - because `autoPublish` is on - publishes it once validation passes.

This needs a Central Portal user token in `settings.xml` under the server id
`central` (not the old `ossrh`):

    <settings>
      <servers>
        <server>
          <id>central</id>
          <username><!-- portal token username --></username>
          <password><!-- portal token password --></password>
        </server>
      </servers>
    </settings>

Generate the token at https://central.sonatype.com/account. GPG signing, javadoc and sources jars are still required and
are configured by this parent.

### Signing

Since 3.0.0 the gpg plugin lives in a `release-sign-artifacts` profile rather than being bound unconditionally. It
binds to the `verify` phase, so an unconditional binding meant that every `mvn verify` - every local build that ran
the tests - stopped to ask for the signing passphrase.

The release plugin activates that profile for the deploy it forks, through its `releaseProfiles` setting:

    mvn release:prepare release:perform     # signs
    mvn verify                              # does not, and does not ask

Do not reach for `-DperformRelease=true` instead: `useReleaseProfile` defaults to false since release plugin 3.0.0,
so `perform` no longer sets that property, and a profile relying on it would never activate. Publishing without the
release plugin names the profile:

    mvn deploy -Prelease-sign-artifacts

An unsigned deployment is rejected by the Central Portal, so forgetting it fails loudly at upload rather than
publishing something unsigned.

A child project must not list `maven-gpg-plugin` in its own `<build><plugins>`: that binds it unconditionally again
and the prompt returns.

### Integration tests

`maven-failsafe-plugin` is managed here but not activated: surefire matches `*Test`, not `*IT`, so a module whose
integration tests are named `*IT` needs failsafe or they compile and are silently never run. A module that wants them
adds the plugin with no version and no configuration:

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-failsafe-plugin</artifactId>
    </plugin>

and they run at `integration-test`, that is under `mvn verify` but not under `mvn test`.