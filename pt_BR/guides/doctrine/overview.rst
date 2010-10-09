.. index::
   single: Doctrine

Doctrine
========

O projeto `Doctrine`_  � o lar de um conjunto selecionado de bibliotecas PHP 
focadas principalmente em fornecer servi�os de persist�ncia e funcionalidades 
relacionadas. A integra��o entre o Symfony2 e o Doctrine2 implementa a maioria 
dos recursos que o projeto tem a oferecer para trabalhar com bancos de dados 
relacionais, tais como:

* Camada de Abstra��o do Banco de Dados
* Mapeador Objeto Relacional
* Migra��es de Banco de Dados

.. tip::
   Voc� pode aprender mais sobre a `API DBAL`_ e a `API ORM`_ no site oficial 
   do Doctrine2.

.. index::
   single: Doctrine; DBAL

Doctrine DBAL
-------------

A Camada de Abstra��o do Banco de Dados do Doctrine (DBAL) oferece 
uma API intuitiva e flex�vel para se comunicar com os bancos de dados 
relacionais mais populares que existem hoje. Para iniciar o uso do DBAL, 
vamos configur�-lo:

.. code-block:: yaml

    # config/config.yml

    doctrine.dbal:
        driver:   PDOMySql
        dbname:   Symfony2
        user:     root
        password: null

Acesse a conex�o a partir de seus controladores, atrav�s do servi�o 
``database_connection``::

    class UserController extends Controller
    {
        public function indexAction()
        {
            $conn = $this->container->getService('database_connection');

            $users = $conn->fetchAll('SELECT * FROM users');
        }
    }

Voc� pode ent�o executar uma consulta e buscar os resultados com o m�todo ``fetchAll()``, 
como demonstrado acima.

.. index::
   single: Doctrine; ORM

Mapeador Objeto Relacional do Doctrine
--------------------------------------

O Mapeador Objeto Relacional do Doctrine (ORM) � a biblioteca pr�mio, 
sob o guarda-chuva do Projeto Doctrine. Ele � constru�do sobre o Doctrine 
DBAL (Camada de Abstra��o do Banco de Dados) e oferece uma persist�ncia 
transparente de objetos PHP5 para um banco de dados relacional.

Antes de usar o ORM, voc� deve habilit�-lo na configura��o:

.. code-block:: yaml

    # config/config.yml

    doctrine.orm: ~

Em seguida, escreva suas classes de entidade. Uma entidade t�pica seria
como a seguinte::

    // Application/HelloBundle/Entities/User.php

    namespace Application\HelloBundle\Entities;

    /**
     * @Entity
     */
    class User
    {
        /**
         * @Id
         * @Column(type="integer")
         * @GeneratedValue(strategy="IDENTITY")
         */
        protected $id;

        /**
         * @Column(type="string", length="255")
         */
        protected $name;

        /**
         * Get id
         *
         * @return integer $id
         */
        public function getId()
        {
            return $this->id;
        }

        /**
         * Set name
         *
         * @param string $name
         */
        public function setName($name)
        {
            $this->name = $name;
        }

        /**
         * Get name
         *
         * @return string $name
         */
        public function getName()
        {
            return $this->name;
        }
    }

Agora, crie o esquema, executando o seguinte comando:

.. code-block:: bash

    $ php hello/console doctrine:schema:create

.. note::
   N�o se esque�a de criar o banco de dados se ele ainda n�o existir.

Eventualmente, use a sua entidade e gerencie seu estado de persist�ncia com o Doctrine::

    use Application\HelloBundle\Entities\User;

    class UserController extends Controller
    {
        public function createAction()
        {
            $user = new User();
            $user->setName('Jonathan H. Wage');

            $em = $this->container->getService('doctrine.orm.entity_manager');
            $em->persist($user);
            $em->flush();

            // ...
        }

        public function editAction($id)
        {
            $em = $this->container->getService('doctrine.orm.entity_manager');
            $user = $em->createQuery('select u from HelloBundle:User where id = ?', $id);
            $user->setBody('new body');
            $em->flush();

            // ...
        }

        public function deleteAction($id)
        {
            $em = $this->container->getService('doctrine.orm.entity_manager');
            $user = $em->createQuery('select e from HelloBundle:User where id = ?', $id);
            $em->remove($user);
            $em->flush();

            // ...
        }
    }

.. _Doctrine: http://www.doctrine-project.org/
.. _API DBAL: http://www.doctrine-project.org/projects/dbal/2.0/docs/en
.. _API ORM:  http://www.doctrine-project.org/projects/orm/2.0/docs/en
