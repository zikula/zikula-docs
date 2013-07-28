.. _Language: http://docs.doctrine-project.org/en/latest/reference/dql-doctrine-query-language.html
.. _twig: http://twig.sensiolabs.org
.. _smarty: http://www.smarty.net

======================================
Admin Interface and Entering Data
======================================

:Author:
    Timothy Paustian
    
The admin controller responds to admin input and presents the admin interface for the module. This is where the code to add, edit and delete data should begin. It then marshalls the needed forms and functions of the module to carry out whatever the admin changes. For the case of the StrainID2 module, we are going to do two things, present an admin interface to choose strains to work with, or craete a new strain. When creating a strain or editing one, an editing interface is shown to the admin. Here is the class, which extends from Zikula_AbstractController

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
            //This line checks to make sure we have admin access to this module
            //You should not be able to use these functions unless you do, so always check immediately
            $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADMIN), LogUtil::getErrorMsgPermission());
            //Get the repository. We need this to be able to access the data
            $repository = $this->entityManager->getRepository('StrainID2_Entity_StrainID2');
            //Generate the a strain table. We pass the repository to this func and the view
            //it will get the data land format it for us.
            $strain_table = StrainID2_Api_User::generate_strain_table($this->view, $repository, true);
            //Assign the strain table to the view.
            $this->view->assign('strain_table', $strain_table);
            //Let the template know we are in edit mode and
            //finally render the template.
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
            //This line checks to make sure we have admin access to this module
            //You should not be able to use these functions unless you do, so always check immediately
            $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADD), LogUtil::getErrorMsgPermission());
            //Here we are utilizing the very powerful Form interface for zikula
            //It takes care of lots of the work of getting user input
            //To do this you first get a new form
            $form = FormUtil::newForm('StrainID2', $this);
            //You then send the execute command and pass it a class that will handle
            //form initialization and responding to submission of the form.
            return $form->execute('admin/edit.tpl', new StrainID2_Form_Handler_Admin_Edit());
        }
        
        /**
         * @desc set caching to false for all admin functions
         * @return      null
         */
        public function postInitialize()
        {
            //Here we tell Zikula to not cache any of the data from the form
            //This insures that every time we use a form, it is updated to the
            //latest state of the model.
            $this->view->setCaching(false);
        }
    
    }

StrainID2_Controller_Admin::main
--------------------------------

The main() function is the entry point for creating or editing strain data. First, we check to see if the caller has the right permissions to access this interface (ACCESS_ADMIN) if so, then we get the $repository and use this to call a function, generate_strain_table, that is in our StrainID2_Api_User. This data is then assigned to the view using a template variable and the page is rendered. Note how all of this can be handled in just a few lines of code and we factor out the heavy lifting to another function. We do this because the strain table is going to be used by a number of functions. Here is the generate_strain_table function. This is found in lib/StrainID2/Api/User.php.

::
    
    static public function generate_strain_table($view, $repository, $do_edit_links, $where = "") {
        //we want our strains listed alphabetically
        $orderBy = 'name';
        //call the getStrains fucntion of the repository. This sends back an 
        //array listing the strain data.
        $strains = $repository->getStrains($orderBy, $where);
        //Now assign this to a template variable
        $view->assign('strains', $strains);
        //Let whether this is admin or not.
        $view->assign('is_admin', $do_edit_links);
        //create the strain table.
        return $view->fetch('user/strainTbl.tpl');
    }

Note that this is a static function, which means it can be called by any part of the module as we did in the StrainID2_Controller_Admin::main funciton. There are specific rules about how static functions can behave. The one important thing to remember is that they cannot access instance variables of a class. We grab the strain data by sending a message to the repository called getStrains. We want it ordered by name, but we do not include $where, since we want all the strains. Here is that code, located at lib/StrainID2/Entity/Respository/StrainID2Respository.php

::
    
    public function getStrains($orderBy, $where='')
    {
        $dql = "SELECT a FROM StrainID2_Entity_StrainID2 a";
        
        if (!empty($where)) {
            $dql .= ' WHERE ' . $where;
        }

        $dql .= " ORDER BY a.$orderBy";

        // generate query
        $query = $this->_em->createQuery($dql);


        try {
            $result = $query->getResult();
        } catch (Exception $e) {
            echo "<pre>";
            var_dump($e->getMessage());
            var_dump($query->getDQL());
            var_dump($query->getParameters());
            var_dump($query->getSQL());
            die;
        }
        return $result;
    }
    
First we build the query using Doctrine Query Language_ (dql). The syntax of this language will be familiar to anyone what has every worked with a SQL database and made calls. However, instead of specifying the table in the SQL database, you specify the *class* that you are that you are querying. in the statement SELECT a FROM... the "a" is called a *identification variable* that stands in for the class. This tells dql that we want all instances of the StrainID2 class that match our where clause. FROM tells use what class we are querying. If a where clause is specified, it will limit what gets returned. For example you could build a clause such as a.indole="+". These can be built up into complex statements, as you will see later. We then add the ORDER BY statement.

The database is queried by sending the now built request to the entity manager (_em). The $query class sent a message to get the result, and if successful, will send back an array of entries in the table. These queries should always be placed in a try block, and the catch statement should dump information to the interface to assist in debugging. If the catch block is not called, the result is returned to the caller.

The data returned in this case looks like this...

::
    
    Array
    (
    [0] => StrainID2_Entity_StrainID2 Object
        (
            [sid:StrainID2_Entity_StrainID2:private] => 2
            [name:StrainID2_Entity_StrainID2:private] => Budvicia aquatica
            [indole:StrainID2_Entity_StrainID2:private] => -
            [methylred:StrainID2_Entity_StrainID2:private] => +
            [voguesproskauer:StrainID2_Entity_StrainID2:private] => -
            [simmonscitrate:StrainID2_Entity_StrainID2:private] => -
            [h2s:StrainID2_Entity_StrainID2:private] => +
            [phenylalanine:StrainID2_Entity_StrainID2:private] => -
            [lysine:StrainID2_Entity_StrainID2:private] => -
            [ornithine:StrainID2_Entity_StrainID2:private] => -
            [motility:StrainID2_Entity_StrainID2:private] => v
            [lactose:StrainID2_Entity_StrainID2:private] => +
            [reflection:protected] => 
        )
    ...

Heading back to the generate_strain_table function, lets look at that user/strainTbl.tpl.

::
    
    <div id="StrainID_body">
        <table class="z-datatable">
            <tr class="strain_list_row_header">
                <td class="strain_header">{gt text="Name"}</td>
                <td class="strain_header">{gt text="Indole"}</td>
                <td class="strain_header">{gt text="Methyl Red"}</td>
                <td class="strain_header">{gt text="Vogues-Proskauer"}</td>
                <td class="strain_header">{gt text="Citrate"}</td>
                <td class="strain_header">{gt text="H<sub>2</sub>S"}</td>
                <td class="strain_header">{gt text="Phenyl Alanine"}</td>
                <td class="strain_header">{gt text="Lysine"}</td>
                <td class="strain_header">{gt text="Ornithine"}</td>
                <td class="strain_header">{gt text="Motility"}</td>
                <td class="strain_header">{gt text="Lastose Fermentation"}</td>
            <tr>
        {foreach item='strain' from=$strains}
            <tr class="strain_list_row">
                {if $is_admin}
                <td><a href="{modurl modname='StrainID2' type='admin' func='edit' sid=`$strain.sid`}"><i>{$strain.name}</i></a></td>
                {else}
                <td><i>{$strain.name}</i></td>
                {/if}
                <td class="strain_cell">{$strain.indole}</td>
                <td class="strain_cell">{$strain.methylred}</td>
                <td class="strain_cell">{$strain.voguesproskauer}</td>
                <td class="strain_cell">{$strain.simmonscitrate}</td>
                <td class="strain_cell">{$strain.h2s}</td>
                <td class="strain_cell">{$strain.phenylalanine}</td>
                <td class="strain_cell">{$strain.lysine}</td>
                <td class="strain_cell">{$strain.ornithine}</td>
                <td class="strain_cell">{$strain.motility}</td>
                <td class="strain_cell">{$strain.lactose}</td>
            <tr>
        {/foreach}
        </table>  
    </div>

To access the data in the $strains template variable we use an foreach iterator, the syntax of which is very similar to php syntax. You can learn more about this template code at the smarty_ and twig_ sites. This creates a $strain variable for each row in the table. To access the name, you simple write, $strain.name. The rendered html codes gets passed back to the calling function.

StrainID2_Controller_Admin::edit
--------------------------------

The edit function is called in two instances, to edit a strain, in that case the id of the strain is know, or to create a new strain. The class function is as follows.

::
    
    public function edit()
    {
        //This line checks to make sure we have admin access to this module
        //You should not be able to use these functions unless you do, so always check immediately
        $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADD), LogUtil::getErrorMsgPermission());
        //Here we are utilizing the very powerful Form interface for zikula
        //It takes care of lots of the work of getting user input
        //To do this you first get a new form
        $form = FormUtil::newForm('StrainID2', $this);
        //You then send the execute command and pass it a class that will handle
        //form initialization and responding to submission of the form.
        return $form->execute('admin/edit.tpl', new StrainID2_Form_Handler_Admin_Edit());
    }
    
    
All the heavy lifting here is done by the StrainID2_Form_Handler_Admin_Edit that creates the interface and responds to commands. This is located at lib/StrainID2/Form/Handler/Admin/Edit.php. The Form interface for Zikula is extremely powerful and is worth getting to know. You create a class that extends Zikula_Form_AbstractHandler and then must implement one function, handleCommand to handle the input by the user.

Before the form is shown, the Form handler has the opportunity to initialize variables. Below is show the first part of the StrainID2_Form_Handler_Admin_Edit

::

    /**
     * StrainID2
     *
     * @license GNU/LGPLv3 (or at your option, any later version).
     */
    class StrainID2_Form_Handler_Admin_Edit extends Zikula_Form_AbstractHandler {
    
        /**
         * id of strain.
         *
         * When set this handler is in edit mode.
         *
         * @var integer
         */
        private $sid;
    
        /**
         * Setup form.
         *
         * @param Zikula_Form_View $view Current Zikula_Form_View instance.
         *
         * @return boolean
         */
        public function initialize(Zikula_Form_View $view) {
            $sid = FormUtil::getPassedValue('sid', null, 'GET', FILTER_SANITIZE_NUMBER_INT);
    
            if ($sid) {
                // load record with id
                $strain = $this->entityManager->getRepository('StrainID2_Entity_StrainID2')->find($sid);
    
                if ($strain) {
                    // switch to edit mode
                    $this->sid = $sid;
                    // assign current values to form fields
                    $view->assign($strain->toArray());
                    $view->assign('mode', 'edit');
                }
            } else {
               $view->assign('mode', 'create');
            }
            $items = array(array('text' => '+', 'value' => '+'),
                array('text' => '-', 'value' => '-'),
                array('text' => 'v', 'value' => 'v'),
                array('text' => 'u', 'value' => 'u'));
    
            $view->assign('reaction', $items);  // Supply items
    
            $view->setStateData('returnurl', ModUtil::url('StrainID2', 'admin', 'main'));
    
            return true;
        }
       ...

In initialize, we first grab the sid (strain id) if one is given. If we do have a sid passed to us, then we are editing a strain. We grab the strain data, set the instance variable $sid for the class, pass the strain data as an array and finally turn on edit mode. If we don't have strain data, then we set the mode to create for the template. A final template variable is created that will be used in our drop down lists for the edit template. This sets up the menu choices as an array of arrays. This array is them assigned to the template variable reaction. Finally we set the turn url. At the end, since our initialization turned out just fine, we return true. If something did not work, you could return false and the Zikula framework would notify the user of why.

The template for this view, specified in the calls to the form class, is templates/admin/edit.tpl. 

::
    
    {* purpose of this template: build the Form to edit an instance of strain *}
    {adminheader}
    {if $mode eq 'edit'}
        {gt text='Edit strain' assign='templateTitle'}
        {assign var='adminPageIcon' value='edit'}
    {elseif $mode eq 'create'}
        {gt text='Create strain' assign='templateTitle'}
        {assign var='adminPageIcon' value='new'}
    {/if}
    <div class="strainid-strain strainid-edit">
        {pagesetvar name='title' value=$templateTitle}
        <div class="z-admin-content-pagetitle">
            {icon type=$adminPageIcon size='small' alt=$templateTitle}
            <h3>{$templateTitle}</h3>
        </div>
    {form cssClass='z-form'}
        {* add validation summary and a <div> element for styling the form *}
        
        {formsetinitialfocus inputId='name'}
    
    
        <fieldset>
            <legend>{gt text='Content'}</legend>
            
            <div class="z-formrow">
                {formlabel for='name' __text='Name' mandatorysym='1'}
                {formtextinput group='strain' id='name' mandatory=true readOnly=false __title='Enter the name of the strain' textMode='singleline' maxLength=255 cssClass='required' text=$name}
            </div>
            
            <div class="z-formrow">
                {formlabel for='indole' __text='Indole' mandatorysym='1'}
                {formdropdownlist group='strain' id='indole' mandatory=true readOnly=false __title='Enter the indole reaction for the strain' items=$reaction cssClass='required' selectedValue=$indole}
            </div>
            
            <div class="z-formrow">
                {formlabel for='methylred' __text='Methyl Red' mandatorysym='1'}
                {formdropdownlist group='strain' id='methylred' mandatory=true readOnly=false __title='Enter the methyl red reaction for the strain' items=$reaction cssClass='required' selectedValue=$methylred}
            </div>
            
            <div class="z-formrow">
                {formlabel for='voguesproskauer' __text='Vogues Proskauer' mandatorysym='1'}
                {formdropdownlist group='strain' id='voguesproskauer' mandatory=true readOnly=false __title='Enter the vogues proskauer reaction for the strain' items=$reaction cssClass='required' selectedValue=$voguesproskauer}
            </div>
            
            <div class="z-formrow">
                {formlabel for='simmonscitrate' __text='Simmons Citrate' mandatorysym='1'}
                {formdropdownlist group='strain' id='simmonscitrate' mandatory=true readOnly=false __title='Enter the simmons citrate reaction for the strain' items=$reaction cssClass='required' selectedValue=$simmonscitrate}
            </div>
            
            <div class="z-formrow">
                {formlabel for='h2s' __text='H2S' mandatorysym='1'}
                {formdropdownlist group='strain' id='h2s' mandatory=true readOnly=false __title='Enter the hydrogensulfide reaction for the strain' items=$reaction cssClass='required' selectedValue=$h2s}
            </div>
            
            <div class="z-formrow">
                {formlabel for='phenylalanine' __text='Phenylalanine' mandatorysym='1'}
                {formdropdownlist group='strain' id='phenylalanine' mandatory=true readOnly=false __title='Enter the phenylalanine reaction for the strain' items=$reaction cssClass='required' selectedValue=$phenylalanine}
            </div>
            
            <div class="z-formrow">
                {formlabel for='lysine' __text='Lysine' mandatorysym='1'}
                {formdropdownlist group='strain' id='lysine' mandatory=true readOnly=false __title='Enter the lysine reaction for the strain' items=$reaction cssClass='required' selectedValue=$lysine}
            </div>
            
            <div class="z-formrow">
                {formlabel for='ornithine' __text='Ornithine' mandatorysym='1'}
                {formdropdownlist group='strain' id='ornithine' mandatory=true readOnly=false __title='Enter the ornithine reaction for the strain' items=$reaction cssClass='required' selectedValue=$ornithine}
            </div>
            
            <div class="z-formrow">
                {formlabel for='motility' __text='Motility' mandatorysym='1'}
                {formdropdownlist group='strain' id='motility' mandatory=true readOnly=false __title='Enter the motility reaction for the strain' items=$reaction cssClass='required' selectedValue=$motility}
            </div>
            
            <div class="z-formrow">
                {formlabel for='lactose' __text='Lactose' mandatorysym='1'}
                {formdropdownlist group='strain' id='lactose' mandatory=true readOnly=false __title='Enter the lactose reaction for the strain' items=$reaction cssClass='required' selectedValue=$lactose}
            </div>
        </fieldset>
        
        
        {* include possible submit actions *}
        <div class="z-buttons z-formbuttons">
            {if $mode eq 'edit'}
                {formbutton id='btnEdit' commandName='edit' __text='Update' class='z-bt-edit'}
                {formbutton id='btnDelete' commandName='delete' __text='Delete' class='z-bt-delete'}
            {elseif $mode eq 'create'}
                {formbutton id='btnEdit' commandName='edit' __text='Create' class='z-bt-edit'}
            {/if} 
            {formbutton id='btnCancel' commandName='cancel' __text='Cancel' class='z-bt-cancel'}
        </div>
    {/form}
    
    </div>
    {adminfooter}
    
    <script type="text/javascript">
    // <![CDATA[
        Zikula.UI.Tooltips($$('.tooltips'));
    // ]]>
    </script>


At the top of each admin template, we add the standard interface html, by using the term

::
    
    {adminheader}

This puts a common look on all admin templates and gives the administrator a standard interface for all modules. Below this code we then decide what we will title the interface and then assign some template variables. These variables are available throughout the rest of the template. Next the title of the template and icon are made and we then get to the actual form. Since we are using the form interface, we can use many of the plugins that are provided with Zikula. These are located at /lib/viewplugins/formplugins. All of these are very well documented in the source, and you can often figure out how to read them by reading the source code. To open a form the template code is..

::
    
    {form cssClass='z-form'}

and to close a form, the template code is..

::
    
    {/form}

We set the initial focus to the name field. Then elements of the form are laid out. In our case this follows a simple pattern. The form label, followed by the form element. For example, the first element is..

::
    
    <div class="z-formrow">
        {formlabel for='name' __text='Name' mandatorysym='1'}
        {formtextinput group='strain' id='name' mandatory=true readOnly=false __title='Enter the name of the strain' textMode='singleline' maxLength=255 cssClass='required' text=$name}
    </div>

The Title of the first label is Name. We then use the formtextinput plugin to render the element for us. The next form element is the indole drop down list where the administrator can specify the reaction of the strain for indole. 

::
    
    <div class="z-formrow">
        {formlabel for='indole' __text='Indole' mandatorysym='1'}
        {formdropdownlist group='strain' id='indole' mandatory=true readOnly=false __title='Enter the indole reaction for the strain' items=$reaction cssClass='required' selectedValue=$indole}
    </div>

Note here that we use the $reaction template variable to populate the list that is created. How to create these lists is described in the formdropdownlist plug in located at /lib/viewplugins/formplugins/function.formdropdownlist.php. This will create a dropdown list with the values +, -, u, and v. All of the other elements in the form are similar to this one. We finally close the form elements with possible submit actions.

::
    
    {* include possible submit actions *}
    <div class="z-buttons z-formbuttons">
        {if $mode eq 'edit'}
            {formbutton id='btnEdit' commandName='edit' __text='Update' class='z-bt-edit'}
            {formbutton id='btnDelete' commandName='delete' __text='Delete' class='z-bt-delete'}
        {elseif $mode eq 'create'}
            {formbutton id='btnEdit' commandName='edit' __text='Create' class='z-bt-edit'}
        {/if} 
        {formbutton id='btnCancel' commandName='cancel' __text='Cancel' class='z-bt-cancel'}
    </div>

Depending upon the mode we either make an Edit and Delete buttons, or we make a Create button. A cancel button is made for every form.

The second half of StrainID2_Form_Handler_Admin_Edit handles the form after the user hits Edit, Create or Cancel. This will call the handleCommand message of our Form Handler class. 

::
    
    public function handleCommand(Zikula_Form_View $view, &$args) {
        $returnurl = $view->getStateData('returnurl');

        // process the cancel action
        if ($args['commandName'] == 'cancel') {
            return $view->redirect($returnurl);
        }

        if ($args['commandName'] == 'delete') {
            if (SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_DELETE)) {
                $strain = $this->entityManager->getRepository('StrainID2_Entity_StrainID2')->find($this->sid);
                $name = $strain['name'];
                $this->entityManager->remove($strain);
                $this->entityManager->flush();
                ModUtil::apiFunc('StrainID2', 'user', 'clearItemCache', $strain);
                LogUtil::registerStatus($this->__f('Item [name %s] deleted!', $name));
                return $view->redirect(ModUtil::url('StrainID2', 'admin', 'main'));
            } else {
                $view->setPluginErrorMsg('title', $this->__('You are not authorized to delete this entry!'));
                return false;
            }
        }

        // check for valid form
        if (!$view->isValid()) {
            return false;
        }

        // load form values
        $data = $view->getValues();

        // switch between edit and create mode
        if ($this->sid) {
            if (SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_EDIT)) {
                $strain = $this->entityManager->getRepository('StrainID2_Entity_StrainID2')->find($this->sid);
            } else {
                $view->setPluginErrorMsg('title', $this->__('You are not authorized to edit this entry!'));
                return false;
            }
            $name = $data['strain']['name'];
            LogUtil::registerStatus($this->__f('Item [name %s] updated!', $name));
            
            $strain->merge($data['strain']);
        } else {
            if (!SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADD)) {
                $view->setPluginErrorMsg('title', $this->__('You are not authorized to create this entry!'));
                return false;
            }
            $strain = new StrainID2_Entity_StrainID2();
            $strain->merge($data['strain']);
            $this->entityManager->persist($strain);
            $name = $data['strain']['name'];
            LogUtil::registerStatus($this->__f('Item [name %s] created!', $name));
            
        }

        try {
            $this->entityManager->flush();
        } catch (Zikula_Exception $e) {
            echo "<pre>";
            var_dump($e->getDebug());
            echo "</pre>";
            die;
        }

        ModUtil::apiFunc('StrainID2', 'user', 'clearItemCache', $strain);

        return $view->redirect($returnurl);
    }
    
If the user chooses cancel, we simply redirect to the redirect url. If the user chooses delete, we first make sure the caller has delete permission. If they do, we grab the name of the strain for later use, then tell the entitymanager to remove that strain and call flush to make it permanent. We then tell Zikula to clear the cache for this item, so that if any cached templates have this strain in them, they will be rerendered. Finally we update the status message to notify the user that that strain was deleted and we redirect to the main page. 

If the user chooses Create/Edit then by default they will wind up at this point in the code. We first validate the data, then grab it. If we have an sid (remember we saved this during initialization) then we are editing. We check to make sure the user has edit access and then if they do, get the strain data and call merge on it, using the data we obtained from the form. Note how easy this is to do. A single line of code, instead of you having to write the complex sql statements to do it for you. We then notify the user of the successful update of the strain.

If we are creating a strain, we check for add permission and if granted, create a new entity and add the data. In this case we have to call persist to add this object to the data model. We then notify the user of the successful creation of the strain. Next, flush is called to write our changes to the object model back to the database. This is encloses in a try block, and if it fails, the reason for failure is reported to the caller. Finally, we redirect the calls to the return url.

That is the admin interface for the StrainID2. Our code is almost done. The only other function we need to have is the ability of the user to search our database for strains. That will be implemented in the User Controller at lib/StrainID2/Controller/User.php

