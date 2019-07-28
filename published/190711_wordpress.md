# wordpress 获取所有管理员的邮箱

```
function get_administrator_email(){
    $blogUsers = get_users('role=Administrator');
    $adminEmails = array();
    foreach ($blogUsers as $user) {
        $adminEmails[] =  $user->user_email;
    }
    return $adminEmails;
}
```

##### References
1. [Get email address of type Administrator](https://wordpress.stackexchange.com/a/143020)