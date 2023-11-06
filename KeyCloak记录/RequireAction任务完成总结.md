# RequireAction任务完成总结

## Tag

- Java后端与FTL前段传参，`${username}`的数据如何设置

```ftl
<input type="text" id="username" name="username" value="${username}" autocomplete="username"
                   readonly="readonly" style="display:none;"/>
```

- `RequiredActionProvider`的具体配置方法以及各个函数的具体作用

- Response相应的创建，以及如何返回一个error的报错界面

- FTL的一些关键解析

## 具体操作

1. 后端java与FTL 传输数据`value="${username}"`类似这种可以在provider中通过`.setAttribute("username",context.getUser().getUsername())`的方式对value中的username进行设置并且给出一个context.getUser().getUsername()的变量加入其中，来实现数据的交互，这里在创建页面的时候将username的内容添加进去以供给后面的方法调用相对应的用户。

```java
        Response challenge = context.form()
                .setAttribute("username",context.getUser().getUsername())
                .createForm("login-update-password.ftl");
        context.challenge(challenge);
```

2. 对于`RequiredActionProvider`的配置，在实现时候需要实现三个方法，evaluateTriggers，processAction，requiredActionChallenge。
   
   - evaluateTriggers为无论是否这个Action被绑定到个别用户，在启动之后登陆时候都会被执行可以进行一些提前的预处理数据
   
   - requiredActionChallenge这个方法用来创建用户所看见的WebUI界面，并且通过一些方法可以给ftl等位置传输参数，来供给用户观察
   
   - processAction为给从requiredActionChallenge传回来的参数的逻辑处理，可以进行一系列的例如密码对比，检测密码是否输入正确等等

3. 除了Provider的创建配置以外Factory的创建与其他的基本相同

```java
@Override
public String getDisplayText() {
    return "Update user password first time";
}

@Override
public RequiredActionProvider create(KeycloakSession session) {
    return new UpdateUserPasswordFirstTime();
}

@Override
public void init(Config.Scope config) {

}

@Override
public void postInit(KeycloakSessionFactory factory) {

}

@Override
public void close() {

}

@Override
public String getId() {
    return "update-password-first-time";
}
```

4. 通过一个Response来创建error的报错界面，其中`.setAttribute("username", userModel.getUsername()`为生成的error.ftl传输所需要的数据，通过数据`${username}`的方式进行传输来实现前后端的交互，`.addError(new FormMessage("password-update-new", "error-updating-password"`的方式来添加报错内容，最后通过`context.challenge(challenge)`显示出来 注意这里不能再`context.succes()`

```java
Response challenge = context.form()
        .setAttribute("username", userModel.getUsername())
        .addError(new FormMessage("password-update-new", "error-updating-password"))
        .createForm("error.ftl");
context.challenge(challenge);
```
