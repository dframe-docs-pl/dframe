Pobieranie
----------

Z poziomu konsoli uruchom polecenie

.. code-block:: bash

 $ composer create-project dframe/dframe-demo appName

alternatywnie

.. code-block:: bash

 php composer.phar create-project dframe/dframe-demo appName

Zaincjalizuje to proces pobierania najnoszej wersji kodu która jest dostępna w serwisie github.com 

Albo wejdz do repozytorium i pobierz kod w `Dframe-demo.zip <https://github.com/dframe/dframe/releases>`_.

Uprawnienia
----------

Nie wszystkie foldery istnieją na starcie, ale takowe mogą się znaleźć. Wszystkie pliki i foldery z wyjątkiem podanych poniżej powinny posiadać |chmod755| na foldery i |chmod664| na pliki. Nie dotyczy się to podanych poniżej które powinny posiadać updawnienia grupy |www-data|

.. code-block:: bash

 chmod 777 -R app/View/cache
 chmod 777 -R app/View/templates_c
 chmod 777 -R app/View/uploads

.. |chmod755| cCode:: chmod 755
.. |chmod664| cCode:: chmod 664
.. |www-data| cCode:: www-data


