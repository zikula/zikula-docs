### Requirement:

###### Case example:

Providing a list view that only shows elements created by the current user.

Basic logic: If tpl is set to `CurrentUser` add a condition to the query.

The URL parameter `?own=1` is not an option as the user could manipulate the URL

### Solution:

This approach is valid for modules that have been built with MOST ([ModuleStudio.de](https://modulestudio.de/))

1. Create a custom view template in folder `Resources/views/MyEntity/`.

For example: `viewCurrentUser.html.twig`

This template can be called by appending following parameter to the URL of the standard list view.

`?tpl=CurrentUser`


2. In `Helpers/CollectionFilterHelper.php` you need to add:

```
use Doctrine\ORM\QueryBuilder;
```
In between these two lines:
```
namespace ITThiele\JumbleSeekerModule\Helper;

--> PLACE IT IN HERE !!!
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

     if ($tpl == 'CurrentUser') {
          $qb = $this->addCreatorFilter($qb);
     }
     return $qb;
 }
}
```

whereby `YourEntity` is the upper-cased name of your entity

`blabla` is the name of your filter field

and `123` is your filter value
