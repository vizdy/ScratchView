
Maven publishing related code changes in gradle files.

1. There are two files which handle maven publishing,
   a) scripts/publish-module.gradle
   b) scripts/publish-root.gradle

   scripts/publish-root.gradle is included in lib's top 
   level build.gradle file as last line.

2. Lib's  Scratch/build.gradle file defines 3 env variables needed,
   ext {
     PUBLISH_GROUP_ID = 'com.sovilo.scratch'
     PUBLISH_ARTIFACT_ID = 'Scratch'
     PUBLISH_VERSION = '2.0.1'
   }

3. Notice below changes to include dependencies in plugin in top
   level build.gradle file i.e.
       dependencies {
           ...
           classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.30")
           classpath("org.jetbrains.dokka:dokka-gradle-plugin:1.5.0")
       }

       plugins {
          id 'io.github.gradle-nexus.publish-plugin' version '1.0.0'
       }

4. With above changes code should compile.


Publish to local repository
===========================
  Ensure fresh compilation, if any changes are done in code,
  before publishing.
  # ./gradlew build
  # ./gradlew tasks
  Module name 'Scratch' before task is not compulsory
  # ./gradlew Scratch:publishReleasePublicationToMavenLocal

Verify local publishing
=======================
  - After publishing to local maven, check signed data for lib version i.e.
  ls ~/.m2/repository/com/sovilo/scratch/Scratch/2.0.1/

  - Check content of aar file i.e. # jar tvf Scratch-2.0.1.aar
  and extract contents # jar xvf Scratch-2.0.1.aar
  
  - Check classes.jar file extract from above aar to check its classes.
  This jar will be empty if minifyEnabled is set to true in
  Scratch/build.gradle file. So ensure for the lib, always set minifyEnabled
  as false.

Publish to Sonatype staging repository
======================================
  Ensure fresh compilation, if any changes are done in code,
  before publishing.
  # ./gradlew build
  # ./gradlew tasks
  # ./gradlew publishReleasePublicationToSonatypeRepository

Release to mavenCentral
=======================

For manual release,
    a) Visit latest staging server i.e. https://s01.oss.sonatype.org/
       (not https://oss.sonatype.org) and login there.
       Login / password for this server, is the same which is used to
       create login to file Jira ticket on https://issues.sonatype.org/login.jsp
       i.e. request was made to create staging repo for com.sovilo.scratch through
       https://issues.sonatype.org/browse/OSSRH-72929 and it was created
       within 24 hrs.

    b) Now execute cd to publish lib on above Sonatype staging repo i.e.
       # ./gradlew publishReleasePublicationToSonatypeRepository

    c) Now login again on https://s01.oss.sonatype.org/ and check latest push
       there. See bottom area and check 'Content' tab. If all looks fine then,
       look at top area and click on Close. Without any comment click on confirm
       to close the staging repo. If this goes fine then 'Release' option will
       appear in top area. Click on 'Release' button which will again open
       Confirm window. After confirmation, in 10-15 mins, it will get
       published.

    d) Published lib can be verified by accessing the release URL i.e
       https://repo1.maven.org/maven2/com/sovilo/scratch/Scratch/2.0.1/

    e) Once it is visible in release repo, it can be used in your app by
       including below line in dependencies in app/build.gradle,
       implementation 'com.sovilo.scratch:Scratch:2.0.1'
       Also, in top level build.gradle file ensure mavenCentral() is present
       in buildscript repositories section.


