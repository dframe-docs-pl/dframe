.. title:: Token - Biblioteka do tworzenia tokenów CSRF

.. meta::
    :description: Bibliteka do tworzenia tokenów zabepiecza cię przed CSRF (Cross-Site Request Forgery) czyli nieautoryzowanym wykonaniem akcji.
    :keywords: dframe, Token, CSRF, tokens, Cross-Site Request Forgery, dframeframework  


Tokeny
----------

Bibliteka do tworzenia tokenów zabepiecza cię przed CSRF (Cross-Site Request Forgery) czyli nieautoryzowanym wykonaniem akcji.

Inicjalizacja tokenu 

By móc zacząć używać tokenów do pliku Bootstrap.php należy dopisać do __construct

.. code-block:: php

 $this->session  = new \Dframe\Session(APP_NAME);
 $this->token  = new \Dframe\Token($this->session);

Przyład użycia:

.. code-block:: php

 if (!$this->baseClass->token->isValid('evidenceToken', (isset($_POST['token']) ? $_POST['token'] : null))) {
     return Response::renderJSON(['code' => 403, 'message' => 'Formularz wygasł.'])->status(403);
 }
 
 $evidenceToken = $this->baseClass->token->generate('evidenceToken')->get('evidenceToken');

Smarty3 Plugin
===========

.. code-block:: php

 <?php
 /*
  * Smarty plugin for Dframe\Token
  * -------------------------------------------------------------
  * File:     function.token.php
  * Type:     function
  * Name:     token
  * Purpose:  outputs a token
  * -------------------------------------------------------------
  */
 
 /*
  * Instalation:
  * Put file in to app/Libs/Plugins/smarty/function.token.php
  * Usage: {token name='userToken'}
  */
 function smarty_function_token($name)
 {
     $token = new \Dframe\Token(new \Dframe\Session(APP_NAME));
     return $token->generate($name['name'])->get($name['name']);
 }
