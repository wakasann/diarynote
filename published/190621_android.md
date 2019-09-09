Android 编译失败

##### Reference
1. [Program type already present: com.google.gson.FieldAttributes](https://blog.csdn.net/rainminism/article/details/81098273) 添加 `configurations{
        all*.exclude group: 'com.google.code.gson'
    }`
2. [TaskExecutionException: Execution failed for task ':app:transformJackWithJackForDebug'最权威有效的解决方案](https://blog.csdn.net/wjj1996825/article/details/79838430) 使用了`compileOptions` 中添加`incremental false`