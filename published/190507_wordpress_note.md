# Wordpress 文章编辑页面添加 metabox



```
 add_meta_box($id,$title,$callback,$screen,$context:,$priority);
```



参数
* $id （字符串）（必需）Meta模块的 HTML“ID”属性

* $title （字符串）（必需）Meta模块的标题，对用户可见

* $callback （回调）（必需）为Meta模块输出 HTML代码的函数

* $post_type （字符串）（必需）显示Meta模块的文章类型，可以是文章（post）、页面（page）、链接（link）、附件（attachment） 或 自定义文章类型（自定义文章类型的别名）

* $context (字符串）（可选）Meta模块的显示位置（’normal’,’advanced’, 或 ‘side’）默认值：’advanced’

* $priority （字符串）（可选）Meta模块显示的优先级别（’high’, ‘core’, ‘default’or ‘low’）默认值： ‘default’

* $callback_args （数组）（可选）传递到 callback 函数的参数。callback 函数将接收 $post 对象和其他由这个变量传递的任何参数。

源文件

`wp-admin/includes/template.php`



##### Reference

1. [WordPress 添加Meta Box的方法步骤](https://www.cnblogs.com/2881064178dinfeng/p/6233579.html)
2. [Plugin API/Action Reference/add meta boxes](https://codex.wordpress.org/Plugin_API/Action_Reference/add_meta_boxes) 案列
3. [add_meta_box()](https://developer.wordpress.org/reference/functions/add_meta_box/) add meta box 的参数
3. [Adding Custom Meta Boxes to the WordPress Admin Interface](https://www.sitepoint.com/adding-custom-meta-boxes-to-wordpress/)
4. [How To Create Wordpress Meta Boxes](https://www.smashingmagazine.com/2011/10/create-custom-post-meta-boxes-wordpress/)
6. [WordPress中添加Add_Meta_Box使用方法](http://www.phpfensi.com/cms/20150323/8804.html) 有add_meta_box()的中文参数说明和案例
7. [WordPress函数：add meta box（自定义添加Meta模块）](https://www.jiloc.com/43846.html)
