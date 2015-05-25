---
title: "nanoc 4 upgrade guide"
up_to_date_with_nanoc_4: true
---

This document lists all backwards-incompatible changes made to nanoc 4.0, and contains advice on how to migrate from nanoc 3.x to nanoc 4.0.

The good news is that nanoc 4.0 is quite similar to 3.8. Upgrading a nanoc 3.x site to nanoc 4.0 will likely only take minutes.

## Quick upgrade guide

* Upgrade to Ruby 2.2 or higher, or JRuby 9000.

* Change mentions of `Nanoc3` to `Nanoc`.

* Change mentions of `@site.config` to `@config`.

* Add `identifier_style: legacy` to the individual data source configurations.

* Add `string_pattern_type: legacy` to the root of the configuration.

* In the `preprocess` block, use `@items.create` rather than instantiating `Nanoc::Item`. For example:

      #!ruby
      # Old approach -- NO LONGER WORKS!
      @items << Nanoc::Item.new('Hello', {}, '/hello/')

      # New approach
      @items.create('Hello', {}, '/hello/')

* In data sources, use `#new_item` or `#new_layout` rather than instantiating `Nanoc::Item` or `Nanoc::Layout`. For example:

      #!ruby
      # Old approach -- NO LONGER WORKS!
      def items
        [Nanoc::Item.new('Hello', {}, '/hello/')]
      end

      # New approach
      def items
        [new_item('Hello', {}, '/hello/')]
      end

## Extended upgrade guide

TODO: Describe how to convert a site to use full identifiers and glob patterns.

nanoc 4 has support for identifiers with extensions (full-style identifiers) as well as glob patterns. For details, see the [identifiers and patterns](/docs/reference/identifiers-and-patterns/) page.

TODO: Encourage the use of the output diff to ensure no unexpected changes occur.

## Troubeshooting

* If you use nanoc with a Gemfile, ensure you call nanoc as <kbd>bundle exec nanoc</kbd>. nanoc no longer attempts to load the Gemfile.

* If you get a `NoMethodError` error on `Nanoc::Identifier`, call `.to_s` on the identifier before doing anything with it. In nanoc 4.x, identifiers have their own class and are no longer strings.

      #!ruby
      # Old approach -- NO LONGER WORKS!
      item.identifier[7..-2]

      # New approach
      item.identifier.to_s[7..-2]

* If you get a `NoMethodError` that you did not expect, you might be using a private API that is no longer present in nanoc 4.0. In case of doubt, ask for help on the [discussion group](http://nanoc.ws/community/#discussion-groups).

TODO: Describe how to switch away from the static data source.

## Removed features

The `watch` and `autocompile` commands have been removed. Both were deprecated in nanoc 3.6. Use [guard-nanoc](https://github.com/guard/guard-nanoc) instead.

Because nanoc’s focus is now more clearly on compiling content rather than managing it, the following features have been removed:

- the `create-item` and `create-layout` commands
- the `update` and `sync` commands
- VCS integration (along with `Nanoc::Extra::VCS`)
- the `DataSource#create_item` and `DataSource#create_layout` methods.
