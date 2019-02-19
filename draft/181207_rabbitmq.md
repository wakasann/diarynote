1. docker run
```
sudo docker run -d  -p 35662:5672 -p 35672:15672 -p 35673:15673 -p 35675:15675  -e RABBITMQ_SSL_CACERTFILE=/var/rabbitmq/lib/fullchain.pem -e RABBITMQ_SSL_CERTFILE=/var/rabbitmq/lib/cert.pem -e RABBITMQ_SSL_FAIL_IF_NO_PEER_CERT=true -e RABBITMQ_SSL_KEYFILE=/var/rabbitmq/lib/privkey.pem -e RABBITMQ_SSL_VERIFY=verify_peer -v /data/rabbitmq-data2/:/var/rabbitmq/lib --name foodboyrabbitmqtt test_rabbitmqtt:v1

sudo docker run -d  -p 15671:15671 -p 15672:15672 -p 15675:15675 -p 15673:15673 -p 4369:4369 -p 5661:5671 -p 5672:5672  -e RABBITMQ_SSL_CACERTFILE=/etc/rabbitmq/tls/cacert.pem -e RABBITMQ_SSL_CERTFILE=/etc/rabbitmq/tls/cert.pem -e RABBITMQ_SSL_FAIL_IF_NO_PEER_CERT=true -e RABBITMQ_SSL_KEYFILE=/etc/rabbitmq/tls/privkey.pem -e RABBITMQ_SSL_VERIFY=verify_peer -v /home/ray/dockers/mqtttls/:/etc/rabbitmq/tls --name test_rabbitmq test_rabbitmqtt:v4



sudo docker run -d  -p 15671:15671 -p 15672:15672 -p 15675:15675 -p 4369:4369 -p 5671:5671 -p 5672:5672 --name test_rabbitmq test_rabbitmqtt:v1
```


```
function database_update_for_custom_update(){
    global $wpdb;
    $sql = "ALTER TABLE ". WP_G_NEWS_ANNOUNCEMENT." ADD `gNews_updated_at` DATETIME NOT NULL AFTER `gNews_type`";
    $result = $wpdb->query($sql);

    if ( true === $result ) {
        update_option( '_current_nas_db_custom_version', '8.8.3' );
    }
}

function nas_database_update() {
    $nas_db_version = get_option( '_current_nas_db_version', 'no' );
    if ( $nas_db_version == 'no' ) {
        database_update_for_8_8_3();
    }
    $custom_nas_db_version = get_option( '_current_nas_db_custom_version', 'no' );
    if ( $custom_nas_db_version == 'no' ) {
        database_update_for_custom_update();
    }
}
```