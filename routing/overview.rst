.. title:: Routing - Prosty Router PHP

.. meta::
    :description: Routing - Elastyczny routing linków. Tworząc aplikacje warto zadbać by miały one przyjazne linki.
    :keywords: dframe, router, routing, urls, seo, url, friendlyurl, htaccess, routes, dframeframework, pozycjonowanie  

Tworząc aplikacje warto zadbać by miały one „przyjazne” linki. Ma to duży wpływ na pozycjonowanie seo.
Router linków działa na zasadzie jak routery sieciowe. Ma on za zadanie wywołać naszą metodę z kontrollera.

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

 '_params' => [
     '[name]/[value]/',
     '[name]=[value]'
 ]

określa w jaki sposób mają być interpretowane dodatkowe parametry foo=bar

**Config/router.php**

.. code-block:: php

 <?php
 
 return [
     'https' => false,
     'NAME_CONTROLLER' => 'page',    // Default Controller for router
     'NAME_METHOD' => 'index',       // Default Action for router
     'publicWeb' => '',              // Path for public web (web or public_html)
 
     'assets' => [
         'minifyCssEnabled' => true,
         'minifyJsEnabled' => true,
         'assetsDir' => 'assets',
         'assetsPath' => APP_DIR.'View/',
         'cacheDir' => 'cache',
         'cachePath' => APP_DIR.'../web/',
         'cacheUrl' => HTTP_HOST.'/',
     ],
 
     'routes' => [
         'docs/:pageId' => [
             'docs/[pageId]/', 
             'task=page&action=[docsId]&type=docs'
         ],
         
         'error/:code' => [
             'error/[code]/', 
             'task=page&action=error&type=[code]',
             'code' => '([0-9]+)',
             'args' => [
                 'code' => '[code]'
             ]
         ],
         
         ':task/:action' => [
             '[task]/[action]/[params]',
             'task=[task]&action=[action]',
             'params' => '(.*)',
             '_params' => [
                 '[name]/[value]/',
                 '[name]=[value]'
             ]
         ],
          
         'default' => [
             '[task]/[action]/[params]',
             'task=[task]&action=[action]',
             'params' => '(.*)',
             '_params' => [
                 '[name]/[value]/', 
                 '[name]=[value]'
             ]
         ]
     ]   
 
 ];


Kontroler
---------

 - makeUrl - służy go generowania pełnego adresu. Przykład |makeurl| - Metoda używana do przekierowań odpowiednik |header| z tym ze parametr jest kluczem z tablicy Config/router.php. W przypadku używania docs/:docsId wygląda to następująco |redirect| 

**Controller/Page.php**

.. code-block:: php

 <?php
 namespace Controller;
 
 use Dframe\Controller;
 use Dframe\Router\Response;
 
 class PageController extends Controller
 {
     public function index()
     {
         echo $this->router->makeUrl('docs/:docsId?docsId=23');
         return;
     }
 
     public function docs()
     {
 
         if (!isset($_GET['docsId'])) {
             return $this->router->redirect('error/:code?code=404');
         }
     }
 
     public function error($status = '404') 
     {
         $routerCodes = $this->router->response();
 
         if (!array_key_exists($status, $routerCodes::$code)) {
             return $this->router->redirect('error/:code?code=500');
         }
 
         $view = $this->loadView('index');
         $smartyConfig = Config::load('view/smarty');
 
         $patchController = $smartyConfig->get('setTemplateDir', APP_DIR.'View/templates').'/errors/'.htmlspecialchars($status).$smartyConfig->get('fileExtension', '.html.php');
 
         if (!file_exists($patchController)) {
             return $this->router->redirect('error/:code?code=404');
         }
 
         $view->assign('error', $routerCodes::$code[$status]);
         return Response::create($view->fetch('errors/' . htmlspecialchars($status)))->headers(['refresh' => '4;' . $this->router->makeUrl(':task/:action?task=page&action=index')]);
     }
     
 }
     
     
.. |router| cCode:: 
 <?php $this->router; ?>
.. |page/index| cCode:: 
 <?php $this->router->makeUrl(':task/:action?task=page&action=index'); ?>
.. |$router| cCode:: {$router}
.. |$makeurl| cCode:: {$router->makeUrl(':task/:action?task=index&action=page&page=1')}


Widok
-----

assign - jest metodą silnika templatki która przypisuje wartość do zmiennej którą wykorzystujemy w plikach templatki

**View/templates/index.html.php**

.. customLi:: myTabs
 :php: active/php
 :smarty: smarty

  .. code-block:: php

   <?php include "header.html.php" ?>
   Example site created using the Dframe Framework

   Routing:
   <?php $this->router->makeurl(':task/:action?task=index&action=page'); ?> <!-- http://example.com/index/page -->
   <?php $this->router->makeurl('error/:code?code=404'); ?> <!-- http://example.com/page/404 -->
   <?php $this->router->publicWeb('css/style.css'); ?> <!-- http://example.com/css/style.css -->

   <?php $this->domain('https://example.com')->makeurl('error/:code?code=404'); ?> <! -- http://examplephp.com/page/404 -->

   <?php include "footer.html.php" ?>
   Using only PHP

  - |router| all already available methods used like in |page/index|

  next

  .. code-block:: php

   {include file="header.html.php"}
   Example site created using the Dframe Framework

   Routing:
   {$router->makeurl(':task/:action?task=index&action=page')} <! -- http://example.com/index/page -->
   {$router->makeurl('error/:code?code=404')} <!-- http://example.com/page/404 -->
   {$router->publicWeb('css/style.css')}  <!-- http://example.com/css/style.css -->

   {$router->domain('https://examplephp.com')->makeurl('error/:code?code=404')}  <!-- http://examplephp.com/page/404 -->

   {include file="footer.html.php"}
   S.M.A.R.T.Y Engine used in the example

  - |$router| all already available methods are used like in |$makeurl|

**View/index.php**

.. code-block:: php

 namespace View;
 use Dframe\Asset\Assetic;
 
 
 class IndexView extends \View\View
 {
     public function init()
     {
         $this->router->assetic = new Assetic();
         $this->assign('router', $this->router);
 
         /* ... */

.. center::

 Dframe\Router\Response

Rozszerzenie podstawowego **Dframe\Router** jest **Dframe\Router\Response** dodaje on funkcjonalność ustawiania statusu odpowiedzi (404, 500 itp) oraz ich nagłówków. 

.. code-block:: php

 return Response::create('Hello Word!')
        ->status(200)
        ->headers([
            'Expires' => 'Mon, 26 Jul 1997 05:00:00 GMT', 
            'Cache-Control' => 'no-cache',
            'Pragma', 'no-cache'
        ]); 

Dla generowania html

.. code-block:: php

 return Response::render('Hello Word!');

Dla generowania html

Wyświetlanie w formacie JSON

.. code-block:: php

 return Response::renderJSON(['return' => '1']); 

Wyświetlanie w formacie JSONP 

.. code-block:: php

 return Response::renderJSONP(['return' => '1']); 

Przekierowanie

.. code-block:: php

 return Response::redirect(':task/:action?task=page&action=login');

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
.. |redirect| cCode:: $this->router->redirect(':task/:action?task=index&action=page');
