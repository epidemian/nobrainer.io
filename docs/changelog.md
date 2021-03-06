---
layout: docs
title: Changelog
prev_section: roadmap
next_section: 3rd_party_integration
permalink: /changelog/
---

### git HEAD

* Added a modular profiler, which feeds the logger and the rails controller runtime instrumentation.
* Validation of foreign keys in `belongs_to` associations are always validated: [#133](https://github.com/nviennot/nobrainer/issues/133).
* Bug fix: Associations properly support custom primary keys
  [#132](https://github.com/nviennot/nobrainer/issues/132).

### 0.22.0 -- Mar. 7, 2015

* `find_by()` introduced [#127](https://github.com/nviennot/nobrainer/issues/127).
* `find()` no longer applies `unscoped`.
* `store_in` accepts symbols.
* Bug fix: NoBrainer considered all validated attributes to be DB fields.

### 0.21.0 -- Jan. 19, 2015

* Regexp matches now support `/i` and `/m` properly:
  [#120](https://github.com/nviennot/nobrainer/issues/120).
* `required => true` validation shorthand uses the presence validator except for
  `Boolean` which uses the non null validator:
  [#109](https://github.com/nviennot/nobrainer/issues/109),
  [#110](https://github.com/nviennot/nobrainer/issues/110),
  [#118](https://github.com/nviennot/nobrainer/issues/118).
* Bug fix: the uniqueness validator would raise an exception when dealing with
  invalid types: [#117](https://github.com/nviennot/nobrainer/issues/117).
* `auto_create_databases` and `auto_create_tables` are no longer configurable.
  Automatic database/table creation is always on.
* Added the validation shorthands `min_length` and `max_length`.
* Added a new type `Text` meant to be used for long strings. Regular `String`
  types are now limited to `255` characters. This limit is configurable with
  `config.max_string_length`.
* `ActiveModel::ForbiddenAttributesError` is raised if Rails controller params
  are passed directly to NoBrainer without using strong parameters.
* Implementation of a distributed lock `NoBrainer::Lock`, which is used by the
  uniqueness validator.
* Drop criteria cache when it has too many elements to avoid out of memory
  issues (configurable with `config.criteria_cache_max_entries`, defaults to `10,000`).
* Raise errors when using attributes from controller params without sanitizing
  them with strong params. The check is performed during attribute assignement
  and in `where()` queries.
* `preload()` is now `eager_load()`.
* default value procs are now executed in the context of the document. This is
  useful to set values depending on other values.
* Added a `:scope` option for `has_many` and `has_many_through` associations.
  The provided scope is run in the context of the target model, which is useful
  to reuse named scoped: [#115](https://github.com/nviennot/nobrainer/issues/115).
* `has_many_through` associations have a `target_model` method to access the underlying model:
  [#114](https://github.com/nviennot/nobrainer/issues/114).
* Allow `default_scope` to be defined multiple times on models and their subclasses.
  They all get to be applied: [#113](https://github.com/nviennot/nobrainer/issues/113).
* Validations are only run against changed fields.
* Atomic operations use sane default values.
* Allow atomic values to be read, it's easier for debugging and callbacks handlers.
* Detect potential miseuses with `:or` arguments (`[{...},{...}]` vs `[...,...]`).
  Using `:_or` does not check these misuses.
* Added a `:length` validation shorthand on field definitions.
* Added the not null validator. `validates_not_null :field` or `validates :field, :not_null`
  can be used to validates non null values.
* Bug fix: Allow `delete_all/update_all/replace_all` to operate on multi indexes.
* Introduced `criteria.without_distinct` keyword to ommit `distinct` on multi indexes.
* Bug fix: `where(:field.defined => value)` properly cast `value` to a boolean.

### 0.20.0 -- Dec. 3, 2014

* Renamed `find!` to `find`. Sticking with ORMs established API to be safer overall.
* Geo support. Introduced the `Geo::Point` type and others. Querying with
  `where(:field.near => {})` and `where(:field.intersects => geo_object)`.
* Generated primary keys use a `[A-Za-z0-9]{14}` format.

### 0.19.0 -- Nov. 17, 2014

* `:as` is now `:store_as`.
* To access a `multi` index, `any` must be used. e.g. `Post.where(:tags.any => 'tag')`.
* Introducing `all` and `any` modifiers in `where()` queries.
* Bug fix: Table creation should use the aliased primary key: [#80](https://github.com/nviennot/nobrainer/issues/80).
* `where()` only allow queries on known fields, unless the model uses dynamic attributes.
* `created_at` and `updated_at` can be updated.
* Extracted the symbol decoration logic into a separate [gem](https://github.com/nviennot/symbol_decoration).
* Alias `create!` to `create` to provide backward compatibility with gems.
* Bug fix: indexed `:or` queries should return distinct elements.
* Atomic operations seems now ready for prime time.

### 0.18.1 -- Nov. 8, 2014

* Added `config.app_name` and `config.environment` for non Rails apps.
* `config.max_reconnection_tries` is now `max_retries_on_connection_failure`.
* Added `criteria.extend()` to add chainable extend behavior.
* Bug fix: more atomic operations can now be performed after `save()`.
* Added an `:external` option to indexes.
* Renamed index synchronization rake task to `nobrainer:sync_indexes`.
* Indexes are kept synchronized through an auxilary table.
* Index deletions are no longer confirmed.
* Added a `:uniq` validation shorthand (alias of `:unique`).
* Added a `:format` validation shorthand on field definitions.
* `update` is now an alias of `update_attributes`.
* Renamed rake namespace from `db:` to `nobrainer:`.
* `config.reconnection_tries` defaults to 1 in development mode, or 15 otherwise.
* `criteria` is available in the middleware stack, useful for monitoring tools.
* `order_by` accepts Arrays as arguments as well.
* `min`/`avg`/`max` return `nil` by default.

### 0.17.0 -- Sep. 25, 2014

* Implementation of atomic operations.
* `save()` is now `save?()` and `save!()` is now `save()`. Same for `update_attributes()`.
* Added a `per_thread_connection` configuration flag (until we have a real connection pool from the driver).

### 0.16.0 -- Aug. 27, 2014

* Added support for lazy fetching on given fields.
* Added support for Binary types.
* The logger shows non indexed queries.
* Added support for `pluck()` and `without()`: [#86](https://github.com/nviennot/nobrainer/issues/86).
* `:or` queries are optimized to leverage indexes: [#85](https://github.com/nviennot/nobrainer/issues/86).
* Added aliases support with `:as`: [#84](https://github.com/nviennot/nobrainer/issues/84).
* `with_index()` influences `order_by()`: [#82](https://github.com/nviennot/nobrainer/issues/82).
* Added a connection lock for thread safety: [#80](https://github.com/nviennot/nobrainer/issues/80).
* `with_index()` can take no argument to force the use of an index.
* Bug fix with the Rails reloader: [#78](https://github.com/nviennot/nobrainer/issues/78).
* Added aggregate functions on criteria (min/max/avg/sum).

### 0.15.0 -- July 27, 2014

* Allow custom types serialization and cast methods.
* Added local/utc timezone translation for `Time` typed attributes,
  configurable with `user_timezone` and `db_timezone`.
* Added ISO8601 `Time` casting.
* Added `Date` casting. They are stored as UTC times in the DB.
* Added a `defined` symbol modifier to test for undefined fields.
* Added a `sample` method to pick a random document.
* Added a `:in` field definition shorthand for inclusion validation.
* Support types and indexes when using Rails scaffolding by Josh Kuhn for [http://rethinkdb.com/docs/rails/](http://rethinkdb.com/docs/rails/)

### 0.14.0 -- June 17, 2014

* Added custom primary keys support.
* Optimized certain queries.
* Added some parallel testing (with `rake`)
* Updated to the new RethinkDB driver and its new JSON transport mechanism.

### 0.13.1 -- March 31, 2014

* `update_indexes` waits for completion by default.
* Added the `nin` operator for `where()` queries.
* Renamed `AssociationNotSaved` to `AssociationNotPersisted`.
* Added a `:unique => true` options on fields as a shorthand for the unique validation.
* Added support for queries on belongs_to associations. e.g. `Comment.where(:post => Post.first)`.
* Added support for distributed locks when performing uniqueness validations.
* Removed the `document.attributes=` accessor.
* Bug fix: document dirtiness is now properly cleaned after a database read.
* `Symbol` typed fields are now read back from the database as symbols and not strings.
* Bug fix: `before_validation` callbacks no longer stop the validation chain when returning `false`.
* Added support for Rails 4.1.

### 0.13.0 -- Jan. 12, 2014

* Removed `update()` and `replace()` for the model instance.
* Removed `inc_all()` and `dec_all()` for criteria.
* Reinstantiating a instance model from the database no longer goes through the
  setters to keep things consistent with the rest of the API.
* Dirty tracking tracks changes from an undefined field, to a field set to `nil`.
* Hashes are updated with `r.literal()` to avoid the use of `replace`.
* Added a `:required => true` options on fields as a shorthand for the presence validation.
* Improved the reconnection mechanism.
* Added support for readonly fields.
* Removed Rails3 compatibility.

### 0.12.0 -- Jan. 8,  2014

* Timestamps are no longer enabled by default.
* `where()` validates and casts all the values with respect to their declared
  field types. This avoid potential query injections.
* Saving a model will only update the attributes that have changed. No database
  update will be performed in case nothing has changed.
* `after_find()` callbacks are available on models, and also on criteria.
* Removed `.to_xml`. This feature is still available by including a module.
* License changed from MIT to LGPLv3.

### 0.11.0 -- Jan. 7, 2014

* Using indexes for `gt`, `gte`, `lt`, `lte` operators when possible.
* Renamed `criteria.includes()` to `criteria.preload()`.
* Added a `:validates` option on fields.
* Fixed dirty tracking with mutable values such as hashes and array.
* Added `initialize` callbacks.
* Boolean types adds a `field_name?` method for convenience.

### 0.10.0 -- Jan. 6, 2014

* Fixed a `NameError` bug when trying to include the `DynamicAttributes` module.
Issue: [#54](https://github.com/nviennot/nobrainer/issues/54).
* Make the associations hackable.
* Implementation of the type checking/casting mechanism.

### 0.9.1 -- Jan. 5, 2014

* Added Rails generators for models
* Removed unecessary ActiveSupport requires.

### 0.9.0 -- Jan. 5, 2014

* Removed the `auto_include_timestamps` and `include_root_in_json` settings.
  Because The order in which the models are declared and NoBrainer configured affected
  the result. Related issue [#52](https://github.com/nviennot/nobrainer/issues/52)
* Removed the `cache_documents` setting because it should not be broken.
* Bug fix with `order_by()` which would try to use an index after a RQL
  `filter()` or `get_all()`.
* `includes()` no longer kill the criteria cache.
* Loading a `has_many` association will set the corresponding
  `belongs_to` association, with or without eager loading.
* Added the `has_many through` association. The implementation is done through
  eager loading.
* Added the `has_one` association.
* Renamed `with_options()` -> `with()`.

### 0.8.0 -- Dec. 31, 2013

* First documented release of NoBrainer.
