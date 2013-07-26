.. _Structure: 5_2_Bacis_Module_Structure.rst
.. _StrainID2: https://github.com/paustian/Blank_StrainID2
.. _tutorial: 6_2_Module_Programming.rst
.. _NetBeans: https://netbeans.org/downloads/
.. _installing: 2_3_Content.rst

======================================
Calling Conventions and Initial Setup
======================================

:Author:
    Timothy Paustian

In this section we will cover the basics of how a module functions. You should be familiar with the Basic Module Structure before proceeding to this stage.

Calling a Module
================

The Zikula Application Framework takes standard arguments in the calling URL. It is also possible to call Zikula modules within code, but this will be covered later. The arguments will be explained.

Zikula runs exclusively from the index.php although we do have some wrapper scripts which will be covered later.

index.php expects three arguments module, type, and func in the form 

index.php?module=<module>&type=<type>&func=<func>

module
------

The name of the module you want to call. If not specified will default to the module specified in the Zikula System Settings. In a standard install this is often the News module. So if you provided this link,

index.php?type=<user>&func=<view>

Zikula would look in the News module use the file lib/News/Controller/User.php and call the function view() in the php class News_Controller_User. Zikula will know to get all files required from the module's own directory.

type
----

The type argument tells Zikula which file to find the function required. In effect you tell Zikula to include the file User.php (in the module's own folder determined from the module argument above. If not specified, defaults to User.php. Zikula always looks in the lib/ModuleName/Controller folder

eg.
type=user means include the file lib/ModuleName/Controller/User.php (default if type not specified)

type=admin means include the file lib/ModuleName/Controller/Admin.php

type=test means include the file lib/ModuleName/Controller/Test.php


func
----

The func parameter tells Zikula which function to call in the included file. The default value for func is 'main' if not specified.

e.g.
func=test calls the function test() in the class specified.

Examples
--------

Here are a number of examples to help you get a feel for how the calling process works. In your own modules you are expected to follow this scheme. It makes all modules standard, so others can understand your code and allows others to call functions within your module from other modules. I have also included examples of what the URL will look like if you turn on short urls for you modules in parentheses. Short urls are a human-friendly and search-engine-friendly way for your modules to be called.

index.php?module=helloworld&type=test&func=add (helloworld/test/add)
    look in modules/Helloworld
    
    include lib/Helloworld/Controller/Test.php
    
    call the function add() of the class Test_Controller_User
    
index.php?module=helloworld&type=user&func=show (helloworld/user/show)
    look in modules/Helloworld
    
    include lib/Helloworld/Controller/User.php
    
    call the function show() of the class User_Controller_User

index.php?module=helloworld&type=&admin&func=view (admin functions don't use short urls)
    look in modules/Helloworld
    
    include lib/Helloworld/Controller/Admin.php
    
    call the function view() of the class Admin_Controller_User

index.php?module=New&type=admin (admin functions don't use short urls)
    Look in modules/News
    
    Include lib/News/Controller/Admin.php
    
    call the function main() of the class Admin_Controller_User- (default: func=main)

index.php?module=News (news)
    Look in modules/News folder
    
    Include lib/Helloworld/Controller/User.php (default: type=user)
    
    call function main() of the class User_Controller_User - (default: func=main)

In summary, we can call specific functions from the URL by telling Zikula the module name, the file type, and the function name we wish to call. We will now explore functions a little more.

Writing the Code for a Module
------------------------------

Now it is time for some code. Before we can write our first function, we have some preliminary set up to do. The steps are

1. Get the new module folder and files (we will begin with a blank module)
2. Rename the module folder and all the files in the module to your module name. In this case rename the folder to StrainID2. All the other files should be named correctly.
3. Add our code to the module
4. Install and activate the module

Create the new module files
The latest distributions of Zikula do not contain a blank module. For this tutorial I have created a blank StrainID2_ module that will work. This is a bare bones module that contains stubs for the files that you need to create a module in Zikula. Realize that this is a simple example to get you started. A better way to go in my opinion is using MOST and there is a tutorial_ on gettig started with MOST programming. Unzip the archive of the blank module and drop it into your modules directory just like any other module. Do not install it yet, we will do that after we have made some modifications.

1. Rename the module folder and the module name in all the files to StrainID2
#. Change the name of the folder to StrainID2. The files have the correct names in them, but when you do your own module, you will want to rename every instance of the module you are copying to the name of your module. It would be useful to use the search and replace function to modify all these files, and you may find using an IDE such as NetBeans_ a big help here. Every instance should be be changed, but if you miss a few, it should still work.
#. Open up the /lib/StrainID2/Version.php file and add the following code.

::

    class StrainID2_Version extends Zikula_AbstractVersion
    {
    
        public function getMetaData()
        {
            $meta = array();
            $meta['displayname'] = $this->__('StrainID2');
            $meta['url'] = $this->__(/* !used in URL - nospaces, no special chars, lcase */'strainid2');
            $meta['description'] = $this->__('Tutorial Module For Zikula');
            $meta['version'] = '1.0.0';
    
            $meta['securityschema'] = array('StrainID2::' => '::',
                'StrainID:Strain:' => 'Strain ID::');
            $meta['core_min'] = '1.3.3'; // requires minimum 1.3.3 or later
            $meta['core_max'] = '1.3.99';
            
            return $meta;
        }
    }

This gives information about your module and getMetaData returns an array giving the details. The name and version are especially important. The *display name* is what admins and users will see as the name of your module. The *description* explains the purpose of your module. The *version* is used by Zikula to determine if an upgrade is needed. If the admin installs a newer version of your module, Zikula will notify the admin that an upgrade needs to take place and this will call the upgrade function in Installer.php. This allows the module developer to change the data model of a module and provide an upgrade path for old users of the module. The *securityschema* explains the convention for creating permission rules for your module. Your module is designed so that it follows this schema and can then interface with the permissions functions of Zikula. Finally the *core_min* and *core_max* indicate what versions of Zikula your module is designed to work with. (Don't worry if some of this does not make sense, we will explain it as we go along in the programming)

4. We also need to add some code to the installer file to be able to install it in Zikula. When you try to install a module, Zikula expects to find an install() function inside Installer.php. So lets add the stub functions that are needed. We will fill them in later. Put this code in lib/StrainID2/Installer.php

::

    class StrainID2_Installer extends Zikula_AbstractInstaller
    {
    
        public function install()
        {        
           return true;
        }
        
        protected function createDefaultData()
        {
            
        }
        
        public function upgrade($oldversion)
        {
            $this->throwForbiddenUnless(SecurityUtil::checkPermission('StrainID2::', '::', ACCESS_ADMIN), LogUtil::getErrorMsgPermission());
    
            switch ($oldversion) {
            }
    
            return true;
        }
    
        public function uninstall()
        {
           
            return true;
        }
    
    }

You should now be able to install your module. Go to you admin panel of your Zikula site and install the StrainID2 module.

1. Login as administrator
2. Goto Administration -> Exstentions
3. Find your StrainID2 module
4. Activate the module

There is more information on installing_ modules in the Admin section of the User Manual.

Module functions
----------------

Module functions should return a mixed result, or boolean: they should never echo or print anything directly (to the screen). Information returned from the interface modules (lib/Controller/User.php and lib/Controller/Admin.php) will very often be text. (Other information can be returned, but we will cover those cases later) Zikula will take the return value of the module function and process it, ultimately adding it to the appropriate place in the page layout and displaying it to the user. Later we will learn about views and how to display content using page templates. Using views and template is the preferred method for Zikula (in fact you really don't have any choice), and very powerful. But for now we will keep it simple to demonstrate how to get your module working and displaying  text on the screen. Open up User.php file and type the following code for the class.

::

    class StrainID2_Controller_User extends Zikula_AbstractController
    {
        public function main(){
            return "Hello World";
        }
        
    }

Zikula uses php object oriented programming (oop). This make available to you many powerful functions that you can use. It also makes future upgrades of the Zikula core easier. For our example here, we create a class StrainID2_Controller_User that extends the abstract controller class Zikula_AbstractController. Go look at the code for Zikula_AbstractController and you will see that it maintains a view variable, which is what we can render templates with, and then some housekeeping functions that takes care of setting up the view for us. All we need to do is concentrate on rendering our interface. For this simple example, we are just going to return Hello World. 

To test the code point your browser at ZikulaFolder/index.php?module=strainid2. This will call the main() in lib/Controller/User.php. Zikula will now display 'Hello World!' in a nice little box in the center of the content window. Notice how it's rendered with all the rest of the page content. Congratulations, you have come a long way in understanding the module framework and how to get it up and running. In the next installment, we will work on fleshing out the StrainID module.