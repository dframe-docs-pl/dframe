.. title:: Jak stworzyć strone?

.. meta::
   :description: Jak stworzyć strone - Dframe Framework
   :keywords: guide, tutorial, how to, jak utworzyć

Jak stworzyć strone?
===========

Utworzenie nowej strony wymaga dwóch rzeczy. Pierwsza rzecz to router a druga kontroler.
Jeśli chcesz pod adresem **/about** chcesz mieć stronę do pliku **app/config/router.php** należy dodać poniższy kod:

.. code-block:: php

    /* ... */
    'routes' => [

        'about' => [                     // Nazwana nazwa dla $this->router->makeUrl('about');
            'about',                     // Nasza ścieszka na pasku adresu
            'task=About&action=index',   // Argumenty dla Loader'a
        ],

    ]
    /* ... */


**task** odpowiada za lokalizacje pliku która domyślnie jest wyszukiwania w app/Controller
**action** odpowiada za wywołanie metody w pliku który podaliśmy w **task**

Następnie w folderze **app/Controller** tworzymy nasz plik *About.php* z naszą zawartością

.. code-block:: php

    <?php

    namespace Controller;

    use Dframe\Config;
    use Dframe\Router\Response;

    class AboutController extends \Controller\Controller
    {

        public function index()
        {
            echo '<html><body>Hello Word!</body></html>';
        }

    }



