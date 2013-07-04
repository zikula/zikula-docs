.. _Studio: http://modulestudio.de/en/
.. _Download: http://modulestudio.de/download/MOST-0.6.0/
.. _installation: http://modulestudio.de/en/tutorial/installation-on-various-platforms.html
.. _tutorials: http://modulestudio.de/en/tutorial
.. _basic: http://modulestudio.de/en/tutorial/basic-usage.html
.. 
Using Module Studio
--------------------

Before we begin programming, I want to introduce a tool we will be using for this purpose. Module Studio_ (MOST) is a data modeling and code generation utility developed by Axel Guckelsberger and his team. This is powerful graphic design software that helps you create robust, compliant, maintainable, and extensible Zikula modules. For this tutorial, we will be using MOST to design our module. Download_ the MOST application for your operating system and follow the installation_ instructions. As of this writing version 0.6.0 was available. Axel has created a extensive collection of tutorials_ that show you how to use the application, with videos even! Please take the time to go through basic_ usage and the first `Zikula application in 10 minutes <http://modulestudio.de/en/tutorial/the-first-zikula-application-in-10-minutes.html>`. What you learn there will make what I present in this part of the tutorial much easier to understand.
Creating the StrainID? data model in MOST
After learing the basics of MOST, it's now time to start creating our model. You did read up on MOST and go through those tutorials right? RIGHT? If you didn't, do it now. Don't worry I can wait.....

All set? Good! Now start up MOST and after it loads you will see the initial MOST modeling window. You may be greeted with a welcome screen, if you are, click on the farther right-most icon on the top tool bar. This should shrink down the welcome screen and you should see an outline pane, a properties pane and a main model view area. (The main model view area is not listed just yet.) Choose File->New->Application Diagram and you will be asked to fill in your name, the name of the application (Note that this has to start with a capital letter and be more than 5 characters long), your email and a url. Click on next and verify that it is giving your app a reasonsable name and saving it to an acceptibel directory. When you are satified with everything, click on Finish.
A new file will be generated and the main model view will appear. On the left will be the palette, and on the right the modeling pane. In the modeling pane are three boxes, Model, View and Controller. If you have studied any programming you will have run into this method of creating applications, the Model-View-Controller paradigm. It is a widely accepted method of creating applications, especially when using object-oriented programming. The Zikula 1.3 framework uses OOP and classes to design its model and the MVC methodology dovetails with this nicely. Explaining OOP and MVC modeling is beyond the scoop of this tutorial and to learn more pick up a good book on programming. It is not necessary to understand OOP to follow the creating of the StrainID? module, but it does help and you should familiarize yourself with the concepts of OOP and MVC if you are serious about programming.

Creating the Model
Double-click on the Model box to call up the modeling area. Create the one and only data table by clicking on the Entity tool and then clicking and dragging in the model area. The name of the entity is strain, the multiple is strains and set leading to true. I have found editing these values in the properties view to be the easiest. (This may be a Mac OS X issue in that editing them in the main model view often does not take.)
Next, click on String in the field types, making its Name, name, set leading to true, and leave mandatory as true. We will now enter all the test values. Remember that these are 1 character long and can only have the values +,-,u or v. For each field, create it using the string tool, name it as shown on the table below, set its default value to +, set it Leading to false and Size to 1.
When you are finished, you should have a table with 11 values in it. We have now defined our model for this application. Close the Model window and go back to the main model view, now double click on Controller.
+-----------+-------------------+-----+--------+
| Item Name | Description       |Type |	Size   |
+===========+===================+========+=====+
| name      |name of the species| string | 255 |
+-----------+-------------------+--------+-----+
| indole    | indole test       | string |  1  |
+-----------+-------------------+--------+-----+
|methyl red |methyl red test    | string |  1  |
+-----------+-------------------+--------+-----+
|vogues proskauer|vogues proskauer test|string|1|
+-----------+-------------------+--------+-----+
|simmon's citrate|simmon's citrate test|string|1|
+-----------+-------------------+--------+-----+
|H\ :sub:`2`\ S|production of hydrogen sulfide|string|1|
+-----------+-------------------+--------+-----+
|phenylalanine|deamination of phenyl alanine|string|1|
+-----------+-------------------+--------+-----+
|lysine     |lysine decarboxylation|string| 1  |
+-----------+-------------------+--------+-----+
|ornithine  |ornithine decarboxylation|string|1|
+-----------+-------------------+--------+-----+
| motility  | motility test     | string | 1   |
+-----------+-------------------+--------+-----+
| lactose   |lactose fermentation|string | 1   |
+-----------+-------------------+--------+-----+

We will be making two controllers, Admin and User that correspond to the two ways that the data can be manipulated. An Admin and create, edit and delete strain data. A user can search for strains, view results and view all the strains in the database. 
Click on the Admin tool and drag a box in the Controller pane. To this box add main, display, edit and delete actions. Now click on the User tool and add a main action. We also want a custom search action available for the user. Click on the custom action tool and drag a box inside the user controller. name this action Search. We have now created our model and controller. At the present time it is not possible to create views in MOST. Instead, MOST make a good guess at what you want and generates the code to create these. They are then custom designed as we modify the code that MOST creates. Choose File->Save to save all your changes.
We are now ready to generate the code. To do this, choose File->Generate Application and point at a folder where the generated code will go. Choose this location carefully. I recommend every time you generate an application, you create a new folder, then load in any modified files into the new folder. So pick your folder carefully and choose open in the dialog.
A second dialog will come up asking what cartridges you want made. The only one that is important for making a Zikula application is the zclassic cartridge. The others are for other purposes and are beyond the scope of this tutorial. Make sure at least zclassic is checked and hit OK. If you choose reporting, another dialog will come up asking which reports to generate. When you are all ready, click OK and the application will generate your code and then beautify it. 
Now lets examine what MOST created by going to the folder you specified for creation of the modules. Depending upon what you ask to be generated, you will see a number of folders. For you module, we are interested in the zclassic folder; open it up.
Inside zclassic is a README file, a src folder, and a test folder. Open src/modules and inside it is your generated module, StrainID?. Open this folder and inside you will see the typical arrangement for a Zikula 1.3 and greater module. The folders you will see are
- docs
- images
- javascript
- lib
- locale
- style
- templates
- workflows
These will also contain folders and files inside of each of them. Much of our work will happen in the lib and template folder. If you open up the lib folder you will see a StrainID? folder and inside of that, this structure.
- Api
- Base
- Block
- ContentType
- Controller
- Entity
- Form
- Installer.php
- Listener
- RouterFacade.php
- Util
- Validator.php
- Version.php

Don't be intimidated by all the different folders and files. Making the necessary modifications is actually pretty straight forward, you just need to know where to make them. One very important trait to note, make sure you override the concrete class of each file, and not its base class. The base file is generated by MOST and will be replaced if you generate a new version. The base files are always kept in the Base directory, while the concrete file for you to edit, is in folder with the Base folder. For example, the Installer.php file is in the StrainID? folder (as shown above) while its base class is in the Base folder.
In the next page, we will install our module and test it out.