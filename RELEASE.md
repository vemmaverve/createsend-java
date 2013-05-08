# Releasing createsend-java

## Requirements

 * You must have Apache Maven 3.0.4+ installed.
 * You must have a gpg key which is registered on a public key server (see [How To Generate PGP Signatures With Maven][])
 * You must have a Sonatype OSS account (see [Sonatype OSS Maven Repository Usage Guide][])
 * You must have added the Sonatype repositories and your Sonatype credentials to your `~/.m2/settings.xml` file:

 ```xml
 <settings>
   <servers>
     <server>
       <id>sonatype-nexus-snapshots</id>
       <username>username</username>
       <password>password</password>
     </server>
     <server>
       <id>sonatype-nexus-staging</id>
       <username>username</username>
       <password>password</password>
     </server>
   </servers>
   <profiles>
     <profile>
       <id>gpg</id>
       <properties>
         <gpg.passphrase>yourpassphrase</gpg.passphrase>
         <gpg.keyname>yourkeyname</gpg.keyname>
       </properties>
     </profile>
   </profiles>
   <activeProfiles>
     <activeProfile>gpg</activeProfile>
   </activeProfiles>
 </settings>
 ```

## Releasing developer snapshot versions

You can release a developer snapshot version of the package at any time. Snapshot versions have the `-SNAPSHOT` suffix appended, and can be released by running:

```sh
$ gradle uploadArchive
```

Developer snapshot versions can be found in the [snapshot repo][].

## Releasing a production version

Increment version constants in the following files, ensuring that you use [Semantic Versioning][]:

 * `build.gradle`
 * `pom.xml`
 * `src/com/createsend/util/config.properties`

Commit your changes:

```sh
$ git commit -am "Version X.Y.Z"
```

### Releasing to Sonatype OSS staging repository

Ensure that the version specified in `pom.xml` includes the `-SNAPSHOT` suffix: `<version>X.Y.Z-SNAPSHOT</version>`

```sh
$ mvn -Dresume=false release:prepare
$ mvn release:perform
```

This will build and sign all artifacts and upload them to the staging repository.

### Promoting the release from staging

In order to promote the release from staging, log in to [Sonatype OSS][], and from the "Build Promotion" tab on the left hand site select "Staging Repositories".

The release you just uploaded should show up in the list. Select it and pick "Close". This will check if the deployment is complete and properly signed, then create a staging repository which can be used for testing. Once everything works you select "Release" to actually release it to the [release repo][]. The release repo is synced with [Maven Central][].

### Generate and publish javadoc

Generate and publish javadoc:

```sh
$ ./update-javadoc.sh
```

Documentation is published to: http://campaignmonitor.github.io/createsend-java/doc/

[Sonatype OSS Maven Repository Usage Guide]: https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide
[Sonatype OSS]: https://oss.sonatype.org/
[How To Generate PGP Signatures With Maven]: https://docs.sonatype.org/display/Repository/How+To+Generate+PGP+Signatures+With+Maven
[release repo]: https://oss.sonatype.org/content/repositories/releases/com/createsend/createsend-java/
[snapshot repo]: https://oss.sonatype.org/content/repositories/snapshots/com/createsend/createsend-java/
[Maven Central]: http://repo1.maven.org/maven2/
[Semantic Versioning]: http://semver.org/