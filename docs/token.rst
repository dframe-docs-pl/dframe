Tokeny
----------

Bibliteka do tworzenia tokenów zabepiecza cię przed CSRF (Cross-Site Request Forgery) czyli nieautoryzowanym wykonaniem akcji.

Inicjalizacja tokenu 

By móc zacząć używać tokenów do pliku Bootstrap.php należy dopisać do __construct

.. code-block:: php

 $this->session  = new \Dframe\Session(SESSION_NAME);
 $this->token  = new Dframe\Token($this->session);

Przyład użycia:

.. code-block:: php

 if (!$this->baseClass->token->isValid('evidenceToken', (isset($_POST['token']) ? $_POST['token'] : null))) {
     return Response::renderJSON(array('return' => '0', 'response' => 'Formularz wygasł.'));
 }
 
 $evidenceToken = $this->baseClass->token->generate('evidenceToken')->getToken('evidenceToken');
 
