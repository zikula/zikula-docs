============
Installation
============

The code for installation is much simpler and easier to implement. You have to create your Entity class as clearly described in the Symfony_ documentation. Once you have those, the implementation of the installation file is easy.

Here is an example file to use.

::
    
    namespace Paustian\BookModule;


    use Zikula\Core\AbstractExtensionInstaller;
    
    class BookModuleInstaller extends AbstractExtensionInstaller {
        private $entities = array(
            'Paustian\BookModule\Entity\BookArticlesEntity',
            'Paustian\BookModule\Entity\BookChaptersEntity',
            'Paustian\BookModule\Entity\BookEntity',
            'Paustian\BookModule\Entity\BookFiguresEntity',
            'Paustian\BookModule\Entity\BookGlossEntity',
            'Paustian\BookModule\Entity\BookUserDataEntity',
            
        );
        
    public function install() {
            try {
                $this->schemaTool->create($this->entities);
            } catch (Exception $e) {
                return false;
            }
            $this->setVar('securebooks', false);
            return true;
        }


This function is called only once during the lifetime of your module to install the tables need by your application. The uninstall code is just as easy.

::
    
     public function uninstall() {
        try {
            $this->schemaTool->drop($this->entities);
        } catch (\PDOException $e) {
            return false;
        }

        // Delete any module variables.
        $this->delVars();

        // Deletion successful*/
        return true;
    }

.. _Symfony: https://symfony.com/doc/current/doctrine.html