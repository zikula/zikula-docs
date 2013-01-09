Developer FAQ
=============

==== Q. What is the process to introduce a feature to Zikula? ====

A. In order to add a feature you should optionally first discuss the feature to get a general feel for whether it's liked and the best implementation. You may do this on the list. Officially all features should be added to the tracker. At this point the feature will be accepted or rejected by the project lead and assigned to a milestone.

==== Q. Which branch do I commit to? ====

A. This depends 100% on the ticket milestone. We always 'merge up' to all open branches. For example, suppose the open milestones were 1.2.3, 1.3.1 and 1.4.0. If a ticket were assigned to the 1.2.3 milestone, you would have to merge to all branches 1.2.3, 1.3.1 and 1.4.0. If the ticket was assigned to 1.3.1, then you would commit only to 1.3.1 and 1.4.0. All merges should generally be committed before closing the ticket.

==== Q. What is the difference between CHANGELOG and UPGRADING, why do we have to complete both? ====

A. The CHANGELOG is a list of changes for the sake of listing them in a summary. UPGRADING are the steps a developer (in particular) needs to take to upgrade their code to compliance with the milestone. A CHANGELOG might read, "Merged pnRender module to Theme module", where as an UPGRADING entry would read, "Please delete the pnRender module from the system/ folder". UPGRADING is specifically instructional. Something like "Added password meter" or "fixed UI bug" would not need a specific UPGRADING instruction. UPGRADING is not meant to be end-user documentation, just a summary that can be rapidly used to create documentation near the RC release cycle.

==== Q. What is the process for amending the CHANGELOG/UPGRADING ====

A. The changelog should be updated only on closing a ticket. The changelog should be a brief description of what was fixed or that feature was added. For example, "Fixed problem with new user registration", or "Added password strength meter". The changelog should be added to the CHANGELOG for the milestone of the ticket (it should not be merged).

==== Q. What is the difference between a commit notice and changelog? ====

A. Commit notices should summarise the incremental change to made with a reference to the ticket where relevent. A changelog is closer to a summary of the ticket (close to the ticket title).

==== Q. What are the rules about committing tests? ====

A. Tests must be written for anything new, or for stuff with existing tests. There is no need to write tests for legacy.

