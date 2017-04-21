---
RFC: unassigned
Title: Test Kitchen Mixlib Install Unification
Author: Patrick Wright <patrick@chef.io>
Status: Draft
Type: Standards Track
---

# Test Kitchen Mixlib Install Unification

Test Kitchen currently supports two code paths for configuring, downloading and installing chef and chefdk on test instances. Both code paths use Mixlib Install but are implemented differently. In the past year, significant effort has been put into Mixlib Install to make it the canonical source for which all Chef related products are queried for meta-data and for generating the bootstrap installation scripts (install.sh and install.ps1). 

Mixlib Install's new API is currently used for downloads.chef.io and omnitruck.chef.io. It's also used for the chef-ingredient cookbook. Chef transparently implemented the new API and selected config options in Kitchen for testing purposes. It has proven valuable and easy to use. The feature set is limited at this time.

## Motivation

```
As a Kitchen User,
I want to use simple, clear configuration options,
so that I can setup Kitchen config files with less complexity and obfuscation.
```

```
As a Kitchen Developer,
I want to maintain a single Mixlib Install code path,
so that I can benefit from the latest API changes and reduce maintenance risk.
```

The current Mixlib Install path used in Kitchen was developed specifically for Kitchen and is tightly coupled with Kitchen's provisioner options. It's referred to as the `ScriptGenerator`. The new API was developed to be used by any project that needs to query Chef product meta-data or generate an installation script. The new API takes into account the specifics of interacting with Chef's distribution infrastructure (Package Router).

Two feature requests have been made which lend themselves to the new API. The first is
caching
user agent

## Specification

This work is separated into 3 phases.

##### Phase 1 - Incrementally add new transparent configuration options

* All existing configuration options behave as expected
* Build feature parity with the new options to match existing functionality
* Setting `product_name` will trigger the new options use cases
* Maintain both Mixlib Install code paths
* Document new options in the Kitchen repo

##### Phase 2 - Provide option deprecation warnings and "how-tos"

* All existing configuration options still behave as expected
* User is clearly warned when using options that will be deprecated
* Deprecation warnings include explanations and resolution instructions

##### Phase 3 - Make the switch

* Raise errors along with instructions when using deprecated options 
* `ScriptGenerator` code path removed from Kitchen
* docs.chef.io and kitchen.ci documentation updates

### Phase 1 Details

Here is an example of how the new options `product_version` and `install_strategy` replace `require_chef_omnibus`: https://gist.github.com/wrightp/60f8723b48d80de9b1ed0fc028757e32

The gist is an example for meeting requirements to maintain feature parity with Kitchen's current configuration options. Since `require_chef_omnibus` is a complex option it warranted this level of detail. Not all options will require this.

For example, provisioner options `chef_omnibus_install_options`, `chef_omnibus_url`, `chef_metadata_url`, `chef_omnibus_root`, and `ruby_bindir` have been shown how they can be deprecated in this __experimental__ branch: https://github.com/test-kitchen/test-kitchen/compare/master...wrightp:pw/config-deprecations. Most of them are simple.

To further separate new from old functionality, none of the options should cross over to one another. We will discuss any exceptions as they arise. `product_name` will act as the pivot point between which code path to follow (`ScriptGenerator` or the new API).

As new options are added a markdown file in the Kitchen repo will be maintained with descriptions along with deprecation information.

### Phase 2 Details

The planned deprecated config options will be turned "on". Users will start getting deprecation warnings and begin resolving issues. At this phase we expect to be feature complete, however, there will likely be use cases or configuration combinations that are missed. Any detected deprecation warnings will also instruct the user to create issues if the resolutions don't work or don't need their requirements.

### Phase 3 Details

## Documentation
Sources that will require updates:
* docs.chef.io
* kitchen.ci
* github.com/test-kitchen

## Copyright

This work is in the public domain. In jurisdictions that do not allow for this,
this work is available under CC0. To the extent possible under law, the person
who associated CC0 with this work has waived all copyright and related or
neighboring rights to this work.
