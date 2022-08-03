+++
title = "Return Laravel Json Resource as Associative Array"
date = "2021-03-04T10:02:07+02:00"
cover = ""
tags = ["php", "laravel","architecture","api"]
keywords = ["php", "laravel","architecture","api"]
description = ""
summary="I recently ran into the same issue as mentioned in this stackoverflow thread: https://stackoverflow.com/questions/52613298/return-array-in-laravel-api-resource-without-reordering-keys If you have custom data structures and you want to use JsonResource's for constency sake,…"
slug="return-laravel-json-resource-as-associative-array"
+++

I recently ran into the same issue as mentioned in this stackoverflow thread: <https://stackoverflow.com/questions/52613298/return-array-in-laravel-api-resource-without-reordering-keys>
If you have custom data structures and you want to use JsonResource's for constency sake, read further to make sure your data is intact when it is send to the client.

At a certain point in time, when building an API in Laravel, you may have a requirement to run a report on some data living in your app. The resulting data-structure of this report can take different forms due to its nature or some other reason. Maybe it's more performant to use a specific data format so the client does not have to do the heavy lifting or vice-versa, etc...

For example: when generating a report to collect the total number of sales invoices per client and invoice type, you probably already know we'll do a simple `GROUP BY` on `client_id` and `invoice_type`. The result will look something like this:

```json

[
  "1" => [
    "invoice" => 3,
    "credit_note" => 1
  ],
  "2" => [
    "invoice" => 120,
    "credit_note" => 6
  ],
  // ... n clients
]

```

When you use the above result as an input for a `JsonResource`-object in Laravel, you'll end up with something like this on the client:

```json

data: [
  {
    "invoice": 3,
    "credit_note": 1
  },
  {
    "invoice": 120,
    "credit_note": 6
  },
  [
    {
      "invoice": 120,
      "credit_note": 9
    },
    {
      "invoice": 7,
      "credit_note": 45
    }
  ],
  // ... n clients
]

```

Not exactly what you'd want, right? The underlying issue here is that Laravel uses a `filter`-method when parsing the provided resource (Model, array, stdClass, ...) which removes unnecessary fields and keys. In this case, well, not so unnecessary data. The solution is quite simple, as always! In the custom `JsonResource`-class you created, you just need to override the `filter`-method:

```php

class MyCustomResource extends JsonResource
{
    /**
     * Keep resource keys as they are.
     * If set to `false` (default), the JsonResource's filter will flatten the array/collection without numerical keys
     *
     * @var boolean
     */
    protected $preserveKeys = true;
}

```