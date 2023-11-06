# UserPasswordRest相关重置

## 主要问题点

- authenticate方法里为什么要有那么多判断？只是自己开发用的代码，很简单的小代码- 

- userDataisBlank为什么要建一个方法出来，只是一个小判断没有必要建方法

- validateUserInputs的参数太多，没有必要在外面获取之后传到方法里，直接把user传进去不行吗？

- 自己创建的用户属性没有用到，用的都是keycloak自带的属性

## 修改后代码

```java
public class UserPasswordRest implements Authenticator {

    private static final Logger logger = Logger.getLogger(UserPasswordRest.class);

    @Override
    public void authenticate(AuthenticationFlowContext context) {
        
        Response challenge = context.form().createPasswordReset();
        context.challenge(challenge);
    }

    @Override
    public void action(AuthenticationFlowContext context) {

        EventBuilder event = context.getEvent();
        EventBuilder errorEvent = event.clone().event(EventType.UPDATE_PASSWORD_ERROR);
        AuthenticationSessionModel authSession = context.getAuthenticationSession();
        MultivaluedMap<String, String> formData = context.getHttpRequest().getDecodedFormParameters();
        String username = formData.getFirst("username");


        if (userDataisBlank(username)) {
            event.error(Errors.USERNAME_MISSING);
            Response challenge = context.form()
                    .addError(new FormMessage(Validation.FIELD_USERNAME, Messages.MISSING_USERNAME))
                    .createPasswordReset();
            context.failureChallenge(AuthenticationFlowError.INVALID_USER, challenge);
            return;
        }

        username = username.trim();

        RealmModel realm = context.getRealm();
        UserModel user = context.getSession().users().getUserByUsername(realm, username);

        if (user == null) {
            event.error(Errors.USERNAME_MISSING);
            Response challenge = context.form()
                    .addError(new FormMessage(Validation.FIELD_USERNAME, Messages.MISSING_USERNAME))
                    .createPasswordReset();
            context.failureChallenge(AuthenticationFlowError.INVALID_USER, challenge);
            return;
        }

        context.getAuthenticationSession().setAuthNote(AbstractUsernameFormAuthenticator.ATTEMPTED_USERNAME, username);

        validateUserInputs(user, errorEvent, context);

    }

    public static boolean userDataisBlank(String str) {
        return Validation.isBlank(str);
    }

    public void validateUserInputs(UserModel user, EventBuilder errorEvent, AuthenticationFlowContext context) {

        MultivaluedMap<String, String> formData = context.getHttpRequest().getDecodedFormParameters();
        String userEmail = user.getEmail();
        String userFirstName = user.getFirstName();
        String userLastName = user.getLastName();
        String inputEmail = formData.getFirst("useremail");
        String inputUserFirstName = formData.getFirst("user-firstname");
        String inputUserLastName = formData.getFirst("user-lastname");

        if (userDataisBlank(userEmail)) {
            handleError(context, "This Count Don't have Email", user, errorEvent);
            return;
        }
        if (userDataisBlank(userFirstName)) {
            handleError(context, "This Count Don't have Firstname", user, errorEvent);
            return;
        }
        if (userDataisBlank(userLastName)) {
            handleError(context, "This Count Don't have Lastname", user, errorEvent);
            return;
        }

        if (userEmail.equals(inputEmail) && userFirstName.equals(inputUserFirstName) &&
                userLastName.equals(inputUserLastName)) {
            handleValidInput(context, user);
        } else {
            handleError(context, "wrong email or password or Firstname or Lastname", user, errorEvent);
            return;
        }
    }

    private void handleError(AuthenticationFlowContext context,
                             String errorMessage,
                             UserModel user,
                             EventBuilder errorEvent) {

        errorEvent.detail(Details.USERNAME, user.getUsername()).detail("type", errorMessage).error(Errors.USER_TEMPORARILY_DISABLED);
        Response challenge = context.form()
                .setAttribute("username", user.getUsername())
                .addError(new FormMessage("password-new", errorMessage))
                .createForm("error.ftl");

        context.challenge(challenge);

    }

    private void handleValidInput(AuthenticationFlowContext context, UserModel user) {
        context.setUser(user);
        context.getUser().credentialManager().isConfiguredFor(PasswordCredentialModel.TYPE);
        context.getAuthenticationSession().addRequiredAction(UserModel.RequiredAction.UPDATE_PASSWORD);
        context.success();
    }


    @Override
    public boolean requiresUser() {
        return false;
    }

    @Override
    public boolean configuredFor(KeycloakSession session, RealmModel realm, UserModel user) {
        return true;
    }


    @Override
    public void close() {

    }

    @Override
    public void setRequiredActions(KeycloakSession session, RealmModel realm, UserModel user) {

    }
```

## 主要修改部分

1. 通过调试之后删除了一些没有采用的代码用更少的代码量解决问题，直接生成用户所需的重置密码界面

```java
    public void authenticate(AuthenticationFlowContext context) {

        Response challenge = context.form().createPasswordReset();
        context.challenge(challenge);
    }

```

2. 对于userDataisBlank来说单独建立一个方法可以提供给不同其他方法来

3. 对于validateUserInputs的修改来说可以只通过传入一个context以及一个user，然后再通过使用context.getfirst等方式来获取各种数据，user可以通过同样方式进行获取，极大程度的减少的输入参数的复杂程度

```java
public void validateUserInputs(UserModel user, EventBuilder errorEvent, AuthenticationFlowContext context) {

    MultivaluedMap<String, String> formData = context.getHttpRequest().getDecodedFormParameters();
    String userEmail = user.getEmail();
    String userFirstName = user.getFirstName();
    String userLastName = user.getLastName();
    String inputEmail = formData.getFirst("useremail");
    String inputUserFirstName = formData.getFirst("user-firstname");
    String inputUserLastName = formData.getFirst("user-lastname");

    if (userDataisBlank(userEmail)) {
        handleError(context, "This Count Don't have Email", user, errorEvent);
        return;
    }
    if (userDataisBlank(userFirstName)) {
        handleError(context, "This Count Don't have Firstname", user, errorEvent);
        return;
    }
    if (userDataisBlank(userLastName)) {
        handleError(context, "This Count Don't have Lastname", user, errorEvent);
        return;
    }

    if (userEmail.equals(inputEmail) && userFirstName.equals(inputUserFirstName) &&
            userLastName.equals(inputUserLastName)) {
        handleValidInput(context, user);
    } else {
        handleError(context, "wrong email or password or Firstname or Lastname", user, errorEvent);
        return;
    }
}
```

4. 后续的注册更改其他数据验证会跟上注册时候认证时候设置的数据来实现多属性校验

5. 对于一些变量名称应更多去注意命名规范小驼峰命名法通常用于变量、方法和属性的命名，以提高代码的可读性和一致性。这与大驼峰命名法（Pascal Case）不同，大驼峰命名法将每个单词的首字母都大写，通常用于类名和类型名的命名。

```java
    public static boolean userDataIsBlank(String str) {
        return Validation.isBlank(str);
    }
```

## 添加自定义配置选项校验

- 添加了两个自定义配置选项userID以及birth_years

- 使用.isblank的方式来校验是否出现问题并且如果有错误则提供一个网页来显示错误，这里是修改之后的代码，通过校验输入的数据以及user下能查询到的数据做对比，在所有的属性都对比成功之后对context添加一个requireAction进行密码重置，从而实现密码的重新设置功能

```java
    public void validateUserInputs(UserModel user, EventBuilder errorEvent, AuthenticationFlowContext context) {

        MultivaluedMap<String, String> formData = context.getHttpRequest().getDecodedFormParameters();
        String userEmail = user.getEmail();
        String userFirstName = user.getFirstName();
        String userLastName = user.getLastName();
        String userUserID = user.getFirstAttribute("userID");
        String userBrith =  user.getFirstAttribute("birth_years");
        String inputEmail = formData.getFirst("useremail");
        String inputUserFirstName = formData.getFirst("user-firstname");
        String inputUserLastName = formData.getFirst("user-lastname");
        String inputUserID = formData.getFirst("userID");
        String inputUserBrith =  formData.getFirst("birth_years");


        if (userDataIsBlank(userEmail)) {
            handleError(context, "This Count Don't have Email", user, errorEvent);
            return;
        }
        if (userDataIsBlank(userFirstName)) {
            handleError(context, "This Count Don't have Firstname", user, errorEvent);
            return;
        }
        if (userDataIsBlank(userLastName)) {
            handleError(context, "This Count Don't have Lastname", user, errorEvent);
            return;
        }
        if (userDataIsBlank(userUserID)) {
            handleError(context, "This Count Don't have UserID", user, errorEvent);
            return;
        }
        if (userDataIsBlank(userBrith)) {
            handleError(context, "This Count Don't have Birth", user, errorEvent);
            return;
        }

        if (userEmail.equals(inputEmail) && userFirstName.equals(inputUserFirstName) && userLastName.equals(inputUserLastName) && userUserID.equals(inputUserID) && userBrith.equals(inputUserBrith)) {
            context.setUser(user);
            context.getUser().credentialManager().isConfiguredFor(PasswordCredentialModel.TYPE);
            context.getAuthenticationSession().addRequiredAction(UserModel.RequiredAction.UPDATE_PASSWORD);
            context.success();
        } else {
            handleError(context, "wrong email or password or Firstname or Lastname", user, errorEvent);
            return;
        }
    }
```


