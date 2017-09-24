=============================================
Providing admin and user links to your module
=============================================

A well-behaved module will have a number of links listing all the functions that the admin and users can use while using the module. These are normally present as drop-down menus anchored to a menubar. Zikula takes care of all the formatting, and you as the developer just have to provide a hierarchical array of links. Before 2.0 this was handled by having a function getLinks() in the AdminApi.php and UserApi.php classes. In 2.0, this is not allowed and a dedicated class extended from the LinkContainer class is required. Implementation of this is a little tricky because you have to make Zikula aware that your module provides this link service. There are three parts to this:

-- First, provide a services file in your Resources/config folder name services.yml that lists all the services your module implements. Here is the code for providing this service.

::
    
     services:
      paustian_book_module.container.link_container:
            class: Paustian\BookModule\Container\LinkContainer
            arguments:
              - "@translator.default"
              - "@router"
              - "@zikula_permissions_module.api.permission"
            tags:
                - { name: zikula.link_container }

Note that services must appear at the top of the file. The service listed points to your LinkContainer class. 

-- You have to implement this LinkContainer class. Inside it you list the links of your module. Here is an example:

::

    <?php

    namespace Paustian\BookModule\Container;

    use Symfony\Component\Routing\RouterInterface;
    use Zikula\Common\Translator\TranslatorInterface;
    use Zikula\Core\LinkContainer\LinkContainerInterface;
    use Zikula\PermissionsModule\Api\ApiInterface\PermissionApiInterface;

    class LinkContainer implements LinkContainerInterface
    {
        /**
         * @var TranslatorInterface
         */
        private $translator;

        /**
         * @var RouterInterface
         */
        private $router;

        /**
         * @var PermissionApiInterface
         */
        private $permissionApi;

        /**
         * constructor.
         *
         * @param TranslatorInterface $translator
         * @param RouterInterface $router
         * @param PermissionApiInterface $permissionApi
         **/
        public function __construct(
            TranslatorInterface $translator,
            RouterInterface $router,
            PermissionApiInterface $permissionApi
        )
        {
            $this->translator = $translator;
            $this->router = $router;
            $this->permissionApi = $permissionApi;
        }

        /**
         * get Links of any type for this extension
         * required by the interface
         *
         * @param string $type
         * @return array
         */
        public function getLinks($type = LinkContainerInterface::TYPE_ADMIN)
        {
            if (LinkContainerInterface::TYPE_ADMIN == $type) {
                return $this->getAdmin();
            }
            if (LinkContainerInterface::TYPE_ACCOUNT == $type) {
                return $this->getAccount();
            }
            if (LinkContainerInterface::TYPE_USER == $type) {
                return $this->getUser();
            }

            return [];
        }

        /**
         * get the Admin links for this extension
         *
         * @return array
         */
        private function getAdmin()
        {
            $links = [];
        
            if ($this->permissionApi->hasPermission($this->getBundleName() . '::', '::', ACCESS_ADMIN)) {


                $submenulinks = [];
                $submenulinks[] = [
                    'url' => $this->router->generate('paustianbookmodule_admin_edit'),
                    'text' => $this->translator->__('Create New Book'),
                    ];

                $submenulinks[] = [
                    'url' => $this->router->generate('paustianbookmodule_admin_modify'),
                    'text' => $this->translator->__('Edit or Delete Book'),
                     ];

                $links[] = [
                    'url' => $this->router->generate('paustianbookmodule_admin_edit'),
                    'text' => $this->translator->__('Books'),
                    'icon' => 'book',
                    'links' => $submenulinks];

                /*more linke here*/

            }
            return $links;
        }
    //You can provide dummy functions if you do no have links for this type.
        private function getUser()
        {
            $links = [];

            return $links;
        }

        private function getAccount()
        {
            $links = [];

            return $links;
        }

        /**
         * set the BundleName as required by the interface
         *
         * @return string
         */
        public function getBundleName()
        {
            return 'PaustianBookModule';
        }
    }
