.. meta::
   :description: Prosta strona autoryzacji - Dframe Framework
   :keywords: guide, tutorial, login, register, authentication, dframe framework, controller, php, php 7,

Prosta autoryzacja
===========

 
Zacznijmy od stworzenia encji w której będziemy przechowywać dane o użytkowniku. Ważnymi w tej klasie metodami pomocniczymi są
**isLogged()** który w przypadku wywołania z parametrem w konstuktorze ustawa nam wartość logiczną true/false. Stworzymy go jako moduł
by w późniejszym czasie można było go rozbudować. 

**modules/Users/src/Entity/UserEntity.php**

.. code-block:: php

 <?php

 /**
  * Project Name
  * Copyright (c) Firstname Lastname
  *
  * @license http://yourLicenceUrl/ (Licence Name)
  */
   
 namespace Modules\Users\Entity\Users;
 
 /**
  * Class User
  *
  * @package Modules\Users\Entity\Users
  */
 class UserEntity
 {

     /**
      * @var int
      */
     protected $id;

     /**
      * @var string
      */
     protected $username;

     /**
      * @var string
      */
     protected $email;

     /**
      * @var string
      */
     protected $lastActive;

     /**
      * @var bool
      */
     private $isLogged = false;


     /**
      * User constructor.
      *
      * @param null $id
      */
     public function __construct($id = null)
     {
         if (isset($id) and !empty($id)) {
             $this->isLogged = true;
             $this->setId($id);
         }
     }

     /**
      * @return bool
      */
     public function isLogged()
     {
         return $this->isLogged;
     }

     /**
      * @param $id
      */
     public function setId($id)
     {
         $this->id = $id;
     }

     /**
      * @return mixed
      */
     public function getId()
     {
         return $this->id;
     }

     /**
      * @return mixed
      */
     public function getUsername()
     {
         return $this->username;
     }

     /**
      * @return mixed
      */
     public function getEmail()
     {
         return $this->email;
     }

     /**
      * @param $username
      */
     public function setUsername($username)
     {
         $this->username = $username;
     }

     /**
      * @param $email
      */
     public function setEmail($email)
     {
         $this->email = $email;
     }

     /**
      * @return array
      */
     public function getData()
     {
         return [
             'id' => $this->id,
             'email' => $this->email
         ];
     }
 }

Gdy stworzymy Encje w folderze **modules/users/src/** tworzymy plik **Modules.php**

**modules/users/src/Modules.php**

.. code-block:: php

 <?php
 
 namespace Users;
 
 use Dframe\Modules\ManagerModule;
 
 class Module extends ManagerModule
 {
 
     public function boot()
     {
     }
 
 
     public function register()
     {
     }
 
 }

Do Composer.json należy dodać namespace **\\Modules\\Users\\**

.. code-block:: json

    "autoload": {
        "psr-4": {
            "": "app/",
            "Modules\\Users\\": "modules/users/src",
        }
    }
    
Oraz do **app/Bootstrap.php** należy dodać

.. code-block:: php

 $this->providers['modules'] = [
     \Modules\Users\Module::class
 ];
 
    
Następnie musimy stworzyć config w którym ustawimy dane do logowania. Ten już stworzymy w głównym folderze aplikacji.

**app/Config/users.php**

.. code-block:: php

 <?php 
 
 return [
     'users' => [
         [
             'id' => 1,
             'username' => '__Login__',
             'password' => '__Password__For_Example_MD5__'
         ]
     ]
 ];

Na końcu stworzmy nasz kontroler do logowania 

**app/Controller/User.php**

.. code-block:: php

 <?php
 
 namespace Controller;
 
 use Dframe\Config;
 
 class UserController extends \Controller\Controller
 {
     /**
      * @return mixed
      */
     public function login()
     {
 
         if (isset($_POST) and !empty($_POST)) {
 
             if (!$this->baseClass->token->isValid('loginToken', (isset($_POST['token']) ? $_POST['token'] : null),
                 true)) {
                 return $this->baseClass->msg->add('e', 'Formularz wygasł.', 'page/login');
             }
 
             $UsersConfig = Config::load('users');
 
             foreach ($UsersConfig->get('users') as $key => $value) {
                 if ($value['username'] == $_POST['username'] AND $value['password'] == md5($_POST['password'])) {
                     $this->baseClass->session->register();
                     $this->baseClass->session->set('id', $value['id']);
                     $this->baseClass->session->set('username', $value['username']);
                     return $this->router->redirect('page/index');
                 }
             }
 
             return $this->baseClass->msg->add('e', 'Not Found', 'page/login');
 
         }
 
         return $this->router->redirect('page/login');
     }
 
     /**
      * @return mixed
      */
     public function logout()
     {
         $this->baseClass->session->end();
         //usuwanie cookie z last page
         unset($_COOKIE['currentPage']);
         setcookie("currentPage", "", time() - 3600, "/");
         return $this->router->redirect('page/login');
     }
 
 }
 
 
Gdy już stworzymy kontroler który nam ustawia dane do sesji użymy 


**app/Controller/Page.php**

.. code-block:: php

 <?php

  /**
  * Project Name
  * Copyright (c) Firstname Lastname
  *
  * @license http://yourLicenceUrl/ (Licence Name)
  */
  
  namespace Controller;
 
 class PageController extends \Controller\Controller
 {
     /**
      * @return mixed
      */
     public function login()
     {
 
         $User = new \Modules\Users\Entity\User($this->baseClass->session->get('id', 0));
         if ($User->isLogged() !== true) {  // Sprawdzany czy użytkownik jest zalogowany
             $View = $this->loadView('Index');
 
             $loginToken = $this->baseClass->token->get('loginToken'); // Generowanie tokena Zabezpieczającego kontroler logowania
             $View->assign('loginToken', $loginToken);
             return $View->render('page/login');
         }
         
         
 
         return $this->router->redirect('');
     }
 
 }

Teraz czas na abstrakcyjną klasę która będzie naszym middleware'm i będzie sprawdzała czy osoba jest zalogowana. 

**app/Controller/AbstractAuthController.php**

.. code-block:: php

 <?php
 
 /**
  * Project Name
  * Copyright (c) Firstname Lastname
  *
  * @license http://yourLicenceUrl/ (Licence Name)
  */
   
 namespace Controller;
 
 abstract class AbstractAuthController extends \Controller\Controller
 {
     
     /**
      * @var \Modules\Users\Entity\User
      */
     protected $User;
 
     /**
      * @return mixed
      */
     public function init()
     {
         $currentPage = (isset($_SERVER['HTTPS']) ? "https" : "http") . "://$_SERVER[HTTP_HOST]$_SERVER[REQUEST_URI]";
         @setcookie("currentPage", $currentPage, 0, '/');
         
         /** 
          * Sprawdzamy czy istenije sesja
          */
         if ($this->baseClass->session->authLogin() != true) {
             return $this->router->redirect('page/login');
         }
         
         $this->User = new \Modules\Users\Entity\User($this->baseClass->session->get('id', 0));
         $this->User->setUsername($this->baseClass->session->get('username'));
     }
 
 
 }
 
Teraz przykłądowa klasa która będzie objęta naszą abstrakcyjną klasą

**app/Controller/Panel/Services.php**

.. code-block:: php
 
 <?php
 
 /**
  * Project Name
  * Copyright (c) Firstname Lastname
  *
  * @license http://yourLicenceUrl/ (Licence Name)
  */
 
 namespace Services\Controller\Panel;
 
 use Dframe\Router\Response;
 
 /**
  * Here is a description of what this file is for.
  *
  */
 class ServicesController extends \Controller\AbstractAuthController
 {
 
     /**
      * Route services
      * Notify
      *
      * @return void
      */
     public function index()
     {
         $View = $this->loadView('Index');
         $View->assign('urls', [
             'services' => $this->router->makeUrl('api/panel/services'),
         ]);
 
         return Response::Create($View->fetch('services/index'))->status(200);
     }
 
 
