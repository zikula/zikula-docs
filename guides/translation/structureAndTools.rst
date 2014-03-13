Locale Structure
================

Core translations live in the /locale directory in the following structure. We will use the example of English,
using the code ``en-us``

    /locale
    /locale/zikula.pot
    /locale/en_US/locale.ini
    /locale/en_US/LC_MESSAGES/zikula.po
    /locale/en_US/LC_MESSAGES/zikula.mo

Explanation:
 - ``zikula.pot`` - a template of all raw untranslated text
 - ``locale.ini`` - contains information about the locale
 - ``zikula.po`` - the file translator translate
 - ``zikula.mo`` - the compiled translation

All core translation files are named the same ``zikula.pot`` ``zikula.po`` ``zikula.mo``

Module translations live in the locale directory in the module directory. They contain the following files. The naming
convention is with the prefixed ``module_`` followed by the module name all in lower case.

Example module 'Foo'


    Foo/locale/module_foo.pot
    Foo/locale/en_US/LC_MESSAGES/module_foo.po
    Foo/locale/en_US/LC_MESSAGES/module_foo.mo


Theme translations live in the locale directory in the theme directory. They contain the following files. The naming
convention is with the prefix 'theme_' followed by the theme name all in lower case.

Example theme 'Bar'

    Bar/locale/theme_bar.pot
    Bar/locale/en_US/LC_MESSAGES/theme_bar.po
    Bar/locale/en_US/LC_MESSAGES/theme_bar.mo

Overriding Translations
-----------------------
You can override any translation domain within Zikula by creating a language structure in the config/locale like the
example below. You must copy the locale.ini file from the language directory in locale/LANG for the language you want
to override.

You can then copy any module, theme or the main zikula translation ``.po`` and ``.mo`` files over.

Here is an example with en_US to override the core translation domain and one module called 'foo':

    config/locale
    config/locale/en_US/locale.ini
    config/locale/en_US/LC_MESSAGES/zikula.po
    config/locale/en_US/LC_MESSAGES/zikula.mo
    config/locale/en_US/LC_MESSAGES/module_foo.po
    config/locale/en_US/LC_MESSAGES/module_foo.mo

To edit a translation file you simply edit the file in POEdit then save, copying the ``.po`` and ``.mo`` file to the relevant
place in the ``config/locale`` override space. See the translator's sections on this.


Tools
=====
Here are some recommended tools. We recommend you try them all to find your favorite tool.

PO Translation File Editors
---------------------------
PO-Edit_ Works on Windows, Mac and Linux.
Virtaal_ Excellent tool!
`Zikula Translation Portal`_

Translation Key Extraction Tools
--------------------------------
`Gettext Module`_


.. _PO-Edit: http://www.poedit.net/
.. _Virtaal: http://translate.sourceforge.net/wiki/virtaal/index
.. _Zikula Translation Portal: http://translate.zikula.org/
.. _Gettext Module: http://community.zikula.org/module-Gettext.htm