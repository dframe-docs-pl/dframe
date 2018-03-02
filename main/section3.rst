.. div:: row
 .. div:: col-md-6
  |img/restful.jpg|

 .. div:: col-sm-6
  Przyjazne linki
  ^^^^^^^^^^^^^^^

  W wersji produkcyjnej dla samego nawet bezpieczeństwa warto używać.
  |listing|

.. |img/restful.jpg| router:: img/restful.jpg
 :publicWeb:
 :img:
 :width: 100%


.. |listing| customLi:: myTab
 :apache2: Apache (.htaccess)
 :nginx: active/Nginx (.conf)
  .. code-block:: apache
   RewriteEngine On
   
   RewriteCond %{REQUEST_FILENAME} !-d
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^(.*)$ web/$1
   
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^(.*)$ web/index.php [QSA,L]
  next
  .. code-block:: nginx
   location / {
       root   /home/widget/htdocs/web;
       index  index.html index.php index.htm;
       if (!-e $request_filename) {
           rewrite ^/(.*)$ /index.php?q=$1 last;
       }
   }
   location ~ .php$ {
       try_files $uri = 404;
       fastcgi_pass 127.0.0.1:9000;
       fastcgi_index web/index.php;
       fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
       include fastcgi_params;
   }




