- **gradle的一些理解https://github.com/rujews/android-tech-docs/blob/master/new-build-system/user-guide/README.md**

![这里写图片描述](http://img.blog.csdn.net/20170223020846568?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
-------------------

##用AS的gradle-->uploadArchives(上传档案),打包成aar生成Maven，提交到GitHub上

这个其实很简单
### 第一步-->project 中的gradle.properties参数配置
``` 
#project的根目录-->这里写死吧，因为写根目录的话它指的是module的根目录而不是project的根目录，或许我不会更好的方法
aar.path=C:/TongsonCode/GitHub/hello-maven/

#版本name&&release
APP_VERSION_NAME=1.0.0
#groupId
PUBLISH_GROUP_ID = pr.Tongson
#artifactId
PUBLISH_ARTIFACT_ID = demo
``` 
### 第二步-->需要Android lib的module中的gradle配置

``` 
//申请maven插件
apply plugin: 'maven'

// ext is a gradle closure allowing the declaration of global properties
ext {
    PUBLISH_GROUP_ID = "${PUBLISH_GROUP_ID}"
    PUBLISH_ARTIFACT_ID = "${PUBLISH_ARTIFACT_ID}"
    PUBLISH_VERSION = android.defaultConfig.versionName
}

//上传文档配置
uploadArchives {
    repositories.mavenDeployer {
        def deployPath = file(getProperty('aar.path'))
        repository(url: "file://${deployPath.absolutePath}")
        pom.project {
            groupId project.PUBLISH_GROUP_ID
            artifactId project.PUBLISH_ARTIFACT_ID
            version project.PUBLISH_VERSION
        }
    }
}
``` 

### 第三步-->uploadArchives生成aar
![这里写图片描述](http://img.blog.csdn.net/20170223011330976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR2VQcmluaXVzY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 大功告成，把代码提交到GitHub！


## 项目应用GitHub上的maven
这个其实更特么简单了！
```
//依赖中添加
dependencies {
    //compile 'groupId:artifactId:release'
    compile 'pr.Tongson:demo:1.0.0'
}
//仓库中添加
repositories {
    jcenter()
    maven {
//        https://github.com/gepriniusce/hello-maven
//                                                maven的github链接/分支
//      url "https://raw.githubusercontent.com/gepriniusce/hello-maven/master"
        url "https://raw.githubusercontent.com/gepriniusce/hello-maven/master"
    }
}

```
### 大功告成-->分享一下demo咯
https://github.com/gepriniusce/hello-world
https://github.com/gepriniusce/hello-maven