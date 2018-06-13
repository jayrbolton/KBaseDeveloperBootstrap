
# How to work with packages in the Artifactory

KBase's JFrog Artifactory is a package management server that can host packages for many languages and projects.

* Artifactory URL: http://ci.kbase.us:8081

Work with an existing dev to get set up as an admin to publish repositories and artifacts.

## Creating a Gradle project

### Java

Follow: https://guides.gradle.org/building-java-applications/

Some quick startup commands

```sh
$ mkdir my-library
$ cd my-library
$ gradle init --type java-library
$ gradle tasks
$ gradle test
$ gradle build
```

Now you have a `lib-0.0.1.jar` file in `build/libs`. If you go to the artifactory in your browser, select a repository, and click "Deploy", you will be able to publish your project.

Set up your project to work more directly with artifactory by going to the artifactory web app (http://ci.kbase.us:8081), going to the repository browser, clicking a repository, and then clicking "Set Me Up" at the top right. From there, click "Generate Gradle Settings" and then "Generate Settings" to get a Gradle build file that you can use.

Also, be sure to click "Download gradle.properties" and place that file in the root of your project. Make sure that file is ignored by git -- **do not check the gradle.properties into git**.

A typical Gradle build file might look like this:

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'org.jfrog.buildinfo:build-info-extractor-gradle:4+'
    }
}

plugins {
    id 'java-library'
}

allprojects {
    apply plugin: 'com.jfrog.artifactory'
}

dependencies {
    api 'com.fasterxml.jackson.core:jackson-core:2.2.3'
    api 'com.fasterxml.jackson.core:jackson-databind:2.2.3'
    api 'com.fasterxml.jackson.core:jackson-annotations:2.2.3'

    // Install the kbase/auth-0.5.0 library from the artifactory
    api 'kbase:auth:0.5.0'

    // Use JUnit test framework
    testImplementation 'junit:junit:4.12'
} 

version = '0.0.25'

jar {
    manifest {
        attributes(
            'Implementation-Title': project.name,
            'Implementation-Version': project.version
        )
    }
}

// Artifactory configuration
// This uses data from your gradle.properties file
artifactory {
    contextUrl = "${artifactory_contextUrl}"
    publish {
        repository {
            repoKey = 'gradle-dev-local'
            username="${artifactory_user}"
            password="${artifactory_password}"
            maven = true
        }
    }
    resolve {
        repository {
            repoKey = 'gradle-dev'
            username = "${artifactory_user}"
            password = "${artifactory_password}"
            maven = true
        }
    }
}
```


## Converting an ant project to Gradle

The easiest way is to create a new project with `gradle init --type java-library`, move source files over individually from the old project, and test as you go. 

Be sure to configure the the project layout by configuring the appropriate source set, as seen on https://docs.gradle.org/current/userguide/java_plugin.html 

## Deploying

#### Revisions

When deploying a gradle Java package, deploy to 'gradle-dev-local' or 'gradle-release-local' and select the 'Deploy According To Layout' option in the "Deploy" modal.

Set the 'org' as 'kbase', the module as the name of your library, and 'baseRev' to your semantic version (eg. 0.0.1). Leave the 'folderItegRev', 'fileItegRev', and 'classifer' fields blank.

Folder integration revision (`folderItegRev`)
