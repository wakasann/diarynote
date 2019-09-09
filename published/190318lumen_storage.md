# Lumen 5.5.x Storage #

在使用 `Lumen 5.5.*` 的項目中，想使用 Laravel 自帶的`Storage`類，使用 Storage 類進行文件操作時，遇到以下2个錯誤提示::

```
Class filesystem does not exist
Class 'League\\Flysystem\\Adapter\\Local' not found
```

第1個錯誤提示， 需要在 `bootstrap/app.php` 中 添加

```
// load config filesystems.php file
$app->configure('filesystems');

$app->singleton(
    Illuminate\Contracts\Filesystem\Factory::class,
    function ($app) {
        return new Illuminate\Filesystem\FilesystemManager($app);
    }
);
class_alias('Illuminate\Support\Facades\Storage', 'Storage');


$app->register(Illuminate\Filesystem\FilesystemServiceProvider::class);
```

第2個提示是文件系統的 `league/flysystem` 依賴包不存在，可通過執行以下命令進行安裝

```
composer require league/flysystem
```

------

在 `filesystems.php` 這個配置文件下的 `disks` 键，原來是可以配置绝对路径的,如:

```
'disks' => [

        'local' => [
            'driver' => 'local',
            'root' => storage_path('app'),
        ],

        'public' => [
            'driver' => 'local',
            'root' => storage_path('app/public'),
            'url' => env('APP_URL').'/storage',
            'visibility' => 'public',
        ],

        's3' => [
            'driver' => 's3',
            'key' => env('AWS_KEY'),
            'secret' => env('AWS_SECRET'),
            'region' => env('AWS_REGION'),
            'bucket' => env('AWS_BUCKET'),
        ],

        // 自定义另外一个上传路径(使用绝对路径)
        'uploads' => [
            'driver' => 'local',
            'root' => env('CUSTOM_UPLOAD_DIR','/opt/lampp/htdocs/test/wp-content/uploads/temp/'),
        ],
    ],
```



##### Reference #####
1. [Storage Filesystem doesn't work](https://github.com/laravel/lumen-framework/issues/168)
2. [laravel5.4使用Storage::delete()删除文件失败原因](http://www.58vb.com/a331.html)