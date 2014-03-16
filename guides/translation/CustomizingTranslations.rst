Customizing and Translations
============================

This is a guide to using customizing translations in within Zikula Core 1.3+.

This document explains how to customize the Zikula translations to modify the default English strings or handle your own
site translations. Before go to the details, you need to read the structure_ and understand the Gettext elements
involved in a translation.

First, the *Core and the Extensions* using Gettext provides a **Catalog** or template (.POT file) that you will find in its
/locale sub-directory. From it, you can create translations for any language that you need, using a tool like poEdit,
Virtaal, etc.

A translation consists of a Portable Object (**.PO file**) derived from the POT file, where you translate each original
string to a specific language. There is one .PO file per language. The PO files are meant to be read and edited by
humans, but Zikula needs the Machine Object (**.MO file**), meant to be read by programs, and are binary files. By default,
poEdit compiles and generate the .MO file each time you save the .PO file when translating.


Customizing
-----------

Let's suppose that want do some customizations to a module Catalog, i.e. Profile, and that you have a multilingual site
with *English* and *Spanish*. The first step then, is to copy the **Catalog** to customize
(at ``/modules/Profile/locale/module_profile.pot``) in your ``/config/locale/`` directory.

To customize the default *English* words, we need to create the **English .PO** to override the original ones with our own
translations. So, let's suppose we want to change 'Users' to 'Members' and 'Profile' to 'Account'.

poEdit has a friendly 'Create a new translation from a POT' option, so, we need to use it to create the *English* **.PO**
translation; fill the headers, save the file as ``/config/locale/en/LC_MESSAGES/module_profile.po``, and begin to
translate the original words we want to override. The final .PO source code may look like:

**It is not recommended to manipulate the .PO file manually.**

    ...
    #. ! pntemplates/profile_user_members_view.htm:102
    msgid "Profile"
    msgstr "Account"

    #: pntemplates/profile_user_members_view.htm:104
    msgid "Users"
    msgstr "Members"

    #: pntemplates/profile_user_members_view.htm:106
    msgid "Newest User"
    msgstr "Newest Member"

    #: pntemplates/profile_user_members_view.htm:107
    msgid "On-line:"
    msgstr ""
    ...

After save, poEdit will generate a smaller **.MO** with only the translated strings, and you will observe that the
site will display the changed strings.

Note that On-line is not translated, because there's no need of it. We just want to change the mentioned words. Now,
the same process can be applied to the Spanish translation, generate the .PO
``/config/locale/es/LC_MESSAGES/module_profile.po`` and translate all to the way we want it for the Web Site in question.


.. _structure:StructureAndTools.rst