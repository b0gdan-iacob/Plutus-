## !!!! Do not post your replies as comments! Clone the gist and send it to us

1. List the top 3 things you do not like about Laravel
`
Abstracted Database Operations Can Mask Performance Issues,Laravel's Eloquent ORM simplifies database interactions, but this abstraction can sometimes mask inefficient database queries. 

While Laravel provides an array of out-of-the-box features that expedite development, this richness comes at the cost of resource intensiveness. The framework can be heavier compared to leaner PHP frameworks, potentially leading to slower response times and greater resource consumption, particularly in applications where performance is critical.

Laravel's high level of abstraction, aimed at simplifying development, can sometimes be a double-edged sword. This abstraction can hide important details, making it difficult for developers to understand what's going on behind the scenes. This can lead to challenges in debugging and optimizing application performance.
`
2. List the top 3 things you like about Laravel
`
Laravel's out-of-the-box support for testing with PHPUnit, including database migrations and seeding, makes it easier to adopt a test-driven development (TDD) approach. This focus on testing is often underappreciated but is crucial for building reliable and maintainable applications.

Middleware for HTTP Request Handling: Laravel's middleware offers a powerful and flexible way to handle HTTP requests and responses. It allows developers to perform actions like authentication, logging, and request filtering in a clean and reusable way, which is often overlooked when considering Laravel's features.

Laravel comprehensive and well-structured documentation, making it accessible. Additionally, the vibrant community provides an abundance of resources, tutorials, and forums, which greatly aids in problem-solving and learning.
`
3. For service providers, what is the difference between boot() and register()?
`
 register() method is used for binding services into the service container. It's about registering services, bindings, and listeners. It is always executed before any boot() methods.
 The boot() method is used for bootstrapping services, like registering events, routes, filters, and so on. It's called after all services have been registered (register() method) and is a good place to put code that requires all other services to be already registered.
`
4. Present a scenario (or more) where you would create ServiceProvider for your app and NOT use those already provided by Laravel
`
Event and Listener Registration
Scenario:
For applications with a complex event-driven architecture, you might find the built-in event service provider limiting. Creating a CustomEventServiceProvider would allow you to:
Register a large number of events and listeners in a more organized way.
Set up specific event-related middleware or route groupings.
Customize how events are logged or handled, beyond Laravel's default capabilities.`

`Data Transformation and Reporting
Scenario:
If your application requires complex data transformation, analytics, or reporting features, a DataServiceProvider could be used to:
Initialize and configure data processing libraries or services.
Bind interfaces for various data transformers or report generators to concrete implementations.
Register any routes, commands, or controllers that are specific to data processing and reporting tasks.
`
5. List some Laravel packages you have used recently and that you liked working with
`
laravel/passport
spatie/laravel-permission
alymosul/laravel-exponent-push-notifications
`
6. Present some techniques to organize a Laravel application as to be loosely coupled with the framework.
`
Implement Laravel's interfaces to decouple code from specific implementations of Laravel services.
Use dependency injection rather than facades for easier testing and flexibility.
Service providers for binding interfaces to implementations, which can be easily swapped.
`
7. Given the overhead they introduce why would you use Blade components instead of including sub-views?
`
Blade components are more reusable and maintainable, as they allow for more structured and clear hierarchies.
They provide a better way to bind data and handle logic, making templates cleaner.
In conclusion despite the overhead, the use of Blade components often leads to more organized, maintainable, and scalable templates compared to traditional sub-views.
`
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

Used `collect` function collection from the $marketplaces array then `mapWithKeys` method is used to iterate over the items. This method passes each value to the given callback and uses the returned array's keys to associate with the values and `match` determining the status. It works similarly to a switch-case but is more concise.
The status is merged into the $marketplace array using array_merge() then returns a collection of marketplaces with their updated statuses. 

```php

function getMarketplacesStatuses($marketplaces) {
    return collect($marketplaces)->mapWithKeys(function ($marketplace) {
        $status = match($marketplace['status']) {
            'active' => 'active',
            'error', 'in_progress' => 'error',
            default => 'queue'
        };
        return [$marketplace['type'] => array_merge($marketplace, ['status' => $status])];
    });
}


```