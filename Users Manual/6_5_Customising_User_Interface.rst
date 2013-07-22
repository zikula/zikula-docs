We are now ready to start the user interface. This is very simple for this module. It consists of a main page that displays all the strains in the database and then a search page that allows the user to enter the tests they have for an unknown and search for strains that match those tests. Let's see what the default user page looks at, go to StrainID-user-main in your browser. If you are still at the admin page, just click on the FrontEnd link. You will see a link to the Back End (the admin section) and a message that says "Welcome to the user section of the Strain i d module". Well gee thanks, that is really nice of you, but Mr. rendered page, you're totally useless. Let's override that template right away. The template we are overriding is located at modules/StrainID/templates/user/main.tpl and we will place it at templates/YourTheme/templates/modules/StrainID/user/main.tpl. You should be getting the pattern down for where to find templates and where to place them in your theme to override them. With main.tpl open, put the following code into it.

::

    {* purpose of this template: strains main view in user area *}
    {include file='user/header.tpl'}
    <p><a href="{modurl modname='StrainID' type='user' func='search' }">{gt text='Search for a strain.'}</a></p>
    <h2>{gt text="Current Strains"}</h2>
    {$strain_table}
    {include file='user/footer.tpl'}

This creates a link to the search functionality and also lists the strain table after it. We now need to override StrainID-user-main to generate the strain table and assign it to the template. Open the file modules/StrainID/lib/StrainID/Controller/User.php and place this text in the class.

::

    /**
     * This method is the default function handling the user area called without defining arguments.
     *
     * @param array $args List of arguments.
     *
     * @return mixed Output.
     */
    public function main(array $args = array())
    {
        $this->throwForbiddenUnless(SecurityUtil::checkPermission($this->name . '::', '::', ACCESS_OVERVIEW), LogUtil::getErrorMsgPermission());
        // set caching id
        $this->view->setCacheId('main');
        $repository = $this->entityManager->getRepository('StrainID_Entity_strain');
        $strain_table = StrainID_Util_View::generate_strain_table($this->view, $repository, false);
        $this->view->assign('strain_table', $strain_table);
        // return main template
        return $this->view->fetch('user/main.tpl');
    }

Note that most of the hard work has already been done for us since we wrote a function to generate the strain table. First we verify that the caller has at least OVERVIEW status to view the page. We then fetch the repository, this is our link to the data stored in the database. We then call our utility function generate_strain_table, except this time with the third variable set to false so that links to the strain editing form are not generated with each species name. The returned strain table is then assigned to a template variable and the main.tpl is rendered and returned. Save your files and then call up the page in the browser again. You should see a link to a search page and then a list of all the strains in the database.

Our last task is to create the search page. Click on Search for a Strain and you will see a page begging you to override it. This is MOST's hint for you to add your custom template and coding. Remember we made a custom action and called it search. Most created a default method. Create a template at templates/YourTheme/templates/modules/StrainID/user/serach.tpl and place the following code into it.

::

    {* purpose of this template: build the Form to serach for matching strains *}
    {include file='user/header.tpl'}
    <div class="strainid-strain strainid-edit">
    <div class="z-admin-content-pagetitle">
        {icon type=edit size='small' alt=$templateTitle}
        <h3>{gt text="Search for a Strain"}</h3>
    </div>
    {form cssClass='z-form'}
        {* add validation summary and a <div> element for styling the form *}
        {strainidFormFrame}
        {formsetinitialfocus inputId='indole'}
    
        <fieldset>
        <legend>{gt text='Content'}</legend>
        
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
        {if $mode eq 'create'}
            {notifydisplayhooks eventname='strainid.ui_hooks.strains.form_edit' id=null assign">assign">assign='hooks'}
        {else}
            {notifydisplayhooks eventname='strainid.ui_hooks.strains.form_edit' id=$strain.id assign">assign">assign='hooks'}
        {/if}
        {if is_array($hooks) && isset($hooks[0])}
            <fieldset>
                <legend>{gt text='Hooks'}</legend>
                {foreach key='hookName' item='hook' from=$hooks}
                <div class="z-formrow">
                    {$hook}
                </div>
                {/foreach}
            </fieldset>
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
            {formbutton id='btnUpdate' commandName='search' __text='Search' class='z-bt-ok'}
            {formbutton id='btnCancel' commandName='cancel' __text='Cancel' class='z-bt-cancel'}
        </div>
      {/strainidFormFrame}
    {/form}
    {if $strain_table ne ''}
    <h3>{gt text="Lastest Strain Search Results"}</h3>
    {$strain_table}
    {/if}
    
    </div>
    {include file='user/footer.tpl'}
    
To create it, the text from the edit.tpl was copied and then minor modifications were made. You will note that this code is very similar to the edit template, except we do not ask for a name. That is what the search function is going to find. In this form, you choose what all your tests are, and then the code searches for matching strains in the database. It also takes advantage of the built in Form processing in Zikula. This time we have to create and manage the class ourselves, as MOST cannot make custom code. MOST has no idea what we want this custom action to do. This codes goes into modules/StrainID/lib/StrainID/Controller/User.php; the same place you put the main function. Place this second function in the class.

::

    public function search($args)
    {
        $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID::', '::', ACCESS_OVERVIEW));
        // create new Form reference
        $view = FormUtil::newForm($this->name, $this);
        return $view->execute('user/search.tpl', new StrainID_Form_Handler_User_Search());
    }
    
The search function checks permission again and then turns over control to a new Form Handler class we will write from scratch. Note that we are calling a new type of renderer using FormUtil. If you are familiar with the smarty rendering templates of Zikula, this may be different to you. We send along the Controller ($this->name) which is User.php, and the module ($this), which is StrainID. We then tell the form to execute listing the template to use, the one we just created, and the Form Handler class StrainID_Form_Hanlder_User_Search() that will handle user interaction and processing of the form. We will create that class next. 

If we were staying true to form to the way MOST does things, we would probably create a separate file and place it in the Form Handler User folder. I find it easier to code, and to follow, to have my simple form classes in with the code that is using them. So inside the file modules/StrainID/lib/StrainID/Controller/User.php, but outside the StrainID_Controller_User class, place the following code.

::

    class StrainID_Form_Handler_User_Search extends Zikula_Form_AbstractHandler {

    /**
     * Initialize form handler.
     *
     * This method takes care of all necessary initialisation of our data and form states.
     *
     * @return boolean False in case of initialization errors, otherwise true.
     */
    public function initialize(Zikula_Form_View $view) {
        parent::initialize($view);
        //initialize the drop down menu choices. This will get used for all the strains choices
        $reaction = array(array('text' => '+', 'value' => '+'),
            array('text' => '-', 'value' => '-'),
            array('text' => 'u', 'value' => 'u'));
        $strain_table = SessionUtil::getVar('search_results');
        $view->assign('strain_table', $strain_table);
        $view->assign('reaction', $reaction); // Supply items
        // everything okay, no initialization errors occured
        return true;
    }

    public function handleCommand(Zikula_Form_View $view, &$args) {
        if ($args['commandName'] == 'search') {

            //get the values from the form as an array
            $result = $view->getValues();
            $indole = $result['strain']['indole'];
            $methyl_red = $result['strain']['methyl_red'];
            $voges_pros = $result['strain']['vogues_proskauer'];
            $citrate = $result['strain']['simmons_citrate'];
            $h2s = $result['strain']['h2s'];
            $phenylalanine = $result['strain']['phenylalanine'];
            $ornithine = $result['strain']['ornithine'];
            $motility = $result['strain']['motility'];
            $lactose = $result['strain']['lactose'];
            $lysine = $result['strain']['lysine'];

            $where = array();


            if ($indole == 'u') {
                $where['indole'] = "+|-|u|v";
            } else {
                $where['indole'] = "$indole|u|v";
            }
            if ($methyl_red == 'u') {
                $where['methyl_red'] = "+|-|u|v";
            } else {
                $where['methyl_red'] = "$methyl_red|u|v";
            }
            if ($voges_pros == 'u') {
                $where['vogues_proskauer'] = "+|-|u|v";
            } else {
                $where['vogues_proskauer'] = "$voges_pros|u|v";
            }
            if ($citrate == 'u') {
                $where['simmons_citrate'] = "+|-|u|v";
            } else {
                $where['simmons_citrate'] = "$citrate|u|v";
            }
            if ($h2s == 'u') {
                $where['h2s'] = "+|-|u|v";
            } else {
                $where['h2s'] = "$h2s|u|v";
            }
            if ($phenylalanine == 'u') {
                $where['phenylalanine'] = "+|-|u|v";
            } else {
                $where['phenylalanine'] = "$phenylalanine|u|v";
            }
            if ($ornithine == 'u') {
                $where['ornithine'] = "+|-|u|v";
            } else {
                $where['ornithine'] = "$ornithine|u|v";
            }
            if ($motility == 'u') {
                $where['motility'] = "+|-|u|v";
            } else {
                $where['motility'] = "$motility|u|v";
            }
            if ($lactose == 'u') {
                $where['lactose'] = "+|-|u|v";
            } else {
                $where['lactose'] = "$lactose|u|v";
            }
            if ($lysine == 'u') {
                $where['lysine'] = "+|-|u|v";
            } else {
                $where['lysine'] = "$lysine|u|v";
            }
            
            $repository = $this->entityManager->getRepository('StrainID_Entity_strain');
            $strains = $repository->selectSearchAnd($where, 'name');
            //Now assign this to a template variable
            $view->assign('strains', $strains);
            $view->assign('is_admin', $do_edit_links);
            //create the strain table.
            $strain_table = $view->fetch('strainTbl.tpl');
            //we need to pass this information to the page that will render this
            //This will save it over the session.
            SessionUtil::setVar('search_results', $strain_table);
        }
        $url = ModUtil::url('StrainID', 'user', 'search');
        return $this->view->redirect($url);
       }
    }
    
This is the most complex code of the module, so let's walk through it carefully. To use the Form interface in Zikula, you create a class that extends Zikula_Form_AbstractHandler. The only required function that you must override is handleCommand for the form to work, but in most cases you may want to also override initialize to set up any variables in your form. (There are also a bunch of other routines in this class that can do marvelous things. Make sure you check out the Zikula_Form_AbstractHandler class to see what's possible.) We do want to set up a variable so we will also override initialize. In initialize we create the array that will hold the values that need to be in each drop down form. We want to give the choices +, -, and u to each drop down list, so we create the $reaction array, with those values in it. This is then assigned to the view. We also grab from the Session utility, the variable $search_results and send this to the view. The first pass through this form, there are no search results, so this just passes an empty string to the view. No harm done. This comes into play the second time through, after the first search.

The function handleCommand is where all the action is. This routine is called when the user activates the form by either hitting Search or Cancel. If the user hit cancel, we drop to the end of the function and just redirect to the search page. If the user chooses to search, we first get the results of the form and place it in the obviously named $results variable.  To simplify coding, we place each individual result in its own variable. So what do we need to search for? Well this is a little complex, because for some strains, the result of the test is either variable (v) or unknown (u) and we don't want exclude strains that are (v) or (u) from our search results. Also, we want to give the user the option of choosing (u) for a test for which they don't have data. Therefore, we will either put +|-|u|v for a test where we don't have the results. This all has to be folded into a where statement that will be used to search through the database. The where statement we need to build for Doctrine is quite complex and we are going to let php build much of it for us. Here is an example of what the where statement our search function wants would look like.
::
    (tbl.indole LIKE '%-%' OR tbl.indole LIKE '%u%' OR tbl.indole LIKE '%v%') AND (tbl.methyl_red LIKE '%-%' OR tbl.methyl_red LIKE '%u%' OR tbl.methyl_red LIKE '%v%') AND (tbl.voges_pros LIKE '%+%' OR tbl.voges_pros LIKE '%u%' OR tbl.voges_pros LIKE '%v%') AND (tbl.citrate LIKE '%+%' OR tbl.citrate LIKE '%u%' OR tbl.citrate LIKE '%v%') AND (tbl.h2s LIKE '%-%' OR tbl.h2s LIKE '%u%' OR tbl.h2s LIKE '%v%') AND (tbl.phenylalanine LIKE '%+%' OR tbl.phenylalanine LIKE '%u%' OR tbl.phenylalanine LIKE '%v%') AND (tbl.ornithine LIKE '%+%' OR tbl.ornithine LIKE '%u%' OR tbl.ornithine LIKE '%v%') AND (tbl.motility LIKE '%+%' OR tbl.motility LIKE '%u%' OR tbl.motility LIKE '%v%') AND (tbl.lactose LIKE '%+%' OR tbl.lactose LIKE '%u%' OR tbl.lactose LIKE '%v%') AND (tbl.lysine LIKE '%+%' OR tbl.lysine LIKE '%u%' OR tbl.lysine LIKE '%v%')

That would be a massive pain to build by hand. To make this easier, we are going to create an associative array in our handleCommand function that has each choice that we want in the where statement and then use programming to create the rest. As you can see in the code, each item in the array is a string with the acceptable choices separated by a | character. For example if we are searching for (+) indole results, the where array would be:

::
    
    $where['indole'] = '+|u|v'

So in handleCommand we build a where array with all the choices for each test built into it. This then gets passed to selectSearchAnd. This is a function that we create to help build the where clause. Since we are using object-oriented programming and classes we can simple override the Doctrine class. In fact, MOST has already done that for us. Open up modules/StrainID/lib/StrainID/Entity/Repository/Strain.php and you will find a class all ready for you to override functions. Place the following code inside the class StrainID_Entity_Repository_Strain

::
    
    public function selectSearchAnd($items_to_search, $orderBy = '', $useJoins = true)
    {
        $where = '';    
        foreach($items_to_search as $key => $item){
             $item = DataUtil::formatForStore($item);
             $search_strings = explode("|", $item);
             $whereSub = '';
             foreach($search_strings as $search_string){
                 $whereSub .= ((!empty($whereSub)) ? ' OR ' : '') . 'tbl.' . $key . ' LIKE \'%' . $search_string . '%\'';
             }
             $where .= ((!empty($where)) ? ' AND (' . $whereSub . ')' : '(' . $whereSub . ')');
        }
        return $this->selectWhere($where, $orderBy, $useJoins);
    }

The function first builds the where statement by walking through the provided associative array. It then explodes the value string and puts each into a multiple OR statement. For example, the first $whereSub when finished with the inside foreach loop would read.

::
    
    tbl.indole LIKE '%-%' OR tbl.indole LIKE '%u%' OR tbl.indole LIKE '%v%'

Each of these multiply OR statements is then chained together with an AND to give a finished where clause as shown above. We then call the Base class function selectWhere. This will return all strains that match the test results we have specified as an array. Returning to our handleCommand function, we generate our strain table.

::
    
    //Now assign this to a template variable
    $view->assign('strains', $strains);
    //create the strain table.
    $strain_table = $view->fetch('strainTbl.tpl');
    /we need to pass this information to the page that will render this
    //This will save it over the session.
    SessionUtil::setVar('search_results', $strain_table);
    $url = ModUtil::url('StrainID', 'user', 'search');
    return $this->view->redirect($url);

Finally we have to do something a little tricky. The Form utility when done allows only redirection to another page, and will not directly send back text to be displayed. Therefore, we first save our search results in the session variable search_results and then redirect back to the strain search form. Remember that the strain search form looks for this variable and adds it to the template before rendering the page. On the second round, the results are displayed on the search form. The SessionUtil::setVar routine will allow you to add any variables you want to the session and these will be remembered until the session is over. 

That completes this tutorial of building a module and will hopefully give you an introduction into how to use MOST for module design and code rendering. Also, it gives you a first peek at how to override and customize your module.
