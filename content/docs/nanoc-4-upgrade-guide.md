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

* Add `identifier_type: legacy` to the individual data source configurations.

* Add `string_pattern_type: legacy` to the configuration file.

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

This section describes how to upgrade a site to identifiers with extensions and glob patterns. For details, see the [identifiers and patterns](/docs/reference/identifiers-and-patterns/) page.

This section assumes you have already upgraded the site using the [quick upgrade guide](#quick-upgrade-guide) above.

Before you start, add `enable_output_diff: true` to the configuration file. This will let the <span class="command">compile</span> command write out a diff with the changes to the compiled output. This diff will allow you to verify that no unexpected changes occur.

NOTE: If you use a filter that minifies HTML content, such as `html5small`, we recommend turning it off before upgrading the site, so that the output diff becomes easier to read.

### Enabling glob patterns

Before enabling them, ensure you are familiar with glob patterns. See the [glob patterns](/docs/reference/identifiers-and-patterns/#glob-patterns) section for documentation.

To use glob patterns, remove `string_pattern_type: legacy` from the configuration file. Replace `*` and `+` with `**/*` in the arguments to `#compile`, `#route` and `#layout` in the <span class="filename">Rules</span> file, and also in calls to `@items[…]` and `@layouts[…]` throughout the site.

This approach should work out of the box: nanoc should not raise errors and the output diff should be empty.

Here is an example of a rule that uses the legacy and glob patterns:

    #!ruby
    # With legacy patterns
    compile '/assets/style/*/' do
      # (code here)
    end

    # With glob patterns
    compile '/assets/style/**/*/' do
      # (code here)
    end

Here is an example of legacy and glob patterns in calls to `@items[…]`:

    #!ruby
    # With legacy patterns
    @items['/articles/*/']

    # With glob patterns
    @items['/articles/**/*/']

### Enabling full-style identifiers

This section assumes that glob patterns have already been enabled.

TODO: Write me.

### Other

TODO: Describe how to switch away from the static data source.

## Troubeshooting

* If you use nanoc with a Gemfile, ensure you call nanoc as <kbd>bundle exec nanoc</kbd>. nanoc no longer attempts to load the Gemfile.

* If you get a `NoMethodError` error on `Nanoc::Identifier`, call `.to_s` on the identifier before doing anything with it. In nanoc 4.x, identifiers have their own class and are no longer strings.

      #!ruby
      # Old approach -- NO LONGER WORKS!
      item.identifier[7..-2]

      # New approach
      item.identifier.to_s[7..-2]

* If you get a `NoMethodError` that you did not expect, you might be using a private API that is no longer present in nanoc 4.0. In case of doubt, ask for help on the [discussion group](http://nanoc.ws/community/#discussion-groups).

## Removed features

The `watch` and `autocompile` commands have been removed. Both were deprecated in nanoc 3.6. Use [guard-nanoc](https://github.com/guard/guard-nanoc) instead.

Because nanoc’s focus is now more clearly on compiling content rather than managing it, the following features have been removed:

- the `create-item` and `create-layout` commands
- the `update` and `sync` commands
- VCS integration (along with `Nanoc::Extra::VCS`)
- the `DataSource#create_item` and `DataSource#create_layout` methods.
