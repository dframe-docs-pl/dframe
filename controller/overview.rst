.. meta::
   :description: Overview Controller - Dframe Framework
   :keywords: dframe framework, controller, php, php7, php5

Controller
---------
Kontroller jest wywoływany poprzez wcześniej ustawiony routing. Co prawda nie potrzeba ustawiać routingu gdyż strona może być ładowana poprzez 
ścieszkę katalogów np. 

* example.com/**{controller}**/**{metoda}** 
* example.com/**{controller}**/**{metoda}**/**{parametr}**/**{wartosc}**
* example.com/**{folder},{controller}**/**{metoda}**

Nie mniej jednak może to w większych aplikachach utrudnić poruszanie się i tworzenie linków. Można do tego użyć **annotacji** lub klasycznie 
pliku config/router.php

**Controller/Page.php**

.. customLi:: myTabs
 :annotation: annotation
 :php: active/php

  .. code-block:: php

    <?php

    namespace Controller;
    
    use Dframe\Controller;
    
    class PageController extends Controller
    {
    
        /**
         * @Route("/page/index", name="test/test")
         */
        public function index()
        {
            echo $this->router->makeUrl('docs/:docsId?docsId=23');
            return;
        }
    
        /**
         * Route("/docs/[docs]", name="docs/:docs")
         *
         * @return mixed
         */
        public function docs()
        {
    
            if (!isset($_GET['docsId'])) {
                return $this->router->redirect('error/:code?code=404');
            }
        }
    
        /**
         * @Route("/error/[code]", name="error/:code")
         *
         * @param string $status
         *
         * @return mixed
         */
        public function error($status = '404')
        {
            $routerCodes = $this->router->response();
    
            if (!array_key_exists($status, $routerCodes::$code)) {
                return $this->router->redirect('error/:code?code=500');
            }
    
            $view = $this->loadView('index');
            $smartyConfig = Config::load('view/smarty');
    
            $patchController = $smartyConfig->get('setTemplateDir', APP_DIR . 'View/templates') . '/ errors/' . htmlspecialchars($status) . $smartyConfig->get('fileExtension', '.html.php');
    
            if (!file_exists($patchController)) {
                return $this->router->redirect('error/:code?code=404');
            }
    
            $view->assign('error', $routerCodes::$code[$status]);
            $view->render('errors/' . htmlspecialchars($status));
        }
    }
    

  next


  **Config/router.php**

  .. code-block:: php

    /* ... */
    'page/index' => [
        'page/index', 
        'task=page&action=index'
    ],
    'docs/:docs' => [
        'docs/[docs]/', 
        'task=page&action=docs&docs=[docs]'
    ],
    'error/:code' => [
        'error/[code]/', 
        'task=page&action=error&code=[code]'
    ],
    /* ... */
         

  **Controller/page.php**

  .. code-block:: php

    <?php
    
    namespace Controller;
    
    use Dframe\Controller;
    
    class PageController extends Controller
    {
    
        /**
         * @return void
         */
        public function index()
        {
            echo $this->router->makeUrl('docs/:docsId?docsId=23');
            return;
        }
    
        /**
         * @return mixed
         */
        public function docs()
        {
    
            if (!isset($_GET['docsId'])) {
                return $this->router->redirect('error/:code?code=404');
            }
        }
    
        /**
         * @param string $status
         * @return mixed
         */
        public function error($status = '404')
        {
            $routerCodes = $this->router->response();
    
            if (!array_key_exists($status, $routerCodes::$code)) {
                return $this->router->redirect('error/:code?code=500');
            }
    
            $view = $this->loadView('index');
            $smartyConfig = Config::load('view/smarty');
    
            $patchController = $smartyConfig->get('setTemplateDir', APP_DIR . 'View/templates') . '/errors/' . htmlspecialchars($status) . $smartyConfig->get('fileExtension', '.html.php');
    
            if (!file_exists($patchController)) {
                return $this->router->redirect('error/:code?code=404');
            }
    
            $view->assign('error', $routerCodes::$code[$status]);
            $view->render('errors/' . htmlspecialchars($status));
        }
    }
    
    
