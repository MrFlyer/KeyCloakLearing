# KeyCloak的Policy插件记录

## Tag

- 字符串分割`String inputEmailFront = email.split("@")[0]`  

- `PasswordPolicyProvider`Policy的创建以及如和使用

- 配置Policy到所有用户

### 具体操作

- 按照正擦操作进行实现PasswordPolicyProvider以及PasswordPolicyProviderFactory

- 在PasswordPolicyProvider中需要实现三个方法一以及一个多态，这里的validat对应两个方法，根据参数的数目不同来选择进入到哪一个方法中，这种操作称之为多态

- 

```java
@Override
public PolicyError validate(RealmModel realm, UserModel user, String password) {
    return validate(user.getEmail(),password);
}

@Override
public PolicyError validate(String email , String password) {
    String inputEmailFront = email.split("@")[0];
    if (password.isBlank()){
        return null;
    }
    if (email.isEmpty()){
        return null;
    }
    if (inputEmailFront.equals(password)){
        return ERROR_PASSWORD_INPUT;
    }else{
        return null;
    }
}

@Override
public Object parseConfig(String value) {
    return null;
}

@Override
public void close() {

}
```
