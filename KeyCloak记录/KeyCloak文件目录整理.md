# KeyCloak文件目录整理

```shell
C:\USERS\USER\DESKTOP\PROVIDERAIOWITHTHEME
├─.idea
├─provider #provider插件工程
│  ├─src
│  │  ├─main
│  │  │  ├─java
│  │  │  │  └─org
│  │  │  │      └─provider
│  │  │  │          ├─authentication
│  │  │  │          │  ├─authenticators
│  │  │  │          │  │  ├─browser
│  │  │  │          │  │  └─resetcred #用户登陆重设Provider
│  │  │  │          │  ├─forms #表单提交Provider存放 如：注册事件
│  │  │  │          │  └─requiredactions #用户重设密码之后进入provider
│  │  │  │          ├─policy #用户密码等等输入检查策略
│  │  │  │          └─services
│  │  │  │              ├─resource #一些资源的获取方式 如RealmResource创建一个reatful的api
│  │  │  │              └─scheduled #存放一些用于定时任务
│  │  │  └─resources
│  │  │      └─META-INF
│  │  │          └─services #将组建注册进入到KeyCloak之中
│  │  └─test
│  │      └─java
│  └─target
│      ├─classes
│      │  ├─META-INF
│      │  │  └─services
│      │  └─org
│      │      └─provider
│      │          ├─authentication
│      │          │  ├─authenticators
│      │          │  │  └─resetcred
│      │          │  ├─forms
│      │          │  └─requiredactions
│      │          ├─policy
│      │          └─services
│      │              ├─resource
│      │              └─scheduled
│      ├─generated-sources
│      │  └─annotations
│      ├─generated-test-sources
│      │  └─test-annotations
│      ├─maven-archiver
│      ├─maven-status
│      │  └─maven-compiler-plugin
│      │      ├─compile
│      │      │  └─default-compile
│      │      └─testCompile
│      │          └─default-testCompile
│      └─test-classes
└─theme #KeyCloak主题文件夹
    ├─src
    │  ├─main
    │  │  └─resources
    │  │      ├─META-INF #存放keycloak-themes.json配置文件
    │  │      └─theme
    │  │          └─resettheme 
    │  │              └─login #这里命名与上面的.json相对对应才可以被选
    │  │                  ├─messages #存放messages_en.properties，页面所有的文本信息存放于此
    │  │                  └─resources #所有需要的ftl文件存放于此用于provider调用
    │  │                      ├─css #页面css
    │  │                      └─img #页面图片
    │  └─test
    │      └─java
    └─target
        ├─classes
        │  ├─META-INF
        │  └─theme
        │      └─resettheme
        │          └─login
        │              ├─messages
        │              └─resources
        │                  ├─css
        │                  └─img
        ├─generated-test-sources
        │  └─test-annotations
        ├─maven-archiver
        ├─maven-status
        │  └─maven-compiler-plugin
        │      └─testCompile
        │          └─default-testCompile
        └─test-classes
```

# 
