.. title:: Instalacja oraz konfiguracja Dframe Framework

.. meta::
    :description: Instalacja oraz konfiguracja Dframe Framework - dframeframework.com
    :keywords: dframe, instalation, composer, github, download, chmod, dframeframework   

Instalacja
===========

Wymagania
----------

 - PHP >= 7.0
 - Rewrite Module (nginix/apache2)
 - Composer
 
 
Pobieranie
----------

Z poziomu konsoli bash wykonaj polecenie composera*

.. code-block:: bash

 $ composer create-project dframe/dframe-demo appName

albo

.. code-block:: bash

 php composer.phar create-project dframe/dframe-demo appName

**Composer** to narzędzie pozwalające na bardzo łatwe zarządzanie zależnościami między bibliotekami w projekcie. `(Możesz go pobrać z tutaj) <https://getcomposer.org/download/>`_ Jak kiedyś pobierało się paczki z skryptem ktory był nam potrzebny to composer robi to za nas i jednym poleceniem można je aktualizować.

Zainicjuje to pobieranie najnoszego kodu dostępnego na `Github.com <https://github.com/dframe/dframe-demo>`_.

Albo wejdz do repozytorium i pobierz kod w `Dframe-demo.zip <https://github.com/dframe/dframe-demo/releases>`_.
Możesz też ręcznie pobrać projekt w .zip z github'a i odpalić w konsoli bash poniższe polecenie

.. code-block:: bash

 $ composer install
 
Po tej operacji będziesz miał gotowy szkielet twojej aplikacji

Uprawnienia
----------

Nie wszystkie foldery istnieją na starcie, ale takowe mogą się znaleźć. Wszystkie pliki i foldery z wyjątkiem podanych poniżej powinny posiadać |chmod755| na foldery i |chmod664| na pliki. Nie dotyczy się to podanych poniżej które powinny posiadać updawnienia grupy |www-data|

.. code-block:: bash

 chmod 777 -R app/View/cache
 chmod 777 -R app/View/templates_c
 chmod 777 -R app/View/uploads
 

**Jak zmienić uprawnienia do folderu i wszystkich jego podfolderów i plików w systemie Linux?**

Aby zmienić wszystkie katalogi na 755 (drwxr-xr-x):

.. code-block:: bash

 $ find /opt/lampp/htdocs -type d -exec chmod 755 {} \;

Aby zmienić wszystkie pliki na 644 (-rw-r--r--):
 
.. code-block:: bash

 $ find /opt/lampp/htdocs -type f -exec chmod 644 {} \;

Serwer HTTP
----------
Po instalacji należy skonfigurować serwer aplikacji tak by wskazywał na katalog /web. Upewnij się, że załadowałeś mod_rewrite

.. customLi:: myTab
 :apache2: Apache (.htaccess)
 :nginx: active/Nginx (.conf)
 
  .. code-block:: apache
  
   RewriteEngine On
   
   #Deny access for hidden folders and files
   RewriteRule (^|/)\.([^/]+)(/|$) - [L,F]
   RewriteRule (^|/)([^/]+)~(/|$) - [L,F]
   
   #Set root folder to web directory
   RewriteCond %{REQUEST_FILENAME} !-d
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^(.*)$ web/$1
   
   #Redirect all queries to index file
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^(.*)$ web/index.php [QSA,L]
  next
  
  .. code-block:: nginx
  
   #Set root folder to web directory
   location / {
       root   /home/[project_path]/htdocs/web;
       index  index.html index.php index.htm;
       if (!-e $request_filename) {
           rewrite ^/(.*)$ /index.php?q=$1 last;
       }
   }
   
   #Redirect all queries to index file
   location ~ .php$ {
       try_files $uri = 404;
       fastcgi_pass 127.0.0.1:9000;
       #fastcgi_pass unix:/run/php/php7.1-fpm.sock;
       fastcgi_index web/index.php;
       fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
       include fastcgi_params;
   }


Konfiguracja
----------

Po instalacji w pliku **web/config.php** znajdziemy zmienne stałe które sa widoczne w całym projekcie. Należy je ustawić po instalacji.

.. code-block:: nginx

 // Application name
 define('APP_NAME', "App name");  
 
 // Check PSR-2: Coding Style
 define('CODING_STYLE', true);    
 
 // Website configuration
 define('VERSION', "Dframe");     // Version aplication
 define('SALT', "YOURSALT123");   // SALT default: YOURSALT123
 
 // Website adress
 define('HTTP_HOST', 'website.url');



.. |chmod755| cCode:: chmod 755
.. |chmod664| cCode:: chmod 664
.. |www-data| cCode:: www-data
