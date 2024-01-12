## !!!! Do not post your replies as comments! Clone the gist and send it to us

1. List the top 3 things you do not like about Laravel
2. List the top 3 things you like about Laravel
3. For service providers, what is the difference between boot() and register()?
4. Present a scenario (or more) where you would create ServiceProvider for your app and NOT use those already provided by Laravel
5. List some Laravel packages you have used recently and that you liked working with
6. Present some techniques to organize a Laravel application as to be loosely coupled with the framework.
7. Given the overhead they introduce why would you use Blade components instead of including sub-views?

# Refactor this code

The purpose of the refactoring is to make the code more readable and, maybe, use features available in Laravel

```php
// $purchase is an Eloquent model that has item (ie: purchase items) which belong to a product
$sections = [];
foreach ($purchase->items as $item) {
    $section = $item->product->type == 'electronics' ? 'electronics' : 'non-electronics';
    if ( ! in_array($section, $sections)) {
        $sections[] = $section;
    }
}
```

Refactored the above code to be more readable $purchase->items is the collection of items, i've used `map` to loop and transform each item into either 'electronics' or 'non-electronics' type. Then with `unique` Removed any duplicates from the collection, `values` Resets the keys on the collection and aditionaly with `all()` to convert the collection back into a plain PHP array.
```php

$sections = $purchase->items
    ->map(function ($item) {
        return $item->product->type == 'electronics' ? 'electronics' : 'non-electronics';
    })
    ->unique()
    ->values();

```

# Refactor this code
```php
function getMarketplacesStatuses() {
  $result = [];

  /**
   * example $marketplace = ['type' => 'ebay', 'status' => 'error']
   */
  foreach ($marketplaces as $marketplace) {
      $status = null;

      if ($marketplace['status'] == 'active') {
          $status = 'active';
      } elseif ($marketplace['status'] == 'error' || $marketplace['status'] == 'in_progress') {
          $status = 'error';
      } else {
          $status = 'queue';
      }

      $marketplace['status']                    = $status;
      $result[$marketplace['type']] = $marketplace;
  }

  return $result;
}
```  