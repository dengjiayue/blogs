[toc](使用gomail.v2库调用邮箱的SMTP服务发送邮件)

### 功能介绍
* 使用程序实现邮件的发送

之前做登录功能的项目的时候需要用到邮箱验证的功能需要发送邮箱验证码

目前可用的验证码发送可以使用几个方式

1. 使用第三方邮件服务提供商（如SendGrid、Mailchimp、Amazon SES等）
2. 使用主机提供商提供的邮件服务（腾讯云的邮件服务）。这将确保您可以从您的应用程序中发送电子邮件。
3. 可以使用邮箱的SMTP服务进行发送，

### 实现


#### 开启邮箱的SMTP功能
有些邮箱不支持SMTP功能

我开始想使用gmail的功能，发现现在gmail不支持SMTP功能了，关闭了


然后我就使用了QQ邮箱

1. 打开设置->账户

2. 找到POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务

3. 开启服务

4. 记录你的邮箱授权码,不要泄露,不然会有安全问题

5. 端口号一般为端口号465或587


具体操作可参考:

[https://wx.mail.qq.com/account/index?sid=zV1EQoxGV2kuGzFiANFjQgAA#/?tab=safety&r=1684856844007](https://wx.mail.qq.com/account/index?sid=zV1EQoxGV2kuGzFiANFjQgAA#/?tab=safety&r=1684856844007)


##### 安装gomail.v2

打开终端或命令行窗口，并输入以下命令以安装 gomail：

```
go get gopkg.in/gomail.v2
```
这会自动下载并安装最新版本的 gomail 库。以将 v2 替换为其他版本，以安装特定的版本。

如果使用的是 Go Modules，请确保在项目目录中存在一个 go.mod 文件，然后输入以下命令以将 gomail 添加为依赖项：

```
go get gopkg.in/gomail.v2
```
这会自动将 gomail 添加到您的项目中，并更新 go.mod 文件。

等待安装完成。一旦安装完成，可以在代码中导入 gomail 并开始使用它了。

```
import (
    "gopkg.in/gomail.v2"
)
```

### 配置邮箱发送服务

封装:创建SMTP客户端+发送邮件服务

```go
import (
	"fmt"

	"github.com/badoux/checkmail"
	"gopkg.in/gomail.v2"
)

// 使用gomail库创建SMTP客户端
func CreatDialer() *gomail.Dialer {
	return gomail.NewDialer("smtp.qq.com", 465, "xxx@qq.com", "xxxx") //AuthCode为邮箱的授权码(填自己的)
}



// 发送邮件函数,+邮箱可用性验证
func SendEmail(dialer *gomail.Dialer, to string, data string) error {
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
	message.SetHeader("Subject", "xxx")
	message.SetBody("text/plain", "data")

	// 发送邮件消息,开携程发生邮件
	go dialer.DialAndSend(message)

	return nil
}
```
也可以根据自己需要确定传入参数的数量设置邮箱发送的形式与内容


之后我们先创建一个dailer

```go
//连接邮箱
	dailer := funcmod.CreatDialer()
```

然后直接调用sendemail函数发送邮件就可以实现邮件的发送

```go
//发送验证码
		err = funcmod.SendEmail(dailer, Email, data)
```

#### 验证邮箱可用性

其实到这里还有缺陷

就是邮箱是否可用不能确定,我们需要增加邮箱可用性的验证才算完整

我在上面的代码中用到了checkmail包就是用来检查邮箱可用性的,需要go get一下这个包.

有兴趣也可以自己写.

```go
// 检查电子邮件地址是否可用
	err := checkmail.ValidateFormat(to)
	if err != nil {
		return fmt.Errorf("email address %s is not available: %s", to, err.Error())
	}
```