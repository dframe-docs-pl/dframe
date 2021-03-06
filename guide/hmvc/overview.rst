.. title:: HMVC z Domain Driven Design

.. meta::
   :description: HMVC z ddd - Dframe Framework
   :keywords: guide, tutorial, hmvc, ddd,  Domain Driven Design, dframe framework, controller, php
   
HMVC z Domain Driven Design
===========

Praca z domyślną strukturą projektu udostępnioną przez Dframe jest absolutnie dobra, gdy pracujemy w małym lub średnim projekcie. Ale kiedy jest to wielka aplikacja z ponad 30 modelami, wtedy zaczynamy dusić się na własnej bazie kodu.

Utrzymanie dużej aplikacji nie jest prosta. Zwłaszcza, gdy nie jest odpowiednio zorganizowana.

Z pomocą przychodzi HMVC z dodatkiem Domain Driven Design

- **Aplikacja** (*app/*) - zwykle posiada, kontroler, middleware oraz router. 
- **Domena** (*modules/*) -  zawiera logikę biznesową (modele, repozytorium, encje itp.). 
- **Infrastruktura** (*app/bin, app/Config, app/Lib*) -  zwykle posiada wspólne usługi oraz odpowiada za przechowywanie oraz dostęp do danych
- **Interfejs** (*app/View*) - zwykle zawiera widoki, język, zasoby.  
     
     
Pierwszym krokiem do rozpoczęcia jest dodanie do composer.json naszego namespace'a

.. code-block:: json

    "autoload": {
        "psr-4": {
            "": "app/",
            "MyProject\\SubModule\\": "modules/MyProject/SubModule/src",
        }
    }
    
    
     
Aby moduł zaczął działać należy dodać do pliku app/Bootstrap.php poniższy kod 

.. code-block:: php

 $this->providers['modules'] = [
     /** ... */
     \MyProject\SubModule\Module::class,
 ];

W głównym katalogu aplikacji towrzymy folder **Modules** w nim umieszczamy nasz moduł np

**MyProject/subModule/src** w folderze src umieszaczamy poniższy kod

.. code-block:: php

 <?php
 namespace MyProject\SubModule;

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


Dla rejestracji widoku:  

.. code-block:: php

 <?php
 namespace MyProject\SubModule\View;

 class Index extends \View\IndexView
 {
     public $dir = __DIR__.'/templates';
 }
 
Przykład ładowania modelu

.. code-block:: php

 /** Ładowanie modelu */
 $MyModelModel = $this->loadModel('MyModel', 'MyProject/SubModule');
 /** Ładowanie widoku */
 $View = $this->loadView('Index', 'MyProject/SubModule');
 

Struktura Dframe z użyciem DDD wygląda w następujący sposób

.. code-block:: bash

   |   composer.json
   |   composer.lock
   |   LICENSE
   |
   +---app
   |   |   Bootstrap.php
   |   |
   |   +---Config
   |   |   |   router.php
   |   |   |
   |   |   \---view
   |   |           smarty.php
   |   |
   |   +---Controller
   |   |   |    Controller.php
   |   |   |    Page.php
   |   |   |
   |   |   \---Users
   |   |           Users.php
   |   \---View
   |       |   Index.php
   |       |   View.php
   |       |
   |       +---templates
   |           |   footer.html.php
   |           |   header.html.php
   |           |   index.html.php
   |           |
   |           +---errors
   |           |       404.html.php
   |           |
   |           +---page
   |                 test.html.php
   +---Modules
   |   |
   |   +---Users
   |       |
   |       +---src
   |           |   Module.php
   |           |
   |           +---Command
   |           |      Users.php
   |           |
   |           +---Config
   |           |      users.php
   |           |
   |           +---Model
   |           |      Users.php
   |           |      Role.php
   |           |
   |           +---Entity
   |                  Users.php
   |                
   |---vendor
   \---web
       |   config.php
       |   index.php
       |
       +---assets
