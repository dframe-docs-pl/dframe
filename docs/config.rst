====
Opis
====

Jeden z często przydatnych rozwiązań. Pojedyncze pliki określające konfiguracje danego modułu. W przykładowej wersji aplikaci znajduje sie Config/router.php oraz Config/View/smarty.php

.. code-block:: php

 return array(
     'setTemplateDir' => APP_DIR.'View/templates',            // Default './View/templates'
     'setCompileDir' => APP_DIR.'View/templates_c',           // Default './View/templates_c'
     'addPluginsDir' => '',                                  // Default template dir ./Libs/Plugins/smarty
     'debugging'     => false,                               // Default False
     'fileExtension' => '.html.php'                         // Default '.html.php'
 );

W praktyce jest używany do wielu podstawowych rzeczy od pobrania danych. W przykładowej aplikacji została wykorzystana ta klasa do pobrania "setTemplateDir" w przeciwnym wypadku gdyby nie było ustalonej wartości ma załadować nam ścieszkę podaną jako drugi parametr.

.. code-block:: php

 namespace Controller;
 use Dframe\Controller;
 use Dframe\Config;

 class PageController extends Controller
 {
     /**
      * initial function call
      */
     public function init(){

     }
     
     /**
      * Catch-all method for requests that can't be matched.
      *
      * @param  string    $method
      * @param  array     $parameters
      * @return Response
      */
      
     public function __call($method, $test)
     {
         if (method_exists($this, $_GET['action'])) { // Skip dynamic page if method in controller exist
             return;
         }
         $smartyConfig = Config::load('view/smarty');
         $view = $this->loadView('Index');
         $patchController = $smartyConfig->get('setTemplateDir', APP_DIR.'View/templates').'/page/'.htmlspecialchars($_GET['action']).$smartyConfig->get('fileExtension', '.html.php');
        
         if (!file_exists($patchController)) {  
             return $this->router->redirect('page/index');
         }
         return Response::create($view->fetch('page/'.htmlspecialchars($_GET['action'])));
        
     }
 }
