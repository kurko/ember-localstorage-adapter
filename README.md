Ember Data Local Storage Adapter
================================

Store your ember application data in localStorage.

Compatible with Ember Data 1.0.beta.5.

**NOTE**: New versions of the `localStorage` adapter are no longer compatible
with older versions of Ember Data. For older versions, checkout the `pre-beta`
branch.

Usage
-----

Include `localstorage_adapter.js` in your app and then like all adapters:

```js
App.ApplicationSerializer = DS.LSSerializer.extend();
App.ApplicationAdapter = DS.LSAdapter.extend({
    namespace: 'yournamespace'
});
```

### Local Storage Namespace

All of your application data lives on a single `localStorage` key, it defaults to `DS.LSAdapter` but if you supply a `namespace` option it will store it there:

```js
DS.LSAdapter.create({
  namespace: 'my app'
});
```

### Models

Whenever the adapter returns a record, it'll also return all
relationships, so __do not__ use `{async: true}` in you model definitions.

#### Namespace

If your model definition has a `url` property, the adapter will store the data on that namespace. URL is a weird term in this context, but it makes swapping out adapters simpler by not requiring additional properties on your models.

```js
var List = DS.Model.extend({
  // ...
});
List.reopen({
  url: '/some/url'
});
```

### Quota Exceeded Handler

Browser's `localStorage` has limited space, if you try to commit application data and the browser is out of space, then the adapter will trigger the `QUOTA_EXCEEDED_ERR` event.

```js
App.store.adapter.on('QUOTA_EXCEEDED_ERR', function(records){
  // do stuff
});

App.store.commit();
```

### Searching for records
The adapter supports queries that look like this:

```js
{
    <property to query>: <value to match>,
    ...
}
```

The ```<value>``` can currently be either:

* A value that the property must be equal to.
* A regex for strings that it must match.
* An array of values, similar to SQL.

The first two are used depending on if the value is just a single value or if it is a Regex.

In order to use the array of values one must make an object with an in_array property, an example of this can be seen here:

```js
{
    user_id: { in_array: [1,2,3] }
}
```

This will return records where the user_id is either of the values: 1, 2, or 3.
This is similar to the way SQL's IN works.

Todo
----

- Make the repo nicer to work with long-term (do something more intelligent with dependencies found in `vendor`, etc.)

Tests
-----

Open `tests/index.html` in a browser. If you have `phantomjs` installed,
run

    phantomjs test/runner.js test/index.html

License & Copyright
-------------------

Copyright (c) 2012 Ryan Florence
MIT Style license. http://opensource.org/licenses/MIT
