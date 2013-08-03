======================================
User Interface and Strain Searching
======================================

:Author:
    Timothy Paustian

The user interface for our strain ID module is very simple. It lists the table of strains and provides a search interface where you can input test results and have the database call up all strains that match. This can save countless hours digging through tables to find a match.

View
----

The entry interface for our module will actually be the view function. (I am leaving the main function because we did that earlier in the tutorial). This will have a link to take a user to search for a strain and then also a table listing all the strains in the database. Here is the code for the handling the view interface (located at lib/StrianID2/Controller/User.php. It is called when a user navigates to yourZikulaSite.com/index.php?module=strainid2&type=User&func=view

::

    public function view()
    {
        // check module permissions
        $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_OVERVIEW), LogUtil::getErrorMsgPermission());
        $repository = $this->entityManager->getRepository('StrainID2_Entity_StrainID2');
        $strain_table = StrainID2_Api_User::generate_strain_table($this->view, $repository, false);
        $this->view->assign('strain_table', $strain_table);

        return $this->view->fetch('user/view.tpl');
        
    }

We first check permissions. Note that here we allow access to anyone who has overview permission, the most lenient. We want to this part of the interface to be fairly open since many visitors should be able to search for strains. We then generate the strain table in much the same manner as we did for the admin interface. First, grabbing the repository and then sending it to generate_strain_table. This time we tell it to not generated links to an editing interface for each strain by sending false as the third parameter. Users who visit this page may not be able to edit the strain data, so we don't want to create a bunch of dead end links that if clicked upon will lead to an access denied message. We then assign the table to the view and finally render the template by calling fetch on the view.

Here is the template for the view function (user/view.tpl). This should all be familiar to you now.

::

    {ajaxheader modname='StrainID2' ui=true}
    <h2>{gt text='Strain ID2'}</h2>
    
    {insert name="getstatusmsg"}
    {modulelinks type='User'}
    <p><a href="{modurl modname="StrainID2" type="user" func="search"}">{gt text="Search for a strain"}</a></p>
    
    {$strain_table}

Search
------

A user coming to the search interface wants to enter data that is very simlar to what gets entered when you are creating strain data. In a fit of economy, these two functions (create and search) share the similar templates. When search is called, the search function in /lib/StrainID2/Controller/User.php is called

::

    public function search($args)
    {
        $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID::', '::', ACCESS_OVERVIEW));
        // create new Form reference
        $view = FormUtil::newForm($this->name, $this);
        return $view->execute('user/search.tpl', new StrainID2_Form_Handler_User_Search());
    }
    
Again, the permission is overview, to allow many visitors to search. A search form is created, first by creating a new form view. The form is then executed, passing the class StrainID2_Form_Handler_User_Search to handle the class. The code for this class is located at lib/StrainID2/Form/Handler/User/Search.php. Here is the initialization code. 

::
    
    class StrainID2_Form_Handler_User_Search extends Zikula_Form_AbstractHandler {

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
        $view->assign('reaction', $reaction); // Supply items
        $strain_table = SessionUtil::getVar('search_results');
        if(!$strain_table){
            $repository = $this->entityManager->getRepository('StrainID2_Entity_StrainID2');
            $strain_table = StrainID2_Api_User::generate_strain_table($this->view, $repository, false);
        }
        $view->assign('strain_table', $strain_table);
        // everything okay, no initialization errors occured
        return true;
    }
    
We used this Zikula Form interface when working on the admin section of StrainID2, so this should be familiar. In the initialization function we again create the reaction array for the drop down menus in the form. We then see if there is any search data that has been created. If there was, it will be stored in the session variable, search_results. If this comes back negative, we just add the entire strain table to the bottom of the search form. We assign the $strain_table to the view and return true.

Here is the template for the Search function. Since it is very similar to the edit template, it is shown without comment.

::
    
    {* purpose of this template: build the Form to serach for matching strains *}
    {ajaxheader modname='StrainID2' ui=true}
    <h3>{gt text='Search for Strain'}</h3>
    
    {insert name="getstatusmsg"}
    {modulelinks type='User'}
    <div class="strainid-strain strainid-edit">
        <div class="z-admin-content-pagetitle">
            {icon type=edit size='small' alt=$templateTitle}
            <h3>{gt text="Search for a Strain"}</h3>
        </div>
        {form cssClass='z-form'}
        {* add validation summary and a <div> element for styling the form *}
        {formsetinitialfocus inputId='indole'}
        <fieldset>
            <legend>{gt text='Content'}</legend>
    
            <div class="z-formrow">
                {formlabel for='indole' __text='Indole' mandatorysym='1'}
                {formdropdownlist group='strain' id='indole' mandatory=true readOnly=false __title='Enter the indole reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='methylred' __text='Methyl Red' mandatorysym='1'}
                {formdropdownlist group='strain' id='methylred' mandatory=true readOnly=false __title='Enter the methyl red reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='voguesproskauer' __text='Vogues Proskauer' mandatorysym='1'}
                {formdropdownlist group='strain' id='voguesproskauer' mandatory=true readOnly=false __title='Enter the vogues proskauer reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='simmonscitrate' __text='Simmons Citrate' mandatorysym='1'}
                {formdropdownlist group='strain' id='simmonscitrate' mandatory=true readOnly=false __title='Enter the simmons citrate reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='h2s' __text='H2s' mandatorysym='1'}
                {formdropdownlist group='strain' id='h2s' mandatory=true readOnly=false __title='Enter the hydrogensulfide reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='phenylalanine' __text='Phenylalanine' mandatorysym='1'}
                {formdropdownlist group='strain' id='phenylalanine' mandatory=true readOnly=false __title='Enter the phenylalanine reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='lysine' __text='Lysine' mandatorysym='1'}
                {formdropdownlist group='strain' id='lysine' mandatory=true readOnly=false __title='Enter the lysine reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='ornithine' __text='Ornithine' mandatorysym='1'}
                {formdropdownlist group='strain' id='ornithine' mandatory=true readOnly=false __title='Enter the ornithine reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='motility' __text='Motility' mandatorysym='1'}
                {formdropdownlist group='strain' id='motility' mandatory=true readOnly=false __title='Enter the motility reaction for the strain' items=$reaction cssClass='required'}
            </div>
    
            <div class="z-formrow">
                {formlabel for='lactose' __text='Lactose' mandatorysym='1'}
                {formdropdownlist group='strain' id='lactose' mandatory=true readOnly=false __title='Enter the lactose reaction for the strain' items=$reaction cssClass='required'}
            </div>
        </fieldset>
    </div>
            
            {* include possible submit actions *}
        <div class="z-buttons z-formbuttons">
            {formbutton id='btnSearch' commandName='search' __text='Search' class='z-bt-edit'}
            {formbutton id='btnCancel' commandName='cancel' __text='Cancel' class='z-bt-cancel'}
        </div>
    {/form}
    {$strain_table}

The handleCommand function is where the new code is located. Here is all the code of the function.

::

    public function handleCommand(Zikula_Form_View $view, &$args) {
        if ($args['commandName'] == 'cancel') {
            $url = ModUtil::url('StrainID2', 'user', 'view');
            return $this->view->redirect($url);
        }
        if ($args['commandName'] == 'search') {

            //get the values from the form as an array
            $result = $view->getValues();
            $indole = $result['strain']['indole'];
            $methyl_red = $result['strain']['methylred'];
            $voges_pros = $result['strain']['voguesproskauer'];
            $citrate = $result['strain']['simmonscitrate'];
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
                $where['methylred'] = "+|-|u|v";
            } else {
                $where['methylred'] = "$methyl_red|u|v";
            }
            if ($voges_pros == 'u') {
                $where['voguesproskauer'] = "+|-|u|v";
            } else {
                $where['voguesproskauer'] = "$voges_pros|u|v";
            }
            if ($citrate == 'u') {
                $where['simmonscitrate'] = "+|-|u|v";
            } else {
                $where['simmonscitrate'] = "$citrate|u|v";
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
            
            $repository = $this->entityManager->getRepository('StrainID2_Entity_StrainID2');
            $strains = $repository->selectSearchAnd($where, 'name');
            //Now assign this to a template variable
            $view->assign('strains', $strains);
            $view->assign('is_admin', $do_edit_links);
            //create the strain table.
            $strain_table = $view->fetch('user/strainTbl.tpl');
            //we need to pass this information to the page that will render this
            //This will save it over the session.
            SessionUtil::setVar('search_results', $strain_table);
        }
        $url = ModUtil::url('StrainID2', 'user', 'search');
        return $this->view->redirect($url);
    }

If the user chooses cancel, we just redirect to the view. If the user chooses search, then the function first grabs the data by calling get results. This returns an array organized by group. Since we placed our data into a 'strain' group, strain is used as a key in the results array. The strain key is another array with all our data in it. It is easy to pull the data out by using array notation. For clarity, each value from the results array is placed into its own variable.

::

    //get the values from the form as an array
    $result = $view->getValues();
    $indole = $result['strain']['indole'];
    $methyl_red = $result['strain']['methylred'];
    $voges_pros = $result['strain']['voguesproskauer'];
    $citrate = $result['strain']['simmonscitrate'];
    $h2s = $result['strain']['h2s'];
    $phenylalanine = $result['strain']['phenylalanine'];
    $ornithine = $result['strain']['ornithine'];
    $motility = $result['strain']['motility'];
    $lactose = $result['strain']['lactose'];
    $lysine = $result['strain']['lysine'];
    
The major trick in making a functional search is building a where statement. We do this by creating an array containing the search parameters for each data point. When we do the search, this where statement will be expanded into something doctrine can understand.

::

    $where = array();


            if ($indole == 'u') {
                $where['indole'] = "+|-|u|v";
            } else {
                $where['indole'] = "$indole|u|v";
            }
            if ($methyl_red == 'u') {
                $where['methylred'] = "+|-|u|v";
            } else {
                $where['methylred'] = "$methyl_red|u|v";
            }
            ....

After our where array is built, we grab the repository and call selectSearchAnd with our where array, telling it to order the result by the name of each item. The results are assigned to a variable in the template and then rendered. The finished render template is passed into a session variable. We then redirect to the search interface. The session variable is now set, and thus when it is rendered, the search results will be placed at the bottom of the search page.

The last thing to explain is the selectSearchAnd function located at lib/StrainID2/Entity/Repository/StrainIDReposotory.php

::
    
    public function selectSearchAnd($items_to_search, $orderBy = '')
    {
        $where = '';    
        foreach($items_to_search as $key => $item){
             $item = DataUtil::formatForStore($item);
             $search_strings = explode("|", $item);
             $whereSub = '';
             foreach($search_strings as $search_string){
                 $whereSub .= ((!empty($whereSub)) ? ' OR ' : '') . 'a.' . $key . ' LIKE \'%' . $search_string . '%\'';
             }
             $where .= ((!empty($where)) ? ' AND (' . $whereSub . ')' : '(' . $whereSub . ')');
        }
        return $this->getStrains($orderBy, $where);
    }
    
This builds the Doctrine query language state by iterating through the $items_to_search array and building each where statement. What this does is convert the data in the array, for example

$item[indole] => +|u|v 

into

"a.indole LIKE '%+%' OR a.indole LIKE '%u%' OR a.indole LIKE '%v%'"

What this means is, match any strain where indole is + OR u OR v. A completed where statement will look like this.

"(a.indole LIKE '%+%' OR a.indole LIKE '%u%' OR a.indole LIKE '%v%') AND (a.methylred LIKE '%+%' OR a.methylred LIKE '%u%' OR a.methylred LIKE '%v%') AND (a.voguesproskauer LIKE '%+%' OR a.voguesproskauer LIKE '%u%' OR a.voguesproskauer LIKE '%v%') AND (a.simmonscitrate LIKE '%+%' OR a.simmonscitrate LIKE '%u%' OR a.simmonscitrate LIKE '%v%') AND (a.h2s LIKE '%+%' OR a.h2s LIKE '%u%' OR a.h2s LIKE '%v%') AND (a.phenylalanine LIKE '%+%' OR a.phenylalanine LIKE '%u%' OR a.phenylalanine LIKE '%v%') AND (a.ornithine LIKE '%+%' OR a.ornithine LIKE '%u%' OR a.ornithine LIKE '%v%') AND (a.motility LIKE '%+%' OR a.motility LIKE '%u%' OR a.motility LIKE '%v%') AND (a.lactose LIKE '%+%' OR a.lactose LIKE '%u%' OR a.lactose LIKE '%v%') AND (a.lysine LIKE '%+%' OR a.lysine LIKE '%u%' OR a.lysine LIKE '%v%')"

Once this where statement is make, we call getStrains.

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
    
This builds the Select statement in doctrine query language, creates the query and then grabs the result. Getting the result is wrapped in a try statement and if it fails, the user is provided information on why it failed. If successful, the result is returned to the caller.

That is all the code in the StrainID2 module. If you have worked through this, you have a good understanding of how to code a module and get it running. When creating your modules from scratch, it is often useful to start with a simple module and build from there. You are welcome to use this module as a template. Better yet, now that you understand the basics of module creation, go on to learning about MOST in chapter 6 of this User Manual. MOST automates much of the module creation tasks.