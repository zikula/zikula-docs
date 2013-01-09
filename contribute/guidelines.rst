Developer Guidelines
====================

This document aims at being a step by step guide for developers on how to commit code to the core including all the QA steps.

COMMITMENT
----------

Being a Core Developer is to be considered a privilege and a responsibility.  Thousands of people rely on the Core development team to maintain stability, progress and momentum of the Zikula Project.  Core Developers are expected to contribute regularly to the project.  They are expected to check the tracker regularly and proactively find tasks to do.  Developers should at least inform the project lead, or (preferable) the entire team when they wont be able to contribute regularly due to personal circumstances.  This helps team co-ordination.

COMMUNICATIONS
--------------

All developers should at the very least subscribe to the commit notices [http://groups.google.com/group/zikula-commits mailing list] or if you use an RSS reader, subscribe to the project [/timeline timeline]'s [/timeline?downloads=on&amp;ticket=on&amp;changeset=on&amp;milestone=on&amp;wiki=on&amp;max=50&amp;daysback=90&amp;format=rss RSS feed]. It is essential you keep your pulse on the commits to the repository and also the bug tracker. Please also check Google Wave regularly or use a Google Wave notifier.

TICKETS
-------

All work must be done against tickets:  All features, bugs and tasks must be entered into the tracker and assigned to a milestone before you may work on the issue.  Tickets will be generally be assigned to milestones by the project lead, but in the case of bug tickets, developers may use their discretion.  Feature Request and Task tickets should only be worked on after approval from the project lead (when assigned with ticket status "approved".  Developers are expected to do their fair share of bug fixes along with feature requests/tasks.

If you are unable to complete the ticket tickets assigned to you or accepted by you, please inform the project lead or find another developer to take on the ticket.  If you cannot find someone please 'orphan' the ticket.

Any tickets with "approved" status can be taken over by anyone who feels that they are capable of completing the work. Simply assign it to yourself, and begin working on it. As above, if you later find that you cannot complete the ticket, work with another developer to take it over, or (if you have not been able to work on it at all) please 'orphan' the ticket so someone else can take it on.

DIFFS
-----
Diffs are an important part of your own QA process. Once you feel ready your code can be commited to the central repository you should diff the entire commit first before you actually commit. This is how you can catch all manner of mistakes (including accidently commiting the wrong files, or accidently leaving in some debug code that you may have commented out. You should use a visual coloured diff tool if possible as they are much easier to read than a textonly diff.

COMMIT NOTICES
--------------

When committing work to the repository, please write a commit notice that adequately describes the changes you made. The commit notice should include the ticket number the change relates to with the word "closes", "refs", "fixes" or "fixed" followed by the ticket number. e.g.

    `Changed template delimiters from old to new format refs #1234`

The words "refs, closes, fixed, and fixes" will update the ticket in the tracker with the commit message and close the ticket. The word "refs" will update the ticket in the bug tracker". If you are making a partial commit, please use the word "refs". You may only use "closed, fixed, fixes" when you have completed all the work and can close the corrisponding tracker ticket.

Please additionally explain the rationale for the changes in a separate paragraph after a blank line

   `"Changed constructor refs #1234`

   `Injecting the instance here helps with testability.`
   `Also removed some unnecessary argument checks because`
   `typecasting in the signature covers these cases."`

CHANGELOG & UPGRADING
---------------------

Whenever you complete a ticket you must record a summary of the complete change in the CHANGELOG wave only for the milestone the ticket is assigned.  Example: "Fixed issue css issues in installer under IE8"

The `UPGRADING` & `UPGRADING_FOR_DEVELOPERS` is a summary document.  It's is not meant as end user documentation, but something we can construct documentation from.  There are two classes of entry: end-user, and module-developer.  It should contain specific things a user must do as a consequence of the changes you made.

If aimed at a user then something like: `"Delete the entire folder system/Profile"`.
If aimed at a module developer, `"Change all template occurrences of <!--[ and ]--> to { and }"`.

The `CHANGELOG`, `UPGRADING`, and `UPGRADING_FOR_DEVELOPERS` can be found in the src/docs/ folder of the codebase.

MERGING
-------
It is important to merge your work regularly to any other branches that require it. The general rule of thumb is to "merge up" unless there are specific instructions to do something different. This means if a ticket is assigned to a milestone, you should commit your work to corrisponding branch and all open branches above it.

You could find more information about this on the [wiki:development/merging Current Merging Instructions] wiki page.

UNIT TESTING
------------

It is essential that you write tests for anything new you write. If you are fixing code that already has tests you must also add test cases in general. You must generally never alter a released API's tests. If you believe the need occurs please bring up a discussion on the matter.

Before making a commit you are required to run the entire test suite.  You may not commit if the test suite is broken (meaning test execution halts with a PHP error).  It is suggested that you run the test suite at command line with {{{phpunit --coverage-html /path/to/html/report tests}}} to be 100% sure.

MODIFICATION OF 3RD PARTY LIBRARIES
-----------------------------------

Modification of 3rd party libraries is completely forbidden. While it's easy to include local patches to the library to add features that upstream doesn't have or fix bugs that upstream hasn't addressed, this has several negative effects.

When a security issue appears, it becomes harder to fix the application bundling the library. If you attempt to upgrade to a newer version, you have to make sure your important local modifications get ported to the new version. If you attempt to backport, you have to merge the upstream fix to your own code-base which may have conflicts with the local modifications.

When working with the library that comes from upstream, there is a community of people who are interested in that library to fall back on for help. When working on your own private copy that community may not be interested in helping you work on your modified sources since they don't have control or knowledge of what your modified sources do.

Forking dilutes one of the strengths of open-source development. Instead of a project getting stronger with more people supplying patches to help drive the project and build a bigger community, the community of people interested in it are splintering, developing more and more divergent code-bases, solving the same problem over and over in different ways in different private copies of the library. Instead of everyone benefiting, everyone has to pay.

3RD PARTY LIBRARIES LICENSING
-----------------------------

There are strict rules about acceptable 3rd party libraries licensing. We only accept licenses that are compatible with "GPL version 3 or at your option, any later version", so called "permissive copyleft" licenses but specifically not any version of the GPL.

Permissible licenses are licenses such as LGPLv3 with the clause "or at your option, any later version", LGPL without version number, MIT, modified BSD and Apache 2.0.

If libraries are LGPL without version number or if they are LGPL less than version 3 (with the upgrade clause "or at your option, any later version"), you must upgrade the license to version 3 before adding them - this is just a matter of changing the relevant headers.

Incompatible licenses can be found at:

.. _GPL Incompatible Licenses: http://www.fsf.org/licensing/licenses/index_html#GPLIncompatibleLicenses
.. _Non-free licenses: http://www.fsf.org/licensing/licenses/index_html#NonFreeSoftwareLicense
