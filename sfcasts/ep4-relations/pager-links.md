# Themed Pagination Links

Pagerfanta is now controlling the query for this page and returning only the
first 5 results. So... how do we get to page 2? How can we render some pagination
links?

Pagerfanta makes this delightfully easy. Scroll down. After the `endfor`, render
the pagination links with `{{ pagerfanta(pager) }}`.

Let's try it! Refresh and... bah!

> Unknown `pagerfanta()` Function

## Installing the Pagerfanta Twig Librari

This is *another* feature that's a "plugin" for Pagerfanta. Click back to the
library's docs and go to "Installation and set up? It lists a bunch of different
adapters... and also one *other* special package if you want Twig support for
Pagerfanta. Copy that package name, find your terminal, and install it:

```terminal
composer require pagerfanta/twig
```

Once that finishes, try the homepage again. This time... it works! Those links
are pretty ugly... but we'll fix that in a minute.

## Setting the Current Page

If you hover over the links, each adds a different `?page=`. There's 4 pages because
we have 20 total questions. So these links are smart: they render the correct
number based on the how many results you have and how many you're showing per page.

Go to page 2. Hmm, I think this is actually the same results as page 1. And if
look down at the links... even though you see `?page=2` on the URL, Pagerfanta
still highlights that we're on page 1. Why?

Because... we need to *help* Pagerfanta know which page we're on: we need to
*read* the `?q=` and *pass* it in.

Back in the controller, to read the query parameter. we need the request object.
Add `$request` argument type-hinted with the `Request` class from HttpFoundation
to get it. Then, below, add `$pagerfanta->setCurrentPage()` pasing
`$request->query->get('page', 1)` so that this will return `1` if there is *no*
`?q=` on the URL.

One small word of warning. At the time of recording, you can't *switch* these two
lines. You need to set the max for page and *then* the current page. If you swap
them, weird things happens. This may get fixed, but to be safe, put the lines in
this order.

*Anyways*, when we refresh now... beautiful! It sees that we're on page 2...
and the results look different. If we go to page 3... that works too! Woo!


## Customizing the Pagerfanta "View"

So let's talk about making these links prettier. You can *totally* customize these
links as much as you want, including with a custom template. But there are several
built-in, sort of "themes"... including one for Bootstrap 5.

Back on the bundle documentation, click on "Default Configuration". This bundle
has a `default_view` key... and one of the built-in views is called
`twitter_bootstrap5`.

So... where do we make this config change? When we installed the bundle, it
did *not* create a configuration file for us. And... that's fine! The bundle
works fine with the *default* config, so the author chose not to ship one.
And we can create one ourselves.

Copy this `babdev_pagerfanta` key. then, in `config/packages/`create a new file
called `babdev_pagerfanta.yaml`. Now technically, this file could be called
*anything*: there's no significance to the filenames in this directory. But
this name makes sense.

Inside, paste the root key, then set `default_view:` to `twitter_bootstrap5`.
Before recording, I dug into the documentation to discover that this is one of
the valid values.

Let's check it! Refresh and... huh... nothing changes: it's still rendering
*exactly* like before. I wonder if Symfony didn't see my new config file.
Let's manually clear the cache to be sure:

```terminal
php bin/console cache:clear
```

Refresh again and... got it! You shouldn't normally need to clear Symfony's
cache while developing, but if you're ever not sure, it's safe to try. The point
is, this *now* renders with Bootstrap 5 markup and it looks much better.

## Putting the {page} Into the Route

Let's try one more thing. What if, instead of having `?q=2` on the URL, we wanted
a URL like `/2`... so where the page is *inside* the main part of the URL.

That's... no problem. Over in `QuestionController`, add a new `/{page}` to the URL.
Now I need to be *very* careful because this is a wildcard. And so, if there are
any other URLs on the site that are just `/onething`, this route *could* break
those if it matches first.

To avoid that, let's make this route *only* match if the `{page}` part of the URL
is a *number*. Do that by adding a requirement - `<>` - with a regular expression
inside: `\d+`.

So, only match this route if `{page}` is a *digit* of any length. If we go to
`/foo`, this route won't match.

Give the controller an `int $page` argument and default it to 1. This will allow
the user to go to *just* `/`... and `$page` will be 1.

Below, pass the `$page` variable in directly. And.. we don't need the request
object at all anymore.

Phew! Let's try it! Refresh. It jumped back to page 1 because we're not reading
the page from the query parameter anymore. Click page 2. Yes! It's `/2`... then
`/3`! So cool!

Ok team! Congratulations on finishing *both* Doctrine courses! Doctrine is one of
*the* most important parts of Symfony and it will unlock you for almost *anything*
else you do. So let us know what cool stuff you're building and, if you have any
questions or ideas, we're here for you down in the comments.

Alright friend, seeya next time!