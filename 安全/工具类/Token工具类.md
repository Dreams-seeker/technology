### 生成Token工具类

```java
/**
* JWT方式生成token 并非MD5传统方式
**/
public class TokenUtil {
    /**
     * 设置密钥和生存时间
     */
    //设置过期时间
    private static final long EXPIRE_DATE=30*60;
    //token秘钥
    private static final String TOKEN_SECRET = "caohan_study";
    //基于JWT的token认证实现
    public static String getTokenSecretJwt(String username,String password){
        String token = "";
        try{
            //过期时间
            Date date = new Date(System.currentTimeMillis()+EXPIRE_DATE);
            //秘钥及加密算法
            Algorithm algorithm = Algorithm.HMAC256(TOKEN_SECRET);
            //设置头部信息
            Map<String,Object> header = new HashMap<>();
            header.put("typ","JWT");
            header.put("alg","HS256");
            //携带username，password信息，生成签名
            token = JWT.create()
                    .withHeader(header)
                    .withClaim("username",username)
                    .withClaim("password",password).withExpiresAt(date)
                    .sign(algorithm);

        }catch (Exception e){
            e.printStackTrace();
        }
        return token;
    }

    public static boolean verify(String token){
        /**
         * @desc   验证token，通过返回true
         * @params [token]需要校验的串
         **/
        try {
            Algorithm algorithm = Algorithm.HMAC256(TOKEN_SECRET);
            JWTVerifier verifier = JWT.require(algorithm).build();
            DecodedJWT jwt = verifier.verify(token);

            return true;
        }catch (Exception e){
            e.printStackTrace();
            return  false;
        }
    }

    public static void main(String[] args) {
//        String username ="zhangsan";
//        String password = "123";
//        String token = getTokenSecretJwt(username,password);
//        System.out.println(token);
        boolean b = verify("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJwYXNzd29yZCI6IjEyMyIsImV4cCI6MTYyMjcwODg5NiwidXNlcm5hbWUiOiJ6aGFuZ3NhbiJ9.ykcIw5z-4R1qo4VcCzLkxLNjumx9u2xRsOnqzufIQas");
        System.out.println(b);
    }

}
```