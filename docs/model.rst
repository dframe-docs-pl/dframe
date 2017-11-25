====
Opis
====

W modelu tworzysz poszczególne metody które mają zapytania do bazy danych oraz odpowiadają za obróbkę danych. Należy pamietać ze przed rozpoczęciem prac należy wgrać paczkę dframe/database dostępną na `|github| GITHUB <https://github.com/dusta/database>`_ bądź za pośrednictwm composera 

.. code-block:: php

 namespace Model;
    
    class RequestModel extends \Model\Model
    {
        /**
         * @parms int 
         * return array(boolean, array)
         */
    
        public functio1n getRequestSettings($requestId){
            $row = $this->baseClass->db->pdoQuery('SELECT * FROM `request_type` WHERE request_type_id = ?', array($requestId))->result();
            return $this->methodResult(true, $row);        
    }

Warto zwrócić uwagę ze praktycznie wszystkie metody, po za kilkoma wyjątkami, zwracają dane w postaci tablicy gotowej do odczytu i zwracane są przez metodę

.. code-block:: php

 /**
 * @parms boolean
 * @parms array
 */

 $this->methodResult(true, $row);

.. |github| fa-icon:: fa-github
