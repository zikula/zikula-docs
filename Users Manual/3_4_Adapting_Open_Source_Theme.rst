Adapting an Open Source Theme
=============================

:Author:
    Timothy Paustian

Finding a Theme
===============

You may not find a design that you like in the Zikula community. From here you can roll your own, or you can adapt from other web design efforts. There are literally thousands of open source web designs available for you to adapt and many of these can be found at Open Source Themes_. Browse through their amazing collection and find one that appeals to you. Then download the theme. If you look inside, you will typcially find a various folders. These can be organized in many ways and the trick to adapting them to Zikula is bending the design so Zikula can read it. 

Adpating CrystalX
=================

For this example, we will manipulate the CrystalX_ theme. Download the theme, unzip it and examine the folder structure. You will see that it has a css folder and a design folder. We can map the css folder to the style folder in our Zikula theme. The design folder is loaded with images, so that will become our image folder. The index file contains both the design and the text, so we will adpat this to our template. Drop this folder into your Zikula Themes directory and we will begin modifiying it. 

Create Zikula Theme Structure
=============================

First create the appropriate folder structure. Rename the css folder to style, and the design folder to images. Create a templates folder and move the index.html file inside. Rename index.html to master.tpl. Create blocks and config folders. Copy the master.ini and pageconifiguration.ini from Andreas08 and place them in the conifig folder. Also copy over block.tpl, topnavblock.tpl and searchblock.tpl from Andreas08 to the blocks folder. Finally copy a version.php file from Andreas08 to your CrystalX folder. When finished, your directory should look like this.

.. figure:: CrystalX_Folder_Struct.png
    :alt: Folder Structure of the theme
    
    Folder stucture of the theme once everything has been renamed.

version.php
===========

We first need to provide information about the theme to Zikula. This is information that Zikula displays to administrators and provides information about how the theme can be used. Change the code in the version.php file to match the following.

::

    $themeversion['name'] = 'CrystalX';
    $themeversion['displayname'] = __('CrystalX');
    $themeversion['description'] = __("Based upon the open source theme by vit.dlouhy (http://www.oswd.org/user/profile/id/19895).");
    $themeversion['version'] = '1.0';
    $themeversion['admin'] = 1;
    $themeversion['user'] = 1;
    $themeversion['system'] = 0;
    $themeversion['license'] = 'GPL';
    $themeversion['xhtml'] = true;


.. _Themes: http://www.oswd.or
.. _CrystalX: http://www.oswd.org/design/preview/id/3465