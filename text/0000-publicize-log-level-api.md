- Start Date: 2015-06-15
- RFC PR: (leave this empty)
- Ember Issue: (leave this empty)

# Summary

Deprecations and warnings in Ember.js should have configurable default log
levels (raise, log, silence) and individual notice log levels. For example,
in Ember 1.13 many view deprecations are silenced in the Ember codebase
itself.

Currently this is implemented using private APIs:

  * `Ember.Debug._addDeprecationLevel`
  * `Ember.Debug._deprecationLevels`

However Ember-Data and the Ember Inspector have both requested a public
API solution for this challenge.

# Motivation

`Ember.deprecate` and `Ember.warn` usually log messages. With `ENV.RAISE_ON_DEPRECATION`
all deprecations will throw an exception. In some scenarios, this
is less than ideal:

* Ember itself needs a way to silence some deprecations before their usage
  is completely removed from tests. For example, many view APIs in Ember 1.13.
* The Ember inspector desires to raise on specific deprecations, or silence
  specific deprecations.
* Ember-Data also desires to silence some deprecations in tests

In [PR #1141](https://github.com/emberjs/ember.js/pull/11419)
a private log level API has been introduced, which allows finer grained control
if specific deprecations should be logged, throwing an error or be silenced
completely. For example:

```js
Ember.Debug._addDeprecationLevel('my-feature', Ember.Debug._deprecationLevels.LOG);
// ...
Ember.deprecate("x is deprecated, use Y instead", false, { id: 'my-feature' });
```

This RFC proposes to extend and make public the functionality of that interal
API.

# Detailed design

A log level API should be exposed for deprecations and warnings:

```js
Ember.Debug.deprecations.setLevel(id, level);
Ember.Debug.deprecations.setDefaultLevel(level);
Ember.Debug.warnings.setLevel(id, level);
Ember.Debug.warnings.setDefaultLevel(level);
```

And the various levels should be exposed:

```js
Ember.Debug.logLevels.RAISE
Ember.Debug.logLevels.LOG
Ember.Debug.logLevels.SILENCE
```

Setting a default level applies that level to any calls without an explicitly
set level. For example:

```js
Ember.Debug.deprecations.setDefaultLevel(Ember.Debug.logLevels.RAISE);
Ember.deprecate('Oh my, this raises');
```

An id can be passed to `setLevel` to override the default:

```js
Ember.Debug.deprecations.setDefaultLevel(Ember.Debug.logLevels.RAISE);
Ember.Debug.deprecations.setLevel('foo', Ember.Debug.logLevels.SILENCE);
Ember.deprecate('Shhhh, stay quiet', false, {id: 'foo'});
```

# Drawbacks

This is inspired by a new API, just added recently in Ember 1.13. We may be
missing a more general primative unrelated to log level, or want to extend
this functionality for assertions.

# Alternatives

Each app can stub out `deprecate` and `warn`.

# Unresolved questions

None
