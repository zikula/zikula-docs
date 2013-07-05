If you want to download the finished module and follow along, this can be found as a `zip file <http://inst.bact.wisc.edu/StrainID_example.zip>`. Let's begin by adding a link to the main admin page that takes us to the edit page. 

The template you want to override is at templates/admin/main.tpl. There are tons of templates, how did I know that? If you look in the templates folder, you will find these folders.

- admin
- block
- contenttype
- external
- mailz
- plugins
- search
- user
- include_pdfheader.tpl

It is logical to place admin templates in the admin folder, so I looked there. You can verify that you have the right template by opening it, typing something in it and then checking in your browser. Now we do not want to edit this template. If we do, then when we make changes in MOST and drop our new module in, it will overwrite our template. You can place overriding templates in either your theme or the config folder. For this example, we will drop them in the theme we are using. Create a folder inside the template folder of your theme called modules. Inside that folder create another folder called StrainID. The path should be themes/YourTheme/template/Modules/StrainID/. Now you have to create a folder hierarchy identical to the one inside the StrainID module. For this case we need to create an admin folder, and drop the main.tpl in there. Place the following code in this main.tpl.

::

{* purpose of this template: strains main view in admin area *}
{include file='admin/header.tpl'}
<p><a href="{modurl modname='StrainID' type='admin' func='edit' }">{gt text="Enter a new strain in the database."}</a></p>
{$strain_table}
{include file='admin/footer.tpl'}

This code generates a link to the edit template. modurl is a function that generates the link if you specifcy the module, its type and the function. Note also the use of gt. This is the getText utility that allows the formation of translations. All text templates should be wrapped in these calls to allow easy translation to other languages. 

You will also notice the $strain_table variable. We need to generate this table and add it to the template. To do this, we need to locate the file that generates the StrainID-admin-main page. This is located in StrainID/lib/Controller/Base/Admin.php. However, we do not want to modify this base class, we instead override the concrete class that is located at StrainID/lib/Controller/Admin.php. So open that file.
You will notice that this file contains a class StrainID_Controller_Admin that extends StrainID_Controller_Admin_Base, its base class. Any function you write in here will override a function in the base class. So we need to write a main function. Here is the code to add to the base class.

::

public function main($args) {
  $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID::', '::', ACCESS_ADMIN));

    $repository = $this->entityManager->getRepository('StrainID_Entity_strain');
    $strain_table = StrainID_Util_View::generate_strain_table($this->view, $repository, true);
    $this->view->assign('strain_table', $strain_table);

    // return main template
    return $this->view->fetch('admin/main.tpl') . $retText;
}
    
Most of the action takes place in generate_strain_table. This is a utility function that we will be using in a number of places. Therefore, it is factored out and added to the file at lib/StrainID/Util/View.php. We send it the view and the repository. The view is a representation of the view we are creating and handles generation of the html. The repository is our interface with the modules model (its data). Here is the code for that function.

::

/**
 * generate strain table sends back a complete formatted list of
 * all of the strains that have been entered into the datbase
 * @return text The list of strains as html
 */
public function generate_strain_table($view, $repository, $do_edit_links, $where="") {
    $strains = $repository->selectWhere($where, 'name');
    //Now assign this to a template variable
    $view->assign('strains', $strains);
    $view->assign('is_admin', $do_edit_links);
    //create the strain table.
    return $view->fetch('strainTbl.tpl');
}

Add the above code. The respository makes a call to the database selectWhere. If where is empty, as it is now, it sends back all the data. We then assign the resulting $strains variable to the template and also, $do_edit_links. This tells the template to render the names of the strains as links to edit forms with the data. We then return the rendered template, strainTbl.tpl. Create the strainTbl.tpl at templates/YourTheme/modules/StrainID/strainTbl.tpl and add the following code

::

<div id="StrainID_body">
    <table class="strain_list">
        <tr class="strain_list_row_header">
            <td>{gt text="Name"}</td>
            <td>{gt text="Indole"}</td>
            <td>{gt text="Methyl Red"}</td>
            <td>{gt text="Vogues-Proskauer"}</td>
            <td>{gt text="Citrate"}</td>
            <td>{gt text="H<sub>2</sub>S"}</td>
            <td>{gt text="Phenyl Alanine"}</td>
            <td>{gt text="Lysine"}</td>
            <td>{gt text="Ornithine"}</td>
            <td>{gt text="Motility"}</td>
            <td>{gt text="Lastose Fermentation"}</td>
        <tr>
    {foreach item='strain' from=$strains}
        <tr class="strain_list_row">
            {if $is_admin}
            <td><a href="{modurl modname='StrainID' type='admin' func='edit' id=`$strain.id`}"><i>{$strain.name}</i></a></td>
            {else}
            <td><i>{$strain.name}</i></td>
            {/if}
            <td class="strain_cell">{$strain.indole}</td>
            <td class="strain_cell">{$strain.methyl_red}</td>
            <td class="strain_cell">{$strain.vogues_proskauer}</td>
            <td class="strain_cell">{$strain.simmons_citrate}</td>
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

This page demonstrates how to create html and use what comes back from your repository calls. The $strains variable has an array of values that correspond to each row in the table. You simiply use a foreach call and then use dot notation to call each value in the row. Notice how the name of each value exactly matches what you called each value when you created your model in MOST?

Now if you load the main admin page, you will see a link to the edit page and then a table below that that lists every strain in the database. There are two things we need to fix with the look of the admin page. One, default values have been entered into the database, and these really don't mean anything. We should add some better test results. Second, the results of the tests should be +/-/u/v, not 1. 

Let's attack the default data first. If you open up Installer.php (lib/StrainID/Base/Installer.php) you will find a called in the install function to createDefaultData. Further down the file is the create default data class. We again need to override this, but don't do it here. As before, find the concrete class at lib/StrainID/Installer.php and past the function into this class. Add the following code:

::

/**
     * Create the default data for StrainID.
     *
     * @param array $categoryRegistryIdsPerEntity List of category registry ids.
     *
     * @return void
     */
    protected function createDefaultData($categoryRegistryIdsPerEntity)
    {
        $entityClass = 'StrainID_Entity_Strain';
        $this->entityManager->getRepository($entityClass)->truncateTable();
        $strain1 = new \StrainID_Entity_Strain();
        $strain2 = new \StrainID_Entity_Strain();
        $strain3 = new \StrainID_Entity_Strain();
        $strain4 = new \StrainID_Entity_Strain();
        $strain5 = new \StrainID_Entity_Strain();
        
        $strain1->setWorkflowState('initial');
        $strain1->setName('Esherichia coli');
        $strain1->setIndole('+');
        $strain1->setMethyl_red('+');
        $strain1->setVogues_proskauer('-');
        $strain1->setSimmons_citrate('-');
        $strain1->setH2s('-');
        $strain1->setPhenylalanine('-');
        $strain1->setLysine('+');
        $strain1->setOrnithine('v');
        $strain1->setMotility('+');
        $strain1->setLactose('+');
        
        $strain2->setWorkflowState('initial');
        $strain2->setName('Budvicia aquatica');
        $strain2->setIndole('-');
        $strain2->setMethyl_red('+');
        $strain2->setVogues_proskauer('-');
        $strain2->setSimmons_citrate('-');
        $strain2->setH2s('+');
        $strain2->setPhenylalanine('-');
        $strain2->setLysine('-');
        $strain2->setOrnithine('-');
        $strain2->setMotility('v');
        $strain2->setLactose('+');
        
        $strain3->setWorkflowState('initial');
        $strain3->setName('Enterobacter taylorae');
        $strain3->setIndole('+');
        $strain3->setMethyl_red('+');
        $strain3->setVogues_proskauer('+');
        $strain3->setSimmons_citrate('+');
        $strain3->setH2s('+');
        $strain3->setPhenylalanine('+');
        $strain3->setLysine('+');
        $strain3->setOrnithine('+');
        $strain3->setMotility('+');
        $strain3->setLactose('+');
        
        $strain4->setWorkflowState('initial');
        $strain4->setName('Enterobacter aerogenes');
        $strain4->setIndole('-');
        $strain4->setMethyl_red('-');
        $strain4->setVogues_proskauer('+');
        $strain4->setSimmons_citrate('+');
        $strain4->setH2s('-');
        $strain4->setPhenylalanine('-');
        $strain4->setLysine('+');
        $strain4->setOrnithine('+');
        $strain4->setMotility('+');
        $strain4->setLactose('+');
        
        $strain5->setWorkflowState('initial');
        $strain5->setName('Yersinia pestis');
        $strain5->setIndole('-');
        $strain5->setMethyl_red('+');
        $strain5->setVogues_proskauer('-');
        $strain5->setSimmons_citrate('-');
        $strain5->setH2s('-');
        $strain5->setPhenylalanine('-');
        $strain5->setLysine('-');
        $strain5->setOrnithine('-');
        $strain5->setMotility('-');
        $strain5->setLactose('-');
        
        
        // execute the workflow action for each entity
        $action = 'submit';
        $workflowHelper = new StrainID_Util_Workflow($this->serviceManager);
        try {
            $success = $workflowHelper->executeAction($strain1, $action);
            $success = $workflowHelper->executeAction($strain2, $action);
            $success = $workflowHelper->executeAction($strain3, $action);
            $success = $workflowHelper->executeAction($strain4, $action);
            $success = $workflowHelper->executeAction($strain5, $action);
        } catch(\Exception $e) {
            LogUtil::registerError($this->__('Sorry, but an unknown error occured during example data creation. Possibly not all data could be created properly!'));
        }
    }

You will notice that creation of the data is very straightforward. This gets added when the module is installed. So we will need to uninstall the module in the extentions module. Click on the Systems Tab in the admin area, then on the extensions module. Scroll down to the StrainID module in the list, click on the inactivate icon and then after it is inactive, click on uninstall. It will warn you that you will lose all data. Click on OK. Finally reinstall the module by clicking on install. If you then go to the admin page for StrainID, you wil now see a table of our newly entereed strains. 

Another problem is that if you click on add a new strain to the database, it opens the edit page. Test it out. You will notice that it takes you to an already generated form. If you click out of a text area on the form without entering anything, it warns you that this is a required field. Again, MOST has written much of the editing and validation code for you. It has also made it pretty darn fancy with javascript and immediate feedback. However, the strain test values should again be restricted to +/-/u/v. It would be better to have a drop down menu here instead of making sure we get +, -, u, or v after the fact. Let's edit that template and add some code to the class that generates it. You will be overriding the template at StrainID/tempates/admin/strain/edit.tpl and placing the file at theme/YourTheme/templates/modules/StrainID/admin/strain/edit.tpl. Here is the code to add to the edit.tpl file. 

::

{* purpose of this template: build the Form to edit an instance of strain *}
{include file='admin/header.tpl'}
{pageaddvar name='javascript' value='modules/StrainID/javascript/StrainID_editFunctions.js'}
{pageaddvar name='javascript' value='modules/StrainID/javascript/StrainID_validation.js'}
{if $mode eq 'edit'}
    {gt text='Edit strain' assign='templateTitle'}
    {assign var='adminPageIcon' value='edit'}
{elseif $mode eq 'create'}
    {gt text='Create strain' assign='templateTitle'}
    {assign var='adminPageIcon' value='new'}
{else}
    {gt text='Edit strain' assign='templateTitle'}
    {assign var='adminPageIcon' value='edit'}
{/if}
<div class="strainid-strain strainid-edit">
    {pagesetvar name='title' value=$templateTitle}
    <div class="z-admin-content-pagetitle">
        {icon type=$adminPageIcon size='small' alt=$templateTitle}
        <h3>{$templateTitle}</h3>
    </div>
{form cssClass='z-form'}
    {* add validation summary and a <div> element for styling the form *}
    {strainidFormFrame}

    {formsetinitialfocus inputId='name'}


    <fieldset>
        <legend>{gt text='Content'}</legend>
        
        <div class="z-formrow">
            {formlabel for='name' __text='Name' mandatorysym='1'}
            {formtextinput group='strain' id='name' mandatory=true readOnly=false __title='Enter the name of the strain' textMode='singleline' maxLength=255 cssClass='required' }
            {strainidValidationError id='name' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='indole' __text='Indole' mandatorysym='1'}
            {formdropdownlist group='strain' id='indole' mandatory=true readOnly=false __title='Enter the indole reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='indole' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='methyl_red' __text='Methyl_red' mandatorysym='1'}
            {formdropdownlist group='strain' id='methyl_red' mandatory=true readOnly=false __title='Enter the methyl red reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='methyl_red' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='vogues_proskauer' __text='Vogues_proskauer' mandatorysym='1'}
            {formdropdownlist group='strain' id='vogues_proskauer' mandatory=true readOnly=false __title='Enter the vogues proskauer reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='vogues_proskauer' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='simmons_citrate' __text='Simmons_citrate' mandatorysym='1'}
            {formdropdownlist group='strain' id='simmons_citrate' mandatory=true readOnly=false __title='Enter the simmons citrate reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='simmons_citrate' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='h2s' __text='H2s' mandatorysym='1'}
            {formdropdownlist group='strain' id='h2s' mandatory=true readOnly=false __title='Enter the hydrogensulfide reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='h2s' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='phenylalanine' __text='Phenylalanine' mandatorysym='1'}
            {formdropdownlist group='strain' id='phenylalanine' mandatory=true readOnly=false __title='Enter the phenylalanine reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='phenylalanine' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='lysine' __text='Lysine' mandatorysym='1'}
            {formdropdownlist group='strain' id='lysine' mandatory=true readOnly=false __title='Enter the lysine reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='lysine' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='ornithine' __text='Ornithine' mandatorysym='1'}
            {formdropdownlist group='strain' id='ornithine' mandatory=true readOnly=false __title='Enter the ornithine reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='ornithine' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='motility' __text='Motility' mandatorysym='1'}
            {formdropdownlist group='strain' id='motility' mandatory=true readOnly=false __title='Enter the motility reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='motility' class='required'}
        </div>
        
        <div class="z-formrow">
            {formlabel for='lactose' __text='Lactose' mandatorysym='1'}
            {formdropdownlist group='strain' id='lactose' mandatory=true readOnly=false __title='Enter the lactose reaction for the strain' items=$reaction cssClass='required'}
            {strainidValidationError id='lactose' class='required'}
        </div>
    </fieldset>
    
    {if $mode ne 'create'}
        {include file='admin/include_standardfields_edit.tpl' obj=$strain}
    {/if}
    
    {* include display hooks *}
    {assign var='hookid' value=null}
    {if $mode ne 'create'}
        {assign var='hookid' value=$strain.id}
    {/if}
    {notifydisplayhooks eventname='strainid.ui_hooks.strains.form_edit' id=$hookId assign='hooks'}
    {if is_array($hooks) && count($hooks)}
        {foreach key='providerArea' item='hook' from=$hooks}
            <fieldset>
                {$hook}
            </fieldset>
        {/foreach}
    {/if}
    
    {* include return control *}
    {if $mode eq 'create'}
        <fieldset>
            <legend>{gt text='Return control'}</legend>
            <div class="z-formrow">
                {formlabel for='repeatcreation' __text='Create another item after save'}
                {formcheckbox group='strain' id='repeatcreation' readOnly=false}
            </div>
        </fieldset>
    {/if}
    
    {* include possible submit actions *}
    <div class="z-buttons z-formbuttons">
    {foreach item='action' from=$actions}
        {assign var='actionIdCapital' value=$action.id|@ucwords}
        {gt text=$action.title assign='actionTitle'}
        {*gt text=$action.description assign='actionDescription'*}{* TODO: formbutton could support title attributes *}
        {if $action.id eq 'delete'}
            {gt text='Really delete this strain?' assign='deleteConfirmMsg'}
            {formbutton id="btn`$actionIdCapital`" commandName=$action.id text=$actionTitle class=$action.buttonClass confirmMessage=$deleteConfirmMsg}
        {else}
            {formbutton id="btn`$actionIdCapital`" commandName=$action.id text=$actionTitle class=$action.buttonClass}
        {/if}
    {/foreach}
        {formbutton id='btnCancel' commandName='cancel' __text='Cancel' class='z-bt-cancel'}
    </div>
    {/strainidFormFrame}
{/form}

</div>
{include file='admin/footer.tpl'}

{icon type='edit' size='extrasmall' assign='editImageArray'}
{icon type='delete' size='extrasmall' assign='deleteImageArray'}


<script type="text/javascript">
/* <![CDATA[ */

    var formButtons, formValidator;

    function handleFormButton (event) {
        var result = formValidator.validate();
        if (!result) {
            // validation error, abort form submit
            Event.stop(event);
        } else {
            // hide form buttons to prevent double submits by accident
            formButtons.each(function (btn) {
                btn.addClassName('z-hide');
            });
        }

        return result;
    }

    document.observe('dom:loaded', function() {

        strainAddCommonValidationRules('strain', '{{if $mode ne 'create'}}{{$strain.id}}{{/if}}');
        {{* observe validation on button events instead of form submit to exclude the cancel command *}}
        formValidator = new Validation('{{$__formid}}', {onSubmit: false, immediate: true, focusOnError: false});
        {{if $mode ne 'create'}}
            var result = formValidator.validate();
        {{/if}}

        formButtons = $('{{$__formid}}').select('div.z-formbuttons input');

        formButtons.each(function (elem) {
            if (elem.id != 'btnCancel') {
                elem.observe('click', handleFormButton);
            }
        });

        Zikula.UI.Tooltips($$('.strainidFormTooltips'));
    });

/* ]]> */
</script>

This is a long code entry, but note that much of this code is borrowed from the base template generated by Most, but we are changing 

::

{formtextinput group='strain' id='indole' mandatory=true readOnly=false __title='Enter the indole of the strain' textMode='singleline' maxLength=1 cssClass='required'}

to

::

{formdropdownlist group='strain' id='indole' mandatory=true readOnly=false __title='indole' items=$reaction cssClass='required'}

We do need to make one more modification to get this to work. We need to add the $reaction variable. Code needs to be overridden to do this. We will be overriding the inilization of this form, but taking advantage of the parent class. Open up the file lib/StrainID/Form/Handler/Admin/Edit.php and add the following code to the class.

::
public function initialize(Zikula_Form_View $view) {
    $result = parent::initialize($view);
    //everything was fine with the parent
    if ($result) {
        $items = array(array('text' => '+', 'value' => '+'),
            array('text' => '-', 'value' => '-'),
            array('text' => 'v', 'value' => 'v'),
            array('text' => 'u', 'value' => 'u'));

        $view->assign('reaction', $items);  // Supply items
    }
    return $result;
}

First we take advantage of another feature of object programming. We call the parent class and have it do all its initilization, and then add our little amount of custom programming. In this case we create the options for the drop down list by creating an array. We then assign this as the reaction variable in our few template. 

Save this file and again load StrainID-admin-edit. You will now see a form with drop down lists. The Form functionality built into Zikula is very powerful, taking care of validation and providing all sorts of utility functions to make dealing with user input easier. The admin area is now finished. In the next section we finish the module by modifying the main user page and then providing the search function.