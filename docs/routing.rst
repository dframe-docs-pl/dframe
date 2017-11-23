Konfiguracja
------------

W pliku konfiguracyjnym definiujemy tablicę z adresami dla naszej aplikacji.

 - |https| - true/false wymuszanie https
 - |NAME_CONTROLLER| - Nazwa domyślnego kontrolera
 - |NAME_METHOD| - Nazwa domyślnej metody z kontrolera w NAME_CONTROLLER
 - |publicWeb| - Główny folder z którego ma czytać pliki (js, css)
 - |assetsPath| - Folder dynamicznych

 - |docs/docsId| - Przykładowy routing ze zmienną |docsId| w której jest definicja adresu |docs/[docsId]/| oraz do jakich parametrów jest ona przypisana |task|
 - |error/404| - j.w
 - |default| - domyślna definicja ładujaca controler/method. |params| definiuje możliwość pojawienia się dodatkowych parametrów, natomiast 

.. code-block:: php
 '_params' => array(
     '[name]/[value]/',
     '[name]=[value]'
 )

określa w jaki sposób mają być interpretowane dodatkowe parametry foo=bar

**Config/router.php**

.. code-block:: php
 return array(
    'https' => false,
    'NAME_CONTROLLER' => 'page',
    'NAME_METHOD' => 'index',
    'publicWeb' => '',
    'assetsPath' => '',

    'docs/:docsId' => array(
        'docs/[docsId]/', 
        'task=page&action=[docsId]&type=docs'
    ),
    
    'error/:status' => array(
        'error/[status]/', 
        'task=page&action=error&status=[status]',
        'args' => array(
            'status' => '[status]'
        ),
    ),
    'default' => array(
        '[task]/[action]/[params]',
        'task=[task]&action=[action]',
        'params' => '(.*)',
        '_params' => array(
            '[name]/[value]/', 
            '[name]=[value]'
            )
        ),       
 );

Kontroler
---------

 - makeUrl - służy go generowania pełnego adresu. Przykład |makeurl| - Metoda używana do przekierowań odpowiednik |header| z tym ze parametr jest kluczem z tablicy Config/router.php. W przypadku używania docs/:docsId wygląda to następująco |redirect| 

**Controller/Page.php**

.. code-block:: php
 namespace Controller;
 use Dframe\Controller;
 
 class PageController extends Controller 
 {
     public function index(){
         echo $this->router->makeUrl('docs/:docsId?docsId=23');
         return;
     }
 
     public function docs() {
         if(!isset($_GET['docsId'])){
             $this->router->redirect('error/404');
             return;
         }
 
     public function error($status = '404'){
         $routerCodes = $this->router->response();
 
         if(!array_key_exists($status, $routerCodes::$code)){
             $this->router->redirect('error/500');
             return;
         }
 
         $view = $this->loadView('index');
         $smartyConfig = Config::load('view/smarty');
 
         $patchController = $smartyConfig->get('setTemplateDir', appDir.'../app/View/templates').'/ errors/'.htmlspecialchars($status).$smartyConfig->get('fileExtension', '.html.php');
 
         if(!file_exists($patchController)){
             $this->router->redirect('error/404');
             return;
         }
 
         $view->assign('error', $routerCodes::$code[$status]);
         $view->render('errors/'.htmlspecialchars($status));
     }
 
     }
 }

.. |router| cCode:: 
 <?php $this->router; ?>
.. |page/index| cCode:: 
 <?php $this->router->makeUrl('page/index'); ?>
.. |$router| cCode:: {$router}
.. |$makeurl| cCode:: {$router->makeUrl('index/page')}


Widok
-----

assign - jest metodą silnika templatki która przypisuje wartość do zmiennej którą wykorzystujemy w plikach templatki

**View/templates/index.html.php**

.. customLi:: myTabs
 :php: active/php
 :smarty: smarty
  .. code-block:: php
   <?php include "header.html.php" ?>
   Przykładowa strona stworzona na Frameworku Dframe
   
   Routing:
   <?php $this->router->makeurl('page/index'); ?> index/page
   <?php $this->makeurl('error/404'); ?> page/404
   
   <?php $this->domain('https://examplephp.com')->makeurl('error/404'); ?> page/404
   
   <?php include "footer.html.php" ?>
  Przy wykorzystaniu czystego PHP

  - |router| wszystkie już dostępne metody używa analogicznie do |page/index|

  next
  .. code-block:: php
   {include file="header.html.php"}
   Przykładowa strona stworzona na Frameworku Dframe
   
   Routing:
   {$router->makeurl('page/index')} index page
   {$router->makeurl('error/404')} page 404
   
   {$router->domain('https://examplephp.com')->makeurl('error/404')} page 404
   
   {include file="footer.html.php"}
  W przykładzie użyty jest silnik S.M.A.R.T.Y

  - |$router| wszystkie już dostępne metody używa analogicznie do |$makeurl|

**View/index.php**
.. code-block:: php
 namespace View;
 use Dframe\Asset\Assetic;
 
 
 class IndexView extends \View\View
 {
     public function init(){
         $this->router->assetic = new Assetic();
         $this->assign('router', $this->router);
 
         /* ... */

.. center::
 Dframe\Router\Response

Rozszerzenie podstawowego **Dframe\Router** jest **Dframe\Router\Response** dodaje on funkcjonalność ustawiania statusu odpowiedzi (404, 500 itp) oraz ich nagłówków. 

.. code-block:: php
 return Response::create('Hello Word!')
        ->status(200)
        ->header([
            'Expires' => 'Mon, 26 Jul 1997 05:00:00 GMT', 
            'Cache-Control' => 'no-cache',
            'Pragma', 'no-cache'
        ]); 

Dla generowania html

.. code-block:: php
 return Response::render('Hello Word!');

Dla generowania html

.. code-block:: php
 return Response::renderJSON(array('return' => '1')); 

.. |https| cCode:: https
.. |NAME_CONTROLLER| cCode:: NAME_CONTROLLER
.. |NAME_METHOD| cCode:: NAME_METHOD
.. |publicWeb| cCode:: publicWeb
.. |assetsPath| cCode:: assetsPath
.. |docs/docsId| cCode:: docs/:docsId
.. |docsId| cCode:: :docsId
.. |docs/[docsId]/| cCode:: docs/[docsId]/
.. |task| cCode:: task=page&action=docs&docsId=[docsId]
.. |error/404| cCode:: error/404
.. |default| cCode:: default
.. |params| cCode:: 'params' => '(.*)'

.. |makeurl| cCode:: $this->router->makeUrl('docs/:docsId?docsId=23');
.. |header| cCode:: Header('Location: ""');
.. |redirect| cCode:: $this->router->redirect('page/index');