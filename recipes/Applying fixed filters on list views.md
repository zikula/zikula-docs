### Requirement:

###### Case example:

Providing a list view that only show elements created by the current user.

### Solution:

This approach is valid for modules that have been built with MOST ([ModuleStudio.de](https://modulestudio.de/))

1. Create a custom view template in folder `Resources/views/MyEntity/`.

For example: `viewCurrentUser.html.twig`

This template can be called by appending following parameter to the URL of the standard list view for the Entity

`?tpl=CurrentUser`

basic logic: If tpl is set to CurrentUser add a condition to the query


2. in `Helpers/CollectionFilterHelper.php` you need to add:

```
use Doctrine\ORM\QueryBuilder;
```




```
namespace ITThiele\JumbleSeekerModule\Helper;



use Doctrine\ORM\QueryBuilder;

use ITThiele\JumbleSeekerModule\Helper\Base\AbstractCollectionFilterHelper;



/**

* Entity collection filter helper implementation class.

*/

class CollectionFilterHelper extends AbstractCollectionFilterHelper

{

/**

  * @inheritDoc

  */

 protected function applyDefaultFiltersForArtikel(QueryBuilder $qb, array $parameters = [])

 {

     $qb = parent::applyDefaultFiltersForArtikel($qb, $parameters);

     if (null === $this->request) {

         return $qb;

     }



     $tpl = $this->request->query->get('tpl', '');

     if ($tpl == 'Anbieter') {

          $qb = $this->addCreatorFilter($qb);

     }



     return $qb;

 }

}
```
