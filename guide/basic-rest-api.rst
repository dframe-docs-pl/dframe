.. title:: Proste Rest Api - Dframeframework.com

.. meta::
    :description: Dodawanie użytkownika można zrobić na wiele sposóbów np encje. Jednak przedstawię tutaj najszybszy sposób z minimalną ilością plików.
    :keywords: dframe, router, routing, urls, url, restapi, htaccess, routes, php7, dframeframework, dodawanie użytkownika  


Dodawanie użytkownika można zrobić na wiele sposóbów np encje. Jednak przedstawię tutaj najszybszy sposób z minimalną ilością plików.

Jak mówi sama nazwa MVC zacznijmy od Modelu.
Stówrzmy sobie Model pod ścieszką **app/Model/Users.php**

W jego zawartości umieśćmy.

.. code-block:: php
 
 namespace Model;
 
 /**
  * Class UserModel
  *
  * @package Model
  */
 class UserModel extends Model
 {
     /**
      * Pobieranie listy użytkowników
      *
      * @return array
      */
     public function getUsers(){
         $results = $this->db->pdoQuery('SELECT * FROM `users`')->results();
         return $this->methodResult(true, ['data' => $results]);
     }
     
     /**
      * Pobranie użytkownika
      *
      * @return array
      */
     public function getUser($userId){
         $result = $this->db->pdoQuery('SELECT * FROM `users` WHERE `users`.`user_id` = ?', [$userId])->result();
         return $this->methodResult(true, ['user' => $result]);
     }
     
     /** 
      * Dodawanie użytkownika do bazy
      * @param array $user
      *
      * @return array
      */
     public function addUser($user)
     {
         $data = [
             'email' => $user['email']
         ];
 
         try {
             
             /**
              * Rozpoczęcie Transakcji
              */
             $this->db->start();
 
             $addedId = $this->db->insert('users', $data)->getLastInsertId();
             if (!isset($addedId)) {
                 throw new \Exception('Cannot add new user.');
             }
 
             /**
              * Commit Transakcji
              */
             $this->db->end();
 
         } catch (\Exception $e) {
 
             /**
              * Rollback Transakcji
              */
             $this->db->back();
             return $this->methodResult(false, ['response' => $e->messages()]);
         }
 
 
         return $this->methodResult(true, ['lastInsertId' => $addedId]);
     }
 
 }

Naszym widokiem w RESTAPI będzie json który jest wbudowany w klasę Dframe\Router\Request
Przejdzmy do stowrzenia Controllera pod ścieszką **app/Controller/Api/Users.php**

.. code-block:: php
 
 namespace Controller\Api;
 
 use Dframe\Router\Response;
 
 /**
  * Class UserController
  *
  * @package Controller\Api
  */
 class UserController extends \Dframe\Controller
 {
     /**
      * Routing do tego kontrollera ustawimy na api/users
      * @return Response
      */
     public function index()
     {
         switch ($_SERVER['REQUEST_METHOD']) {
             case 'GET':

                 $UsersModel = $this->loadModel('Users');
                 $users = $UsersModel->getUsers();
                 
                 $data = ['users' => []]
                 foreach($users as $key => $user) {
                     $data[] = [
                         'id' => $user['user_id'],
                         'email' => $user['user_email'],
                 }
                 
                 return Response::renderJSON(['code' => 200, 'data' => $data])->status(200);
                 break;
 
             case 'POST':
 
                 $errors = [];
                 if (!isset($_POST['email']) OR empty($_POST['email'])) {
                     $errors['email'] = 'Nie podano adres email.';
                 }
 
                 /**
                  * W tym miejscu możemy wstawć wszelkie walidacje.
                  */
 
                 if (!empty($errors)) {
                     return Response::renderJSON(['code' => 400, 'message' => 'Invalid params', 'errors' => $errors])->status(400);
                 }
 
                 $data = [
                     'email' => (isset($_POST['email'])) ? htmlspecialchars($_POST['email']) : '',
                 ];
 
                 $UsersModel = $this->loadModel('Users');
 
                 try {
 
                     $addUser = $UsersModel->addUser($data);
                     if (!isset($addUser) OR $addUser['return'] !== true) {
                         throw new \Exception('Error model...');
                     }
 
                     if (!is_numeric($addUser['lastInsertId']) OR $addUser <= 0) {
                         throw new \Exception('Invalid id.');
                     }
 
                 } catch (\Exception $e) {
                     return Response::renderJSON(['code' => 500, 'message' => 'Internal Error'])->status(500);
                 }
 
                 return Response::renderJSON(['code' => 200, 'response' => 'Account Created.', 'data' => ['user' => ['id' => $addUser['lastInsertId']]]])->status(200);
                 break;
 
         }
         return Response::renderJSON(['code' => 405, 'message' => 'Method not allowed.'])->status(405);
     }
 
 
     /**
      * Routing do tego kontrollera ustawimy na api/users/:userId
      *
      * @return Response
      */
     public function one()
     {
         switch ($_SERVER['REQUEST_METHOD']) {
 
             case 'GET':

                 $UsersModel = $this->loadModel('Users');
                 $user = $UsersModel->getUser($_GET['userId']);
                 if (is_null($user['data'])) {
                     return Response::renderJSON(['code' => 404, 'message' => 'User not found.']])->status(404);
                 }
                 
                 $data = [
                     'user' => [
                         'id' => $user['user_id']
                         'email' => $user['user_email']
                     ]
                 ];
 
                 return Response::renderJSON(['code' => 200, 'data' => $data])->status(200);
                 break;
 
 
         }
         
         return Response::renderJSON(['code' => 405, 'message' => 'Method not allowed.'])->status(405);
     }
 
 }
 
Teraz już do poprawnego działania potrzebujemy tylko odpowiedniego Routingu.
W pliku **app/Config/router.php**, do naszego routingu musimy dodać odpowiednie adresowanie. 
 
.. code-block:: php
 
     'routes' => [
 
         /** ... */
         
         'api/users' => [
             'api/users',
             'task=api,users&action=index',
         ],
 
         'api/users/:userId' => [
             'api/users/[userId]/',
             'task=api,users&action=index',
             'userId' => '([0-9]+)',
         ]
 
     ]
 
   
