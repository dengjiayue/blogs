[toc]()

### 功能
实现GitHub授权,获取用户在GitHub的有关信息

### 搭建

1. 注册app

在GitHub的setting->Developer Settings（开发者设置）中,点击New OAuth App（新建OAuth应用）

根据提示填写主页URL、回调URL等信息，并生成一个client_id与client_secre(客户端ID和客户端密钥。)

2. 部署到客户端

在客户端定义一个登录按钮或链接，当用户点击时，会跳转到GitHub的登录页面。在跳转链接中，你需要包含以下参数：

* client_id：客户端ID。
* redirect_uri：回调url,用户登录成功后将被重定向到的URL。
* scope：请求访问GitHub API的权限范围，如用户的公开资料、存储库访问等。
* state：一个随机生成的字符串，用于防止跨站请求伪造攻击。

3. 用户授权

...

4. 获取访问令牌

在回调url时会附带一条授权码信息,拿到授权码(回调处理部署的位置就是回调url),

回调处理: 向GitHub的api发送一条POST请求来交换授权码和访问令牌。该请求需要包含以下参数：

client_id：客户端ID。
client_secret：客户端密钥。
code：重定向时收到的授权码。
redirect_uri：与之前的redirect_uri相同。

接收访问令牌：如果请求成功，GitHub将返回一个JSON响应，其中包含访问令牌。


7. 前端vue模块示例

功能:实现授权登录

思路:

1. 定义到GitHub授权页面,由用户进行授权

2. 处理回调:

授权成功:发送post请求获取访问令牌

失败:处理展示失败信息

3. 登录处理:向后端接口发起请求,携带访问令牌,由后端获取邮箱用户名等信息,在进行注册验证,验证邮箱是否注册,

如果已经注册就登录该邮箱对应的账号

如果未注册就根据邮箱生成新的账号,并登录新生成的账号

最后统一给改邮箱发送一份提醒邮件,登录提醒.
```html
<template>
    <button @click="login">GitHub授权登录</button>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    methods: {
      async login() {
        try {
          const authorizationUrl = 'https://github.com/login/oauth/authorize';
          const clientId = '我的客户端id';
          const redirectUri = '我的回调url';
  
          // 发起授权请求，获取授权码
          const { code } = await this.getCode(authorizationUrl, clientId, redirectUri);
  
          // 使用授权码获取访问令牌
          const accessToken = await this.getAccessToken(code, clientId, redirectUri);
  
          // 发送访问令牌到后端进行处理
          await this.sendAccessToken(accessToken);
  
          // 处理成功后的逻辑
          console.log('访问令牌已发送到后端进行处理');
        } catch (error) {
          console.error('登录失败', error);
        }
      },
  
      async getCode(authorizationUrl, clientId, redirectUri) {
        const response = await axios.get(authorizationUrl, {
          params: {
            client_id: clientId,
            redirect_uri: redirectUri,
            scope: 'user',
          },
        });
  
        const code = response.data.code;
        return code;
      },
  
      async getAccessToken(code, clientId, redirectUri) {
        const tokenUrl = 'https://github.com/login/oauth/access_token';
        const response = await axios.post(tokenUrl, null, {
          params: {
            client_id: clientId,
            client_secret: '我的客户端密钥',
            code: code,
            redirect_uri: redirectUri,
          },
          headers: {
            Accept: 'application/json',
          },
        });
  
        const accessToken = response.data.access_token;
        return accessToken;
      },
  
      async sendAccessToken(accessToken) {
        const backendUrl = '我的后端处理接口';
        const data = {
          access_token: accessToken,
        };
  
        await axios.post(backendUrl, data);
      },
    },
  };
  </script>
```


5. 获取用户信息
应用程序可以使用访问令牌发送请求来访问GitHub API获取用户的GitHub信息，例如获取用户的个人资料、操作存储库等。

golang后端处理

```go
type User struct {
	Login string `json:"login"`
	Email string `json:"email"`
}

//github登录操作
//获取数据
func GetUserInfo(accessToken string) (*User, error) {
	url := "https://api.github.com/user"
	req, err := http.NewRequest("GET", url, nil)
	if err != nil {
		return nil, err
	}

	req.Header.Set("Authorization", "Bearer "+accessToken)

	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		return nil, err
	}
	defer resp.Body.Close()

	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		return nil, err
	}

	user := &User{}
	err = json.Unmarshal(body, user)
	if err != nil {
		return nil, err
	}

	return user, nil
}

//后续处理
//后续就是MySQL和redis的增删改查操作
//....

```



