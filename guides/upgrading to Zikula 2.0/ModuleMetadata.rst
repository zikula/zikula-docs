=====
Module metadata
=====

In previous Zikula versions, module metadata was stored in a version php file entitled FooModuleVersion.php. Inside this module you would implement a function, getMetaData. This function would fill an array with pertinent information about your module and return it. The function would be something like this...
::
    <?php
    namespace Paustian\BookModule;

    class BookModuleVersion extends \Zikula_AbstractVersion
    {
        public function getMetaData()
        {
            $meta = array();
            $meta['name'] = __('Book');
            $meta['version'] = '3.0.0';
            $meta['displayname'] = __('Book');
            $meta['description'] = __('A module for displying a large structured document, creating figure descriptions for the book, and a glossary.');
            // this defines the module's url and should be in lowercase without space
            $meta['url'] = $this->__('book');
            $meta['core_min'] = '1.4.0'; // Fixed to 1.3.x range
            $meta['capabilities'] = array(HookUtil::SUBSCRIBER_CAPABLE => array('enabled' => true),
                                         AbstractSearchable::SEARCHABLE => array('class' => 'Paustian\BookModule\Helper\SearchHelper'));
            $meta['securityschema'] = array('PaustianBookModule::' => 'Book::Chapter');
            $meta['author'] = 'Timothy Paustian';
            $meta['contact'] = 'http://http://www.bact.wisc.edu/faculty/paustian/';
        
            return $meta;
        }
    }

In Zikula 2.0, this has been replaced by a .json file that is read in to get the metadata. For any module placed into the modules folder, if you go to the extensions page to read it in, Zikula scans the root folder of the module for a file entitled, composer.json. This is then parsed to obtain the metadata. It is a required file in 2.0. Below I place the same information that used to be in the Module Version php file into a composer.json file.

::
    {
      "name": "paustian/book-module",
      "version": "4.0",
      "description": "A module for displying a large structured document, creating figure descriptions for the book, and a glossary.",
      "type": "zikula-module",
      "license": "LGPL-3.0+",
      "authors": [
        {
          "name": "Timothy Paustian",
          "homepage": "http://www.microbiologytext.com/"
        }
      ],
      "autoload": {
        "psr-4": {
          "Paustian\\BookModule\\": ""
        }
      },
      "require": {
        "php": ">5.4.0"
      },
      "extra": {
        "zikula": {
          "url": "book",
          "class": "Paustian\\BookModule\\PaustianBookModule",
          "core-compatibility": ">=1.4.3",
          "displayname": "Book Module",
          "oldnames": [],
          "capabilities": {
            "admin": {"route": "paustianbookmodule_admin_edit"},
            "user": {"route": "zikulausersmodule_user_view"}
          },
          "securityschema": {
            "PaustianBookModule::": "Book::Chapter"
          }
        }
      }
    }

As you can see it contains all the same information, just in another format. One major exception is the capabilities area. This is where you define the default routes that link to your module. This are used in the extension list to point at your module.