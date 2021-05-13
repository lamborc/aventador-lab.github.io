# Android Studio


### ISSUE

> 

```js
  buildscript { 
    repositories { 
      google() maven { 
        url 'http://maven.aliyun.com/nexus/content/groups/public/' } 
      jcenter() 
    } 
    dependencies { 
      classpath 'com.android.tools.build:gradle:3.2.1' 
    }
  }

  allprojects { 
    repositories { 
      google() maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' } jcenter() 
    }
  }

  task clean(type: Delete) { 
    delete rootProject.buildDir
  }
```