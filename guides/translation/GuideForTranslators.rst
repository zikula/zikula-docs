Translation Guide for Translators
=================================

This is a guide to using Gettext translation within Zikula Core 1.3+.

This document is means solely for those wishing to create translations for the core, modules or themes. Please make sure
you read the `Locale Structure`_ before proceeding. You will also want to decide on the plural rules before you proceed.

New Core Translations
---------------------
The 'Core' consists of everything in the standard Zikula 'Core'. For the sake of convenience, everything is wrapped into
ONE translation file. The ``/modules`` and ``/themes`` directories are for 3rd party extensions, however, for clarity
sake the following themes are considered 'Core'::

    /themes/andreas08
    /themes/rss
    /themes/Atom
    /themes/Printer
    /themes/SeaBreeze

To create a core translation the first job is to create the layout in the ``/locale`` directory. You must select the
language code you will be using according to the guidelines. In this example we will create a German translation.

First create the following directory structure::

    /locale/de
    /locale/de/LC_MESSAGES

Now create a file called ``/locale/de/locale.ini`` to tell the system about the language. At the moment the the only value
is ``language_code = ltr`` (for left to right) or ``rtl`` (for right to left) e.g.::

    [language]
    language_code = ltr

Next open PO-Edit. Now select *File -> New Catalog From POT file*
Browse for the file ``/locale/zikula.pot`` - this contains all the main untranslated strings.

You will be presented with a dialog asking some questions. Complete the form and add:
Charset: UTF-8
Source code charset: utf-8
Plural Forms: `Plural Forms`_

Click OK, name the file ``zikula.po``, and the path to save to will be in ``/locale/de/LC_MESSAGES``

You are now ready to translate using PO-Edit. Complete your translations and save. It will automatically create a
``zikula.mo`` also. It is required that both ``zikula.po`` and ``zikula.mo`` exist in your translation.

Please see the `POEdit Video Tutorial`_

New Module or Theme Translation
-------------------------------

This section applies ONLY to translation of 3rd party modules or themes.

We know from the `locale structure`_ that module's translation files are named in lower case: *module_$modulaname*, e.g.
``module_foo`` and that themes are named in lower case: *theme_$themename*, e.g. ``theme_foo``

The process to create a new translation is almost the same. For each module or theme there will be a ``.pot`` file in it's
own ``locale`` directory. In our example we will use ``locale/module_foo.pot``

In this example we will continue with a German translation of module 'Foo'. We follow the same process as for the Core
but we do not need a ``locale.ini`` file. We again create the directories in the module/theme directory::

    locale/de
    locale/de/LC_MESSAGES

Now open PO-Edit and follow the same process to *File -> Create New Catalog from POT file*
This time save the file in the module Foo's own ``locale/de/LC_MESSAGES`` directory as ``module_foo.po``

Translate and save. This will automatically generate the ``module_foo.mo`` It is required that both ``module_foo.po`` and
``module_foo.mo`` exist.

Updating translations
---------------------

So what happens when a new version of Zikula or a module are released? This is where the use of the ``.pot`` and ``.po`` files
will be seen.

When a new release is made the ``.pot`` files will be regenerated. We can use PO-Edit to merge the changes with the
translation ``.po`` you already completed. This will result in 3 possible changes:

1. new, untranslated strings will appear requiring translation
2. deleted translations will be removed
3. existing strings that were updated need revision (called "fuzzy" translation)

You can do this in PO-Edit by opening your ``.po`` file then selecting *Catalog* from the menu and *Update from POT file*,
browse to the ``.pot`` and it will inform you of the changes.

Updated translations will be marked as **fuzzy** this means it requires attention.

Again, once you save the file it will automatically generate the ``.mo``

Now it should be clear why ``.pot``, ``.po`` & ``.mo`` files should be distributed with each release.

.. _Locale Structure:StructureAndTools.rst
.. _Plural Forms:Plurals.rst
.. _POEdit Video Tutorial:http://zikula.org/tutorials/POEdit
.. _locale structure:StructureAndTools.rst
