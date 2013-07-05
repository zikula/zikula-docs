.. _install: https://github.com/paustian/zikula-docs/blob/master/Users%20Manual/2_1_installation.rst
.. _Previously: https://github.com/paustian/zikula-docs/blob/master/Users%20Manual/5_1_Module_Programming.rst

Installing the module
---------------------

Previously_, we installed and introduced MOST and then showed how to model a module and generate its code. Here we install the module and give it a test drive. To install the module, drag the StrainID folder that is found inside the zclassic/src folder into your modules folder and install_ it. If you don't know how to install modules in zikula, what are you doing here?!!!

Once your module is installed, wonder at that. The module installed and added a strain table to your database, ready to record your data without you doing anything. You can examine your database with phpMyAdmin or whatever your favorite tool is and it will be there.

While in the extensions module, click on the name StrainID and note that there is a stub admin and user page already made for you. MOST has taken care of writing much of the backend code for a module, saving you tons of time. Cool!

We will obviously want to make improvements to the user interface. So here is how we want our interface to look. When a user comes to the main page, we want a link going to a search page. It would also be nice to display the entire strain table, just for examination. (This table will only have about 116 entries when done. so that won't tax the server.) The search page should have a form, where the user can input test results and a search button to start the search. Below the search form, the results of the latest search should be displayed.

In the main page of the admin section, there should be a link to a form for adding data. In addition, a table of all the strains could again be displayed, but in this case, each strain name should be a link to a form where that strain can be edited. When you first start learning how MOST does things, the really difficult part is figuring out where to put your code and using the right technologies to access the database and render your code. Adding the concrete code to the StrainID module will hopefully give you a head start up that learning curve.