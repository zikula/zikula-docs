Gettext with Javascript
=======================

Using the Javascript Gettext is similar to how it is done in PHP or templates. Below you will find information on a
few key differences.

How it works
------------

Due to the nature of Javascript, which is a client-side language, Gettext translation must be sent to the browser -
the same as the js scripts itself.
So if you use translations, the system will add to the html header the additional element, which looks something
like this:


    script type="text/javascript" src="mo2json.php?lang=pl&zikula_js=zikula_js"

Translations for this embedded file are generated automatically by the same development methods. What you will need
to do is to declare the need for a specific translation by ``PageUtil::addVar()``, which is described below.

Domains
-------

Core translations for Javascript are contained in separate domain: "zikula_js". The separation of these translations
is done due to the fact that the file for the main domain ("zikula") is very large, and Javascript uses only a small
part of it. They were separated into a "zikula_js" domain to avoid loading to the browser a very large file with this
translations. But usually you wont need to think about the core translations - they are automatically loaded when needed.

In case of extensions (modules, themes) translations for Javascript may also be contained in a separate domain, but
not necessarily. If your module has a small file with translations creating a separate domain for Javascript
translations does not make sense. However, if your translation catalog is big - it's worth it to move Javascript
translation into a separate domain (e.g. for module "Foo" it could be "module_foo_js" domain). Separate domains
mean a separate POT file. So it's up to you, as the extension's developer, to decide which way is best for your
project.

Which domain is used is automatically detected based on the code, so you don't need to do anything with it.

Translations for Javascript, just like any other Gettext translations, are automatically detected by `Gettext
extraction tool`_.

Loading translations
--------------------

To use the Javascript Gettext you need to load the appropriate translation, just like any other js script. To do
this, use the method ``PageUtil::addVar()`` with the following syntax:

PHP:
``PageUtil::addVar('jsgettext', 'module_domain:module_name');``

Template:
``{pageaddvar name="jsgettext" value="module_domain:module_name"}``


For example, for News module it would look like this:

PHP:
``PageUtil::addVar('jsgettext', 'module_news:News');``

Template:
``{pageaddvar name="jsgettext" value="module_news:News"}``


However, if your module uses a separate domain for Javascript translations:

PHP:
``PageUtil::addVar('jsgettext', 'module_news_js:News');``

Template:
``{pageaddvar name="jsgettext" value="module_news_js:News"}``


Core translations are automatically loaded for scripts, which require such translations. However, if you need to
load the translations yourself - you can do the following:

PHP:
``PageUtil::addVar('jsgettext', 'zikula_js');``

Template:
``{pageaddvar name="jsgettext" value="zikula_js"}``

(for core translations it is sufficient to provide only the domain)

Usage
-----

There are four methods for translating Gettext strings:

    Zikula.__() // translates simple message
    Zikula.__f() // translates and format message using sprintf formatting rules
    Zikula._n() // translates plural message
    Zikula._fn() //translates and format plural message


Both the syntax and the names are the same as those used in PHP. You can read about them on the `Using Gettext
with Modules`_ page.
Here are some examples:

    Zikula.__('hello','module_foo'); // message, domain
    Zikula.__f('hello %s',['A'],'module_foo'); // message, formatting params, domain
    Zikula._n('hello my friend','hello my friends',2,'module_foo'); // singular message, plural message, count, domain
    Zikula._fn('hello my friend %s','hello my friends %s',2,['A','B'],'module_foo') // singular message, plural message, count, formatting params, domain

.. _Gettext extraction tool:http://community.zikula.org/module-Gettext-extract.htm
.. _Using Gettext with Modules:GetTextInModules