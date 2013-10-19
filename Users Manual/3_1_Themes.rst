Working with Themes
===================

:Author:
    Timothy Paustian
    
In the installation chapter we talked about configuring_ your theme. This is great if you find a theme that has *just* what you are looking for, but in most cases you are going to want to customize that theme to meet your needs. This chapter will give you an introduction into working with themes. It is assumed you have a working knowledge of writing html code using style sheets. For a refresher, the W3School has an excellent set of web pages to get you started.

When the web was new, those many years ago, all the data of a page (content and layout code) were put together. It  quickly became clear that seperating content from layout would save time and effort. One layout could be designed for a page, and the content could then be separated. A theme is a set of templates that describes how to layout your site. You then put in content various places where you say it is allowed in your overall design. Zikula themes are incredibly flexible. You can dictate, as fine-grained as you like, where content goes, and what style is applied. In this chapter we will go through theme organization,  overall configuration, master/home templates, includes, plugins, block configuration, , and module overrides.


Theme organization
==================

Themes are located in the themes folder (what a surprise!) of your Zikula folder. There is a standard layout for every theme. For Zikula 1.3.5, the theme layout contains 5 folders: image, javascript, plugins, style, and templates. There is also a version.php file that lists the version of your theme. 

docs
====

This folder contains any documentation that is needed explaining how to use the theme. In many cases there will be no documentation because themes are self explanatory. Most often what you will find here is a license explaining where you can use a theme.
images
======

The images folder contains all the images that are used in your theme. The theme engine in Zikula knows to look in this folder if an image is referenced in your code. For example

::

    <img src="{$imagepath}/anImage.jpg" />


Will cause a correct link to the file anImage.jpg in the images folder of your theme. This directory should store any theme specific images. Do not store any other images inside this directory, such as for content. Those should go in the site images folder.

style
=====

The style folder contains the style sheet for your html code. All modern themes should define their styles using a style.css file. This dictates the look and feel of your site, without cluttering up the html.

templates
=========

The templates folder is where most of the code for the theme is stored. Here is where the layout of each page is described. Inside the templates folder, will be three more folders, blocks, config and includes. You will may also add a modules folder if you want to override the behavior of any module templates.

blocks
======

The blocks folder contains a template for laying out any block code for you site. Here you can endure that the block conforms to your theme and has a similar appearance. You need to point to these block configuration files in your config files so that Zikula knows where to find them.

config
======

The config folder outlines to Zikula the page templates of the theme, how to lay them out and where to find them. The overall layout of the theme is indicated in the pageconfiguration.ini 

*page configuration assignments* - a page configuration assignment defines which page configuration will be used for a particular set of parameters in the URL. These parameters can be a particular module, type and function and/or and custom arguments. Pre-defined dummy modules exist for the 'home page' and 'admin pages'. Page configuration assignments are held in <theme name>/templates/config/pageconfigurations.ini

*master.ini* This is the default config file for the theme. Unless otherwise specified, this is initializes the variables for this theme. The master.ini file must be described in your pageconfiguration.ini and must be implemented in your theme.

It is also to have other ini files besides the master that will be called in special situations. A common one is *home.ini*. This sets up a separate template for the home page, that is different than the default template.

Theme variables can also be defined in *themevariables.ini*. These are used in your theme templates and allow the administrator to have control over the appearance of your theme. Some themes have theme variables to manipulate, others do not.

The *themepalettes.ini* is still provided in the theme config folder, but I would recommend not using it. This type of manipulation is really best implemented through css in the style.css file.

includes
========

The includes file can contain templates that may be used by other templates. This is a convenient place to save code that is reused in more than one template.

modules
=======

The modules folder may be present, or you can add it to your theme. This allows you to override module templates to get them to match your theme. This is a very powerful feature of Zikula in that you can take content expressed by a module, and get it rendered in any layout that you, as theme designer want. If you do override specific templates, it is now necessary to tell Zikula about it.  You can do it by adding a file called 'overrides.yml' directly in your `themes/<ThemeName>/` folder.  This file is in YAML format.

Simply specify the relative path of the template you want to override on the left followed by a colon and space, then the relative path to the overridden template. There is one warning here.  The path and template name specificed in the module, block or theme must match the original module layout.

So if a module calls template.tpl, then you can have  <pathto>/template.tpl` but if a module calls `user/template.tpl`, then you have to override with `<pathto>/user/template.tpl`.

Finally the templates folder will have template files that describe how to layout the content for the theme. This can be in any number of template files (or even in subfolders)

In the next section, we will describe a standard theme that comes with Zikula, Andreas08, and how it implements these various parts.

.. _configuration: 2_2_configuration.rst
.. _W3School: http://www.w3schools.com/html/