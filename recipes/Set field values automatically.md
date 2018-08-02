Requirement:

Automated assignation of values to hidden form fields.

Case example:

Creation of a part number consisting of the 'current user ID' & timestamp.

A URL parameter like `&set_partnumber=xyz` is not an option in this case as the user potentially could manipulate the field value, even if the form field is 'read only' or 'hidden'.

Assuming that a user is only logged in once at a time, the combination of userID & timestamp leads to a unique part number.

Solution:

This approach is valid for modules that have been built with MOST

1. In `Resources/config/entityFactory.yml` find the definition of your entity intialiser and then add below it's arguments:

`calls:`
`- [setCurrentUserApi, ['@zikula_users_module.current_user']]`


2. Next edit `Entity/Factory/EntityInitialiser.php` and replace

`/**

 * Entity initialiser class used to dynamically apply default values to newly created entities.

 */

class EntityInitialiser extends AbstractEntityInitialiser

{

    // feel free to customise the initialiser

}`

with

`use Zikula\UsersModule\Api\ApiInterface\CurrentUserApiInterface;
use ITThiele\JumbleSeekerModule\Entity\FooEntity;


/**

 * Entity initialiser class used to dynamically apply default values to newly created entities.

 */

class EntityInitialiser extends AbstractEntityInitialiser

{

    /**

     * @var CurrentUserApiInterface

     */

    protected $currentUserApi;



    public function setCurrentUserApi(CurrentUserApiInterface $currentUserApi)

    {

        $this->currentUserApi = $currentUserApi;

    }



    /**

     * @inheritDoc

     */

    public function initFoo(FooEntity $entity)

    {

        $entity = parent::initFoo($entity);



        $userId = $this->currentUserApi->get('uid');

        $entity->setTableColumn($userId . '-' . time());



        return $entity;

    }

}`

Replace `Foo` with the name of your Entity.

Replace `TableColumn` with the name of the column you want to fill automatically.

3. Inside MOST look at the application properties,

add `Resources/config/entityFactory.yml` to `markFiles` and
add `Entity/Factory/EntityInitialiser.php` to `skipFiles`
so that the generator won't override your changes.

4. Also inside MOST look at the properties of the `TableColumn` field and set `visible=false`.

5. Regenerate and copy/move the new files to your existing installation.

6. Test if it works (the TableColumn field should not be part of the edit form anymore, but it's value should be set automatically without any GET or POST parameters)
