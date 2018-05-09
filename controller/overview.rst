Controller
---------


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
        * @Route("/docs/[docs]", name="docs/:docs")
        */
        public function docs()
        {
    
            if (!isset($_GET['docsId'])) {
                return $this->router->redirect('error/:code?code=404');
            }
        }

        /**
        * @Route("/error/[code]", name="error/:code")
        */ 
        public function error($status = '404')
        {
            $routerCodes = $this->router->response();
    
            if (!array_key_exists($status, $routerCodes::$code)) {
                return $this->router->redirect('error/:code?code=500');
            }
    
            $view = $this->loadView('index');
            $smartyConfig = Config::load('view/smarty');
    
            $patchController = $smartyConfig->get('setTemplateDir', APP_DIR.'View/templates').'/ errors/'.htmlspecialchars($status).$smartyConfig->get('fileExtension', '.html.php');
    
            if (!file_exists($patchController)) {
                return $this->router->redirect('error/:code?code=404');
            }
    
            $view->assign('error', $routerCodes::$code[$status]);
            $view->render('errors/'.htmlspecialchars($status));
        }
    }

  next


  **Config/router.php**

  .. code-block:: php

    /* ... */
    'page/index' => array(
        'page/index', 
        'task=page&action=index'
    ),
    'docs/:docs' => array(
        'docs/[docs]/', 
        'task=page&action=docs&docs=[docs]'
    ),
    'error/:code' => array(
        'error/[code]/', 
        'task=page&action=error&code=[code]'
    ),
    /* ... */
         

  **Controller/page.php**

  .. code-block:: php

    <?php
    namespace Controller;
    use Dframe\Controller;
    
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
    
            $patchController = $smartyConfig->get('setTemplateDir', APP_DIR.'View/templates').'/ errors/'.htmlspecialchars($status).$smartyConfig->get('fileExtension', '.html.php');
    
            if (!file_exists($patchController)) {
                return $this->router->redirect('error/:code?code=404');
            }
    
            $view->assign('error', $routerCodes::$code[$status]);
            $view->render('errors/'.htmlspecialchars($status));
        }
    }

