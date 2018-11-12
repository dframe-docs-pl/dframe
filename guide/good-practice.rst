.. meta::
   :description: Dobre Praktyki - Dframe Framework
   :keywords: guide, tutorial, Dobre Praktyki, Good Practice, dframe framework, controller, model, php, php 7

Dobre Praktyki
===========

Dobre praktyki pozwolą utrzymać kod w harmonii. Staraj się, by kod pisany przez ciebie był czytelny i zrozumiały dla innych. Kiedyś może się zdarzyć sytuacja, że będziesz potrzebować pomocy innej osoby, a jeśli twój kod będzie nieczytelny przysporzy problemów w analizie i wydłuży czas pracy innej osobie.

 - Przed załadowaniem widoku załaduj najpierw model.
 - Do nazwania zmiennej w której znajduje się obiekt używaj $StudlyCaps
 - Do nazwy zmiennej która ma swój rodzaj, postaraj się dodać typ na końcu jej typ (np $NazwaModel, $NazwaEntity, $NazwaConfig) 
 - W przypadku zwykłych widoku staraj używać się zmiennej $View
 

.. code-block:: php

 public function myMethod()
 {
     $FirstModel = $this->loadModel('First');
     $SecondModel = $this->loadModel('Second');
     $View = $this->loadView('Index');
     
     /* ... */
 }

Ładuj widoki i modele dopiero tam gdzie będziesz je używać. W przykładzie z autoryzacją np:

.. code-block:: php

 public function myProcetedMethod()
 {
     if ($this->baseClass->session->authLogin() != true) {
         return $this->baseClass->msg->add('s', 'Unauthorized.', 'page/index');
     }
 
     $FirstModel = $this->loadModel('First');
     $SecondModel = $this->loadModel('Second');
     $View = $this->loadView('Index');
     
     /* ... */
 }


W przypadku JSON

.. code-block:: php

 public function myProcetedAndPostMethod()
 {
     if ($this->baseClass->session->authLogin() != true) {
         return Response::renderJSON(['code' => 401, 'message' => 'Unauthorized'])->status(401);
     }

     $method = $_SERVER['REQUEST_METHOD'];
     switch ($method) {
     
        case 'GET':
           /* ... */
           break;
           
        case 'POST'
        
           if (!isset($_POST['someValue']) AND !empty($_POST['someValue'])) {
              return Response::renderJSON(['code' => 400, 'message' => 'Bad Request', 'errors' => ['Invalid Values']]))->status(400);
           }

           $FirstModel = $this->loadModel('First');
           $SecondModel = $this->loadModel('Second');
           
           /* ... */
           
           return Response::renderJSON(['code' => 200, 'message' => '', 'data' => []]);
           break;
           
     }
     
     return Response::renderJSON(['code' => 405, 'message' => 'Method Not Allowed'])->status(405);
     
     //...
 }
