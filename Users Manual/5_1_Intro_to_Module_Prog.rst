.. _HTML: http://www.w3schools.com/htmL/
.. _Style: http://www.w3schools.com/Css/default.asp
.. _template: http://twig.sensiolabs.org
.. _php: http://php.net/manual/en/tutorial.php
.. _object: http://net.tutsplus.com/tutorials/php/object-oriented-php-for-beginners/
.. _zikula-modules: https://github.com/zikula-modules

Introduction to Module Writing
------------------------------

So you want to design your own custom module?

The Module design system in Zikula is not for the faint-of-heart, but with a bit of effort and some patience, you can make some very powerful functionality. This tutorial assumes you are familiar with...

*Writing html code (HTML_ tutorial)
*Controlling text and page layout using style sheets (Style_ sheet tutorial)
*Templating using a template_ engine (optional)
*Programming in php (php_ for beginners). Understanding object_ php is also very helpful
*A good understanding of how zikula works from an administrators perspective

If these are new technologies for you, it would be wise to first become familiar with the basics of these technologies, before you attempt to write a module. Do you have to read all that background material?!? No, but the references that I point to are great places to go if you get stuck. At least take a look at them to check your background. It is possible to code a module without a lot of experience in php, html and css, but you will need to be patient.

Good, you are still here, that means you're committed to a module. First sit down and think hard about what you are trying to accomplish with your module. You need to make sure that you have a very clear picture of where you want to go. What you want your module to do. How you want it to display. Once you have that set, before you start writing code, search zikula-modules on github. Many modules have been created by the community and someone may have implemented your idea. If you can't find a perfect module, maybe you can find one close to what you want and contribute to its development.

If you really do have an original idea, then its time to start writing code. There is one last final important decision, will it be made available to the entire community or is this a 'For Your Site Only' module. In most cases it would be advisable to share your code, unless you have a very good reason not to. No matter if you are starting a module from scratch or contributing to an established module. The linked tutorials here and 
elsewhere can get you started on the basic design for a module.

Whatever direction you do go, make sure you always fight evil and never smoke! - randomblink∞