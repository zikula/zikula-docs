The Search Module
=================
:Author:
    Christian Flach

Module information
------------------
:Full name:
    ZikulaSearchModule
:Display name:
    Site search
:Description:
    This module makes it possbile to search for any content on your site.
:Deactivatable:
    No
:Has admin interface:
    Yes
:Has user interface:
    Yes

Introduction
------------
The search module makes it possible to search your site's content. Every module can (and should) provide a search plugin to tell the search module if it has searchable content.

The admin interface
-------------------
The admin interface only contains one screen, the configuration screen. In most cases you do not need to change something here. However, you have the following options:

General settings
^^^^^^^^^^^^^^^^
:Items per page: 
    How many search results shall be shown to the user at one time.
:Number of characters to display in item summaries:
    Here you can decide how much characters of for example a news article shall be shown as a preview to the user on the search results page.

Disable search plugins
^^^^^^^^^^^^^^^^^^^^^^
Here you see a list of all modules providing search plugins. This means content of any module not listed here or with a checked checkbox is not displayed in the search results. The only core module providing a search plugin is the Users module.

OpenSearch
^^^^^^^^^^
OpenSearch_ tells browsers (and every other client interested) how it can search your page. This is done via a configuration file included in the ``<head>`` section of your site::

  <link rel="search" type="application/opensearchdescription+xml" title="Site name" href="/index.php?module=search&amp;type=user&amp;func=opensearch" />

:Enable OpenSearch:
    Here you can decide if you want to enable OpenSearch (`documentation <http://www.opensearch.org/Home>`_).
:Does this page contain adult content?:
    Tick this box if your page contains adult content. The ``AdultContent`` option in the included configuration file will be set to true (`documentation of this option <http://www.opensearch.org/Specifications/OpenSearch/1.1#The_.22AdultContent.22_element>`_).

The user interface
------------------
The user interface has to functions: The ``search`` page and the ``recent searches`` page.

The search page
^^^^^^^^^^^^^^^
:Function:
    ``form`` (``main`` redirects to the ``form`` function too)

You enter the keywords you are searching for and select in which modules you want to search for content. Modules may add further filter options here.

The recent searches page
^^^^^^^^^^^^^^^^^^^^^^^^
:Function:
    ``recent``

On this page you see a list of the latest searches and how often a particular keyword was searched for.

.. _OpenSearch: http://en.wikipedia.org/wiki/OpenSearch
