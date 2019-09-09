---
title: 修复Woocommerce用户登记不发送管理员邮件
date: 2019-08-19 20:06:28
tags:
  - woocommerce
categories:
  - Wordpress
description:  修复Woocommerce用户登记不发送管理员邮件
---

前提: Woocommerce的设置，`Emails`，`New account`是启用的
![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820181352.png)

Wordpress 安装了 Woocommerce创建，登录和注册页面也是 Woocommerce的`account`页面，在 woocommerce的`account`页面登记，网站管理员邮箱 不会收到 新用户注册的注册通知。




在 主题的 `functions.php` 添加以下代码:

```
add_action( 'woocommerce_created_customer_notification',  'customer_new_account_to_admin', 10, 3 );

 /**
    * send notification to admin
    */
function customer_new_account_admin($customer_id, $new_customer_data = array(), $password_generated = false){
        if ( ! $customer_id ) {
            return;
        }
        do_action( 'register_new_user', $customer_id );
    }
```

然后在 `account`页面再 进行注册成功之后，通过`wp-mail-log`插件，看到 网站管理员收到电邮通知了。

`woocommerce_created_customer_notification` 是在 `woocommerce/includes/class-wc-emails.php` 中第200行找到的`add_action( 'woocommerce_created_customer_notification', array( $this, 'customer_new_account' ), 10, 3 );`


##### References
1. [Woocommerce New Customer Admin Notification Email](https://stackoverflow.com/a/25170599)