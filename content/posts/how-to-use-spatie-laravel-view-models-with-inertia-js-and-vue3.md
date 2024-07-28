+++
title = 'How to use Spatie Laravel View Models with Inertia JS and Vue 3'
description = 'How to use Spatie Laravel View Models with Inertia JS and Vue 3'
date = 2024-07-28T17:21:27+05:45
draft = false
+++

View models are a useful concept for passing data to the view. They help clean up the code and allow us to reuse similar data, usually in our create and update forms.

### Problem:

Recently, I was working on a VILT (Vue, Inertia, Laravel & Tailwind) Stack project where there was a lot of data that needed to be passed to the view for the create form. Almost the same data needed to be passed to the update form as well, which caused duplication and made the controller look messy. To clean things up, I used Spatie Laravel View Models to pass data to the view, and I was using Inertia along with Vue for the frontend.

Here is an example of the data that I needed to pass from the controller's create method:

```php
public function create(): Response
{
    // Example data
    $categories = Category::latest()->get();
    $branches = Branch::latest()->get();
    $candidates = Candidate::query()
        // some complex query
        ->paginate();
    // And more +

    return Inertia::render('SomeFile/Create', [
        'categories' => $categories,
        'branches' => $branches,
        'candidates' => $candidates,
        // And more.
    ]);
}
```

And, the same data needed to be passed to the edit page:

```php
public function edit(SomeModel $someModel): Response
{
    // Example data
    $categories = Category::latest()->get();
    $branches = Branch::latest()->get();
    $candidates = Candidate::query()
        // some complex query
        ->paginate();
    // And more +

    return Inertia::render('SomeFile/Edit', [
        'categories' => $categories,
        'branches' => $branches,
        'candidates' => $candidates,
        // And more.
    ]);
}

```

To prevent this duplication and the messy-looking code, I used Spatie view models. Let me show you how to refactor it step by step, starting from the installation.

### Installation
```sh
composer require spatie/laravel-view-models
```

### Create ViewModel File.
```sh
php artisan make:view-model SomepageViewModel
```

```php
// SomepageViewModel
// SomepageViewModel
class SomepageViewModel extends ViewModel
{
    public function __construct(private readonly ?SomeModel $someModel = null)
    {
    }

    public function someModel()
    {
        if (!$this->someModel) {
            return null;
        }

        return $this->someModel;
    }

    public function categories()
    {
        return Category::latest()->get();
    }

    public function branches()
    {
        return Branch::latest()->get();
    }

    public function candidates()
    {
        Candidate::query()
            // some complex query
            ->paginate();
            // And more +
    }

    // And more other functions
}

```

Now, let's use this view model in our controller's create and edit methods.

```php
public function create(): Response
{
    $viewModel = new SomepageViewModel();

    // Here we are calling toArray methods so that it will pass all the props
    // that we specify in our vue file eg: defineProps({ categories: Object, branches: Object ...})
    return Inertia::render('SomeFile/Create', $viewModel->toArray());
}
```

Similarly, we can use the view model in our edit method:

```php
public function edit(SomeModel $someModel): Response
{
    $viewModel = new SomepageViewModel($someModel);

    return Inertia::render('SomeFile/Edit', $viewModel->toArray());
}

```

Finally, we refactored the code by introducing view models, which helped us remove the duplication and bulky code in our controller. I hope this post was helpful for you. Make sure to share this post with your friends. Cheers!
