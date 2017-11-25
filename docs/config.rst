====
Opis
====

Jeden z często przydatnych rozwiązań. Pojedyncze pliki określające konfiguracje danego modułu. W przykładowej wersji aplikaci znajduje sie Config/router.php oraz Config/View/smarty.php

.. code-block:: php

 return array(
    'setTemplateDir' => '../app/View/templates',
    'setCompileDir' => '../app/View/templates_c',
    'debugging'     => false,
    'fileExtension' => '.html.php'
 );

W praktyce jest używany do wielu podstawowych rzeczy od pobrania danych. W przykładowej aplikacji została wykorzystana ta klasa do pobrania "setTemplateDir" w przeciwnym wypadku gdyby nie było ustalonej wartości ma załadować nam ścieszkę podaną jako drugi parametr.

.. code-block:: php

 namespace Controller;
 use Dframe\Controller;
 use Dframe\Config;
 
 class PageController extends Controller 
 {
     /** 
      * Dynamiczny loader stron wykrywa akcje jako plik i stara sie go za ładować
      */
 
     public function init(){
     	if(method_exists($this, $_GET['action'])) // Skip dynamic page if method in controller exist
             return;
     	
         $smartyConfig = Config::load('view/smarty');
         $view = $this->loadView('Index');
 
         $type = 'page';
 
         if(isset($_GET['type']))
             $type = htmlspecialchars($_GET['type']);
 
         $patchController = $smartyConfig->get('setTemplateDir', appDir.'../app/View/ templates').'/'.$type.'/'.htmlspecialchars($_GET['action']).$smartyConfig->get('fileExtension', '.html.php');
         
         if(!file_exists($patchController)){
             $this->router->redirect('page/index');
             return;
         }
         
         $view->render($type.'/'.htmlspecialchars($_GET['action']));
         return;
         
     }
