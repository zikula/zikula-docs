EXAMPLES IN USE
===============

Here are some code examples::

    /* This is a fictitious example of events in action */
    // setup event manager
    $eventManager = new Zikula_EventManager();
    $avatar = new Avatar();

    // attach a static callable Stuff::logLogin($event);
    $eventManager->attach('user.login', array('Stuff', 'logLogin'));

    // attach a read instanciated class
    $eventManager->attach('user.create', array($avatar, 'create'));  // $avatar->create($event);
    $eventManager->attach('user.create', array('Stuff', 'sendMail')); // Stuff::sendMail($event);

    $example = new Example($eventManager);
    $example->login('Joe');
    $example->create('Sally');

    class Example
    {
        protected $eventManager;

        public function __construct(Zikula_EventManager $eventManager)
        {
            $this->eventManager = $eventManager;
        }

        public function login($user)
        {
            //... stuff here
            $event = new Zikula_Event('user.login', null, array('username' => $user));
            $this->eventManager->notify($event);
        }

        public function create($username)
        {
            //... stuff here
            $user = new User($username);
            $user->save();
            $event = new Zikula_Event('user.create', $user);
            $this->eventManager->notify($event);
        }
    }

    class Stuff
    {
        public static function logLogin(Zikula_Event $event)
        {
            MyLogger::write($event->getName(), $event->getArg('username'), $_SERVER['REMOTE_ADDR']);
        }

        public function sendMail(Zikula_Event $event)
        {
            if ($event->hasArg('emailaddress')) {
                mail($this->getArg('emailaddress'));
            }
        }
    }

    class Avatar
    {
        public function create(Zikula_Event $event)
        {
            if ($event->getSubject() instanceof User) {
                $user = $event->getSubject();
                $user->addProperty('image', 'avatar.gif');
                $user->save();
            }
        }
    }

