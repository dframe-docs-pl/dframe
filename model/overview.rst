.. title:: Model - Stwórz metody które mają zapytania do bazy

.. meta::
    :description:  W modelu tworzysz poszczególne metody które mają zapytania do bazy danych oraz odpowiadają za obróbkę danych.
    :keywords: dframe, model, mysql, database, dframeframework  
    
====
Opis
====

W modelu tworzysz poszczególne metody które mają zapytania do bazy danych oraz odpowiadają za obróbkę danych. Należy pamietać ze przed rozpoczęciem prac należy wgrać paczkę dframe/database dostępną na `|github| GITHUB <https://github.com/dusta/database>`_ bądź za pośrednictwm composera.

.. code-block:: php

 namespace Model;

 class RequestModel extends \Model\Model
 {

     /**
      * @param $requestId
      *
      * @return array
      */
     public function getRequestSettings($requestId)
     {
         $row = $this->db->pdoQuery('SELECT * FROM `request_type` WHERE request_type_id = ?', [$requestId])->result();
         return $this->methodResult(true, $row);
     }


Warto zwrócić uwagę ze praktycznie wszystkie metody, po za kilkoma wyjątkami, zwracają dane w postaci tablicy gotowej do odczytu i zwracane są przez metodę.

.. code-block:: php

 /**
  * @param boolean
  * @param array
  */
 $this->methodResult(true, $row);


.. |github| fa-icon:: fa-github
