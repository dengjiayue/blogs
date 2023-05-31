[toc]

## 需求
实现一个登录功能

实现的功能

1. 注册(邮箱注册)
2. 登录,GitHub授权登录
3. 重置密码
4. 查看操作记录(登录, 注册, 重置密码, 登出等)
5. 登出





### 后端接口设计

#### 1. 人机验证
**只要下面出现 人机验证 的功能都需要使用到该接口**

路径:http://localhost:8888/verifypic

方法get

功能:获取图片验证码

请求参数: 无

响应数据(示例)
```
{
    "code": 200,
    "msg": "ok",
    "data": {
        "picid": "52fa7b86-cf25-4822-a12c-d48e5b4d7f31",
        "png": "iVBORw0KGgoAAAANSUhEUgAAAPAAAABQCAMAAAAQlwhOAAAAP1BMVEUAAABNKGM1EEu9mNOUb6pvSoVFIFtZNG96VZA2EUy5lM93Uo2ifbhEH1pFIFt/WpVkP3qrhsFkP3pKJWBnQn3MHHtHAAAAAXRSTlMAQObYZgAABDFJREFUeJzsWuuSsyAMTdZpd6b2Mjv7/u/6zacFchGINkXdevaPi1xyOCEELBw4cGCH+F7bgMb4/v44xmsb8Ll4PB6q7GsVS9rg8dCMv77+MOMnYYx/8AkKI4GsECbhbwABR75MYPIWxkmwce667k12uiHQmRI4FAxvLZS7bvOMkWooFA5l49sJX1fYMOFgfOKqawSWgXMdmyX8n8L4kI3LyJdubFHGNvlSveKj2nl1/NojYgAKAifdOF+rE28ZmKJuJJP31N3zDVtLoi0UlvX3TTg4sllhW6ZhTkjagrB9ShzMpNELZMahSmW3xs25NaKooFQNvp223NhoqIyYTyvRnH41hs4M5aaEOvsIkzQxFaTjLStM7GL/osilIZXTqD7Zra/Cfd+79DN9IiDPIpVmjTB7WBrlN6ZfFvS9L2NWQNawOizRRtwNZKe+W5cnYa4OglZ4fO66LlQWbiH6QNyywjL88kSaEBlOOnQ7yiwD8q9nyOr7/selI6VwLrKOhInCopHu0lNhAPj5cWIs/mVkTumFUli0Ar2knTclH74KjO+JM4aMwpzkAoWvXtYvALPxJN/lUkpKkmZqtjGv1/UYl1XJKQzIFX6W7UBhtS2rt4MaE0ELdd61xcRSoZz+DnyvVxDbMKUb82zvKP0mlK0MhNNGjBpxOvagMBatRAiEAymhLLuw34PC9SWMIaIOCnNVISaUiFjuajMoWTl1YoqeDfGEDJDk3T7jgi5CM/ovcWExI43MXo4CX3FiILsQkutsovsqCs8cUG6kgMlhhb4kXkmFeXEbKBczDsxFQfGllF7zCbLAvTvNQQvoTdE61bwiD7h8xbKTAhdfXpO8gyIzmm8WQD4i1MeWjLnnhtIo5/SdFv9q/F7G7DoRVSZQHVspPLGspaNmPJcM/yqrrLXyJjlukskRK6NPzYq8EZn8Oq6L9FrwBqbkZ/p1nXHdOvNdM1/IpiazYJrQuovVrLMbLxaVrZH19wFm37nVPoGUO5klFln81nb2X0QYGd9ut2hJyfGLoxhNCi3Cg6n+bL61ijfaIhdqyqMYTXq2AP5hztqu0qsp/k62Mo0VLnUWHOhR5m3mZudsf0uvFlJMqVQMlzoL8kTeaE7AO5/Pyjhk1w5zbYkmVB11ILxEXsXRvCT+KxwNo1kbvJqgp17znYyEl06pPIzMyOkRdc7ocE9WOV4gwsD3hf5Zb2avpmJwLDZFWRLW9NSVs8soQC+CDHVBHddcUzbiQuyOzvlUi3ODbFq8nmYA926Ml+liWn8dhpm9Et95wEQZJvhgv78OjGfmqA0Q3VrDhe/MxMsN9/s986aBLSswvt/zjFtgdvR6FUXCpwYG+G80ZZQIsx87vA0rMM6+a8HXP6/ZBfbwyc0ZW/yV8YEDBzaKy+WytglNcbl8GOOD8J/Hp/HdJP4FAAD//55dD54XIp4vAAAAAElFTkSuQmCC"
    }
}
```

png为base64编码数据,解码可以得到验证码图片,需要展示给用户

picid为图片的唯一标识符,验证时需要带上才能正常验证,否则将报错


#### 2. 注册1:发送注册验证码:

路径: http://localhost:8888/loginemail1

方法post

功能:**人机验证**+邮箱验证码发送

请求数据(示例) :数据格式json
```
{
{
  "picid": "c19c20d2-ebea-487a-9c7d-f9c98705728f",
  "email": "...@gmail.com",
  "piccode": [
    2,
    3,
    9,
    9
  ]
}
```
picid为人机验证执行http://localhost:8888/verifypic时返回储存的,

piccode为图片中展示的验证码,需要用户输入

email是用户输入的注册邮箱

响应数据:数据格式json
```
{
code:状态码判断响应状态
msg:响应提示信息,一般为出错的时候展示给用户
data:nil(data为interface,根据需要进行返回,这里不需要返回值,所以为空nil)
}
```

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414200825.png)

#### 3. 注册2:验证+信息填写
路径: http://localhost:8888/signup2

方法post

请求参数示例:
```
{
    "name": "小明",
    "password": "abcd123",
    "emailverificationcode": {
        "email": "...@gmail.com",
        "verification": 987639
    }
}
```

name,password,verification为用户输入,Email为注册1时填写的数据

返回:数据格式json
```
{
  "code": 200,
  "msg": "ok",
  "data": nil
}

```
提示用户成败信息

#### 4. 邮箱密码登录
路径: http://localhost:8888/loginpsw

方法:post

功能:人机验证+密码登录

请求参数:数据格式json
```
{
    "picid": "3a00e077-0e2f-47c0-9123-04a827235a6e",
    "piccode": [
       7,1,9,3
    ],
    "email": "...@gmail.com",
    "password": "abcd123"
}
```
piccode,Email,password为用户输入,picid为人机验证功能执行方法



返回:数据格式json
```

{
  "code": 200,
  "msg": "ok",
  "data": {
    idcode:身份码(string)
  }
}
```
这里如果成功data中会包含一条idcode信息,需要储存cookie,后面home下功能需要用到

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414201123.png)

#### 5. 邮箱验证码登录1:发送邮箱验证码
路径: http://localhost:8888/loginemail1

方法post

功能:人机验证+邮箱验证码发送

请求参数:数据格式json
```
{
    "picid": "a3e6bcf5-e23b-4406-bd1a-14edf55d82b4",
    "piccode": [
        1,7,9,9
    ],
    "email": "...@gmail.com"
}
```

响应数据:数据格式json
```
{
  "code": 200,
  "msg": "ok",
  "data":nil
}

```
需要处理响应结果

#### 6. 验证码登录2
路径:http://localhost:8888/loginemail2

方法post

功能:验证验证码

请求参数:数据格式json
```

{
    "email": "...@gmail.com",
    "verification": 715562
}

```
Email为邮箱验证登录1时填写的数据

verification为用户输入

返回数据:数据格式json
```
{
    "code": 200,
    "msg": "ok",
    "data": {
        "idcode": "384a0457-7857-4da4-88b3-72c080634cf5"
    }
}
```
idcode存cookie,便于后期使用




![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414200940.png)

#### 7. 密码改密
http://localhost:8888/home/resetpaswbypasw

方法post

功能:身份验证+密码验证+改密

参数:数据格式json
```
{
    "password": "abcd123",
    "newpassword": "abcd1234",
    "idcode": "aed48dfc-868d-46c7-a7ed-23ec3fb09257"
}
```
idcode从cookie中获取,其他为用户填写,需要确保信息的合理性

返回:数据格式json
```
{
    "code": 200,
    "msg": "ok",
    "data": nil
}
```
处理成败信息

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414201938.png)

#### 8. 查询操作信息
http://localhost:8888/getoperations

方法post

功能:身份验证+查询操作信息

请求参数:数据格式json
```
{
idcode:身份码
}
```
从cookie中获取

返回数据:数据格式json
```
{
code:200
msg:"ok"
{
  "operations": [
    {
      "id": 10004,
      "behavior": "注册成功",
      "time": 1682166659
    },
    {
      "id": 10004,
      "behavior": "signin成功",
      "time": 1682167322
    },
    {
      "id": 10004,
      "behavior": "登出成功",
      "time": 1682167868
    },
    {
      "id": 10004,
      "behavior": "signin成功",
      "time": 1682169145
    },
    {
      "id": 10004,
      "behavior": "signin成功",
      "time": 1682170421
    },
    {
      "id": 10004,
      "behavior": "signin成功",
      "time": 1682170825
    },
    {
      "id": 10004,
      "behavior": "登出成功",
      "time": 1682171024
    },
    {
      "id": 10004,
      "behavior": "signin成功",
      "time": 1682171528
    },
    {
      "id": 10004,
      "behavior": "改密成功",
      "time": 1682171658
    }
  ],
  "msg": "ok"
}
```
成功:展示"operations"中的 信息,失败返回失败原因

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414202210.png)

#### 9. 验证码改密1
路径http://localhost:8888/home/resetpaswbyemail1

方法post



功能:人机验证+身份验证+邮件验证码发送

参数:数据格式json
```
{
    "picid": "52fa7b86-cf25-4822-a12c-d48e5b4d7f31",
    "piccode": [
        7,5,6,4
    ],
    "idcode": "ebbeb952-27d2-42cc-979c-bd99e517f0cd"
}
```
piccode为人机验证功能时返回图片中的验证码,由用户输入

返回:数据格式json
```
{
    "code": 200,
    "msg": "ok",
    "data": null
}
```
处理成败信息

#### 10. 登出
http://localhost:8888/logout

方法post

功能:身份验证+查询操作信息

请求参数:数据格式json
```
{
idcode:身份码
}
```
从cookie中获取

返回数据:数据格式json
```
{
code:200
msg:"ok"
data:nil
```
处理成败信息

#### 9. 验证码改密2
路径http://localhost:8888/home/resetpaswbyemail2

方法post

功能:身份验证+邮箱验证码验证+修改密码




参数:数据格式json
```
{
    "newpassword": "abcdef1234ad",
    "idcode": "ebbeb952-27d2-42cc-979c-bd99e517f0cd",
    "verification": 123455
}
```


返回(错误示例):数据格式json
```
{
    "code": 501,
    "msg": "验证码错误验证码错误sql: no rows in result set",
    "data": null
}
```

上面示例为验证码错误产生的返回

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414201800.png)

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230414201321.png)

## MySQL表

1. LoginList(注册列表)

|  名称   | 数据类型    |
| --- | --- |
id(主键)|int
|  name(唯一)   |   string  |
|   password  |   string  |
|   Email(唯一)  |  string   |
GitHub|string(GitHub账号)

2. SignInList(登录列表)

| 名称    |  数据类型   |
| --- | --- |
| id (主键)   | int    |
|  idcode  |  string   |
ExpirationTime|int

解释:身份码为随机字符串,用于对用户操作是进行身份验证,ExpirationTime储存过期时间,如果用户发送请求的时间超过过期时间,则视为用户掉线,需要重新登陆.

3. OperationRecord(操作列表)

| 名称    |  数据类型   |
| --- | --- |
| id   | int    |
|  操作  |  string  |
time|time

4. VerificationCode(邮箱验证码储存库)

|名称|数据类型|
|-|-|
Email|string
Verification|int
ExpirationTime|int
time|int

ExpirationTime储存验证码过期时间,如果用户验证时间超过ExpirationTime则验证无效

每次只查询最后一条匹配的数据(最新生成的验证码数据)

5. verifition_codes(图片验证表)

|  名称   | 数据类型    |
| --- | --- |
picid|string
code|[4]int
|  create_at  |  time  |

## 常用功能模块
#### 1. 邮箱验证码功能
1. 传入邮箱信息
4. 生成验证码(随机6位数),与Email一起存入VerificationCode(验证码储存库),同时储存验证码过期时间,次数为默认值0
5. 向用户邮箱发送验证码
6. 服务端使用用户的提交验证码时的Email去VerificationCode(验证码储存库)获取最后一条匹配的数据(最新生成的验证码数据)的过期时间,和验证码值,如果现在的时间大于过期时间,返回验证码过期,如果小于过期时间,对比传入的数据与数据库中验证码的值Verification(验证码值)是否相同,.
7. 处理用户请求
8. 每30分钟扫描一次数据库,删除超过30的数据


sendemail.go

发送邮件功能

我们这里使用的QQ邮箱的SMTP功能进行发送,你需要有一个QQ邮箱开通该功能
```go
// 使用gomail库创建SMTP客户端
func CreatDialer() *gomail.Dialer {
	return gomail.NewDialer(".qq.com", 123, "@qq.com", "*****") //AuthCode为邮箱的授权码
}

// 发送邮件函数,+邮箱可用性验证
func SendEmail(dialer *gomail.Dialer, to string, verification int) error {
	// 发送邮件的QQ邮箱地址
	qqEmail := "xxxx@qq.com"

	// 检查电子邮件地址是否可用
	err := checkmail.ValidateFormat(to)
	if err != nil {
		return fmt.Errorf("email address %s is not available: %s", to, err.Error())
	}
	// 创建邮件消息
	message := gomail.NewMessage()
	message.SetHeader("From", qqEmail)
	message.SetHeader("To", to)
	message.SetHeader("Subject", "验证码")
	message.SetBody("text/plain", fmt.Sprintf("验证码:%d", verification))

	// 发送邮件消息,开携程发生邮件
	go dialer.DialAndSend(message)

	return nil
}
```

储存验证码到数据库

```go
// 增:验证码列表插入信息
func InsertVerification(db *sql.DB, data *EmailVerificationCode) error {

	// 准备SQL语句

	stmt, err := db.Prepare("REPLACE INTO VerificationCode (Email, Verification, ExpirationTime, time) VALUES (?, ?, ?, ?)")
	if err != nil {
		return err
	}
	defer stmt.Close()

	// 执行插入操作
	_, err = stmt.Exec(data.Email, data.Verification, time.Now().Unix()+5*60, 0)
	if err != nil {
		return err
	}
	return nil
}
```


储存验证码到数据库与发送逻辑

```go
//获取验证码
verification := GetuVerification(db, emial)
		//储存验证码到数据库
		err = InsertVerification(db, &EmailVerificationCode{
			Email:        emial,
			Verification: verification})
		if err != nil {
			ctx.Returnfunc(401, "发送失败,稍后再试"+err.Error(), nil)
			return
		}
		//发送验证码
		err = SendEmail(dialer, emial, verification)
		if err != nil {
			ctx.Returnfunc(401, "邮箱错误"+err.Error(), nil)
			return
		}
		ctx.Returnfunc(200, "ok", nil)
```

getverification获取验证码函数

```go
// 获取验证码
// 检查是否进行验证过,用于重新发送验证码时调用,检查用户是否验证过
// 如果没有就发送与原来相同的验证码,否则就发送新验证码
func GetuVerification(db *sql.DB, email string) int {

	// 查询验证码是否正确
	var expirationTime int64 //储存过期时间

	verification, t := 0, -1

	err := db.QueryRow("SELECT time, Verification, ExpirationTime FROM VerificationCode WHERE Email = ?", email).Scan(&t, &verification, &expirationTime)
	//当没有验证码发送记录||已经验证过||超出时间限制  则需要重新生成验证码
	if err != nil || t == 1 || expirationTime+2500*60 < time.Now().Unix() {
		fmt.Printf("重新生成验证码%#v\n")
		rand.Seed(time.Now().UnixNano())
		// 生成100000到999999之间的随机数,作为验证码
		return rand.Intn(899999) + 100000
	} else {
		fmt.Printf("旧验证码=%#v\n", verification)
		return verification
	}
}
```

特殊情况:\
9. 重新提交:如果验证码生成后,该用户未进行任何验证(time=0),则生成相同的验证码,并告知用户,如果之前有验证失败(time>0),就生成不同的验证码.避免某些用户在5分钟内没法收到验证码的情况.

邮箱验证码验证:
```
// 查: 验证验证码
func Verify(db *sql.DB, email string, verification int) error {

	// 查询验证码是否正确
	var expirationTime int64 //储存过期时间
	//代表已经验证过
	_, err := db.Exec("UPDATE VerificationCode SET time = ? WHERE Email = ?", 1, email)
	if err != nil {
		return fmt.Errorf("出错了" + err.Error())
	}
	err = db.QueryRow("SELECT ExpirationTime FROM VerificationCode WHERE Email = ? AND Verification = ?", email, verification).Scan(&expirationTime)
	if err != nil {
		return fmt.Errorf("验证码错误" + err.Error())
	}

	// 验证身份认证码是否过期
	if time.Now().Unix() > expirationTime {
		return fmt.Errorf("过期")
	}
	return nil
}
```


#### 图片验证功能

1. 当调用获取图片验证功能,生成一组4位的验证码与验证码唯一标识符picid,储存进数据库,再 根据验证码生成一个base64的图片编码,将图片编码与唯一标识符发送给前端
2. 用户填写验证码提交给服务端,将携带刚刚的图片唯一标识符
3. 后端查找MySQL库中对应的code(验证码)与用户传入的验证码是否相同
4. 相同则继续用户的操作
5. 不同返回验证码错误

生成储存验证码,并响应验证图片
```go
// 发送图片验证码图片中间件(用户提交请求时)
func Picverfi(db *sql.DB) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {

		// 生成验证码
		id := uuid.New().String() //生成唯一标识符
		code := captcha.RandomDigits(4)
		img := captcha.NewImage(id, code, captcha.StdWidth, captcha.StdHeight)
		// 生成验证码
		var buf bytes.Buffer
		_, err := img.WriteTo(&buf)
		if err != nil {
			ctx.Returnfunc(401, "出错了,点击重试", nil)
		}

		// 将验证码和图片存储到数据库中
		encodedImage := base64.StdEncoding.EncodeToString(buf.Bytes())
		verificationCode := VerificationCode{PicCode: code, CreatedAt: time.Now()}
		_, err = db.Exec("INSERT INTO verification_codes (id, code, created_at) VALUES (?, ?, ?)", id, verificationCode.PicCode, verificationCode.CreatedAt)
		if err != nil {
			ctx.Returnfunc(http.StatusInternalServerError, "Internal Server Error:"+err.Error(), nil)
			return
		}

		// 返回图片与唯一标识符
		ctx.Returnfunc(200, "ok", gee7.H{
			"picid": id,
			"png":   encodedImage,
		})

		fmt.Printf("id=%#v\ncode=%d", id, code)
	}
}

```

验证码验证:
```go
// 图片验证码验证操作:用户传入图片id(唯一标识符发送图片信息时给的),+验证码
func Verifypiccode(db *sql.DB, id string, code []byte) error {
	var createdAt []uint8
	err := db.QueryRow("SELECT created_at FROM verification_codes WHERE id = ? AND code = ?", id, code).Scan(&createdAt)
	if err != nil {
		return err
	}
	created_ats := string(createdAt)
	created_at, err := time.Parse("2006-01-02 15:04:05", created_ats)
	if err != nil {
		return err
	}
	// 删除验证码
	db.Exec("DELETE FROM verification_codes WHERE id = ?", id)
	if time.Now().Unix() > created_at.Unix()+5*60 { //时效5分钟
		return fmt.Errorf("验证码过期")
	}
	return nil
}
```


#### 身份验证功能:
为确保用户的身份与登录状态,我们使用身份验证功能来实现

1. 用户请求(自带idcode身份码(cookie中获取))
2. 使用idcode查找SignInList(登录列表)的对应信息(如果没有,返回未登录并退回登录页面)
3. 检查身份码与过期时间(如果身份码不正确或者已过期,返回登录过期并退回登录页面)
4. 核验成功重置过期时间(从核验成功开始30分钟后)
5. 处理用户的请求

```
	//储存登录用户信息
	signinlest := funcmod.SignInList{}
  
  //登录成功
  //向登录列表中加入元素
		signinlest[idcode] = &signindata
```

我们使用的是map进行储存

验证

```
		//身份验证
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}

		//时间验证

		if signinlest[data.IDcode].ExpirationTime <time.Now().Unix(){
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
```

#### 密码验证功能
1. 用户上传邮箱/账号(id)+密码
2. 真人验证:图片验证
3. 核验密码是否规范
4. 密码加密
5. 查找LoginList(注册列表)中对应的邮箱/账号(如果没有返回账号/邮箱未注册)
6. 核验密码是否正确
7. 处理用户请求

密码验证功能

```go
// 查:验证user(注册列表)密码
func Verifypassword(db *sql.DB, id int, password string) error {

	count := 0
	//SELECT COUNT(*)这个函数一般不会报错,如果找不到值,那么count=0
	err := db.QueryRow("SELECT COUNT(*) FROM user WHERE id = ? AND password = ? ", id, encryptPassword(password)).Scan(&count)
	fmt.Printf("count=%#v\n", count)
	if err != nil || count == 0 {
		return fmt.Errorf("出错了:密码错误:%#v", err)
	}
	return nil
}


```

#### 储存操作信息功能

1. 通过请求数据的id与方法确定请求操作的种类,将post操作的内容与id和请求时间一起储存在OperationRecord(操作列表)中


### 后端总和

main.go

```go
package main

import (
	"fmt"
	"goweb/day7/gee7"
	"goweb/logindata/funcmod"
	"goweb/logindata/signup"
	"log"
	"time"
	// "github.com/rs/cors"
)

func main() {
	r := gee7.New()
	// r.Use(cors.Default())
	//设置统一日志输出(日期+时间+输出文件+行+信息)
	r.Use(funcmod.Dfot())
	log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
	db, err := funcmod.Opensql()
	if err != nil {
		fmt.Printf("数据库出错=%#v\n", err)
		return
	}
	defer db.Close()

	//储存登录用户信息
	signinlest := funcmod.SignInList{}
	signinlest["ebbeb952-27d2-42cc-979c-bd99e517f0cd"] = &funcmod.SignInData{
		ID:             10007,
		ExpirationTime: time.Now().Unix() + 30*60,
	}
	r.Use(funcmod.DBMid(db))
	// funcmod.Changepassword(db, 10007, "abcd1234")
	//连接邮箱
	dailer := funcmod.CreatDialer()
	//获取图片验证码
	r.GET("/verifypic", funcmod.Picverfi(db))
	//注册1,1. 图片验证,;2. 发送邮件验证码
	r.POST("/signup1", func(ctx *gee7.Context) {
		data := funcmod.Login1data{}
		//读取用户传入的json数据
		err = ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, "解析数据错误"+err.Error(), nil)
			funcmod.Logwarning("解析数据错误" + err.Error())
			return
		}
		if funcmod.Validatedata(data.Email) {
			ctx.Returnfunc(402, "email不合理,请规范填写数据", nil)
			funcmod.Logwarning("email不合理")
			return
		}
		//验证图片验证码是否正确
		err = funcmod.Verifypiccode(db, data.PicID, data.PicCode)
		if err != nil {
			ctx.Returnfunc(401, "验证码错误"+err.Error(), nil)
			funcmod.Logwarning("验证码错误")
			return
		}
		_, err = funcmod.GetuserId(db, data.Email)
		if err == nil {
			ctx.Returnfunc(410, "账号已经注册了", nil)

			return
		}

		verification := funcmod.GetuVerification(db, data.Email)
		//储存验证码到数据库
		err = funcmod.InsertVerification(db, &funcmod.EmailVerificationCode{
			Email:        data.Email,
			Verification: verification})
		if err != nil {
			ctx.Returnfunc(401, "发送失败,稍后再试"+err.Error(), nil)
			return
		}
		//发送验证码
		err = funcmod.SendEmail(dailer, data.Email, verification)
		if err != nil {
			ctx.Returnfunc(401, "发送失败"+err.Error(), nil)
			return
		}

		ctx.Returnfunc(200, "ok", nil)
	})
	//login2:1.验证验证码,;2. 向用户列表插入数据
	r.POST("/signup2", func(ctx *gee7.Context) {
		data := funcmod.Logindata{}
		//读取用户传入的json数据
		err = ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, "解析数据错误"+err.Error(), nil)
			return
		}
		if funcmod.Validatedata(data.Name) {
			ctx.Returnfunc(402, "name不合理,请规范填写数据", nil)
			return
		}
		if funcmod.Validatedata(data.Password) {
			ctx.Returnfunc(402, "password不合理,请规范填写数据", nil)
			return
		}
		//验证用户验证码是否正确
		err = funcmod.Verify(db, data.Email, data.Verification)
		if err != nil {
			ctx.Returnfunc(501, "验证码错误"+err.Error(), nil)
			return
		}
		//插入用户数据
		id, err := funcmod.AddLogindata(db, &data)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
		} else {
			// 添加操作信息
			err = funcmod.Addoperation(db, id, "注册成功")
			if err != nil {
				ctx.Returnfunc(401, err.Error(), nil)
			} else {
				ctx.Returnfunc(200, "ok", nil)
			}
		}
	})
	r.POST("/loginpsw", signup.SigninBypassword(db, signinlest))
	r.POST("/loginemail1", signup.SigninByEmail1(db, dailer))
	r.POST("/loginemail2", signup.SigninByEmail2(db, signinlest))

	home := r.Group("/home")
	home.Use(funcmod.Addoperationmid(db))
	//修改密码
	//密码改密
	home.POST("/resetpaswbypasw", funcmod.ResetpaswBypassword(db, signinlest))
	//验证码改密
	home.POST("/resetpaswbyemail1", funcmod.ResetpaswByEmail1(db, dailer, signinlest))
	home.POST("/resetpaswbyemail2", funcmod.ResetpaswByEmail2(db, signinlest))
	// 登出
	r.POST("/signout", func(ctx *gee7.Context) {
		data := funcmod.IDCode{}
		//读取用户传入的json数据
		err = ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, "解析数据错误"+err.Error(), nil)
			return
		}

		//身份验证
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}
		ctx.Userid = signinlest[data.IDcode].ID

		//时间验证

		if signinlest[data.IDcode].ExpirationTime < time.Now().Unix() {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		//删除登录列表信息
		delete(signinlest, data.IDcode)

		ctx.Returnfunc(200, "ok", nil)

	})
	// 获取操作信息
	r.POST("/getoperations", func(ctx *gee7.Context) {
		data := funcmod.IDCode{}
		//读取用户传入的json数据
		err = ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, "解析数据错误"+err.Error(), nil)
			return
		}
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}
		r, err := funcmod.Getoperations(db, signinlest[data.IDcode].ID)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		ctx.Returnfunc(200, "ok", gee7.H{
			"operations": r,
		})
	})

	r.Run(":8888")
}
```
各种响应函数与中间件函数

```go


// 允许跨域访问中间件
func Dfot() gee7.HandlerFunc {
	return func(ctx *gee7.Context) {
		// 处理预检请求
		if ctx.Req.Method == "OPTIONS" {
			// 验证预检请求的来源、头部字段和请求方法是否符合预期
			// ...

			// 设置响应头部字段
			ctx.Writer.Header().Set("Access-Control-Allow-Origin", "*")
			ctx.Writer.Header().Set("Access-Control-Allow-Methods", "POST, GET, OPTIONS")
			ctx.Writer.Header().Set("Access-Control-Allow-Headers", "Content-Type")

			// 返回状态码 200 和空响应体
			ctx.Writer.WriteHeader(http.StatusOK)
			return
		}
	}
}



// 统一输出日志
// 程序正常运行
func Lognormal(msg string) {
	log.Println("normal:", msg)
}

// 警告
func Logwarning(msg string) {
	log.Println("warning:", msg)
}

// 错误
func Logerror(msg string) {
	log.Panicln("error:", msg)
}

// 添加用户操作中间件
func Addoperationmid(db *sql.DB) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {
		ctx.Next()
		//没有id信息,直接退出无需插入操作信息
		if ctx.Userid == 0 {
			return
		}
		defer func() {
			if ctx.StatusCode >= 400 {
				Addoperation(db, ctx.Userid, "操作"+ctx.Path+"失败")
			} else {
				Addoperation(db, ctx.Userid, "操作"+ctx.Path+"成功")
			}
		}()
	}
}

// 发送图片验证码图片中间件(用户提交请求时)
func Picverfi(db *sql.DB) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {

		// 生成验证码
		id := uuid.New().String() //生成唯一标识符
		code := captcha.RandomDigits(4)
		img := captcha.NewImage(id, code, captcha.StdWidth, captcha.StdHeight)
		// 生成验证码
		var buf bytes.Buffer
		_, err := img.WriteTo(&buf)
		if err != nil {
			ctx.Returnfunc(401, "出错了,点击重试", nil)
		}

		// 将验证码和图片存储到数据库中
		encodedImage := base64.StdEncoding.EncodeToString(buf.Bytes())
		verificationCode := VerificationCode{PicCode: code, CreatedAt: time.Now()}
		_, err = db.Exec("INSERT INTO verification_codes (id, code, created_at) VALUES (?, ?, ?)", id, verificationCode.PicCode, verificationCode.CreatedAt)
		if err != nil {
			ctx.Returnfunc(http.StatusInternalServerError, "Internal Server Error:"+err.Error(), nil)
			return
		}

		// 返回图片与唯一标识符
		ctx.Returnfunc(200, "ok", gee7.H{
			"picid": id,
			"png":   encodedImage,
		})

		fmt.Printf("id=%#v\ncode=%d", id, code)
	}
}

// 用于检验用户传入的数据是否合理
// 正则表达式，匹配长度为 3-40 的 ASCII 字符串
// 合理为false不合理为true
func Validatedata(password string) bool {
	regex := regexp.MustCompile(`^[[:ascii:]]{3,40}$`)
	return !regex.MatchString(password)
}

// 密码改密
func ResetpaswBypassword(db *sql.DB, signinlest SignInList) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {
		data := ResetpaswData{}
		//读取用户传入的Returnfunc数据
		err := ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, "解析数据错误"+err.Error(), nil)
			return
		}
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}
		ctx.Userid = signinlest[data.IDcode].ID
		if Validatedata(data.Password) {
			ctx.Returnfunc(401, "password不合理,请规范填写数据", nil)
			return
		}
		if Validatedata(data.Newpassword) {
			ctx.Returnfunc(401, "newpassword不合理,请规范填写数据", nil)
			return
		}
		//身份验证
		err = SignInVerification(signinlest, data.IDcode)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		//验证密码
		err = Verifypassword(db, signinlest[data.IDcode].ID, data.Password)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		//验证成功:修改密码
		err = Changepassword(db, signinlest[data.IDcode].ID, data.Newpassword)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		ctx.Returnfunc(200, "ok", nil)
	}
}

// 验证改密第一步:1. 图片验证,;2. 发送邮件验证码
func ResetpaswByEmail1(db *sql.DB, dialer *gomail.Dialer, signinlest SignInList) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {
		data := ResetpaswbyemailData1{}
		//读取用户传入的Returnfunc数据
		err := ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(410, err.Error(), nil)
			return
		}
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}
		ctx.Userid = signinlest[data.IDcode].ID
		//验证图片验证码是否正确
		err = Verifypiccode(db, data.PicID, data.PicCode)
		if err != nil {
			ctx.Returnfunc(401, "图片验证码错误"+err.Error(), nil)
			return
		}
		// 验证成功:获取用户邮箱
		emial, err := GetuserEmail(db, signinlest[data.IDcode].ID)
		if err != nil {
			ctx.Returnfunc(410, "未找到邮箱信息,请联系管理员"+err.Error(), nil)
			return
		}
		verification := GetuVerification(db, emial)
		//储存验证码到数据库
		err = InsertVerification(db, &EmailVerificationCode{
			Email:        emial,
			Verification: verification})
		if err != nil {
			ctx.Returnfunc(401, "发送失败,稍后再试"+err.Error(), nil)
			return
		}
		//发送验证码
		err = SendEmail(dialer, emial, verification)
		if err != nil {
			ctx.Returnfunc(401, "邮箱错误"+err.Error(), nil)
			return
		}
		ctx.Returnfunc(200, "ok", nil)
	}
}

// 完成验证码改密功能:验证验证码是否正确,插入数据
func ResetpaswByEmail2(db *sql.DB, signinlest SignInList) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {
		var data ResetpaswbyemailData2
		err := ctx.Getjson(&data)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		if signinlest[data.IDcode] == nil {
			ctx.Returnfunc(420, "非法访问", nil)
			return
		}
		if Validatedata(data.Newpassword) {
			ctx.Returnfunc(402, "password不合理,请规范填写数据", nil)
			return
		}
		//获取邮箱
		email, err := GetuserEmail(db, signinlest[data.IDcode].ID)
		if err != nil {
			ctx.Returnfunc(401, "验证码错误"+err.Error(), nil)
			return
		}
		// 验证用户邮箱验证码是否正确
		err = Verify(db, email, data.Verification)
		if err != nil {
			ctx.Returnfunc(501, "验证码错误"+err.Error(), nil)
			return
		}
		//验证成功,获取id信息
		id, err := GetuserId(db, email)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		//修改密码
		err = Changepassword(db, id, data.Newpassword)
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		// 添加操作信息
		err = Addoperation(db, id, "改密成功")
		if err != nil {
			ctx.Returnfunc(401, err.Error(), nil)
			return
		}
		ctx.Returnfunc(200, "ok", nil)
	}
}
```
底层数据库操作:
```go
//对数据库操作的所有函数
import (
	"database/sql"
	"fmt"
	"math/rand"
	"time"
)

// 添加操作信息,向表OperationRecord(操作列表)添加
func Addoperation(db *sql.DB, id int, operation string) error {

	_, err := db.Exec("insert into OperationRecord (id,behavior,time) VALUES (?, ?, ?)", id, operation, time.Now().Unix())
	if err != nil {
		return err
	}
	return nil
}

// 获取:表OperationRecord(操作列表)的数据
func Getoperations(db *sql.DB, id int) ([]OperationRecord, error) {

	rows, err := db.Query("SELECT behavior, time FROM OperationRecord WHERE id = ?", id)
	if err != nil {
		return nil, err
	}
	defer rows.Close()
	result := []OperationRecord{}
	for rows.Next() {
		p := &OperationRecord{ID: id}
		err = rows.Scan(&p.Behavior, &p.Time)
		if err != nil {
			return result, err
		}
		result = append(result, *p)
	}
	if err := rows.Err(); err != nil {
		return result, err
	}
	return result, nil
}

// user表------------------------------------------------------------------

// add:向user(注册列表)添加元素
func AddLogindata(db *sql.DB, logindata *Logindata) (int, error) {

	r, err := db.Exec("insert into user (name,password,Email) VALUES (?, ?, ?)", logindata.Name, encryptPassword(logindata.Password), logindata.Email)
	if err != nil {
		return 0, err
	}
	id, err := r.LastInsertId()
	return int(id), err
}

// ((id是操作用的,不是给用户登录用的))
// get:获取:向user(注册列表)获取id,登陆成功,获取id
func GetuserId(db *sql.DB, email string) (int, error) {

	id := -1
	err := db.QueryRow("SELECT id FROM user WHERE Email = ?", email).Scan(&id)
	if err != nil {
		return -1, err
	}
	return id, nil
}

// get:获取:向user(注册列表)获取email(改密时查Email,不能由用户传入Email,)
func GetuserEmail(db *sql.DB, id int) (string, error) {

	Email := ""
	err := db.QueryRow("SELECT Email FROM user WHERE id = ?", id).Scan(&Email)
	if err != nil {
		return "", err
	}
	return Email, nil
}

// 查:验证user(注册列表)密码
func Verifypassword(db *sql.DB, id int, password string) error {

	count := 0
	//SELECT COUNT(*)这个函数一般不会报错,如果找不到值,那么count=0
	err := db.QueryRow("SELECT COUNT(*) FROM user WHERE id = ? AND password = ? ", id, encryptPassword(password)).Scan(&count)
	fmt.Printf("count=%#v\n", count)
	if err != nil || count == 0 {
		return fmt.Errorf("出错了:密码错误:%#v", err)
	}
	return nil
}

// 改:修改:user(注册列表)密码
func Changepassword(db *sql.DB, id int, newpassword string) error {

	_, err := db.Exec("UPDATE user SET password = ? WHERE id = ?", encryptPassword(newpassword), id)
	if err != nil {
		return err
	}
	return nil
}

// -SignInlist-----------删除其他操作,使用内存管理登录列表---------------------------------------------------------

// 查:SignIn查询身份验证功能:传入SignInlist
func SignInVerification(signinlest SignInList, idcode string) error {

	// 查询身份认证码是否正确

	if signinlest[idcode] == nil {
		return fmt.Errorf("非法访问")
	}

	// 验证身份认证码是否过期
	if time.Now().Unix() > signinlest[idcode].ExpirationTime {
		return fmt.Errorf("身份验证过期,请重新登陆")
	}

	// 验证成功.更新身份认证码的过期时间
	signinlest[idcode].ExpirationTime = time.Now().Unix() + 30*60
	return nil
}

// // 增:向登录列表插入信息
// func InsertSignInData(db *sql.DB, data *SignInData) error {

// 	// 准备SQL语句
// 	stmt, err := db.Prepare("REPLACE INTO SignInList (id, IDcode, ExpirationTime) VALUES (?, ?, ?)")
// 	if err != nil {
// 		return err
// 	}
// 	defer stmt.Close()

// 	// 执行插入操作
// 	result, err := stmt.Exec(data.ID, data.IDCode, time.Now().Unix()+30*60)
// 	if err != nil {
// 		return err
// 	}

// 	// 输出插入结果
// 	_, err = result.RowsAffected()
// 	if err != nil {
// 		return err
// 	}
// 	return nil
// }

// // delete:删除登录信息; 登出删除登录列表元素
// func Deletesignindata(db *sql.DB, id int) error {
// 	_, err := db.Exec("DELETE FROM SignInList WHERE id = ?", id)
// 	if err != nil {
// 		return err
// 	}
// 	return nil
// }

// VerificationCode(验证码储存库)----------------------------------------------------------

// 获取验证码
// 检查是否进行验证过,用于重新发送验证码时调用,检查用户是否验证过
// 如果没有就发送与原来相同的验证码,否则就发送新验证码
func GetuVerification(db *sql.DB, email string) int {

	// 查询验证码是否正确
	var expirationTime int64 //储存过期时间

	verification, t := 0, -1

	err := db.QueryRow("SELECT time, Verification, ExpirationTime FROM VerificationCode WHERE Email = ?", email).Scan(&t, &verification, &expirationTime)
	//当没有验证码发送记录||已经验证过||超出时间限制  则需要重新生成验证码
	if err != nil || t == 1 || expirationTime+2500*60 < time.Now().Unix() {
		fmt.Printf("重新生成验证码%#v\n")
		rand.Seed(time.Now().UnixNano())
		// 生成100000到999999之间的随机数,作为验证码
		return rand.Intn(899999) + 100000
	} else {
		fmt.Printf("旧验证码=%#v\n", verification)
		return verification
	}
}

// 查: 验证验证码
func Verify(db *sql.DB, email string, verification int) error {

	// 查询验证码是否正确
	var expirationTime int64 //储存过期时间
	//代表已经验证过
	_, err := db.Exec("UPDATE VerificationCode SET time = ? WHERE Email = ?", 1, email)
	if err != nil {
		return fmt.Errorf("出错了" + err.Error())
	}
	err = db.QueryRow("SELECT ExpirationTime FROM VerificationCode WHERE Email = ? AND Verification = ?", email, verification).Scan(&expirationTime)
	if err != nil {
		return fmt.Errorf("验证码错误" + err.Error())
	}

	// 验证身份认证码是否过期
	if time.Now().Unix() > expirationTime {
		return fmt.Errorf("过期")
	}
	return nil
}

// 增:验证码列表插入信息
func InsertVerification(db *sql.DB, data *EmailVerificationCode) error {

	// 准备SQL语句

	stmt, err := db.Prepare("REPLACE INTO VerificationCode (Email, Verification, ExpirationTime, time) VALUES (?, ?, ?, ?)")
	if err != nil {
		return err
	}
	defer stmt.Close()

	// 执行插入操作
	_, err = stmt.Exec(data.Email, data.Verification, time.Now().Unix()+5*60, 0)
	if err != nil {
		return err
	}
	return nil
}

// get:获取:验证次数,重发验证码0无,1有
func GetuVerificationtime(db *sql.DB, email string) (int, error) {
	t := -1
	err := db.QueryRow("SELECT time FROM user WHERE Email = ?", email).Scan(&t)
	if err != nil {
		return -1, err
	}
	return t, nil
}

// // ----------------------------------------------------------
// func Insertuserdata(db *sql.DB, data Logindata) error {

// 	// 准备SQL语句

// 	stmt, err := db.Prepare("INSERT INTO user (Email, name, password) VALUES (?, ?, ?)")
// 	if err != nil {
// 		return err
// 	}
// 	defer stmt.Close()

//		// 执行插入操作
//		_, err = stmt.Exec(data.Email, data.Name, encryptPassword(data.Password))
//		if err != nil {
//			return err
//		}
//		return nil
//	}
//
// 图片验证码验证操作:用户传入图片id(唯一标识符发送图片信息时给的),+验证码
func Verifypiccode(db *sql.DB, id string, code []byte) error {
	var createdAt []uint8
	err := db.QueryRow("SELECT created_at FROM verification_codes WHERE id = ? AND code = ?", id, code).Scan(&createdAt)
	if err != nil {
		return err
	}
	created_ats := string(createdAt)
	created_at, err := time.Parse("2006-01-02 15:04:05", created_ats)
	if err != nil {
		return err
	}
	// 删除验证码
	db.Exec("DELETE FROM verification_codes WHERE id = ?", id)
	if time.Now().Unix() > created_at.Unix()+5*60 { //时效5分钟
		return fmt.Errorf("验证码过期")
	}
	return nil
}
```



事务处理
```go
import (
	"database/sql"
	"goweb/day7/gee7"
)

// 事务中间件,如果用户响应出错,则数据库回滚到响应之前的状态
func DBMid(db *sql.DB) gee7.HandlerFunc {
	return func(ctx *gee7.Context) {

		// 开始事务
		tx, err := db.Begin()
		if err != nil {
			ctx.FailJson(401, err.Error())
			return
		}

		// 调用下一个处理器
		ctx.Next()

		defer func() {
			// 如果响应出现错误，回滚事务,500以内的错误回滚
			if ctx.StatusCode >= 400 && ctx.StatusCode <= 500 {
				tx.Rollback()
				return
			}
			// 提交事务
			tx.Commit()
		}()
	}
}

```

数据体:
```go

import "time"

// 登录列表方便查找,避免查找sql 提升性能.key为身份码
type SignInList map[string]*SignInData

// 用于 储存登录信息
type SignInData struct {
	ID             int   `json:"id"`
	ExpirationTime int64 `json:"ExpirationTime"`
}

// 身份验证信息(用于接收用户传入的身份码)
type IDCode struct {
	IDcode string `json:"idcode"`
}

// 邮箱验证码信息(验证码需要真人验证之后再发送)(登录时可以直接使用)
type EmailVerificationCode struct {
	Email        string `json:"email"`
	Verification int    `json:"verification"`
}

// 注册传入信息2,邮箱验证码信息+用户基本信息
type Logindata struct {
	EmailVerificationCode `json:"emailverificationcode"`
	Name                  string `json:"name"`
	Password              string `json:"password"`
}

// 注册传入信息1,id为图片的唯一标识符,code为用户传入 的图片验证码, Email为需要发送验证邮件的邮箱
type Login1data struct {
	PicID   string `json:"picid"`
	PicCode []byte `json:"piccode"`
	Email   string `json:"email"`
}

// 操作信息列表
type OperationRecord struct {
	ID       int    `json:"id"`
	Behavior string `json:"behavior"`
	Time     int    `json:"time"`
}

// 密码:改密接受信息
type ResetpaswData struct {
	IDCode
	Password    string `json:"password"`
	Newpassword string `json:"newpassword"`
}

// 改密接受信息1:图片验证+身份验证
type ResetpaswbyemailData1 struct {
	IDCode
	PicID   string `json:"picid"`
	PicCode []byte `json:"piccode"`
}

// 验证码验证+新密码
type ResetpaswbyemailData2 struct {
	IDCode
	Verification int    `json:"verification"`
	Newpassword  string `json:"newpassword"`
}

// 验证码数据模型
type VerificationCode struct {
	PicID     string    `json:"picid"`
	PicCode   []byte    `json:"piccode"`
	Image     string    `json:"image"`
	CreatedAt time.Time `json:"created_at"`
}

// 密码登录信息(图片验证信息+邮箱+密码)
type Signupbypasw struct {
	PicID    string `json:"picid"`
	PicCode  []byte `json:"piccode"`
	Email    string `json:"email"`
	Password string `json:"password"`
}

// 发送验证码时上传的信息(图片验证信息+身份验证信息)
type Signupbyemail1 struct {
	PicID   string `json:"picid"`
	PicCode []byte `json:"piccode"`
	Email   string `json:"email"`
}

// 使用邮箱验证码登录时上传的信息(图片验证信息+身份验证信息)
type Signupbyemail2 struct {
	EmailVerificationCode
}
```

哈希加密:(密码)

```go

//密码哈希加密算法

func encryptPassword(password string) string {
	// 将字符串类型的密码转换为字节类型
	passwordBytes := []byte(password)
	// 创建SHA-256哈希对象
	sha256Hash := sha256.New()
	// 将密码字节数据传入哈希对象
	sha256Hash.Write(passwordBytes)
	// 获取哈希值的字节数据
	hashBytes := sha256Hash.Sum(nil)
	// 将字节数据转换为十六进制字符串
	hashString := hex.EncodeToString(hashBytes)
	// 返回十六进制字符串类型的哈希值
	return hashString
}
```



## 前端实现方案


#### 注册1 signup1(注册)

3个按钮:

1 获取图片验证:访问图片验证码接口,展示base64验证图片并储存picid

2 提交:访问注册1接口


表单:


|  名称   | 数据类型    |
| --- | --- |
|   Email  |   string  |
验证码|[4]int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功跳转页面到signup2页面,失败则展示失败信息

3 登录:跳转页面到LoginPsw(登录页面)


![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521231608.png)

代码示例:


```html
<template>
    <div>
      <h3>注册1</h3>
    </div>
    <div>
      <button @click="getVerificationImage">获取验证图片</button>
      <div v-if="verificationImage">
      <img :src="decodeBase64(verificationImage.png)" alt="验证码图片">
    </div>
      <form @submit="submitForm">
        <label for="email">邮箱：</label>
        <input type="email" id="email" v-model="email" required>
        <br>
        <label for="piccode">验证码：</label>
        <input type="text" id="piccode" v-model="piccode" pattern="[0-9]{4}" required>
        <br>
        <button type="submit">提交</button>
        <button @click="goToLogin">登录</button>
      </form>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        verificationImage: null,
        picid: '',
        email: '',
        piccode: ''
      };
    },
    methods: {
      goToLogin() {
        this.$router.push('/LogInPsw');
      },
      async getVerificationImage() {
        try {
          const response = await axios.get('http://localhost:8888/verifypic');
          if (response.data.code === 200) {
            this.verificationImage = response.data.data;
            this.picid = response.data.data.picid;
            
          }
        } catch (error) {
          console.error('获取验证图片失败', error);
        }
      },
      decodeBase64(base64Data) {
      return `data:image/png;base64, ${base64Data}`;
    },
      async submitForm(event) {
        event.preventDefault();
        if (!this.validateEmail(this.email)) {
          console.error('邮箱格式不正确');
          return;
        }
        if (!this.validatePiccode(this.piccode)) {
          console.error('验证码必须为4位数字');
          return;
        }
        try {
          const requestData = {
            picid: this.picid,
            email: this.email,
            piccode: this.piccode.split('').map(Number)
          };
          const response = await axios.post('http://localhost:8888/signup1', requestData);
          if (response.data.code === 200) {
            // 成功后进行页面跳转到
            this.$router.push({
        path: '/signup-tow',
        query: { email: this.email },
      });
          }
        } catch (error) {
          console.error('提交失败', error);
        }
      },
      validateEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      },
      validatePiccode(piccode) {
        return piccode.length === 4 && /^\d+$/.test(piccode);
      }
    }
  };
  </script>
  
```
#### 注册2 signup2
3个按钮:

1 重新发送验证码: 退回到signup1页面

2 提交:访问注册2接口


表单:


|  名称   | 数据类型    |
| --- | --- |
|   name  |   string  |
password|string
验证码|int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功展示注册成功;失败展示失败信息

3 登录:跳转页面到LoginPsw(登录页面)


![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521231833.png)

代码示例:
```html
<template>
  <div>
    <h2>注册2</h2>
    <p>hallo:{{ email }}</p>
  </div>
  <div>
  <form @submit="submitForm">
      <label for="name">Name:</label>
      <input type="text" id="name" v-model="name" required>
      <br>
      <label for="password">Password:</label>
      <input type="password" id="password" v-model="password" required>
      <br>
      <label for="verification">Verification Code:</label>
      <input type="text" id="verification" v-model="verification" pattern="[0-9]{6}" required>
      <button @click="ReVerify">重发验证码</button>
      <br>
      <button type="submit">Submit</button>
    </form>
  </div>
  <div>
    <button @click="goToLogin">登录</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      name: '',
      password: '',
      email: '',
      verification: '',
    };
  },
  created() {
    this.email = this.$route.query.email || '';
  },
  methods: {
    ReVerify() {
        this.$router.push('/signup1');
      },
    goToLogin() {
        this.$router.push('/LogInPsw');
      },
    async submitForm(event) {
      event.preventDefault();
      if (!this.validateName(this.name)) {
        console.error('Name must be less than 20 characters');
        return;
      }
      if (!this.validatePassword(this.password)) {
        console.error('Password must be less than 20 characters');
        return;
      }
      if (!this.validateVerification(this.verification)) {
        console.error('Verification code must be 6 digits');
        return;
      }
      try {
        const requestData = {
          name: this.name,
          password: this.password,
          emailverificationcode: {
            email: this.email,
            verification: parseInt(this.verification),
          },
        };
        const response = await axios.post('http://localhost:8888/signup2', requestData);
        if (response.data.code === 200) {
          console.log('Signup successful');
        } else {
          console.error(response.data.msg);
        }
      } catch (error) {
        console.error('Signup failed', error);
      }
    },
    validateName(name) {
      return name.length > 0 && name.length <= 20;
    },
    validatePassword(password) {
      return password.length > 0 && password.length <= 20;
    },
    validateVerification(verification) {
      return verification.length === 6 && /^\d+$/.test(verification);
    },
  },
};
</script>

```

#### 密码登录loginpsw

4个按钮:

1 获取验证码图片:访问获取验证码图片接口,展示返回的图片并储存picid

2 忘记密码: 跳转到到loginemail1页面

3 提交:访问密码登录接口接口


表单:


|  名称   | 数据类型    |
| --- | --- |
|   Email  |   string  |
password|string
验证码|int

向密码登录接口发送提交请求,携带json数据,并处理响应结果

如果成功储存返回的idcode(cookie)并跳转页面到home页面;失败展示失败信息

4 登录:跳转页面到signup1


![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521231945.png)


```html

<template>
  <div>
      <h3>密码登录</h3>
    </div>
    <div>
      <button @click="getVerificationImage">获取验证图片</button>
      <div v-if="verificationImage">
        <img :src="decodeBase64(verificationImage.png)" alt="验证码图片">
      </div>
      <form @submit="submitForm">
        <label for="email">邮箱：</label>
        <input type="email" id="email" v-model="email" required>
        <br>
        <label for="password">密码：</label>
        <input type="password" id="password" v-model="password" required>
        <br>
        <label for="piccode">验证码：</label>
        <input type="text" id="piccode" v-model="piccode" pattern="[0-9]{4}" required>
        <br>
        <button type="submit">提交</button>
      </form>
    </div>
    <div>
    <button @click="goToLoginE">忘记密码</button>
  </div>
    <div>
    <button @click="goToSignup">注册</button>
   </div>
   
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        verificationImage: null,
        picid: '',
        email: '',
        password: '',
        piccode: ''
      };
    },
    methods: {
      goToLoginE() {
        this.$router.push('/LogInEmail');
      },
      goToSignup() {
        this.$router.push('/signup1');
      },
      async getVerificationImage() {
        try {
          const response = await axios.get('http://localhost:8888/verifypic');
          if (response.data.code === 200) {
            this.verificationImage = response.data.data;
            this.picid = response.data.data.picid;
          }
        } catch (error) {
          console.error('获取验证图片失败', error);
        }
      },
      decodeBase64(base64Data) {
        return `data:image/png;base64, ${base64Data}`;
      },
      async submitForm(event) {
        event.preventDefault();
        if (!this.validateEmail(this.email)) {
          console.error('邮箱格式不正确');
          return;
        }
        if (!this.validatePassword(this.password)) {
          console.error('密码长度不能超过20个字符');
          return;
        }
        if (!this.validatePiccode(this.piccode)) {
          console.error('验证码必须为4位数字');
          return;
        }
        try {
          const requestData = {
            picid: this.picid,
            piccode: this.piccode.split('').map(Number),
            email: this.email,
            password: this.password
          };
          const response = await axios.post('http://localhost:8888/loginpsw', requestData);
          if (response.data.code === 200) {
            // 登录成功后进行页面跳转到 HomeV.vue
            this.$cookies.set('idcode', response.data.data.idcode);
            this.$router.push('/HomeV');
          } else {
            console.error(response.data.msg);
          }
        } catch (error) {
          console.error('提交失败', error);
        }
      },
      validateEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      },
      validatePassword(password) {
        return password.length <= 20;
      },
      validatePiccode(piccode) {
        return piccode.length === 4 && /^\d+$/.test(piccode);
      }
    }
  };
  </script>
  
  ```

#### 验证码登录 

1. loginemail1


4个按钮:

1 获取图片验证:访问图片验证码接口,展示base64验证图片并储存picid

2 提交:访问验证码登录1接口

用户填写表单:


|  名称   | 数据类型    |
| --- | --- |
|   Email  |   string  |
验证码|[4]int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功跳转页面到loginemail2页面,失败则展示失败信息

3 注册:跳转页面到sianup1(注册页面)

4 使用密码登录:跳转到loginpsw(密码登录页面)

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521232351.png)


```html

<template>
    <div>
      <h3>验证码登录1</h3>
    </div>
    <div>
        <VerifyPic @verificationImage="handleVerificationImage"></VerifyPic>
      <form @submit="submitForm">
        <label for="piccode">验证码：</label>
        <input type="text" id="piccode" v-model="piccode" pattern="[0-9]{4}" required>
        <br>
        <label for="email">邮箱：</label>
        <input type="email" id="email" v-model="email" required>
        <br>
        <button type="submit">提交</button>
      </form>
    </div>
    <div>
    <button @click="goToSignup">注册</button>
  </div>
  <div>
    <button @click="goToLogin">使用密码登录</button>
  </div>
  </template>
  
  <script>
  import VerifyPic from './VerifyPic.vue';
  import axios from 'axios';
  
  export default {
    components: {
      VerifyPic
    },
    data() {
      return {
        picid: '',
        piccode: '',
        email: ''
      };
    },
    methods: {
      goToLogin() {
        this.$router.push('/LogInPsw');
      },
      goToSignup() {
        this.$router.push('/signup1');
      },
    handleVerificationImage(picid) {
      this.picid = picid;
    },
      async submitForm(event) {
        event.preventDefault();
        if (!this.validatePiccode(this.piccode)) {
          alert('验证码必须为4位数字');
          return;
        }
        if (!this.validateEmail(this.email)) {
          alert('邮箱格式不正确');
          return;
        }
        try {
          const requestData = {
            picid: this.picid,
            piccode: this.piccode.split('').map(Number),
            email: this.email
          };
          const response = await axios.post('http://localhost:8888/loginemail1', requestData);
          if (response.data.code === 200) {
            // 登录成功后进行页面跳转到 LoginEmail2.vue
            this.$router.push({
        path: '/LogInEmail2',
        query: { email: this.email },
      });
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          alert('提交失败', error);
        }
      },
      validatePiccode(piccode) {
        return piccode.length === 4 && /^\d+$/.test(piccode);
      },
      validateEmail(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      }
    }
  };
  </script>
```

2. loginemail2

4个按钮:

1 重新发送验证码: 退回到loginemail1页面

2 提交:访问验证码登录2接口


表单:


|  名称   | 数据类型    |
| --- | --- |
验证码|int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功展示登录成功,储存idcode到cookie中并跳转页面到home;失败展示失败信息

3 注册:跳转页面到signup1(注册页面)

4 使用密码登录:跳转到loginpsw(密码登录页面)


![](https://files.mdnice.com/user/41613/3b6fa1e6-bd92-43c3-9b05-fe67c0ba9eea.png)

```html
<template>
  <div>
      <h3>验证码登录2</h3>
    </div>
    <div>
      <form @submit="submitForm">
        <label for="verification">验证码：</label>
        <input type="number" id="verification" v-model.number="verification" max="999999" required>
        <button @click="goToLogin1">重发验证码</button>
        <br>
        <button type="submit">登录</button>
      </form>
    </div>
    <div>
    <button @click="goToSignup">注册</button>
  </div>
  <div>
    <button @click="goToLogin">使用密码登录</button>
  </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        email: '',
        verification: null
      };
    },
    created() {
      this.email = this.$route.query.email || '';
    },
    methods: {
      goToLogin1() {
        this.$router.push('/LogInEmail');
      },
      goToLogin() {
        this.$router.push('/LogInPsw');
      },
      goToSignup() {
        this.$router.push('/signup1');
      },
      async submitForm(event) {
        event.preventDefault();
        if (!this.validateVerification(this.verification)) {
          alert('验证码必须为小于6位的数字');
          return;
        }
        try {
          const requestData = {
            email: this.email,
            verification: this.verification
          };
          const response = await axios.post('http://localhost:8888/loginemail2', requestData);
          if (response.data.code === 200) {
            // 登录成功后进行页面跳转到 HomeV.vue
            this.$cookies.set('idcode', response.data.data.idcode);
            this.$router.push('/HomeV');
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          alert('提交失败', error);
        }
      },
      validateVerification(verification) {
        return verification !== null && verification.toString().length <= 6;
      }
    }
  };
  </script>
```


### home页面
/home
* 定义三个按钮 sign out(登出),reset password(重置密码),view(查看操作记录)

 sign out(登出):访问登出接口,提交json数据,退回到loginpsw(登录页面)
 
 查询:访问查询功能接口,并处理响应数据,如果成功展示操作信息,如果失败,展示失败信息
 
 ![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521232627.png)
 
 ```html

<template>
  <div>
    <h1>Welcome to HomeV</h1>
    <button @click="confirmLogout">登出</button>
    <button @click="changePassword">改密</button>
    <button @click="getOperations">查询</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  methods: {
    async confirmLogout() {
      const confirmLogout = confirm('是否确认登出？');
      if (confirmLogout) {
        try {
          const idcode = this.$cookies.get('idcode');
          const requestData = { idcode };
          const response = await axios.post('http://localhost:8888/signout', requestData);
          if (response.data.code === 200) {
            this.$router.push('/');
          }else if (response.data.code === 420) {
            alert(response.data.msg);
            this.$router.push('/');
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          console.error('登出失败', error);
          alert('操作失败');
          this.$router.push('/');
        }
      }
    },
    changePassword() {
      this.$router.push('/RePasswordPsw');
    },
    async getOperations() {
      try {
        const idcode = this.$cookies.get('idcode');
        const requestData = { idcode };
        const response = await axios.post('http://localhost:8888/getoperations', requestData);
        if (response.data.code === 200) {
          const operations = response.data.data.operations;
          // 在此处理返回的操作数据，可以存储到组件的数据中并进行展示
          console.log(operations);
        }else if (response.data.code === 420) {
            alert(response.data.msg);
            this.$router.push('/');
          } else {
          alert(response.data.msg);
        }
      } catch (error) {
        console.error('查询操作失败', error);
        if (error.response && error.response.status === 420) {
          alert('非法访问');
          this.$router.push('/');
        }
      }
    }
  }
};
</script>
```





#### repasswordpsw 旧密码密码改密

三个按钮:

1  忘记密码:跳转到repasswordemail页面(验证码改密)

2 返回:退回到home页面

3 改密:

旧密码改密表单:

| 名称    |  数据类型   |
| --- | --- |
|   password  |  string   |
new password|string
new password again|string
验证码|[4]int

(2). 用户填写表单信息,点击提交.

(3). 调用密码验证功接口 提交json数据,处理响应数据

如果成功展示"改密成功",否则展示错误信息

![](https://djy1-1306563712.cos.ap-shanghai.myqcloud.com/20230521232725.png)

```html

<template>
    <div>
      <h1>密码改密</h1>
      <form @submit="changePassword">
        <label for="password">当前密码:</label>
        <input type="password" id="password" v-model="password" required>
        <br>
        <label for="newpassword">新密码:</label>
        <input type="password" id="newpassword" v-model="newPassword" required>
        <br>
        <button type="submit">改密</button>
      </form>
      <button @click="goBack">返回</button>
      <button @click="goToForgotPassword">忘记密码</button>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        password: '',
        newPassword: ''
      };
    },
    methods: {
      goBack() {
        this.$router.push('/HomeV');
      },
      goToForgotPassword() {
        this.$router.push('/RePasswordEmail1');
      },
      async changePassword(event) {
        event.preventDefault(); // 阻止表单提交的默认行为
  
        try {
          const idcode = this.$cookies.get('idcode');
          const requestData = {
            password: this.password,
            newpassword: this.newPassword,
            idcode: idcode
          };
          const response = await axios.post('http://localhost:8888/home/resetpaswbypasw', requestData);
          if (response.data.code === 200) {
            alert('改密成功');
          }else if (response.data.code===420) {
            alert(response.data.msg);
            this.$router.push('/');
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          console.error('改密失败', error);
          if (error.response && error.response.status === 420) {
          alert('非法访问');
          this.$router.push('/');
        }
        }
      }
    }
  };
  </script>
```




#### repasswordemail 验证码改密1

3个按钮:

1 获取图片验证:访问图片验证码接口,展示base64验证图片并储存picid

2 提交:访问验证码改密1接口


表单:


|  名称   | 数据类型    |
| --- | --- |
验证码|[4]int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功跳转页面到repasswordemai2页面,失败则展示失败信息

3 使用密码改密:跳转页面到repasswordPsw(密码改密页面)

4 返回:退回到home页面


![](https://files.mdnice.com/user/41613/d2a78b50-91f3-436f-ab70-c16046246fb9.png)

```html
<template>
    <div>
      <h1>验证码改密1</h1>
      <VerifyPic @verificationImage="handleVerificationImage"></VerifyPic>
      <form @submit="submitForm">
        <label for="piccode">验证码：</label>
        <input type="text" id="piccode" v-model="piccode" pattern="[0-9]{4}" required>
        <br>
        <button type="submit">提交</button>
      </form>
      <button @click="goBack">返回</button>
      <button @click="goToRePasswordPsw">使用密码改密</button>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import VerifyPic from './VerifyPic.vue';

  
  export default {
    components: {
      VerifyPic
    },
    data() {
      return {
        piccode: '',
        picid: ''
      };
    },
    methods: {
      goBack() {
        this.$router.push('/HomeV');
      },
      goToRePasswordPsw() {
        this.$router.push('/RePasswordPsw');
      },
      handleVerificationImage(picid) {
      this.picid = picid;
    },
    validatePiccode(piccode) {
        return piccode.length === 4 && /^\d+$/.test(piccode);
      },
      async submitForm(event) {
        event.preventDefault(); // 阻止表单提交的默认行为
        if (!this.validatePiccode(this.piccode)) {
          alert('验证码必须为4位数字');
          return;
        }
        try {
          const idcode = this.$cookies.get('idcode');
          const requestData = {
            picid: this.picid, // 请替换为您自己的数据
            piccode: this.piccode.split('').map(Number),
            idcode: idcode
          };
          const response = await axios.post('http://localhost:8888/home/resetpaswbyemail1', requestData);
          if (response.data.code === 200) {
            this.$router.push('/RePasswordEmail2');
          }else if (response.data.code===420) {
            alert(response.data.msg);
            this.$router.push('/');
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          console.error('提交失败', error);
          if (error.response && error.response.status === 420) {
          alert('非法访问');
          this.$router.push('/');
        }
        }
      }
    }
  };
  </script>
```


2. 验证码改密2 repasswordemail2

4个按钮:

1 重新发送验证码: 退回到repasswordemail1页面

2 提交:访问验证码验证码改密2接口


表单:


|  名称   | 数据类型    |
| --- | --- |
newpassword|string
验证码|int

向验证码发送接口提交请求,携带json数据,并处理响应结果

如果成功提示改密成功;失败展示失败信息

3 返回home:跳转页面到home(注册页面)

4 使用密码改密:跳转到repasswordpsw(密码改密页面)


![](https://files.mdnice.com/user/41613/66cce9d2-1f9a-4878-9064-7ec338513a4e.png)

```html
<template>
    <div>
      <h1>验证码改密2</h1>
      <form @submit="submitForm">
        <label for="newpassword">新密码:</label>
        <input type="password" id="newpassword" v-model="newPassword" required>
        <br>
        <label for="verification">验证码:</label>
        <input type="text" id="verification" v-model="verification" required>
      <button @click="ReVerify">重发验证码</button>
        <br>
        <button type="submit">提交</button>
      </form>
      <button @click="goBack">返回</button>
      <button @click="goToRePasswordPsw">使用密码改密</button>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        newPassword: '',
        verification: ''
      };
    },
    methods: {
      ReVerify() {
        this.$router.push('/RePasswordEmail1');
      },
      goBack() {
        this.$router.push('/HomeV');
      },
      goToRePasswordPsw() {
        this.$router.push('/RePasswordPsw');
      },
      async submitForm(event) {
        event.preventDefault(); // 阻止表单提交的默认行为
  
        try {
          const idcode = this.$cookies.get('idcode');
          const requestData = {
            newpassword: this.newPassword,
            idcode: idcode,
            verification: parseInt(this.verification)
          };
          const response = await axios.post('http://localhost:8888/home/resetpaswbyemail2', requestData);
          if (response.data.code === 200) {
            alert('改密成功');
          }else if (response.data.code===420) {
            alert(response.data.msg);
            this.$router.push('/');
          } else {
            alert(response.data.msg);
          }
        } catch (error) {
          console.error('提交失败', error);
          if (error.response && error.response.status === 420) {
          alert('非法访问');
          this.$router.push('/');
        }
        }
      }
    }
  };
  </script>
```




