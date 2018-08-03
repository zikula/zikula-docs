### Requirement:

###### Case example:

Providing a list view that only shows elements created by the current user.

The URL parameter `?own=1` is not an option in this case because the user could manipulate the URL.

### Solution:

This approach is valid for modules that have been built with MOST ([ModuleStudio.de](https://modulestudio.de/))

1. Create a custom view template in folder `Resources/views/MyEntity/`.

For example: `viewCurrentUser.html.twig`

This template can be called by appending following parameter to a standard list view URL.

`?tpl=CurrentUser`

`http://YourDomain/YourModule/YourEntity/view?tpl=CurrentUser`

2. In `Helpers/CollectionFilterHelper.php` you need to add:

```
use Doctrine\ORM\QueryBuilder;
```
In between these two lines:
```
namespace ITThiele\JumbleSeekerModule\Helper;

--> PLACE IT IN HERE !!! <--
use ITThiele\JumbleSeekerModule\Helper\Base\AbstractCollectionFilterHelper;

```

3. Then you need to extend the class `CollectionFilterHelper` as follows:

```
/**
* Entity collection filter helper implementation class.
*/
class CollectionFilterHelper extends AbstractCollectionFilterHelper
{
/**
* @inheritDoc
*/
protected function applyDefaultFiltersForYourEntity(QueryBuilder $qb, array $parameters = [])
 {
     $qb = parent::applyDefaultFiltersForYourEntity($qb, $parameters);

     if (null === $this->request) {
         return $qb;
     }

     $tpl = $this->request->query->get('tpl', '');

     if ($tpl == 'YourTemplate') {
          $qb = $this->addCreatorFilter($qb);
     }
     return $qb;
 }
}
```

`YourEntity` is the upper-cased name of your entity.

`YourTemplate` is the upper-cased name of your template. Here `CurrentUser`
