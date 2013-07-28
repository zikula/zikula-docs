======================================
Admin Interface and Entering Data
======================================

:Author:
    Timothy Paustian
    
The admin controller responds to admin input and presents the admin interface for the module. This is where the code to add, edit and delete data should begin. It then marshalls the needed forms and functions of the module to get things done. For the case of the StrainID2 module, we are going to do two things, present an admin interface to choose strains to work on, or craete a new strain and display an editing interface which gets called when the admin chooses a strain to work on or wants to create a new strain. Here is the class, which extends from Zikula_AbstractController

::

    class StrainID2_Controller_Admin extends Zikula_AbstractController
    {
    
        /**
         * This method provides a generic item list overview.
         *
         * @return string|boolean Output.
         */
        public function main()
        {
            $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADMIN), LogUtil::getErrorMsgPermission());
            $repository = $this->entityManager->getRepository('StrainID2_Entity_StrainID2');
            $strain_table = StrainID2_Api_User::generate_strain_table($this->view, $repository, true);
            $this->view->assign('strain_table', $strain_table);
    
            return $this->view->assign('mode', 'edit')
                            ->fetch('admin/main.tpl');
        }
    
    
        /**
         * Create or edit record.
         *
         * @return string|boolean Output.
         */
        public function edit()
        {
            $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADD), LogUtil::getErrorMsgPermission());
            $form = FormUtil::newForm('StrainID2', $this);
            return $form->execute('admin/edit.tpl', new StrainID2_Form_Handler_Admin_Edit());
        }
    
        public function categoryList(){
        
        }
        /**
         * @desc set caching to false for all admin functions
         * @return      null
         */
        public function postInitialize()
        {
            $this->view->setCaching(false);
        }
    
    }
    
The main() function is the entry point for creating or editing strain data. First, we check to see if the caller has the right permissions to access this interface (ACCESS_ADMIN) if so, then we get the $repository and use this to call a function, generate_strain_table, that is in our StrainID2_Api_User. This data is then assigned to the view using a template variable and the page is rendered. 
