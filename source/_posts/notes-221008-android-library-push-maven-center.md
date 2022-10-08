---
title: Android发布aar到MavenCenter配置文件
tags: Android
categories: 笔记
date: 2022-10-08 15:37:00
---

### Gradle配置文件 

> 在 build.gradle 中添加

```
apply from: 'maven-publish.gradle'
```

> 创建配置文件 gradle.properties

``` properties
GROUP=io.github.thisdk
ARTIFACT_ID=core
VERSION_NAME=0.0.2-SNAPSHOT

POM_NAME=Core
POM_DESCRIPTION=Modern Android Development Library
POM_PACKAGING=aar
POM_URL=https://github.com/thisdk/fast-dev-frame

POM_SCM_URL=https://github.com/thisdk/core
POM_SCM_CONNECTION=scm:git:git://github.com/thisdk/core.git
POM_SCM_DEV_CONNECTION=scm:git:ssh://git@github.com/thisdk/core.git

POM_LICENCE_NAME=The Apache Software License, Version 2.0
POM_LICENCE_URL=http://www.apache.org/licenses/LICENSE-2.0.txt
POM_LICENCE_DIST=repo

POM_DEVELOPER_ID=thisdk
POM_DEVELOPER_NAME=Jason Yuan
POM_DEVELOPER_EMAIL=thisdk@gmail.com
```

> 创建文件 maven-publish.gradle

``` gradle
apply plugin: 'maven-publish'
apply plugin: 'signing'

task androidSourcesJar(type: Jar) {
    archiveClassifier.set("sources")
    from android.sourceSets.main.java.srcDirs
    exclude "**/R.class"
    exclude "**/BuildConfig.class"
}

publishing {
    publications {
        maven(MavenPublication) {
            groupId GROUP
            artifactId ARTIFACT_ID
            version VERSION_NAME
            afterEvaluate { artifact(bundleReleaseAar) }
            artifact androidSourcesJar
            pom {
                name = POM_NAME
                packaging = POM_PACKAGING
                description = POM_DESCRIPTION
                url = POM_URL
                licenses {
                    license {
                        name = POM_LICENCE_NAME
                        url = POM_LICENCE_URL
                        distribution = POM_LICENCE_DIST
                    }
                }
                developers {
                    developer {
                        id = POM_DEVELOPER_ID
                        name = POM_DEVELOPER_NAME
                        email = POM_DEVELOPER_EMAIL
                    }
                }
                scm {
                    url = POM_SCM_URL
                    connection = POM_SCM_CONNECTION
                    developerConnection = POM_SCM_DEV_CONNECTION
                }
            }

            pom.withXml {
                def dependenciesNode = asNode().appendNode('dependencies')
                configurations.implementation.allDependencies.each {
                    if (it.group != null && (it.name != null && "unspecified" != it.name) && it.version != null) {
                        def dependencyNode = dependenciesNode.appendNode('dependency')
                        dependencyNode.appendNode('groupId', it.group)
                        dependencyNode.appendNode('artifactId', it.name)
                        dependencyNode.appendNode('version', it.version)
                        dependencyNode.appendNode('scope', 'implementation')
                    }
                }
            }

        }
    }
    repositories {
        maven {
            def releasesRepoUrl = "https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/"
            def snapshotsRepoUrl = "https://s01.oss.sonatype.org/content/repositories/snapshots/"
            url = VERSION_NAME.endsWith('-SNAPSHOT') ? snapshotsRepoUrl : releasesRepoUrl
            credentials {
                username findProperty("SONATYPE_USERNAME") ?: System.getenv("SONATYPE_USERNAME")
                password findProperty("SONATYPE_PASSWORD") ?: System.getenv("SONATYPE_PASSWORD")
            }
        }
    }
}

signing {
    sign publishing.publications
}
```