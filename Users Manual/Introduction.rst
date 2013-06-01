A Users Guide to Zikula
=======================
:Author:
    Timothy Paustian

History
-------

Welcome to using Zikula. Before we get into the nitty gritty details of using Zikula, lets spend some time talking about the history of Zikula and the goals of the developers. Zikula was born as a fork of the PostNuke project, which itself was a fork of php-nuke. PostNuke was born when the developer of php-nuke decided to take the platform commercial. PostNuke's goals were to enhance php-nuke and make the platform flexible and extendible. The concept of modules, that could expand the capabilities of the platform, and themes, where users and designers could develop skins to the PostNuke platform were introduced. A significant amount of progress was made in maturing PostNuke as a content management system (cms), but due to the initial design of the core code, a number of insurmountable road blocks were encountered and it became clear that the core has to be rewritten. This was especially necessary to separate the appearance of the site from the logic that controlled it. It was also desirable to introduce new and powerful technologies and place the cms on a strong foundation so that it could be easily expanded and maintained. Since the underlying code was so different, and because of naming conflicts, a new name was in order and Zikula was chosen by the developers. 

Zikula is an open source project hosted at GitHub_ with the current stable release host at the Zikula_ foundation web site. It is licensed under the standard GNU GPL and is free for anyone to use and modify.

Goals
-----

The first goal of Zikula is to be a simple to use, yet powerful cms and web application delivery service. Zikula comes with a straight-forward installation process that can get a site up and running in a matter of minutes. Once installed, the user can then focus on making the site their own. 

A second goal of the platform is to provide a powerful backend. As any good cms does, Zikula takes care of many of the boring tasks of serving pages and lets the web developer, and designer, focus on the innovative, and new stuff that makes their site one that people want to visit. The backend uses modern, standards compliant, techniques for delivering pages and saving data back to the database. All the buzzword goodness is there, Apache, MySql (and other databases), Doctrine, php, javascript and much more.  The core of Zikula is kept purposely small and is created using object oriented php. (If all these fancy programming works don't mean anything to you, no worries, its not something you need to understand. Just know that Zikula is a modern and capable system) What it does means, is that going forward Zikula should be more robust and easy to maintain and upgrade. 

A third goal of Zikula is flexibility. Using various extensions, Zikula can be easily expanded to serve any possible purpose a web site can fulfill. These expansions usually fall into two broad categories, creating content and giving the site its own unique appearance. To deliver content the user can install all sorts of programmatic extensions (called modules) that can perform many interesting tasks, including. 

* Serving static and dynamic content in any kind of flavor that you want.
* Calendar programs to plan and remind users of events
* Blogging in the News module
* Polling Software
* Legal Documents (privacy policy, Terms of Service, etc)
* and more
 
In addition if you have the energy, and a willingness to learn, you can become a developer and create your own modules.

The content you create can then be delivered in many different skins. In Zikula these are called themes. A theme uses templates laid out using an easy to understand templating language called smarty_ and standard html to create the appearance of your site. There are themes that are developed for Zikula out of the box and you can use these as a starting point to develop your own special style.

What You Should Know
---------------------

I do expect you to have a basic foundation before setting off on the journey of learning and using Zikula. I expect you to be familiar with using a web browser such as Firefox, Safari or Internet Explorer. You shoudl also know the basics of html_ markup language. Finally, you need to have patience and a willingness to ask questions when you get stuck. And you will get stuck. When this happens, visting the Zikula Forums_

Organization of this Manual
----------------------------

Before you can use Zikula, you need to install it, so the first section of this manual will go through getting Zikula up and running. In this we will talk about hosting requirements, disk space, installation and finally locking down your site. We will finish up this section with a quick tour of a newly installed Zikula site and talk about its basic set up.

The next section will teach you how to install modules into Zikula. We will talk about where to find modules, how to get them, and installing them using the Extensions Module. The rest of this section will cover installing some of the more common modules you may be interested in using and their features. 

A third section will look at themes used in Zikula and how to change the appearance of your site using the Themes module. It will take you through a tour of the structure of a theme and how you can customize it and customize the appearance of any module that you use in your website.

The fourth section will be a cookbook where we present common functionality that users of Zikula want to create in their sites and how you can do them in Zikula. 

Finally, we will close out the manual looking at Module and plugin development and how you, with a little programming background, can contribute to the Zikula project.

.. _smarty: http://www.smarty.net/
.. _GitHub: https://github.com/zikula/core
.. _Zikula: http://zikula.org
.. _html: http://www.w3schools.com/html/default.asp
.. _Zikula Forums: http://community.zikula.org/module-Forum.htm