# Maven Repository considerations #
Since version 0.0.5, this project is hosted on **[Nexus OSS Repository](http://oss.sonatype.org/)** that receive staged artifacts. After a while theses artifacts can be promoted to release artifacts and synced with the **Maven Central Repository** hourly

# Maven project integration #
  * Add in your **pom.xml** a dependency into JHOCR artifact:
```
<dependency>
  <groupId>com.googlecode.jhocr</groupId>
  <artifactId>jhocr</artifactId>
  <version>0.0.7</version>
</dependency>
```


[Search JHOCR on The Central Repository](http://search.maven.org/#browse%7C1318237209)

The [Nexus OSS Release Repository](https://oss.sonatype.org/content/repositories/releases/com/googlecode/jhocr/jhocr/) contains released versions and requires no additionnal configuration.

If you want use snapshot versions, refer to the [Nexus OSS Snapshot Repository](https://oss.sonatype.org/content/repositories/snapshots/com/googlecode/jhocr/) with this configuration:

```
    <repositories>
        <repository>
            <id>sonatype.oss.snapshots</id>
            <name>Sonatype OSS Snapshot Repository</name>
            <url>http://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
              <enabled>false</enabled>
            </releases>
            <snapshots>
              <enabled>true</enabled>
            </snapshots>
        </repository> 
    </repositories>
```


# Building this project #
This project is build on top of maven and need some tools for achieve a complete deployement process. Keep in mind that this process require some private stuff like
  1. Deployement access to the OSS Nexus Repository
  1. PGP Secret Key for sign artifacts.
  1. Google Code Subversion access for commiters (**that can be provided on demand**)
  1. Google code authorization for setting new  manual downloads

That's why, The following guide can help you to build or participate to this project but not really deploy yourself artifacts.

## Required stuff ##
3 tools are needed:
  1. Subversion that can be found http://www.open.collab.net/downloads/subversion/ for checkout or commiters
  * Maven that can be downloaded http://maven.apache.org/download.html for build it
  * GnuPG that can be downloaded http://www.gnupg.org/ for sign it

In order to be synced into the Maven Central Repository, JHOCR artifacts are signed. the public key **1FCA9471** can be found at http://pgp.mit.edu/ (searchstring: 0xb19a415f1fca9471) for authentification control. You don't need to sign it for a simple build. This steps is only required when releasing and deploying the maven project.

You need to do some pre-configurations of theses tools:
  * '%MAVEN\_HOME%/conf/settings.xml' for configure repository access (required for the release)
  * GnuPG ring keys required for sign artifacts (and '$GNUPGHOME' environment variable)

The 'settings.xml' must refer repository access and GPG passphrase like it:
```
<settings>
    <servers>
        <server>
            <id>sonatype-nexus-staging</id>
            <username>your_sonatype_login</username>
            <password>your_sonatype_passwd</password>
        </server>
        <server>
            <id>sonatype-nexus-snapshots</id>
            <username>your_sonatype_login</username>
            <password>your_sonatype_passwd</password>
        </server>
    </servers>

    <profiles>
      <profile>
﻿   <id>sign-artifacts</id>
         <properties>
             <gpg.passphrase>XXXXXX</gpg.passphrase>
         </properties>
      </profile>
   </profiles>
   
   <activeProfiles>
    ﻿  <activeProfile>sign-artifacts</activeProfile>
   </activeProfiles>
</settings>
```

After what, you can build this project following theses commands:
  * Build the project: 'mvn clean package'
  * Install the artifact in local repository: 'mvn install'

## Create a release tag in subversion ##
For this step you require a write access over 'https' on googlecode subversion.
You can be promote to contributors on demand but you need a googlecode account.
  1. 'mvn clean release:prepare -Dusername=XXXXXX -Dpassword=XXXXXXX'
  1. 'mvn release:perform -Dusername=XXXXXX -Dpassword=XXXXXXX'
  1. 'mvn release:clean'

For this step, you should require to run you 'cmd' windows shell in Administrator Mode for Windows Vista / Windows 7

For deploying **apidocs** inside googlecode, we commit this docs under svn and browse it directly. The counter-part of this method is that ìt requires 'svn:mime-type' for all files in order to be correctly rendered.

You must configure your svn 'config' file under '%USER%/AppData/Roaming/Subversion' on windows like it:
```
[miscellany]
enable-auto-props = yes

[auto-props]
*.html = svn:mime-type=text/html
*.css = svn:mime-type=text/css
*.txt = svn:mime-type=text/plain
*.jpg = svn:mime-type=image/jpeg
*.gif = svn:mime-type=image/gif
*.png = svn:mime-type=image/png
*.jar = svn:mime-type=application/java-archive
*.jnlp = svn:mime-type=application/x-java-jnlp-file
```

Be careful, this auto mime-types must be configured in other place to order to work under Netbeans.
The netbeans placeholder is like '%USER%\.netbeans\6.9\config\svn\config'

## Deploying staged artifacts ##
  * 'mvn clean deploy -Dgpg.passphrase=yourpassphrase'
  * You should use after the [OSS Nexus repository](http://oss.sonatype.org/index.html) interface to promote an artifact

# Creating an account on Sonatype OSS repository #
See the [Sonatype Repository hosting guide](http://nexus.sonatype.org/oss-repository-hosting.html)

Your **pom.xml**'s project must describe sonatype repositories:
```
        <repository>
            <id>sonatype-nexus-staging</id>
            <name>Nexus Release Repository</name>
            <url>http://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
        </repository>
        <snapshotRepository>
            <id>sonatype-nexus-snapshots</id>
            <name>Sonatype Nexus Snapshots</name>
            <url>http://oss.sonatype.org/content/repositories/snapshots</url>
        </snapshotRepository>
```

  * On sonatype, you must promote this staged artifact to release

# Maven documentations resources #
  * [Maven lifecycle](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
  * [maven-compiler-plugin](http://maven.apache.org/plugins/maven-compiler-plugin/)
  * [maven-resources-plugin](http://maven.apache.org/plugins/maven-resources-plugin/)
  * [maven-source-plugin](http://maven.apache.org/plugins/maven-source-plugin/)
  * [maven-javadoc-plugin](http://maven.apache.org/plugins/maven-javadoc-plugin/)
  * [how to generate pgp signatures](http://www.sonatype.com/people/2010/01/how-to-generate-pgp-signatures-with-maven/)
  * [Writing Maven Plugins](http://www.sonatype.com/books/mvnref-book/reference/writing-plugins.html)