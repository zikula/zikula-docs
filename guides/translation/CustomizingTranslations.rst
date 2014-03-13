Customizing and Hacking Translations
====================================
This document explains how to customize the Zikula translations to modify the default English strings or handle owns
Site translations. Before go to the details, you need to read the structure_ and understand the Gettext elements
involved in a translation.

First, the *Core and the Extensions* using Gettext provides a **Catalog** or Template (.POT file) that you will find in its
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

**It's not recommended to manipulate the .PO file manually.**

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

After save, poEdit will generate a smaller **.MO** with only the translated strings, and you will appreciate that the
site will display the changes :-)

Note that On-line is not translated, because there's no need of. We just want to change the mentioned words. Now,
the same process can be applied to the Spanish translation, generate the .PO
``/config/locale/es/LC_MESSAGES/module_profile.po`` and translate all to the way we want it for the Web Site in question.


Hacking
-------
**To edit the POT files manually makes no sense. This is a hacky method currently used for some modules that doesn't
support MultiLanguage? features on its items, and needs to use non existing strings. In Zikula 1.3 this may change,
with a rewrite in the Zikula Gettext reader, to handle these**

Profile 1.5 and other modules like PageMaster 0.4 needs to be out of the Gettext conventions to translate their
dynamic items, so, for instance, they need that you add the strings to the Catalog (.POT) of the Profile DUD
(Dynamic User Data) titles and config values, or the PageMaster titles, descriptions and more.

So, by now, you need to add those new strings to the Catalog. Let's suppose you add a new DUD field in the Profile
module named: ``_UDEVELOPER``, as a radio button with values: **Yes, I am**. and **No, I'm not**., to know if the user is a
developer or not.

You need to add those strings to your Catalog to be able to translate them, so carefully add them manually at the
end of the POT file like:


    ...
    #: pntemplates/profile_user_members_view.htm:107
    msgid "On-line:"
    msgstr ""

    #. ! Developer field title
    msgid "_UDEVELOPER"
    msgstr ""

    #. ! Developer field value: Yes
    msgid "Yes, I am."
    msgstr ""

    #. ! Developer field value: No
    msgid "No, I'm not."
    msgstr ""

Now you need to update your Translations (.PO files) from the "Hacked" Catalog.

As we have not created the Spanish translation, we do it from this .POT; fill the headers, save the file as
``/config/locale/es/LC_MESSAGES/module_profile.po``, and begin the translation. The final .PO source code may look like:


    -


Also, the English .PO needs to be updated from the "Hacked" POT. poEdit will tell us the new strings that will be
added to the .PO, and translate them. The English source code may look like this:


    ...
    #: pntemplates/profile_user_members_view.htm:107
    msgid "On-line:"
    msgstr ""

    #. ! Developer field title
    msgid "_UDEVELOPER"
    msgstr "Are you a developer?"

    #. ! Developer field value: Yes
    msgid "Yes, I am."
    msgstr ""

    #. ! Developer field value: No
    msgid "No, I'm not."
    msgstr ""

Again, after saving and generate the .MO, you will note that _UDEVELOPER is translated adequately to the
corresponding text :-)


Updating a "Hacked" Catalog
---------------------------
When a new release comes, you need to update the Catalog and the Translations. So, carefully you copy your strings
to the new Catalog, and update each .PO file with the 'Update from a POT file' option of poEdit.

It will report you which strings are deprecated in the Catalog and which are new ones. Check that you don't lose
any of your strings, and update it. Translate the new ones, and you're ready to use the next version of the module ;-)

.. _structure:StructureAndTools