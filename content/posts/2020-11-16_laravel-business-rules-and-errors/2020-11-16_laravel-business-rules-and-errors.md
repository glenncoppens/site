+++
title = "Laravel Business Rules & Errors"
date = "2020-11-16T11:21:46+02:00"
cover = ""
tags = ["php", "laravel","architecture"]
keywords = ["php", "laravel","architecture"]
description = ""
summary="👉 Wondering how to use business validation errors?"
slug="laravel-business-rules-and-errors"
+++

In my opinion:

-   *Form Requests* are ideally used for tier-1 "Form Validation", not specifically tier-2 "Business rules validation", because there can (and will) be *n-amount of business validation paths* resulting out of only 1 Form Request (based on the user input)
-   so handling and throwing these inside a Form Request or a Controller method is *not the preferred way of doing Business Validation* in your app, so what is the preferred way?
    -   as the term "Business Validation" already implies, this type of validation belongs to the specific domain you're working in
    -   these validations may pop up anywhere in your business logic such as for example in Actions (if you're using a domain driven approach)
-   Laravel already provides you with an easy solution to throw Validation Exceptions ánd let's you even add custom messages. This is how you could do it:

```php

  throw ValidationException::withMessages(['error.business_logic_incorrect' => 'The invoice total amount cannot be greater than 0 if it is a Credit Note.']);

```

PHP

Copy

Have a great day/night!

G.