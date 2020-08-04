## 第1章 shiro介绍

### 1.1 什么是shiro

shiro是apache的一个开源框架，是一个权限管理的框架，实现用户认证、用户授权。 

spring中有spring security ,是一个权限框架，它和spring依赖过于紧密，没有shiro使用简单。 

shiro不依赖于spring,shiro不仅可以实现web应用的权限管理，还可以实现c/s系统，分布式系统权限管理，shiro属于轻量框架，越来越多企业项目开始使用shiro. 

使用shiro实现系统的权限管理，有效提高开发效率，从而降低开发成本。

### 1.2 shiro架构

![shiroæ¶æ](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3184365161,3508917726&fm=27&gp=0.jpg)

- subject:主体，可以是用户也可以是程序，主体要访问系统，系统需要对主体进行认证、授权。 
- securityManager: 安全管理器，主体进行认证和授权都是通过securityManager进行。 
- authenticator: 认证器，主体进行认证最终通过authenticator进行的。 
- authorizer: 授权器，主体进行授权最终通过authenticator进行的。 
- sessionManager: web应用中一般是用web容器对session进行管理，shiro也提供一套session管理的方式。 
- sessionDao: 通过sessionDao管理session数据， 
- cacheManager: 缓存管理器，主要对session和授权数据进行缓存，比如将授权数据通过cacheManager进行缓存管理，和 ehcache整合对缓存数据进行管理。 
- realm: 领域，相当于数据源，通过realm存取认证、授权相关数据。 注意：在realm 中存储授权和认证的逻辑 
- cryptography: 密码管理，提供了一套加密/解密的组件，方便开发。比如提供常用的散列、加/解密等功能。

## 第2章 shiro认证

### 2.1 shiro认证流程

![shiroè®¤è¯æµç¨](https://img-blog.csdn.net/20180524193011853?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZkMjAyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 2.2 shiro认证入门程序

#### 2.2.1 shiro-first.ini

通过此配置文件创建securityManager工厂。 

```
#对用户信息进行配置
[users]
#用户账号和密码
zhangsan=111111
lisi=222222
```

#### 2.2.2 入门程序代码

```java
// 用户登陆和退出
@Test
public void testLoginAndLogout() {
    //创建securityManager工厂，通过ini配置文件创建securityManager工厂
    Factory<SecurityManager> factory = new IniSecurityManagerFactory(
        "classpath:shiro-first.ini");
    //创建SecurityManager
    SecurityManager securityManager = factory.getInstance();
    //将securityManager设置当前的运行环境中
    SecurityUtils.setSecurityManager(securityManager);
    //从SecurityUtils里边创建一个subject
    Subject subject = SecurityUtils.getSubject();
    //在认证提交前准备token（令牌）
    UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "111111");
    try {
        //执行认证提交
        subject.login(token);
    } catch (AuthenticationException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
    //是否认证通过
    boolean isAuthenticated =  subject.isAuthenticated();
    System.out.println("是否认证通过：" + isAuthenticated);
    //退出操作
    subject.logout();
    //是否认证通过
    isAuthenticated =  subject.isAuthenticated();
    System.out.println("是否认证通过：" + isAuthenticated);
}
```

#### 2.2.3 执行流程

1. 通过ini配置文件创建securityManager 

2. 调用subject.login方法主体提交认证，提交的token 

3. securityManager进行认证，securityManager最终由ModularRealmAuthenticator进行认证。 

4. ModularRealmAuthenticator调用IniRealm(给realm传入token) 去ini配置文件中查询用户信息 

5. IniRealm根据输入的token（UsernamePasswordToken）从 shiro-first.ini查询用户信息，根据账号查询用户信息（账号和密码） 
   如果查询到用户信息，就给ModularRealmAuthenticator返回用户信息（账号和密码） 
   如果查询不到，就给ModularRealmAuthenticator返回null 

6. ModularRealmAuthenticator接收IniRealm返回Authentication认证信息 
   如果返回的认证信息是null，ModularRealmAuthenticator抛出异常···（org.apache.shiro.authc.UnknownAccountException）

   如果返回的认证信息不是null（说明inirealm找到了用户），对IniRealm返回用户密码 （在ini文件中存在）和 token中的密码进行对比，如果不一致抛出异常（org.apache.shiro.authc.IncorrectCredentialsException）

#### 2.2.4 小结

ModularRealmAuthenticator作用进行认证，需要调用realm查询用户信息（在数据库中存在用户信息） 

ModularRealmAuthenticator进行密码对比（认证过程）。

realm：需要根据token中的身份信息去查询数据库（入门程序使用ini配置文件），如果查到用户返回认证信息，如果查询不到返回null。

### 2.3 自定义realm

将来实际开发需要realm从数据库中查询用户信息。

#### 2.3.1 realm接口

一般自定义realm实现AuthorizingRealm类，它实现了Realm接口。

#### 2.3.2 自定义realm

```java
public class CustomRealm extends AuthorizingRealm {
    // 设置realm的名称
    @Override
    public void setName(String name) {
        super.setName("customRealm");
    }
    // 用于认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(
        AuthenticationToken token) throws AuthenticationException {
        // token是用户输入的
        // 第一步从token中取出身份信息
        String userCode = (String) token.getPrincipal();
        // 第二步：根据用户输入的userCode从数据库查询
        // ....

        // 如果查询不到返回null
        // 数据库中用户账号是zhangsansan
        /*if(!userCode.equals("zhangsansan")){//
            return null;
        }*/
        
        // 模拟从数据库查询到密码
        String password = "111112";
        // 如果查询到返回认证信息AuthenticationInfo
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
            userCode, password, this.getName());
        return simpleAuthenticationInfo;
    }
    // 用于授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(
        PrincipalCollection principals) {
        return null;
    }
}
```

#### 2.3.3 配置realm

需要在shiro-realm.ini配置realm注入到securityManager中

```ini
[main]
#自定义realm
customerRealm=com.ty.realm.CustomRealm
#将realm设置到securityManager，相当于spring注入额
securityManager.realms=$customRealm
```

#### 2.3.4 测试

同上测试代码

### 2.4 散列算法

通常要对密码进行散列，常用的有md5、sha。 

对MD5加密，如果知道散列后的值可以通过穷举法，得到MD5密码对应的明文。建议对MD5进行散列时加salt(盐），进行加密相当于对原始密码+盐进行散列。 

正常使用时散列方法： 

1. 在程序中对原始密码+盐进行散列，将散列值存储到数据库中，并且还要将盐也要存储在数据库中。 
2. 如果进行密码对比时，使用相同方法，将原始密码+盐进行散列，进行对比。

#### 2.4.1 MD5散列测试程序

```java
public static void main(String[] args) {
    //原始密码 
    String source = "111111";
    //盐
    String salt = "qwerty";
    //散列次数
    int hashIterations = 2;
    //上边散列1次：f3694f162729b7d0254c6e40260bf15c
    //上边散列2次：36f2dfa24d0a9fa97276abbe13e596fc
    //构造方法中：
    //第一个参数：明文，原始密码 
    //第二个参数：盐，通过使用随机数
    //第三个参数：散列的次数，比如散列两次，相当 于md5(md5(''))
    Md5Hash md5Hash = new Md5Hash(source, salt, hashIterations);
    String password_md5 =  md5Hash.toString();
    System.out.println(password_md5);
    //第一个参数：散列算法 
    SimpleHash simpleHash = new SimpleHash("md5", source, salt, hashIterations);
    System.out.println(simpleHash.toString());
}
```

#### 2.4.2 自定义realm支持散列算法

需求：实际开发时realm要进行md5值（明文散列后的值）的对比

```java
public class CustomRealmMd5 extends AuthorizingRealm {
    // 设置realm的名称
    @Override
    public void setName(String name) {
        super.setName("customRealmMd5");
    }
    // 用于认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(
            AuthenticationToken token) throws AuthenticationException {
        // token是用户输入的
        // 第一步从token中取出身份信息
        String userCode = (String) token.getPrincipal();

        // 第二步：根据用户输入的userCode从数据库查询
        // ....

        // 如果查询不到返回null
        // 数据库中用户账号是zhangsansan
        /*
         * if(!userCode.equals("zhangsansan")){// return null; }
         */

        // 模拟从数据库查询到密码,散列值
        String password = "f3694f162729b7d0254c6e40260bf15c";
        // 从数据库获取salt
        String salt = "qwerty";
        //上边散列值和盐对应的明文：111111

        // 如果查询到返回认证信息AuthenticationInfo
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
                userCode, password, ByteSource.Util.bytes(salt), this.getName());
        return simpleAuthenticationInfo;
    }
    // 用于授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(
            PrincipalCollection principals) {
        return null;
    }
}
```

#### 2.4.3 在realm中配置凭证匹配器

```ini
[main]
#定义凭证匹配器
credentialsMatcher=org.apache.shiro.authc.credential.HashedCredentialsMatcher
#散列算法
credentialsMatcher.hashAlgorithmName=md5
#散列次数
credentialsMatcher.hashIterations=1
#将凭证匹配器设置到realm
customRealm=com.ty.shiro.realm.CustomRealmMd5
customRealm.credentialsMatcher=$credentialsMatcher
securityManager.realms=$customRealm
```

## 第3章 shiro授权

### 3.1 授权流程

![æææµç¨](https://img-blog.csdn.net/20180524205244238?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZkMjAyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 3.2 授权方式

shiro支持三种方式的授权：

- 编程式：通过写if/else授权代码块完成：

```java
Subject subject = SecurityUtils.getSubject();
if(subject.hasRole("admin")){
   //有权限
}else{
   //无权限
}
```

- 注解方式:通过在执行的java 方法上放置相应的注解完成；

```java
@RequiresRoles("admin")
public void hello(){
  //有权限
}
```

- jsp标签： 在jsp页面通过相应的标签完成：

```jsp
<shiro:hasRole name="admin">
	<!-- 有权限 -->
</shiro:hasRole>
```

### 3.3 授权测试

#### 3.3.1 shiro-permission.ini

创建存放权限的配置文件shiro-permission.ini,里面的内容相当于在数据库里面，内容如下：

```ini
[users]
#用户zhang的密码是123，此用户具有role1和role2两个角色
zhang=123,role1,role2
wang=123,role2

[roles]
#角色role1对资源user拥有create、update权限
role1=user:create,user:update
#角色role2对资源user拥有create、delete权限
role2=user:create,user:delete
#角色role3对资源user拥有create权限
role3=user:create
```

在ini文件中用户、角色、权限的配置规则是：“用户名=密码，角色1，角色2…” “角色=权限1，权限2…”，首先根据用户名找角色，再根据角色找权限，角色是权限集合。

#### 3.3.2 权限字符串规则

权限字符串的规则是：“资源标识符：操作：资源实例标识符”，意思是对哪个资源的哪个实例具有什么操作，“:”是资源/操作/实例的分割符，权限字符串也可以使用*通配符。

例子：
- 用户创建权限：user:create，或user:create:* 
- 用户修改实例001的权限：user:update:001 
- 用户实例001的所有权限：user：*：001

#### 3.3.3 测试代码

```java
public void testPermission() {
    // 从ini文件中创建SecurityManager工厂
    Factory<SecurityManager> factory = new IniSecurityManagerFactory(
        "classpath:shiro-permission.ini");
    // 创建SecurityManager
    SecurityManager securityManager = factory.getInstance();
    // 将securityManager设置到运行环境
    SecurityUtils.setSecurityManager(securityManager);
    // 创建主体对象
    Subject subject = SecurityUtils.getSubject();
    // 对主体对象进行认证
    // 用户登陆
    // 设置用户认证的身份(principals)和凭证(credentials)
    UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
    try {
        subject.login(token);
    } catch (AuthenticationException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
    // 用户认证状态
    Boolean isAuthenticated = subject.isAuthenticated();
    System.out.println("用户认证状态：" + isAuthenticated);
    
    // 用户授权检测 基于角色授权
    // 是否有某一个角色
    System.out.println("用户是否拥有一个角色：" + subject.hasRole("role1"));
    // 是否有多个角色
    System.out.println("用户是否拥有多个角色：" + subject.hasAllRoles(Arrays.asList("role1", "role2")));       
    //      subject.checkRole("role1");
    //      subject.checkRoles(Arrays.asList("role1", "role2"));
    // 授权检测，失败则抛出异常
    // subject.checkRole("role22");

    // 基于资源授权
    System.out.println("是否拥有某一个权限：" + subject.isPermitted("user:delete"));
    System.out.println("是否拥有多个权限：" + subject.isPermittedAll("user:create:1",    "user:delete"));

    // 检查权限
    subject.checkPermission("sys:user:delete");
    subject.checkPermissions("user:create:1","user:delete");
}
```

### 3.4 自定义realm

与上面认证自定义realm一样，大部分情况是要从数据库获取权限数据，这里直接实现基于资源的授权。

#### 3.4.1 realm代码

在认证章节写的自定义realm类中完善doGetAuthorizationInfo方法，此方法需要完成：根据用户身份信息从数据库查询权限字符串，由shiro进行授权。

```java
// 授权
@Override
protected AuthorizationInfo doGetAuthorizationInfo(
    PrincipalCollection principals) {
    // 获取身份信息
    String username = (String) principals.getPrimaryPrincipal();
    // 根据身份信息从数据库中查询权限数据
    //....这里使用静态数据模拟
    List<String> permissions = new ArrayList<String>();
    permissions.add("user:create");
    permissions.add("user:delete");

    //将权限信息封闭为AuthorizationInfo
    SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
    for(String permission:permissions){
        simpleAuthorizationInfo.addStringPermission(permission);
    }
    return simpleAuthorizationInfo;
}
```

#### 3.4.2 shiro-realm.ini

ini配置文件还使用认证阶段使用的，不用改变。

#### 3.4.3 测试代码

同上边的授权测试代码，注意修改ini地址为shiro-realm.ini。

#### 3.4.4 授权执行流程

1. 执行subject.isPermitted(“user:create”) 
2. securityManager通过ModularRealmAuthorizer进行授权 
3. ModularRealmAuthorizer调用realm获取权限信息 
4. ModularRealmAuthorizer再通过permissionResolver解析权限字符串，校验是否匹配