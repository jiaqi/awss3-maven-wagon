# S3 Maven Wagon

An extension of Maven that read and write dependencies, artifacts and Maven
site files in AWS S3.

To get started, first define the extension in the POM file.

```xml
<build>
  <extensions>
    <extension>
      <groupId>org.cyclopsgroup</groupId>
      <artifactId>awss3-maven-wagon</artifactId>
      <version>1.5.0</version>
    </extension>
  </extensions>
</build>
```

With the extension defined, the protocol `s3://` becomes available for url of
repositories. Now we can define the repository for dependencies with it. This
following definition declares a snapshot repository.

```xml
<repositories>
  <repository>
    <id>my-server</id>
    <name>My snapshot repository</name>
    <releases>
      <enabled>false</enabled>
    </releases>
    <snapshots>
      <enabled>true</enabled>
      <updatePolicy>always</updatePolicy>
      <checksumPolicy>warn</checksumPolicy>
    </snapshots>
    <url>s3://mybucket/maven/snapshot-repository</url>
  </repository>
</repositories>
```

And following definition makes sure artifacts are uploaded to the same s3
repository.

```xml
<distributionManagement>
  <snapshotRepository>
    <id>my-server</id>
    <name>My snapshot server</name>
    <url>s3://mybucket/maven/snapshot-repository</url>
  </snapshotRepository>
</distributionManagement>
```

Unless your s3 bucket is open to public, the credentials need to be added to
`$HOME/.m2/settings.xml`.

```xml
<servers>
  <server>
    <id>my-server</id>
    <username>AWS Key ID</username>
    <password>AWS Secret Key</password>
  </server>
```

With settings above, `mvn deploy` uploads artifacts to the S3 bucket. The same
setup works for site distribution as well.

```xml
<distributionManagement>
  <site>
    <id>my-server</id>
    <url>s3://mybucket/projects/jmxterm</url>
  </site>
</distributionManagement>
```

Now `maven site:deploy` uploads genrated maven site to the s3 bucket.

By defining the extension creatively at the top of the POM structure, people
can create an organization with private maven repository protected by AWS IAM.
