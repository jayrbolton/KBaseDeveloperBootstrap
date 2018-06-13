
# How to work with packages in the Artifactory

KBase's JFrog Artifactory is a package management server that can host packages for many languages and projects.

* Artifactory URL: http://ci.kbase.us:8081

Work with an existing dev to get set up as an admin to publish repositories and artifacts.

## Starting a Gradle project

#### Java

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

## Converting an ant project to Gradle

The easiest way is to create a new project with `gradle init --type java-library`, move source files over individually from the old project, and test as you go. Be sure to configure the the project layout by configuring the appropriate source set, as seen on https://docs.gradle.org/current/userguide/java_plugin.html 

## Deploying

#### Revisions

When deploying a gradle Java package, deploy to 'gradle-dev-local' or 'gradle-release-local' and select the 'Deploy According To Layout' option in the "Deploy" modal.

Set the 'org' as 'kbase', the module as the name of your library, and 'baseRev' to your semantic version (eg. 0.0.1). Leave the 'folderItegRev', 'fileItegRev', and 'classifer' fields blank.

Folder integration revision (`folderItegRev`)
