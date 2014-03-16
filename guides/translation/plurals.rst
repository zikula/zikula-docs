Plural Forms
============

This is a guide to using Gettext plural configurations within Zikula Core 1.3+.

Different languages have different rules about plural cases. For example in English, 1 is singular but everything else
including 0 is plural. e.g. "There are no cats in the house" or "You have 20 dollars in your pocket".

Yet some other languages may have no plural cases at all, while others may have several! Fortunately Gettext handles
this with precision. The plural gettext call ``_ngettext()`` receives both singular, plural translation keys along with an
integer. Gettext will then query the language file for the rules then it will return the correct translation.

Below you will find a table of all the plural cases along with a code snippet. You will need this code snippet for use
with PO-Edit when you generate your first ``.po`` file from the template ``.pot`` file.

If the ruleset specifies there are three plural cases for example, PO-Edit will present THREE translation options for
you numbered from 0-2. 0 is the singular case, and 1 & 2 would be the plural. For Germanic languages there is just one
case you you will see just two cases, 0 and 1.

You can look up the reference code directly by entering the language at LaunchPad_ but be aware the formula is not
complete and must be expressed with ``nplural=x;plural=<rule>;`` where ``x`` is the number of plural cases. e.g. if Launchpad
tells you the rule is::

    The amount of plural forms for German, Low is: 2
    And its default plural form expression is:
    n != 1

Then the formula is::

    nplural=2;plural=n != 1;

There is also a complete country by country reference of `Plural Codes here`_

For clarity there are a list of code here:

Table of Plural Forms
=====================

Plural rule #0 (1 form)
-----------------------

Only one form. Some languages only require one single form. There is no distinction between the singular and plural form.

Families: Asian (Chinese, Japanese, Korean, Vietnamese), Persian, Turkic/Altaic (Turkish), Thai, Lao
everything: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28,
29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, �


An appropriate header would look like this::

    nplurals=1; plural=0;

Plural rule #1 (2 forms)
------------------------

Two forms, singular used for one only

Families: Germanic (Danish, Dutch, English, Faroese, Frisian, German, Norwegian, Swedish), Finno-Ugric (Estonian,
Finnish, Hungarian), Language isolate (Basque), Latin/Greek (Greek), Semitic (Hebrew), Romanic (Italian, Portuguese,
Spanish, Catalan)
is 1: 1
everything else: 0, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27,
28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, �

This is the form used in most existing programs since it is what English is using. The header would look like this::

    nplurals=2; plural=n != 1;

Hungarian does not appear to have a plural if you look at sentences involving cardinal numbers. For example, �1 apple�
is �1 alma�, and �123 apples� is �123 alma�. But when the number is not explicit, the distinction between singular and
plural exists: �the apple� is �az alma�, and �the apples� is �az alm�k�. Since ngettext has to support both types of
sentences, it is classified here, under �two forms�.

Plural rule #2 (2 forms)
------------------------

Exceptional case in the language family.
Families: Romanic (French, Brazilian Portuguese)
is 0 or 1: 0, 1
everything else: 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28,
29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, �
The header would look like this::

    nplurals=2; plural=n>1;

Plural rule #3 (3 forms)
------------------------
special case for zero

Families: Baltic (Latvian)
is 0: 0
ends in 1, not 11: 1, 21, 31, 41, 51, 61, 71, 81, 91, 101, 121, 131, 141, 151, 161, 171, 181, 191, 201, 221, 231, 241,
251, 261, 271, 281, 291, �
everything else: 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 22, 23, 24, 25, 26, 27, 28, 29,
30, 32, 33, 34, 35, 36, 37, 38, 39, 40, 42, 43, 44, 45, 46, 47, 48, 49, 50, 52, 53, 54, 55, �
The header would look like this::

    nplurals=3; plural=n%10==1 && n%100!=11 ? 0 : n != 0 ? 1 : 2;

Plural rule #4 (3 forms)
------------------------
special cases for one and two
Families: Celtic (Scottish Gaelic)
is 1: 1
is 2: 2
everything else: 0, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28,
29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, �
The header would look like this::

    nplurals=3; plural=n==1 ? 0 : n==2 ? 1 : 2;

Plural rule #5 (3 forms)
------------------------
special case for numbers ending in 00 or [2-9][0-9]
Families: Romanic (Romanian)
is 1: 1
is 0 or ends in 01-19: 0, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 101, 102, 103, 104, 105, 106,
107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211,
212, �
everything else: 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44,
45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, �
The header would look like this::

    nplurals=3; plural=n==1 ? 0 : (n==0 || (n%100 > 0 && n%100 < 20)) ? 1 : 2;

Plural rule #6 (3 forms)
------------------------
special case for numbers ending in 1[2-9]
Families: Baltic (Lithuanian)
ends in 1, not 11: 1, 21, 31, 41, 51, 61, 71, 81, 91, 101, 121, 131, 141, 151, 161, 171, 181, 191, 201, 221, 231, 241,
251, 261, 271, 281, 291, �
ends in 0 or ends in 10-20: 0, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 111,
112, 113, 114, 115, 116, 117, 118, 119, 120, 130, 140, 150, 160, 170, 180, 190, 200, 210, 211, 212, 213, 214, 215, 216,
217, 218, 219, 220, �
everything else: 2, 3, 4, 5, 6, 7, 8, 9, 22, 23, 24, 25, 26, 27, 28, 29, 32, 33, 34, 35, 36, 37, 38, 39, 42, 43, 44,
45, 46, 47, 48, 49, 52, 53, 54, 55, 56, 57, 58, 59, 62, 63, 64, 65, 66, 67, 68, 69, 72, 73, �
The header would look like this::

    nplurals=3; plural=n%10==1 && n%100!=11 ? 0 : n%10>=2 && (n%100<10 || n%100>=20) ? 1 : 2;

Plural rule #7 (3 forms)
------------------------

Families: Slavic (Croatian, Serbian, Russian, Ukrainian)
special cases for numbers ending in 1 and 2, 3, 4, except those ending in 1[1-4]
ends in 1, not 11: 1, 21, 31, 41, 51, 61, 71, 81, 91, 101, 121, 131, 141, 151, 161, 171, 181, 191, 201, 221, 231, 241,
251, 261, 271, 281, 291, �
ends in 2-4, not 12-14: 2, 3, 4, 22, 23, 24, 32, 33, 34, 42, 43, 44, 52, 53, 54, 62, 63, 64, 72, 73, 74, 82, 83, 84, 92,
93, 94, 102, 103, 104, 122, 123, 124, 132, 133, 134, 142, 143, 144, 152, 153, 154, 162, 163, 164, 172, 173, 174, 182,
183, �
everything else: 0, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 25, 26, 27, 28, 29, 30, 35, 36, 37, 38,
39, 40, 45, 46, 47, 48, 49, 50, 55, 56, 57, 58, 59, 60, 65, 66, 67, 68, 69, 70, 75, 76, 77, �
The header would look like this::

    plural=n%10==1 && n%100!=11 ? 0 : n%10>=2 && n%10<=4 && (n%100<10 || n%100>=20) ? 1 : 2;

Plural rule #8 (3 forms)
------------------------
special cases for 1 and 2, 3, 4
Families: Slavic (Slovak, Czech)
is 1: 1
is 2-4: 2, 3, 4
everything else: 0, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30,
31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, �
The header would look like this::

    nplurals=3; plural=(n==1) ? 0 : (n>=2 && n<=4) ? 1 : 2;

Plural rule #9 (3 forms)
------------------------
special case for one and some numbers ending in 2, 3, or 4
Families: Slavic (Polish)
is 1: 1
ends in 2-4, not 12-14: 2, 3, 4, 22, 23, 24, 32, 33, 34, 42, 43, 44, 52, 53, 54, 62, 63, 64, 72, 73, 74, 82, 83, 84, 92,
93, 94, 102, 103, 104, 122, 123, 124, 132, 133, 134, 142, 143, 144, 152, 153, 154, 162, 163, 164, 172, 173, 174, 182,
183, �
everything else: 0, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 25, 26, 27, 28, 29, 30, 31, 35, 36,
37, 38, 39, 40, 41, 45, 46, 47, 48, 49, 50, 51, 55, 56, 57, 58, 59, 60, 61, 65, 66, 67, 68, �
The header would look like this::

    nplurals=3; plural=n==1 ? 0 : n%10>=2 && n%10<=4 && (n%100<10 || n%100>=20) ? 1 : 2;

Plural rule #10 (4 forms)
-------------------------
special case for one and all numbers ending in 02, 03, or 04
Families: Slavic (Slovenian, Sorbian)
ends in 01: 1, 101, 201, �
ends in 02: 2, 102, 202, �
ends in 03-04: 3, 4, 103, 104, 203, 204, �
everything else: 0, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30,
31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, �
The header would look like this::

    nplurals=4; plural=n%100==1 ? 0 : n%100==2 ? 1 : n%100==3 || n%100==4 ? 2 : 3;

Plural rule #11 (5 forms)
-------------------------

Families: Celtic (Irish Gaeilge)
is 1: 1
is 2: 2
is 3-6: 3, 4, 5, 6
is 7-10: 7, 8, 9, 10
everything else: 0, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35,
36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, �
The header would look like this:



Plural rule #12 (6 forms)
-------------------------

Families: Semitic (Arabic)
is 1: 1
is 2: 2
ends in 03-10: 3, 4, 5, 6, 7, 8, 9, 10, 103, 104, 105, 106, 107, 108, 109, 110, 203, 204, 205, 206, 207, 208, 209, 210,
 �
ends in 11-99: 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36,
37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, �
everything else but 0: 100, 101, 102, 200, 201, 202, �
is 0: 0
The header would look like this:


Plural rule #13 (4 forms)
-------------------------

Families: Semitic (Maltese)
is 1: 1
is 0 or ends in 01-10: 0, 2, 3, 4, 5, 6, 7, 8, 9, 10, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 201, 202, 203,
204, 205, 206, 207, 208, 209, 210, �
ends in 11-19: 11, 12, 13, 14, 15, 16, 17, 18, 19, 111, 112, 113, 114, 115, 116, 117, 118, 119, 211, 212, 213, 214, 215,
216, 217, 218, 219, �
everything else: 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45,
46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, �
The header would look like this:


Plural rule #14 (3 forms)
-------------------------

Families: Slavic (Macedonian)
ends in 1: 1, 11, 21, 31, 41, 51, 61, 71, 81, 91, 101, 111, 121, 131, 141, 151, 161, 171, 181, 191, 201, 211, 221, 231,
241, 251, 261, 271, 281, 291, �
ends in 2: 2, 12, 22, 32, 42, 52, 62, 72, 82, 92, 102, 112, 122, 132, 142, 152, 162, 172, 182, 192, 202, 212, 222, 232,
242, 252, 262, 272, 282, 292, �
everything else: 0, 3, 4, 5, 6, 7, 8, 9, 10, 13, 14, 15, 16, 17, 18, 19, 20, 23, 24, 25, 26, 27, 28, 29, 30, 33, 34, 35,
36, 37, 38, 39, 40, 43, 44, 45, 46, 47, 48, 49, 50, 53, 54, 55, 56, 57, 58, 59, 60, 63, �
The header would look like this:


Plural rule #15 (2 forms)
-------------------------

Families: Icelandic
ends in 1, not 11: 1, 21, 31, 41, 51, 61, 71, 81, 91, 101, 121, 131, 141, 151, 161, 171, 181, 191, 201, 221, 231, 241,
251, 261, 271, 281, 291, �
everything else: 0, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 22, 23, 24, 25, 26, 27, 28, 29,
30, 32, 33, 34, 35, 36, 37, 38, 39, 40, 42, 43, 44, 45, 46, 47, 48, 49, 50, 52, 53, 54, �
The header would look like this:

.. _LaunchPad: https://translations.launchpad.net/+languages
.. _Plural Codes here: http://translate.sourceforge.net/wiki/l10n/pluralforms