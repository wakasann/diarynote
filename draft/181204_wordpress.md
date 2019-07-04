1. [Make the WooCommerce My Account Navigation Look Awesome with CSS](https://wordx.press/make-great-looking-woocommerce-my-account-navigation-with-css/) 美好 woocommerce 我的账户页面
2. [WooCommerce: Automatically Update Cart on Quantity Change](https://businessbloomer.com/woocommerce-automatically-update-cart-quantity-change/) ,[Auto update cart after quantity change](https://wordpress.stackexchange.com/a/304327) woocommmerce 购物车中当数量改变时，自动更新购物车
3. woocommerce mycred 日志在 woocommerce中分页解决
因为 wordpress 的 `Permalink Settings` 设定为 `Post name`
查考 [WooCommerce Wallet – credit, cashback, refund system](https://wordpress.org/plugins/woo-wallet/) 插件，参考这个插件的结构，在woocommerce中添加了 `endpoint`，主要的功能是显示 mycred 的 会员积分余额和会员积分日志，最大的问题是 `mycard_history` 这个短代码中，分页的链接显示的 `/my-account/[endpoint]/?page=3`,点击这个链接，会跳回 `/my-account/3`页面，分页就处理不到。

当查看了 `http://codex.mycred.me/classes/mycred_query_log/` 中获取页码有

```
/**
 * Get Page Number
 * @since 1.4
 * @version 1.0.3
 */
public function get_pagenum() {

    global $wp;

    $page_key = ( isset( $this->args['page_arg'] ) && $this->args['page_arg'] !== NULL && $this->args['page_arg'] != '' ) ? $this->args['page_arg'] : 'page';
    if ( isset( $wp->query_vars[ $page_key ] ) && $wp->query_vars[ $page_key ] != '' )
        $pagenum = absint( $wp->query_vars[ $page_key ] );

    elseif ( isset( $_REQUEST[ $page_key ] ) )
        $pagenum = absint( $_REQUEST[ $page_key ] );

    elseif ( isset( $_REQUEST[ $page_key ] ) )
        $pagenum = absint( $_REQUEST[ $page_key ] );

    else return 1;

    return max( 1, $pagenum );

}
```

`$this->args['page_arg']` 可以通过 `http://codex.mycred.me/filters/mycred_query_log_args/` 的 `mycred_query_log_args` filter 覆盖 `page_arg`的参数名称，改为 定义的 `[endpoint]` 名称之后，分页就可以了

如:
```
function custom_mycred_transactions_args($args){
    $args['page_arg'] = '[endpoint]';
    return $args;
}
add_filter('mycred_query_log_args','custom_mycred_transactions_args'));
```