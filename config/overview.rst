.. title:: Config - Prosty sposób na ładowanie plików

.. meta::
    :description: Config - Prosty sposób na ładowanie plików - dframeframework.com
    :keywords: dframe, config, loading, loader, dframeframework
    
====
Opis
====

Prosty sposób na ładowanie plików. Jeden z często przydatnych rozwiązań, pozwala ładować pojedyncze pliki określające konfiguracje danego modułu. W przykładowej wersji aplikacji  znajduje sie Config/router.php oraz Config/View/smarty.php

.. code-block:: php
 
 return [
     'setTemplateDir' => APP_DIR . 'View/templates',       // Default './View/templates'
     'setCompileDir' => APP_DIR . 'View/templates_c',      // Default './View/templates_c'
     'addPluginsDir' => '',                                // Default template dir ./Libs/Plugins/smarty
     'debugging' => false,                                 // Default False
     'fileExtension' => '.html.php'                        // Default '.html.php'
 ];

W praktyce jest używany do wielu podstawowych rzeczy od pobrania danych. W przykładowej aplikacji została wykorzystana ta klasa do pobrania "setTemplateDir" w przeciwnym wypadku gdyby nie było ustalonej wartości ma załadować nam ścieszkę podaną jako drugi parametr.

.. code-block:: php

 <?php
 
 namespace Controller;
 
 use Dframe\Config;
 use Dframe\Controller;
 use Dframe\Router\Response;
 
 class PageController extends Controller
 {
     /**
      * Initial function call
      */
     public function init()
     {
 
     }
 
     /**
      * Catch-all method for requests that can't be matched.
      *
      * @param  string $method
      * @param  array  $parameters
      *
      * @return Response
      */
 
     public function __call($method, $test)
     {
         $smartyConfig = Config::load('view/smarty');
         $view = $this->loadView('Index');
         $patchController = $smartyConfig->get('setTemplateDir', APP_DIR . 'View/templates') . '/page/' . htmlspecialchars($_GET['action']) . $smartyConfig->get('fileExtension', '.html.php');
 
         if (!file_exists($patchController)) {
             return $this->router->redirect('page/index');
         }
 
         return Response::create($view->fetch('page/' . htmlspecialchars($_GET['action'])));
 
     }
 }
 
