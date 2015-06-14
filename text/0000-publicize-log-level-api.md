- Start Date: 2015-06-14
- RFC PR: (leave this empty)
- Ember Issue: (leave this empty)

# Summary

Publicize Log Level API for `Ember.deprecate` to allow different log levels.

# Motivation

Currently a call to `Ember.deprecate()` either logs the deprecation message or
throws an error, depending on the `ENV['RAISE_ON_DEPRECATION']` flag. Some
deprecations need much work so the code base is fully transitioned into the
new, preferred way. In [PR #1141](https://github.com/emberjs/ember.js/pull/11419)
a private log level API has been introduced, which allows finer grained control
if specific deprecations should be logged, throwing an error or be silenced
completely:


    Ember.Debug._addDeprecationLevel('my-feature', Ember.Debug._deprecationLevels.LOG);
    ...
    Ember.deprecate("x is deprecated, use Y instead", false, { id: 'my-feature' });


Since this is currently a private API, its use is discouraged outside internals
of Ember.js, though a certain usefulness is given.

# Detailed design

Publicize the current private APIs by simple rename:

    Ember.Debug._addDeprecationLevel -> Ember.Debug.addDeprecationLevel
    Ember.Debug._deprecationLevels -> Ember.Debug.deprecationLevels

# Drawbacks

Unsure of any at this time.

# Alternatives

- custom depreaction handling

# Unresolved questions

- Change API besides a simple rename?
