.. title:: Cron - dframeframework.com

.. meta::
    :description: cron - dframeframework.com
    :keywords: dframe, cron, smarty, cron engine, crobtab, dframeframework
    

Cron
---------

Cron jest usługą do cyklicznego wykonywania zadań. Pozwala ona w określonym czasie wykonać polecenie. Takim przykładem może być wysyłanie maili. 


.. code-block:: php

 <?php
 
 use Dframe\Router\Response;
 
 set_time_limit(0);
 ini_set('max_execution_time', 0);
 date_default_timezone_set('Europe/Warsaw');
 
 require_once dirname(__DIR__) . '/../../../vendor/autoload.php';
 require_once dirname(__DIR__) . '/../../../web/config.php';
 
 /**
  * Aonimowa Klasa Crona która sama siebie się wywołuje
  */
 return (new class() extends \Dframe\Cron\Task
 {
 
     /**
      * Init function
      *
      * @return array
      */
     public function init()
     {
         $cron = $this->inLock('mail', [$this->loadModel('Mail/Mail', 'Mail'), 'sendMails'], []);
         if ($cron['return'] == true) {
             $mail = $cron['response'];
             return Response::renderJSON(['code' => 200, 'message' => 'Cron Complete', 'data' => ['mail' => ['data' => $mail['response']]]]);
         }
 
         return Response::renderJSON(['code' => 403, 'message' => 'Cron in Lock'])->status(403);
 
     }
 
 })->init()->display(); 



Metody
---------

lockTime(string $key, $ttl = 3600)
^^^^^^^^^^^^^^

Blokada czasowa 

.. code-block:: php
    if($this->lockTime('mail'){
        return Response::renderJSON(['code' => 403, 'message' => 'Time Lock'])->status(403);
    }


inLock(string $key, object $loadModel, string $method, $args = [], $ttl = 3600)
^^^^^^^^^^^^^^

Metoda ta ma w sobie wbudowaną metodę która blokuje go do czasu pełnego zakończenia.

.. code-block:: php
    $this->inLock('mail', [$this->loadModel('Mail/Mail', 'Mail'), 'sendMails'], []);

