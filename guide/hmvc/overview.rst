HMVC z Domain Driven Design
===========

Praca z domyślną strukturą projektu udostępnioną przez Dframe jest absolutnie dobra, gdy pracujemy w małym lub średnim projekcie. Ale kiedy jest to wielka aplikacja z ponad 30 modelami, wtedy zaczynamy dusić się na własnej bazie kodu.

Utrzymanie dużej aplikacji nie jest prosta. Zwłaszcza, gdy nie jest odpowiednio zorganizowana.

Z pomocą przychodzi HMVC z dodatkiem Domain Driven Design

- **Aplikacja** (*app/*) - zwykle posiada, kontroler, middleware oraz router. 
- **Domena** (*modules/*) -  zawiera logikę biznesową (model, repozytorium, transformator, zasady itp.). 
- **Infrastruktura** (*app/bin, app/Config, app/Lib*) -  zwykle posiada wspólne usługi
- **Interfejs** (*app/View, app/Libs*) - zwykle zawiera widoki, język, zasoby.  
     
     
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
 
