# cloud.yii
云框架，yii扩展框架，支持包命名空间

###yii

>Yii 是一个高性能，基于组件的 PHP 框架，用于快速开发现代 Web 应用程序。
名字 Yii （读作 易）在中文里有“极致简单与不断演变”两重含义，也可看作 Yes It Is! 的缩写。

###云框架

**云框架在yii的基础上扩展了部分功能，以至于更好的支持包命名空间**


#####使用：

1. 创建一个web项目,项目的目录结构为

     |- data *运行后的资源目录*
     
     |- library *cloud或者其它第三方包(需要完成自动加载命令)*
     
     |- system *应用程序目录*
     
     |- web  *web入口文件目录*
     
2. 配置文件, 正式环境存在 “data/deploy” 文件.
 
	* 测试环境配置文件：data/development/config.php
	
	~~~
	<?php 
	return array(
   
	    'env' => array(
	        'language' => 'zh_cn',
	        'theme' => 'default'
	    ),
	  
	    'databases'=>array(
	
	        'db' => array(
	            'host' => 'localhost',
	            'port' => '3306',
	            'dbname' => 'test',
	            'username' => 'root',
	            'password' => '',
	            'tableprefix' => '',
	            'charset' => 'utf8'
	        ),
	
	    ),
	
	    'components' => array(
	        // URL资源管理器
	        'urlManager' => array(
	            'urlFormat' => 'get',
	            'caseSensitive' => false,
	            'showScriptName' => true,
	            'rules' => array(
	                '<module:\w+>/<controller:\w+>/<action:\w+>' => '<module>/<controller>/<action>', 
	            )
	        ),
	    )

	);
	~~~
	
	* 正式环境配置文件：data/production/config.php
	
	~~~
	<?php 
	return array(
   
	    'env' => array(
	        'language' => 'zh_cn',
	        'theme' => 'default'
	    ),
	  
	    'databases'=>array(
	
	        'db' => array(
	            'host' => 'localhost',
	            'port' => '3306',
	            'dbname' => 'test',
	            'username' => 'root',
	            'password' => '',
	            'tableprefix' => '',
	            'charset' => 'utf8'
	        ),
	
	    ),
	
	    'components' => array(
	        // URL资源管理器
	        'urlManager' => array(
	            'urlFormat' => 'path',
	            'caseSensitive' => false,
	            'showScriptName' => false,
	            'rules' => array(
	                '<module:\w+>/<controller:\w+>/<action:\w+>' => '<module>/<controller>/<action>', 
	            )
	        ),
	    )

	);
	~~~
	

3. 创建一个项目,入口文件: web/index.php

	~~~
	<?php 
	   
	use cloud\Cloud;
	include dirname(dirname(__FILE__))."/library/cloud/bootstrap.php";
	$app = Cloud::createWebApplication();
	$app->run();
	exit;   		  
	~~~

4. 创建一个module,案例main.

	* 创建 system/modules/main/MainModule.php
	
		~~~
   	 	<?php
    
       namespace application\modules\main;
       use cloud\core\modules\Module;
       class MainModule extends Module {
       }
		~~~
    * 创建 system/modules/main/install/config.php 为模块配置文件

  	 ~~~
    <?php
    
         return array(
             'param' => array(
                 'name' => '核心模块',
                 'description' => '系统核心模块',
                 'author' => 'oShine',
                 'version' => '1.0',
                 'indexShow' => array(
                     'link' => 'main/default/index',
                 ),
             ),
             'config' => array(
                 'modules' => array(
                     'main' => array(
                         'class' => 'application\modules\main\MainModule'
                     )
                 ),
                 'components' => array(
                     'errorHandler' => array(
                         'errorAction' => 'main/default/error',
                     ),
                     'messages' => array(
                         'extensionPaths' => array(
                             'main' => 'application.modules.main.language'
                         )
                     )
                 ),
             ),
         );
    
		~~~

    * 创建 system/modules/main/controllers/DefaultController.php 为控制器文件
     
     ~~~
    <?php
        namespace application\modules\main\controllers;
        use cloud\core\controllers\Controller;
        class DefaultController extends Controller
        {
            public function actionIndex(){
                return $this->render('index');
            }
        }
 	~~~
    *   创建 system/modules/main/views/default/index.php 为视图文件
     
          ~~~
          <p>hello world!</p>
          ~~~

    * 访问:http://localhost/web/index.php?r=main/default/index
