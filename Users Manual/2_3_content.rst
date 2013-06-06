Setting Content
---------------

Now that we have the appearance as we like, it's time to start adding content. To add content to Zikula, you need to install a module that will take the content in, store it in the database, and then display the content when asked. There are two very common forms of content that websites post, blogs and documentation. Blogs are time-sensitive information that reports what is going on right now. They can have a different focus depending upon your sites goals or your interests. For our example, it could be tips and comments about the growing season. When to plant flowers, significant weather events, etc. Documentation is more permemant information that visitors may want to read at any time. For the Gardening site this could be comments on the best fertilizer, what kinds of flowers to plant in shady areas or ways to deal with rabbits. Zikula has different modules to deal with either of these.

The Administration Interface
---------------------------

Before getting into setting up the News module, lets generally talk about the administration interface. Modules will usually have administration settings and you click on the Administration link to get to them. This is laid out in a tabbed interface. When modules are first installed they are placed in the uncategorized tab. (This is user configurable in the Administration Panel). To get to a modules Admin interface, just click on its image or name. Each module will have a menu of potential commands to choose from depending upon the module. 

The News Module
----------------

The News Module is the blogging application that is available for Zikula. Before we install the News Module, we are going to install two helper modules that enhance the capabilities of the News module. One is called EZComments that allows the ability to add comments to content from any other module. The other is Scribite. This adds WYSIWYG editing capabilities to forms. EZComments and Scribite are what are termed hookable modules. If the other module has been written in such a way that it can communicate with these modules, then they can hook into Scribite and EZComments and add their capabilities to the module.  

#. Download the News_, EZComments_, and Scribite_ modules and unzip. Drop the unpacked modules folders into Zikula/modules/. Make sure the names of the folders are News, EZComments and Scribite
#. Go to the admin page of your website. Click on the System tab, and then the Extensions Module. A list of all the available modules for you website will appear. 

.. figure:: ModulesPanel.png
    :alt: The modules panel showing News, EZComments, and Scribite
    
    The Modules Panel showing the uninstalled News, EZComments, and Scribite modules. To install the modules, click on the little green (+) pages.

3. To install the modules, Click on the little green plus page. Install the EZComments and Scribite modules before the News Module. The EZComments module can work with the Akismet module to prevent spam comments. For a production site I recommend using this, but for now, we will skip it. Just click Accept when asked about the Akismet module while installing EZComments. 
#. The green (+) should go away and the module should change to active. First, check out the news module by clicking on it s link.
#. There is already an initial demonstration article. Click on the view icon (the eye at the far right in the table) and it will open the article up. You can now read this article. Click on the Edit link to open up an editing form.

.. figure:: NewsEditingForm.png
    :alt: The News Editing Form
    
    The News Editing Form. Here is the interface for editing news articles. Note that we have not added Scribte module capabilities yet.

6. You will notice to write content in this form, you have to just type it in. However it would be nice to have a more sophisticated editing form. This is what Scribite can give us, so lets hook that up now. Click on the Uncategorised tab and then on the News Module. Now click on the Hooks link at the top of the News module interface.  On the right you will see modules that can hook to the News module. On the left you will see functions in the News with which the hook modules can interact. Both the EZComments and the Scribite modules interact with the ui and do not filter the news text. Therefore, drag Scribite from its location on the right, to the News Articles Hooks area.

.. figure:: HookDragging.png
    :alt: Adding the Scribite hook to the News Module
    
    Hooking Scribite to the News Module. This configures the Zikula software to notify Scribite whenever the editing interface for News is shown.

7. While we are here, also add the EZComments hook to the News Articles hook. Make sure that the EZComments hook is first in the list.
#. Navigate back to the News article list (click on News articles list on the left) then open the News introduction article for editing (Click on the red pen). You now have a WYSIWYG editing interface! To be able to add fancy formatting to your text, you do need to change the page type from Plain text to Text formatted with mark-up language. There are all sorts of configuration options in the Scribite Module.
#. You can also see how EZComments affected your article. Go to the News Introduction Article and click on the eye to view it. At the bottom you will see a comment box. Type a comment and hit submit. Many content modules can have comments added to them.
#. At this point you might realized that this is ripe for spamming and abuse. EZComments has some good features to prevent spam and you can modify them in the Admin section of EZComments. To begin, lets lock them down tight, and you can later on decide to loosen them up. Go the the Uncategorised tab of the Administration menu and click on Comments
#. Click on Settings. Under Notification, check send mail on comment requiring notification.
#. Under Moderation, click on Enable Moderation and All comments require moderation. Click on submit. 
#. You have now set up your site so that every comment has to be viewed by you before it is allowed to be posted. This will prevent all spam, but requires work from you. 

- Using Hookable Modules (EZComments and Scribite)
- Adding a Blogging Module (News)
- Adding a Content Module (Content)
- Setting up user groups
- Adding users
- Adding permissions for specific groups
- Setting up allowed HTML tags

.. _News: https://github.com/zikula-modules/News
.. _EZComments: https://github.com/zikula-modules/EZComments
.. _Scribite: https://github.com/zikula-modules/Scribite