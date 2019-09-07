Codeigniter 獲取所有控制器和控制器的方法



下面的代碼是在控制器中的1個方法

```
 public function test(){
        $controller_path = APPPATH . 'controllers/';
        $controller_files = glob($controller_path.'*.php');

        $ignore_controller_file_names = array(
            'Welcome.php'
        );

        if(count($ignore_controller_file_names) > 0){
            foreach ($ignore_controller_file_names as $file_name){
                $test_controller_file  = array_search($controller_path.$file_name, $controller_files);
                if($test_controller_file !== false){
                    unset($controller_files[$test_controller_file]);
                }
            }
        }

        //router need ignore method form extend controller
        $controller_parent_files = array(
            array(
               'file_path' => BASEPATH.'core/Controller.php',
               'class_name' => 'CI_Controller',
            ),
            //if have create MY_Controller.php in application/core folder
            array(
                'file_path' => APPPATH.'core/MY_Controller.php',
                'class_name' => 'MY_Controller',

            ),
        );
        $ignore_methods = array();
        if(count($controller_parent_files) > 0){
            foreach ($controller_parent_files as $controller_parent_file){
                include_once  $controller_parent_file['file_path'];
                $controller_methods = get_class_methods($controller_parent_file['class_name']);
                foreach ($controller_methods as $method){
                    if(!in_array($method,$ignore_methods)){
                        $ignore_methods[] = $method;
                    }
                }
            }
        }

//        $ignore_methods = array(
//            //ignore CI_Controller get_instance method()
//            '__construct',
//            'get_instance',
//            //ignore MY_Controller's public/protected methods
//        );

        $echo_router = true;
        $routers = array();
        foreach ($controller_files as $controller_file){
            include_once $controller_file;
            $tmp_file_name = str_replace($controller_path,'',$controller_file);
            $controller_name = lcfirst(str_replace('.php','',$tmp_file_name));

            $class_methods = get_class_methods($controller_name);
            foreach ($class_methods as $key=>$class_method){
                if(in_array($class_method,$ignore_methods)){
                    unset($class_methods[$key]);
                    continue;
                }
                if($echo_router){
                    echo "{$controller_name}/{$class_method}<br/>";
                }

            }
            $routers[$controller_name] = array(
                'class_name' => ucfirst($controller_name),
                'class' => $controller_name,
                'methods' => $class_methods
            );
        }

        ksort($routers);

//        echo "<pre>";
//        print_r($routers);
//        echo "</pre>";
    }
```





##### References
1. [how to get all list of controllers and methods without MY_controller and Ci_controller methods in codeigniter?](https://cmsdk.com/php/how-to-get-all-list-of-controllers-and-methods-without-mycontroller-and-cicontroller-methods-in-codeigniter.html)

