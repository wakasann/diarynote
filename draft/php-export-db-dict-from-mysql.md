通过Navicat 导出 Mysql 表的数据库字典

通過information_schema.COLUMNS表查询字段

```
SELECT * FROM information_schema.COLUMNS;
```

查詢一個數據庫的一個表的結構

```
SELECT
	TABLE_NAME AS `Name`,
	`COLUMN_NAME` AS `字段名`,
	`COLUMN_TYPE` AS `类型`,
	`COLLATION_NAME` AS `排序规则`,
	`IS_NULLABLE` AS `空`,
	`COLUMN_KEY` AS `索引`,
	`COLUMN_DEFAULT` AS `默认`,
	`EXTRA` AS `额外`,
	`COLUMN_COMMENT` AS `备注`
FROM
	`information_schema`.`COLUMNS`
WHERE
	TABLE_SCHEMA = 'database_name'
AND TABLE_NAME = 'table_name';
```
在 Navicat 的查询页面导出 Excel(在导出向导的第4步，需勾选"包含列的标题")

[![uploads/200110/fs2020011000001L.jpg](https://s2.ax1x.com/2020/01/10/lhLzXn.jpg)](https://imgchr.com/i/lhLzXn)

导出的效果:

[![uploads/200110/fs2020011000002t.jpg](https://s2.ax1x.com/2020/01/10/lhLjpQ.jpg)](https://imgchr.com/i/lhLjpQ)

------
```
SHOW FULL COLUMNS FROM `database_name`.`table_name`; --列出一张表的字段
SHOW INDEXES FROM `database_name`.`table_name`; --列出一張表的索引信息
```
`列出一张表的字段`其中`Key`的值: `PRI`:主键,`UNI`:唯一索引,`MUL`:该列的值可以重复的索引

------

感觉通过 Navicat 一个一个表的导出感觉有点费时间，就写了一个 php 代码，导出一个数据库表的数据字典

```
<?php

/**
* filename: export_db_dict.php
* 依赖于 数据库的表和表的字段都有注释
* It is depend on database table comment and table fieldes comment is not empty
*/
$filename = time().'.xls';
header("Pragma: public");
header("Expires: 0");
header("Cache-Control: must-revalidate, post-check=0, pre-check=0");
header("Content-Type: application/force-download");
header("Content-Type: application/download");
header("Content-Disposition: attachment;filename="  . $filename);
header("Content-Transfer-Encoding: binary ");

// header("Content-type:text/html;charset=utf-8");
$config = array(
	//Database Configuration
	//数据库配置
	'database' => array(
		'hostname' => 'localhost',
		'username' => 'root',
		'password' => 'secret',
		'database' => 'test_export',
	),
	//choose export table,if tables value is empty,it will export all table for connected database,else it  only export tables define in tables array
	//需要导出的表，如果为空，即导出所有的表，如果不为空，只导出定义的表名
	// example: 'tables' => array('users')
	'export_tables' => array(

	)
);

$table_header_color = '#e2efda';


$hostname =  $config['database']['hostname'];
$database =  $config['database']['database'];
$export_tables = $config['export_tables'];

$dsn = "mysql:host=$hostname;dbname=$database;charset=utf8";

try {
    $dbh = new PDO($dsn, $config['database']['username'], $config['database']['password']); //初始化一个PDO对象
    
    $all_table_fields_sql = "SELECT
	TABLE_NAME AS `Name`,`COLUMN_NAME` AS `Field`,
	`COLUMN_TYPE` AS `Type`,
	`COLLATION_NAME` AS `Collation`,
	`IS_NULLABLE` AS `Null`,
	`COLUMN_KEY` AS `Key`,
	`COLUMN_DEFAULT` AS `Default`,
	`EXTRA` AS `Extra`,
	`COLUMN_COMMENT` AS `Comment`
FROM
	`information_schema`.`COLUMNS`
WHERE
	TABLE_SCHEMA = '$database' ORDER BY TABLE_NAME ASC;";
	//根据表名分组
	$table_field_group = array();
	$table_comments = array();
	foreach($dbh->query($all_table_fields_sql, PDO::FETCH_ASSOC) as $row) {

        $table_name = $row['Name'];
        if(count($export_tables) > 0){

        	if(in_array($table_name,$export_tables)){
        		$table_field_group[$table_name][] = $row;

        		if(!array_key_exists($table_name, $table_comments)){
	        		$table_comments[$table_name] = '';
	        	}
			}

        }else{
        	$table_field_group[$table_name][] = $row;

        	if(!array_key_exists($table_name, $table_comments)){
        		$table_comments[$table_name] = '';
        	}
        }


        
    }

    //获取表的表注释
    if(count($table_field_group) > 0){
    	foreach($table_field_group as $key=>$value){
    		$table_comment_sql = "SELECT
	`TABLE_NAME` AS `Name`,
	`TABLE_COMMENT` AS `Comment`
FROM
	`information_schema`.`TABLES`
WHERE
	`TABLE_SCHEMA` COLLATE utf8_bin IN ('$database')
AND `TABLE_NAME` COLLATE utf8_bin = '$key';";

			foreach($dbh->query($table_comment_sql, PDO::FETCH_ASSOC) as $row) {
				if(array_key_exists($row['Name'], $table_comments)){
	        		$table_comments[$row['Name']] = $row['Comment'];
	        	}
			}
    	}
    }

    if(count($table_field_group) > 0){

    	?>
    	<table width="100%" border="1" cellpadding="0" cellspacing="0" style="font-size: 18px; color: #333;">
       <?php
foreach ($table_field_group as $tableName=>$fields):
?>

	<tr>
        <td colspan="6"><?php echo $tableName;?> <?php echo $table_comments[$tableName];?></td>
    </tr>

     <tr>
        <td align="left" style="background: <?php echo $table_header_color;?>;">字段名</td>
        <td align="left" style="background: <?php echo $table_header_color;?>;">数据类型</td>
        <td align="left" style="background: <?php echo $table_header_color;?>;">默认值</td>
        <td align="left" style="background: <?php echo $table_header_color;?>;">空</td>
        <td align="left" style="background: <?php echo $table_header_color;?>;">自动递增</td>
        <td align="left" style="background: <?php echo $table_header_color;?>;">备注</td>
    </tr>

    <?php foreach ($fields as $field):?>
        <tr>
            <td align="left"><?php echo $field['Field'];?></td>
            <td align="left"><?php echo $field['Type'];?></td>
            <td align="left">
            	<?php if($field['Default'] != null):?>
            		<?php echo $field['Default'];?>
           		<?php endif;?>
            </td>
            <td align="left"><?php echo $field['Null'];?></td>
            <td align="left"><?php if($field['Extra'] == 'auto_increment'){ echo "是";};?></td>
            <td align="left"><?php echo $field['Comment'];?></td>
        </tr>
    <?php endforeach;?>
    <tr>
        <td colspan="6">&nbsp;</td>
    </tr>


<?php endforeach; ?>
   </table>
    	<?php

    }else{
    	echo  "<table><tr><td>can not export,database may be no tables</td></tr></table>";
    }


    //关闭链接
    $dbh = null;
} catch (PDOException $e) {
    die ("Error!: " . $e->getMessage() . "<br/>");
}
```



```
ALTER TABLE `table_name` COMMENT = 'comment content'; -- 给表添加注释的sql
```



导出效果:

![uploads/200110/fs2020011000003v.jpg](https://s2.ax1x.com/2020/01/10/lhX0q1.jpg)



格式参考了discuz的数据字典和 phpmyadmin 数据表结构的显示


## References

1. [使用Navicat快速生成MySQL数据字典](https://www.cnblogs.com/lukefan/archive/2019/02/27/10445285.html)
2. [phpmyadmin 中文文档](https://docs.phpmyadmin.net/zh_CN/latest/config.html) 配置可配置phpmyadmin开发模式和调试sql