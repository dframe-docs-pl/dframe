.. title:: Session - Przechowuj informacje o użytkowniku

.. meta::
    :description: To prosty element frameworka który pozwala na szybke operowanie sesjami w php.
    :keywords: dframe, Session, fingerprint, store, information, dframeframework   

====
Opis
====

To prosty element frameworka który pozwala na szybke operowanie sesjami w php. Jedolinijkowe definiowanie zmiennej globalnej. 

Zamiast sprawdzać czy istnieje w jednej linijce otrzymujemy te 2 rzeczy.

|get|

W tym przypadku pobieramy |sesok| i jeśli nie istnieje odrazu definiujemy to drugim parametrem |false|. W miejsce |false| można wstawić dowolną wartość. 

Przypisanie do |baseclass| znajduje się w pliku app/bootstrap.php w postaci |name| . Parametr przekazywany do obiektu ustawia nazwę sesji.

=======
Stalone
=======

Prostowa i minimalizm sprawa ze chce się używać samej klasy. W tym celu w projekcie dostępne są następujące metody. 

=======
Standalone
=======

Simplicity and minimalism make you want to use the same class. In order to achieve that, the following methods are available in the project.

.. code-block:: php
  
 $this->session = new \Dframe\Session('name');
 $session = new Session('HashSaltRandomForSession');
 $session->register(); // Set session_id and session_time - default 60
 $session->authLogin(); // Return true/false if session is registrer
 $session->set($key, $value); // set $_SESSION[$key] = $value;
 $session->get($key, $or = null); // get $_SESSION[$key];
 $session->remove($key) // unset($_SESSION[$key]);
 $session->end(); // session_destroy
 
.. |get| cCode:: $this->baseClass->session->get('ok', false); 
.. |sesok| cCode:: $_SESSION['ok']
.. |baseclass| cCode:: $this->baseClass->session
.. |false| cCode:: false
.. |name| cCode:: $this->session = new \Dframe\Session('name');

.. |newSession| cCode:: $session = new Session('HashSaltRandomForSession'); 
.. |register| cCode:: $session->register(); // Set session_id and session_time - default 60 
.. |authLogin| cCode:: $session->authLogin(); // Return true/false if session is registrer 
.. |set| cCode:: $session->set($key, $value); // set $_SESSION[$key] = $value; 
.. |sGet| cCode:: $session->get($key, $or = null); // get $_SESSION[$key]; 
.. |remove| cCode:: $session->remove($key) // unset($_SESSION[$key]);
.. |end| cCode:: $session->end(); // session_destroy
