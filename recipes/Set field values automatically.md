Requirement:

Automated assignation of values to hidden form fields.

Case example:

Creation of a part number consisting of the 'current user ID' & timestamp.

A URL parameter like '&set_partnumber=xyz' is not an option in this case as the user potentially could manipulate the field value, even if the form field is 'read only' or 'hidden'.

Assuming that a user is only logged in once at a time, the combination of userID & timestamp leads to a unique part number.

Solution:

This approach is valid for modules that have been built with MOST
