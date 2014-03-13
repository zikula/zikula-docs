Using Gettext in Templates
==========================

This is a guide to using Gettext in templates and themes within Zikula.

Themes
------

In order to make a theme multilingual compliant it is necessary that the correct headers appears in the theme.
There are three things we need to have on every HTML page: language, encoding and language direction. Please refer
to the ``xml:lang=``, ``lang=``, ``dir=`` and the ``content-type`` meta tag. Here are some examples:

In Zikula 1.2


    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="<!--[lang]-->" lang="<!--[lang]-->" dir="<!--[langdirection]-->">
    <head>
    " />


In Zikula 2.0

    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="{lang}" lang="{lang}" dir="{langdirection}">
    <head>


GT Plugin
---------

In order to write multilingual compliant templates or themes all text must be expressed using gettext calls.
We have two way to achieve this:


    <!--[gt text="Hello World"]-->


Shortcuts
---------

For long paragraphs of text it may be preferable to use the shortcut. This form is fine straight text that doesn't
require plural handling. You can use single or double quotes following the usual norms:


    %%%'Hello World, it\'s a wonderful day'%%%
    %%%"Hello World, it's a wonderful day"%%%

Multilingual Parameters
-----------------------

Certain smarty tags generate HTML params that require human readable text like ``<!--[pnimg src="logo.jpg" alt="Logo"]-->``
where the ``alt`` tag has some text. We can make these multilinual by prepending a double underscore, eg:


    <!--[pnimg src="phone.jpg" __alt="new phone" __title="new phone"]-->


This only works with plugins. You cannot do this with plain HTML. If you need to gettextise a normal html call then
do something like this


    <img src="phone.jpg" alt="<!--[gt text="new phone"]-->">

or


    <!--[gt text="new phone" assign=alt]-->
    <img src="phone.jpg" alt="<!--[$alt]->">


Plural Handling
---------------

Unlike within PHP code we can use the same plugin for Gettext calls for singular or plural forms:


    <!--[gt text="File deleted" plural="All files deleted" count=$count]-->

String Replacements
-------------------

We can also do string substitution as follows using the ``tagN`` parameter. These follow the standard ``sprintf()`` norms.


    <!--[gt text="Welcome back %s" tag1=$name]-->
    <!--[gt text="Your age is %n and your star sign is %s" tag1=$age tag2=$sign]-->


You may also do positional replacements like:-


    <!--[gt text='%1$s by me %2$s after %3$s' tag1=$name __tag2=$drink __tag3=$time]-->
    <!--[gt text="Your age is %s and your star sign is %s" tag1=$age tag2=$sign]-->


Note the use of **single quotes** when using ``%1$s`` this is because with **double quotes** the ``$s`` is evaluated as a string.
You must use single quotes.

Advanced Replacements
---------------------

The advantage of the positional replacements ``%n$s`` over ``%s`` is that you can reuse it over and over within the call
since ``%n$s`` refers to ``tagN`` like a variable, whereas ``%s`` is evaluated in order and once only - the first occurrence
is ``tag1``, the second is ``tag2``.
e.g.


    <!--[gt text='Please click %1$s and %1$s but not %2$s' tag1=$here tag2=$nothere]-->


Next, we should also try to keep html out of the Gettext requests, here is an example. Notice how double and single
quotes retain the same behaviour as in PHP and how strings are evaluated in side double quotes.


    <!--[assign var='url' value='http://zikula.org/']-->
    <!--[gt text="click here" assign='clickhere']-->
    <!--[gt text='Please %1$s' tag1="<a href='$url'>$clickhere</a>"]-->


Comments to Translators
-----------------------

This is a very useful feature. You can send a comment directly to the translator to help them understand your
meaning, this is especially useful when you are using string replacements. Simple place comment="" at the **end** of
your plugin call.


    <!--[gt text="Welcome back %s" tag1=$name comment="this is a comment"]-->
    <!--[gt text="Your age is %s and your star sign is %s" tag1=$age tag2=$sign comment="this is a comment"]-->


Themes
------

Plugins
~~~~~~~

Within your plugins you must retrieve the theme domain with


    $dom = ZLanguage::getThemeDomain('Foo');


See `Using Gettext with Modules`_ for details on syntax.

version.php
~~~~~~~~~~~


    $themeversion['name'] = 'Foo';
    $domain = ZLanguage::getThemeDomain($themeversion['name']);
    $themeversion['displayname'] = __('Foo', $domain);
    $themeversion['description'] = __("The Foo theme - a very good template for light, CSS-compatible themes.", $domain);
    $themeversion['regid'] = '0';
    $themeversion['version'] = '1.1';
    $themeversion['official'] = '1';
    $themeversion['author'] = 'me';
    $themeversion['contact'] = 'me@example.com';
    $themeversion['admin'] = 1;
    $themeversion['user'] = 1;
    $themeversion['system'] = 0;
    $themeversion['credits'] = '';
    $themeversion['help'] = '';
    $themeversion['changelog'] = '';
    $themeversion['license'] = '';
    $themeversion['xhtml'] = true;

.. _Using Gettext with Modules:GetTextInModules.rst