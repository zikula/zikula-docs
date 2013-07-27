.. _Doctrine: http://www.doctrine-project.org

==========================================
Creating the Data Model and Initialization
==========================================

:Author:
    Timothy Paustian
    
Right now all our module does is say, "Hello!." That's great, but we really want it to store some data. To be able to work with data, we need to program our data model into the code. With the use of Doctrine 2, the new standard in Zikula, this is very easy to do. Doctrine is an object relational mapper (ORM). An ORM will map objects that define our data model to tables in whatever database we choose to store our data. In most cases, Zikula sites used MySQL to store data, but theorhetically other databases or even non-databases could be used for persistence. By setting up these *Entity* classes, the module programmer never needs to think about SQL code and leave all of that to Doctrine. Doctrine_ has many features that are beyond the scope of this introduction. Here I will describe basic usage of these classes to save, get and search data.


Defining the Data Model
=======================

To define the data model, an Entity Access class is created that inherits from Zikula_EntityAccess and a Entity Repository class is created from EntityRepository. In the Entity Access class you defined your data model and implement getters and setters that allow you access to the data. The repository class is for methods for accessing your data. Doctrine separates the definition of your data model from searching the data. This separation prevents your data model being polluted with code that is used to access it. So, the EntityAccess class defines the data model and the EntityReporitory class contains methods for finding stuff in your EntityAccess class. 

Here is the code for defining the Entity Access class, which we will call StrainID2_Entity_StrainID2. We begin at the top of the file
::

    <?php
    
    /**
     * StrainID2
     *
     * @license GNU/LGPLv3 (or at your option, any later version).
     */
    use Doctrine\ORM\Mapping as ORM;
    
    /**
     * StrainID2 entity class
     *
     * Annotations define the entity mappings to database.
     *
     * @ORM\Entity(repositoryClass="StrainID2_Entity_Repository_StrainID2Repository")
     * @ORM\Table(name="strainid2_strainid2")
     */

We need the definitions from the ORM file, so we declare that we will use the Mapping. Doctrine uses Docblock Annotations to allow the definition of meta data right in our data model. We then identify the Repository class that will be able to access this class using a docblock annotation. Finally, we indicate the name of the table to be create upon initialization, straindid2_strainid2. Here is the first part of the class definition.

::

    class StrainID2_Entity_StrainID2 extends Zikula_EntityAccess
    {
        
    
        /**
         * sid field (record sid)
         *
         * @ORM\Id
         * @ORM\Column(type="integer")
         * @ORM\GeneratedValue(strategy="AUTO")
         */
        private $sid;
    
        /**
         * item name
         * 
         * @ORM\Column(length=255) 
         */
        private $name = ''; //type defaults to string
    
        /**
         * item indole
         * 
         * @ORM\Column(length=1)
         */
        private $indole = '+'; //type defaults to string

Here we being our definition of the Entity class that extends the Zikula_EntityAccess class. 
The first column of our data model has a type of integer and we use the mapping class (ORM) to define what it is in the docblock annotation, an Id, of type integer, and we want it to be a generated value that is incretmented automatically. The name item is then defined. We do not need to specify the type, since the default is string. We do need to specify the length, and 255 characters is plenty for a microbial species name. The next data column is the indole reaction. This is also a character, but can only be 4 values (+, -, u or v) so we only need 1 character to store it. The rest of the columns in our table are similar to indole.

For each item in our EntityAccess class, we have to define a getter and a setter. To work with Doctrine, these have to be written to match the name that they were given. Doctrine assumes you will use get, capitalize the first letter in the variable name, and all the rest of the name will be lowercase. For example

variable  = name
getter = getName
setter = setName

variable = sid
getter = getSid
setter = setSid

By using this pattern, it allows Doctrine to access all of your data and you don't have to write any code to make this happen. Below is the complete code of our class.

::

    <?php
    
    /**
     * StrainID2
     *
     * @license GNU/LGPLv3 (or at your option, any later version).
     */
    use Doctrine\ORM\Mapping as ORM;
    
    /**
     * StrainID2 entity class
     *
     * Annotations define the entity mappings to database.
     *
     * @ORM\Entity(repositoryClass="StrainID2_Entity_Repository_StrainID2Repository")
     * @ORM\Table(name="strainid2_strainid2")
     */
    class StrainID2_Entity_StrainID2 extends Zikula_EntityAccess
    {
        
    
        /**
         * sid field (record sid)
         *
         * @ORM\Id
         * @ORM\Column(type="integer")
         * @ORM\GeneratedValue(strategy="AUTO")
         */
        private $sid;
    
        /**
         * item name
         * 
         * @ORM\Column(length=255)
         */
        private $name = '';
    
        /**
         * item indole
         * 
         * @ORM\Column(length=1)
         */
        private $indole = '+';
        
        /**
         * item methyl_red
         * 
         * @ORM\Column(length=1)
         */
        private $methylred = '+';
        
        /**
         * item vogues_proskauer
         * 
         * @ORM\Column(length=1)
         */
        private $voguesproskauer = '+';
        
        /**
         * item simmons_citrate
         * 
         * @ORM\Column(length=1)
         */
        private $simmonscitrate = '+';
        
        /**
         * item h2s
         * 
         * @ORM\Column(length=1)
         */
        private $h2s = '+';
        
        /**
         * item phenylalanine
         * 
         * @ORM\Column(length=1)
         */
        private $phenylalanine = '+';
        
        /**
         * item lysine
         * 
         * @ORM\Column(length=1)
         */
        private $lysine = '+';
        
        /**
         * item ornithine
         * 
         * @ORM\Column(length=1)
         */
        private $ornithine = '+';
    
        /**
         * item motility
         * 
         * @ORM\Column(length=1)
         */
        private $motility = '+';
        
        /**
         * item lactose
         * 
         * @ORM\Column(length=1)
         */
        private $lactose = '+';
        /**
         * Constructor 
         */
        public function __construct()
        {
        }
    
        public function getSid()
        {
            return $this->sid;
        }
    
        public function setSid($sid)
        {
            $this->sid = $sid;
        }
    
        public function getName()
        {
            return $this->name;
        }
    
        public function setName($name)
        {
            $this->name = $name;
        }
        
        public function getIndole()
        {
            return $this->indole;
        }
    
        public function setIndole($indole)
        {
            $this->indole = $indole;
        }
        public function getMethylred()
        {
            return $this->methylred;
        }
    
        public function setMethylred($methylred)
        {
            $this->methylred = $methylred;
        }
    
        public function getVoguesproskauer()
        {
            return $this->voguesproskauer;
        }
    
        public function setVoguesproskauer($voguesproskauer)
        {
            $this->voguesproskauer = $voguesproskauer;
        }
    
        public function getSimmonscitrate()
        {
            return $this->simmonscitrate;
        }
    
        public function setSimmonscitrate($simmonscitrate)
        {
            $this->simmonscitrate = $simmonscitrate;
        }
    
        public function getH2s()
        {
            return $this->h2s;
        }
    
        public function setH2s($h2s)
        {
            $this->h2s = $h2s;
        }
    
        public function getPhenylalanine()
        {
            return $this->phenylalanine;
        }
    
        public function setPhenylalanine($phenylalanine)
        {
            $this->phenylalanine = $phenylalanine;
        }
    
        public function getLysine()
        {
            return $this->lysine;
        }
    
        public function setLysine($lysine)
        {
            $this->lysine = $lysine;
        }
    
        public function getOrnithine()
        {
            return $this->ornithine;
        }
    
        public function setOrnithine($ornithine)
        {
            $this->ornithine = $ornithine;
        }
    
        public function getMotility()
        {
            return $this->motility;
        }
    
        public function setMotility($motility)
        {
            $this->motility = $motility;
        }
    
        public function getLactose()
        {
            return $this->lactose;
        }
    
        public function setLactose($lastose)
        {
            $this->lactose = $lastose;
        }
    
    }


Initialization
==============

When a user clicks install/activate for a module, Zikula looks in your modules directory for the Installer.php file. It then calls the method install() of tne class YourModuleName_Installer. This class must inherit from Zikula_AbstractInstaller. In our case, our installer class is called StrainID2_Installer. The installer funciton calls DoctrineHelper to do the table installation into the database.

::

    public function install()
    {        
        // create the table
        try {
            DoctrineHelper::createSchema($this->entityManager, array('StrainID2_Entity_StrainID2'));
        } catch (Exception $e) {
            return false;
        }
        //creat the default data
        $this->createDefaultData();
        return true;
    }

DoctrineHelper::createSchema lists the entitiy manager to use (a variable created by the parent class Zikula_AbstractInstaller) and the name of your Entity class that we created above. All the rest is taken care of for you and your data table is created in the database. We also call a custom function, createDefaultData that adds some dummy data to our table. Here is the code for createDefaultData...

::
    protected function createDefaultData()
    {
        $strain1 = new StrainID2_Entity_StrainID2();
        $strain2 = new StrainID2_Entity_StrainID2();
        $strain3 = new StrainID2_Entity_StrainID2();
        $strain4 = new StrainID2_Entity_StrainID2();
        $strain5 = new StrainID2_Entity_StrainID2();
        
        $strain1->setName('Esherichia coli');
        $strain1->setIndole('+');
        $strain1->setMethylRed('+');
        $strain1->setVoguesProskauer('-');
        $strain1->setSimmonsCitrate('-');
        $strain1->setH2s('-');
        $strain1->setPhenylAlanine('-');
        $strain1->setLysine('+');
        $strain1->setOrnithine('v');
        $strain1->setMotility('+');
        $strain1->setLactose('+');
        
       ... the rest are initialized in a similar manner
        
        
        // execute the workflow action for each entity
        try {
            $this->entityManager->persist($strain1);
            $this->entityManager->persist($strain2);
            $this->entityManager->persist($strain3);
            $this->entityManager->persist($strain4);
            $this->entityManager->persist($strain5);
            $this->entityManager->flush();
        } catch(\Exception $e) {
            LogUtil::registerError($this->__('Sorry, but an unknown error occured during example data creation. Possibly not all data could be created properly!'));
        }
    }

We create a new StrainID2_Entity_StrainID2 class, set all the variables by sending messages to the setters and then simply tell the entityManager persist on each instance. When the flush call is made, all the data is saved to the database table.

The upgrade function in this class is a stub right now since this is the first version of this module. The uninstall function drops the table from the database using a call to DoctrineHelper::dropSchema.

::
    public function uninstall()
    {
        // drop tables
        DoctrineHelper::dropSchema($this->entityManager, array('StrainID2_Entity_StrainID2'));

        return true;
    }

With these four functions, your installer should now work. You have also learned how to save data to the database.