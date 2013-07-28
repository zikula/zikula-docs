.. _Structure: 5_2_Basic_Module_Structure.rst

------------------------------
Display with Template Engines
------------------------------

In the previous section we returned text directly to the zikula api. That is great for learning, but it completely defeats the purpose of using a database and a CMS. The goal is to separate your data from the html code that displays that data. When done well, this allows the changing of the appearance of a site, its theme, without affecting the data that is displayed. This magic is accomplished using templates and views, which is a Zikula extension of template engines. Right now Zikula supports both Smarty and Symphony templates. Here I show how to use templates that use smarty extensions, but as I learn symphony I update the tutorial. In this section we will begin to explore how to use templates to display your content. If you have not already, familiarize yourself with the Basic Module Structure_ before proceeding

This is an extremely important step forward with web applications. It is similar to the idea of database reports that display data from a table or query.

Templates are stored in the modules own templates subdirectory. The naming scheme is to organize templates based upon who is using them (admin, user, search) and then giving them the name of the function that called them. For example, if your main() function in the Admin.php controller is called, it was render to a template at templates/Admin/main.tpl

A Few Words About Template Engines
----------------------------------

Templating engines are a very powerful and can greatly simplify the generation of html display code from a database. To use it in Zikula you follow four steps.

* Get an instance of the template from zikula. This is available to any class that inherits from Zikula_AbstractController.
* Get any necessary data that is required for the template from the database (or elsewhere).
* Assign this data to template variables using calls to assign. Assign takes two parameters, the template variable to assign the data to, and the variable you are adding. A typical call would be $view->assign('myname', $myname);. It is often a good idea to use the same variable name for the template variable as well as the function variable.
* Finally, generate the finished html code by calling fetch. Calling fetch tells the template engine to generate the specified template using the variables that we previously assigned to this view object. The engine will return the generated html code.

The best way to learn templates is to work with them so let's explore an example where we actually use view to return code to the browser (building upon our previous "hello world" User.php code snippet):

::

    public function main() {
        // Get the username of the current logged in inidividual
        $myUid = SessionUtil::getVar('uid');
        if ($myUid) {
            $myUser = UserUtil::getUsers("uid=$myUid");
            $myName = $myUser[$myUid]['uname'];
            //also find out when they logged in
            $lastLog = $myUser[$myUid]['lastlogin'];
        } else {
            $myName = "anonymous";
            $lastLog = "Never, loser";
        }
        // Assign our variables to the template - this create a variable called 'myname' 
        // and assigns the value in $myname so it can be used in our template.
        // Please note that keeping variables sensibly named and consistent is very helpful.
        // You don't HAVE to do this, but you're asking for confusion if you don't
        $this->view->assign('myName', $myName);

        // this create a variable called lastlog and assigns the value in $lastLog so 
        //it can be used in our template.
        $this->view->assign('lastLog', $lastLog);

        // the fetch call, tells the template engine to process the template
        //and return the results
        return $this->view->fetch('user/main.tpl');
    }



The only other part to fill in is the template. Templates are a mixture of html code and variables. You can layout the html code any way you like. Any legal html tag can be used in a template. Honestly, you can put anything you like in a template, but if you are displaying html code, the finished product should meet the latest html specification. Lets start with a simple tempate. Here are the contents of templates/user/main.tpl:
  <div>
    <p>Hello World!  My name is {$myName} and last time I logged in was {$lastLog}.</p>
 </div>

Note how our two variables are identified, the view tags in Zikula are started by { and ended by }

In the example above {$myname} simply means insert the variable called $myname - this was the variable we assigned in our code before 'fetching' the template. If you like you can set this up by creating the template and saving it at templates/users/main.tpl. Once you have this set up, call the function by going to this URL http://yourzikulainstall/index.php?module=StrainID2&type=user&func=main

 If you get an error saying that the page cannot be found, read the message carefully and it will tell you what the problem is. A common cause of failure is that the template name in the fetch call will not match the template name and path in the templates directory of the StrainID2 module.

This gives you a gentle introduction to templates. There is A TON of magic you can do with templates so dig into them. Just for fun, lets look at the debug interface for zikula templates. This is a very powerful console you can call up to check out what is happening in your template and it is very easy to do. Just add {zdebug} anywhere in the user/main.tpl template and reload the page. It loads as normal, but a console pops up showing you all the template variables that are available. Open up the pncore variable and you will see that the uname and lastLogin were already available to us and we could have just used them by adding {$pncore.user.uname} and {$pncore.user.lastLogin} to our page. 

Plugins
-------

Plugins are functions we can call while inside the display template. Smarty and Sympony, which view is based on, has several built in 'plugins' or functions. We can also use Zikula system wide plugins, or define our own for our specific module. Plugins can be passed arguments like a normal function.

Zikula system wide plugins can be found in [root]/lib/viewplugins and module specific plugins in our module folder in templates/plugins

Plugin files are named function.pluginname.php and contain *one* function inside called
smarty_function_{$pluginname}. With symphony this will change.

A plugin is called within a template like this:
{myplugin}


The example above will include the file in templates/plugins/function.myplugin.php and call the function

smarty_function_myplugin()

Lets walk through a simple example that checks when the person last logged in and chides them if it is more than a month. First, add this line to the bottom of your template user/main.tpl.

{logcheck lastLog=$lastLog}


The example above will include the file in templates/plugins/function.logcheck.php and call the function smarty_function_logcheck() and parse the variable 'lastLog' to the function.

Variables are passed in an associative array of (name => value). Write the following code in any text editor and then save it as the file function.agecheck.php in the StrainID2/templates/plugins/
directory
<?php
//example plugin logcheck

function smarty_function_logcheck($params, &$smarty)
{
    // pull the login info variable out of the $params
    $lastLog = (int)$params['lastLog'];
    //This calculates how much time has passed since your last login in days
    $days = floor((time() - strtotime($lastLog))/86400);
    //We say a message depening upon when you last logged in.
    if($days > 0){
        $result="It has been $days days since you logged in";
    } else {
        $result = "You logged in earlier today. Get a live will ya?";
    }
    return $result;
}
?>


The $smarty object will be the same $view instance that called the plugin. This means the plugin can also modify the $view object. Our function could do something like 

$smarty->assign('foo', 'bar');


Which would mean the variable $foo would now be available to the template. You may be wondering why would you use a plugin, when you can just add the variable to the template? Plugins are very valuable for two reasons. First, they can be reused in many templates by adding a single line that calls them. Second, they can use php code to process a template while it is being rendered. Some very powerful things can be done with them.

Modifiers
---------

A modifier is a different kind of plugin. They are found system wide in [root]/lib/viewplugins and module specific plugins in our module folder in templates/plugins

Files named modifier.nameofmodifer.php and contain one function called smarty_modifier_nameofmodifer
will be passed at least one string. And they will then process that string. One system wide modifier, safehtml, will clean a template variable for display as html code.

<!--[ $myname|safehtml ]-->


Its code is as follows

::
    
    function smarty_modifier_safehtml ($string)
    {
        return DataUtil::formatForDisplayHTML($string);
    }


So lets add a modifier that bolds the date if it is this month. Clearly an active user. Place the following code in a text file and then save it as modifier.activeuser.php in the StrainID2/templates/plugins directory.

<?php
function smarty_modifier_activeuser($string)
{
    $date = strtotime($string);
    $days = (time() - $date)/86400;
    $timelapse = "";
    
    if($days < 30){
        $timelapse="<b>$string</b>";
    } else {
        $timelaspe=$string;
    }
    return $timelapse;
}
?>

Now add change the code slightly in your template; change 

{$lastLog}

to

{$lastLog|activeuser}

When you reload the page, the age of the variable should be bolded if it has been less than 30 days. Modifiers can be very powerful ways of processing text after a variable has been created.