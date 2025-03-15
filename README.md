# 项目介绍
- 这是一个可以定时向邮箱发送邮件的工具，邮件内容默认是获取国际新闻列表。此项目主要目的是邮箱账号“养号”用途。当然你也可以用于每天定时获取国际新闻到你得邮箱里面。
- 项目是基于 <https://github.com/yutian81/auto-email> 的项目改造而来，如果对你有用请给原作者一个start。

## 部署方式一：github action（简单）

- 首先把项目fork到你自己的仓库中

- 在你的 GitHub 仓库中，依次点击 Settings -> Secrets -> Actions，然后点击 New repository secret，创建一个名为`EMAIL_CONFIG`的机密变量，内容为你的邮件配置信息。

`EMAIL_CONFIG`机密变量的`JSON`格式如下：
```
{
  "smtp_server": "smtp.example.com",
  "smtp_port": 587,
  "smtp_user": "your_email@example.com",
  "smtp_pass": "your_password",
  "from_email": "your_email@example.com",
  "to_emails": [
    "recipient1@example.com",
    "recipient2@example.com",
    "recipient3@example.com",
    "recipient4@example.com",
    "recipient5@example.com"
  ],
  "subject": "定时邮件通知",
  "body": "这是一封来自自动化脚本的邮件。"
}
```
**参数含义**

| 变量名 | 填写示例 | 说明 | 是否必填 | 
| ------ | ------- | ------ | ------ |
| smtp_server | smtp.example.com | 发送邮件的邮箱**smtp服务器地址**，如QQ邮箱的是：smtp.qq.com | 是 |
| smtp_port | 587 | smtp服务的端口，基本上都是587 | 是 |
| smtp_user | your_email@example.com | 用于发送邮件的邮箱账号，如QQ邮箱的是：88888@qq.com | 是 |
| smtp_pass | UIDJ7658CVJ | 发送邮件的邮箱账号**授权码**，不是账号的密码，常见邮箱的授权码获取方式见下方备注 | 是 |
| from_email | your_email@example.com | 邮件头部的发件人信息，和smtp_user值一致即可 | 是 |
| to_emails | 7 | 准备接收邮件的账号列表，一行一个，格式为json。注意最后一行没有逗号 | 是 |
| subject | 7 | 邮件的主题 | 是 |
| body | 7 | 邮件的内容，后续可以通过接口加载为动态值 | 是 |

***备注***
- 常见邮箱的授权码获取方式说明：
- **QQ邮箱**：https://service.mail.qq.com/detail/0/75
- **微软邮箱**：https://support.microsoft.com/zh-cn/office/outlook-com-%E7%9A%84-pop-imap-%E5%92%8C-smtp-%E8%AE%BE%E7%BD%AE-d088b986-291d-42b8-9564-9c414e2aa040
- **新浪邮箱**：https://help.sina.com.cn/comquestiondetail/view/1566/
- **谷歌邮箱**：https://support.google.com/mail/answer/185833?hl=zh-Hans
- **其他邮箱**：请咨询谷歌搜索配置方法，xxx邮箱授权码设置。基本上都差不多

**若需要tg通知，则新增以下两个变量**

- TG_ID = tg机器人用户ID

- TG_TOKEN = tg机器人token

**运行检查**
- 在你的 GitHub 仓库中，打开此项目，依次点击Action -> All workflows -> 自动群发邮件 -> run workflow。手动运行一次action，之后便可自动运行。

![image](https://github.com/user-attachments/assets/327f8a9c-936e-4022-926e-2f7cdd713e41)

-默认为每周一次。可自行在action的yml配置文件中修改自动执行频率。时间是以UTC为准的，国内的时间是UTC+8，比定时任务快8小时，所以如果想早上9点收到消息，应该设置成0 1 * * 1

![image](https://github.com/user-attachments/assets/9d977579-c30d-44ea-b05e-f42d18751946)

## 部署方式二：cf worker

到 [resend](https://resend.com/) 注册一个账号，申请 `apitoken`，并且绑定一个域名，根据 resend 的提示到域名托管商处添加相应的 dns 解析记录，有三个 `txt` 和一个 `mx` 记录。

在 cf 新建一个 wokrer，粘贴仓库内 `resend.js` 中的内容

已完成前端界面

![](https://pan.811520.xyz/2025-01/1736779999-%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250113224844.webp)

### 设置以下环境变量：

- RESEND_API_KEY = 填刚刚申请的 `apitoken`
- KEY = 设置一个密码，访问`https://你项目的worker域名?key=你设置的密码`，即为前段面板
- FROM_EMAIL = 发件人邮箱，邮箱域名必须与在 resend 中绑定的域名一致，前缀随意
- TO_EMAILS = 收件人邮箱，支持多个邮箱地址，每行一个
- TG_ID = TG 机器人的 chat id
- TG_TOKEN = TG 机器人的 token
- SUBJECT = 邮件主题
- BODY = 邮件正文
- NEWS_API_KEY = 申请的新闻api的key
- NEWS_COUNTRY = 新闻国家

### 绑定KV
- KV绑定变量名/KV空间名：AUTO_EMAIL

### 设置 `corn` 触发器
实现定时自动群发邮件，建议每周执行一次
