**JWT**

JSON Web Token(JWT) 是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的消息。

一个JWT实际上就是一个字符串，它由三部分组成，头部、载荷、与签名。



**头部（Header）**

头部用于描述关于该JWT的最基本的信息，例如其类型以及签名所用的算法等。这也可以被表示成一个JSON对象。

```json
{"typ":"JWT","alg":"HS256"}
```

在头部指明了签名算法是HS256算法。 我们进行BASE64编码[http://base64.xpcha.com/](https://links.jianshu.com/go?to=http%3A%2F%2Fbase64.xpcha.com%2F)，编码后的字符串如下：

```apl
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

**载荷（playload）**

载荷就是存放有效信息的地方。这个名字像是特指飞机上承载的货品，这些有效信息包含三个部分

（1）标准中注册的声明（建议但不强制使用）

```cpp
iss: jwt签发者
sub: jwt所面向的用户
aud: 接收jwt的一方
exp: jwt的过期时间，这个过期时间必须要大于签发时间
nbf: 定义在什么时间之前，该jwt都是不可用的.
iat: jwt的签发时间
jti: jwt的唯一身份标识，主要用来作为一次性token。
```

（2）公共的声明

公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可解密.

（3）私有的声明

私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的，意味着该部分信息可以归类为明文信息。

这个指的就是自定义的claim。比如前面那个结构举例中的admin和name都属于自定的claim。这些claim跟JWT标准规定的claim区别在于：JWT规定的claim，JWT的接收方在拿到JWT之后，

都知道怎么对这些标准的claim进行验证(还不知道是否能够验证)；而private claims不会验证，除非明确告诉接收方要对这些claim进行验证以及规则才行。

定义一个payload:

```json
{"sub":"1234567890","name":"John Doe","admin":true}
```

然后将其进行base64加密，得到Jwt的第二部分。

```apl
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9
```

**签证（signature）**

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

header (base64后的)

payload (base64后的)

secret

这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。

```css
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

**注意**：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。



## JWT签发与验证token

JWT是一个提供端到端的JWT创建和验证的Java库。永远免费和开源(Apache License，版本2.0)，JWT很容易使用和理解。它被设计成一个以建筑为中心的流畅界面，隐藏了它的大部分复杂性。

官方文档：

[https://github.com/jwtk/jjwt](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fjwtk%2Fjwt)

#### 创建token

（1）新建项目中的pom.xml中添加依赖：

```xml
<dependency>
 <groupId>io.jsonwebtoken</groupId>
 <artifactId>jjwt</artifactId>
 <version>0.9.0</version>
</dependency>
```

（2）创建测试类，代码如下

```csharp
JwtBuilder builder= Jwts.builder()
 .setId("888")   //设置唯一编号
 .setSubject("小白")//设置主题  可以是JSON数据
 .setIssuedAt(new Date())//设置签发日期
 .signWith(SignatureAlgorithm.HS256,"hahaha");//设置签名 使用HS256算法，并设置SecretKey(字符串)
//构建 并返回一个字符串 
System.out.println( builder.compact() );
```

运行打印结果：

```css
eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NTc5MDQxODF9.ThecMfgYjtoys3JX7dpx3hu6pUm0piZ0tXXreFU_u3Y
```

再次运行，会发现每次运行的结果是不一样的，因为我们的载荷中包含了时间。

#### 解析token

我们刚才已经创建了token ，在web应用中这个操作是由服务端进行然后发给客户端，客户端在下次向服务端发送请求时需要携带这个token（这就好像是拿着一张门票一样），那服务端接到这个token 应该解析出token中的信息（例如用户id）,根据这些信息查询数据库返回相应的结果。

```csharp
 String compactJwt="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NTc5MDQxODF9.ThecMfgYjtoys3JX7dpx3hu6pUm0piZ0tXXreFU_u3Y";
 Claims claims = Jwts.parser().setSigningKey("hahaha").parseClaimsJws(compactJwt).getBody();
 System.out.println(claims);
```

运行打印效果：
{jti=888, sub=小白, iat=1557904181}</pre>

试着将token或签名秘钥篡改一下，会发现运行时就会报错，所以解析token也就是验证token.

#### 设置过期时间

有很多时候，我们并不希望签发的token是永久生效的，所以我们可以为token添加一个过期时间。

（1）创建token 并设置过期时间

```cpp
long now=System.currentTimeMillis();
long exp=now+1000*30;//30秒过期
JwtBuilder jwtBuilder = Jwts.builder().setId( "888" )
 .setSubject( "小白" )
 .setIssuedAt( new Date() )//签发时间
 .setExpiration( new Date( exp ) )//过期时间
 .signWith( SignatureAlgorithm.HS256, "hahaha" );
String token = jwtBuilder.compact();
System.out.println(token);
```

运行，打印效果如下：

eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NTc5MDUzMDgsImV4cCI6MTU1NzkwNTMwOH0.4q5AaTyBRf8SB9B3Tl-I53PrILGyicJC3fgR3gWbvUI

（2）解析TOKEN

```csharp
 String compactJwt="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NTc5MDUzMDgsImV4cCI6MTU1NzkwNTMwOH0.4q5AaTyBRf8SB9B3Tl-I53PrILGyicJC3fgR3gWbvUI";
 Claims claims = Jwts.parser().setSigningKey("hahaha").parseClaimsJws(compactJwt).getBody();
 System.out.println(claims);
```

当前时间超过过期时间，则会报错。    

#### 自定义claims

我们刚才的例子只是存储了id和subject两个信息，如果你想存储更多的信息（例如角色）可以定义自定义claims。

```cpp
long now=System.currentTimeMillis();
long exp=now+1000*30;//30秒过期
JwtBuilder jwtBuilder = Jwts.builder().setId( "888" )
 .setSubject( "小白" )
 .setIssuedAt( new Date() )//签发时间
 .setExpiration( new Date( exp ) )//过期时间
 .claim( "roles","admin" )
 .signWith( SignatureAlgorithm.HS256, "hahaha" );
String token = jwtBuilder.compact();
System.out.println(token);
```

运行打印效果：

eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NTc5MDU4MDIsImV4cCI6MTU1NzkwNjgwMiwicm9sZXMiOiJhZG1pbiJ9.AS5Y2fNCwUzQQxXh_QQWMpaB75YqfuK-2P7VZiCXEJI

解析TOKEN:

```csharp
String token ="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLlsI_nmb0iLCJpYXQiOjE1NjIyNTM3NTQsImV4cCI6MTU2MjI1Mzc4Mywicm9sZXMiOiJhZG1pbiJ9.CY6CMembCi3mAkBHS3ivzB5w9uvtZim1HkizRu2gWaI";
Claims claims = Jwts.parser().setSigningKey( "hahaha" ).parseClaimsJws( token ).getBody();
System.out.println(claims);
System.out.println(claims.get( "roles" ));
```

JWT和token的区别:
相同： 都是访问资源的令牌， 都可以记录用户信息，都是只有验证成功后方可登录系统
区别：
服务端验证客户端发来的token信息要进行数据的查询操作；JWT验证客户端发来的token信息就不用， 在服务端使用密钥校验就可以，不用数据库的查询。
### Token 访问资源的令牌
验证流程：
````
1. 把用户的用户名和密码发到后端
2. 后端进行校验，校验成功会生成token, 把token发送给客户端
3. 客户端自己保存token, 再次请求就要在Http协议的请求头中带着token去访问服务端，和在服务端保存的token信息进行比对校验。
````

特点：

  客户端每次都要携带token, 客户端的内容比较多

### JWT

概念: JSON WEB TOKEN 的简写。可以使用在RESTFUL接口定义， 也可以使用在普通的web

组成：
header、payload、签证

验证流程：

```cpp
1. 在头部信息中声明加密算法和常量， 然后把header使用json转化为字符串
2. 在载荷中声明用户信息，同时还有一些其他的内容；再次使用json 把载荷部分进行转化，转化为字符串
3. 使用在header中声明的加密算法和每个项目随机生成的secret来进行加密， 把第一步分字符串和第二部分的字符串进行加密， 生成新的字符串。词字符串是独一无二的。
4. 解密的时候，只要客户端带着JWT来发起请求，服务端就直接使用secret进行解密。

```
```cpp
1. 三部分组成，每一部分都进行字符串的转化
2. 解密的时候没有使用数据库，仅仅使用的是secret进行解密。
3. JWT的secret千万不能泄密！！！
```
