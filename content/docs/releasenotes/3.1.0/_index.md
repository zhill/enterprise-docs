---
title: "Anchore Enterprise Release Notes - Version 3.1.0"
linkTitle: "3.1.0"
weight: 50
---

## Anchore Enterprise 3.1.0

This release adds new capabilities for automated runtime inventory scanning, runtime container compliance checks, a new
vulnerability scanner option in tech preview, a new enterprise CLI, as well as other improvements and fixes.

## New Features

### Runtime Kubernetes Inventory Scanning with UI Support

Building on the runtime inventory features in the 3.0 release, Anchore can now automatically analyze images reported as in
use in kubernetes clusters so that you can easily assess the security risks not only of image in your CI pipelines, but also
in production in your clusters. Additionally, the UI now supports visualizations of kubernetes inventories and the vulnerability
and policy compliance status of the inventory by namespace or cluster.

See [Runtime Kubernetes Inventory]({{<ref "/docs/overview/concepts/images/inventory" >}}) for more information.

### Runtime Compliance Checks for Containers

Anchore now includes the ability to execute and collect runtime compliance checks using industry standard tooling such as OpenSCAP to provide evaluation of running
containers' STIG compliance or any other compliance specification that can be described and checked using XCCDF profiles.

See [Compliance Checks]({{<ref "/docs/overview/runtime_compliance" >}}) for more information on this feature.

### New CLI with Integrated Pipeline Scanning Support

The new `anchorectl` tool provides a new Enterprise-focused CLI experience with support for local analysis of images to import
into your deployment. Using the new tool you can also perform other Enterprise operations such as interacting with new compliance reports
and viewing or configuring inventory scanning.

## Tech Preview Features

A new vulnerability scanner based on [Grype](https://github.com/anchore/grype) is now available in tech preview. See [Vulnerability Scanner V2]{{<ref "/docs/releasenotes/3.1.0/v2_vulnerability_scanning" >}}) for more information.
This update is not configured by default and must be set by opt-in using a configuration value.

## Enterprise Service Changes

This release contains a database schema update to version 0.0.8 for the enterprise schema and 0.0.15 for the engine schema.
The upgrade process will modify the db schema and update some tables in the reporting service for any existing runtime
inventory records. Unless you have a very large number of inventory records, the upgrade should complete in seconds to minutes depending
on your database size.

### Owned Package Filtering Control
A new configuration option: services.analyzer.enable_owned_package_filtering: <bool> is now available in the analyzer service configuration.
By default, the analyzer will filter packages that are determined at analysis time to be "owned" by a parent package when that package
installs all the files of the child package. That behavior can be disabled by setting this configuration value to "false".

The default filtering removes false positives associated with packages installed by distro packages that install language
packages like python, npms, or gems and have backports applied by the distro maintainer with no corresponding
language package version change. However, if you package your own applications as rpms, debs, or similar and need to
ensure all included packages are scanned directly against NVD sources, then you can disable this behavior.

## Added
- New tech-preview vulnerability scanner
- Improved alpine vulnerability scanning by using NVD matches for OS packages for CVEs that are not yet present in Alpine SecDB
- Analyzer service configuration option to control package-ownership filtering. Allows exposing all packages regardless of ownership relationship

### Fixed
- Adds missing fields and fixes errors in the swagger spec for the API
- Restores file package verification data ingress during image load to fix a regression
- Malware policy gate can fail causing policy eval error when malware not enabled and other rules precede malware rule in a policy
- JSON serialization error in internal policy engine user image listing API
- "package_cpe23" field missing in vulnerabilities
- Ensure python38 used in the Dockerfile build, and set tox tests to only run py38
- User to not be able to delete some notification configurations when they should be able based on RBAC role

### Improved
- Performance of GET operations between services improved by better streaming memory management for large payload transfers
- Use UBI 8.4 as base image in Docker build
- Updates skopeo version used to 1.2.1, allowing removal of the 'lookuptag' field in the POST /repositories call for
  watching repositories that do not have a 'latest' tag
- RedHat packages for an Out-of-Support distro release version now indicated as being vulnerable if a newer distro release version is supported and indicated as affected for the package.

Additional minor bug fixes and enhancements

### Known Issues/Errata
Note: the policy engine feed sync configuration is now in the policy engine service configuration as part of the provider
configuration. The provided helm charts, docker-compose.yaml and default configurations handle this change automatically.

### Deprecations
The `affected_package_version` query parameter in GET /query/vulnerabilities is not supported in the V2 scanner (aka Grype mode)
and has known correctness issues in the legacy mode. It is deprecated and will be removed in a future release.

## Enterprise UI Changes

### Added

- From the new Kubernetes Runtime Inventory view you can now inspect
  the spread of compliance and vulnerability information reported by
  the [KAI](https://github.com/anchore/kai/) agent across all detected
  Kubernetes clusters and namespaces in your deployment topology
- Information relating to any items detected by the runtime agent is
  now surfaced in the repository- and tag-level views within the Image
  Selection hierarchy

### Improved
- If the reporting service fails, feature components that require this
  service as a dependency will be disabled in the navigation bar until
  service recovery
- Pie-chart components have been restructured to present selected
  information inclusively when segments are clicked—other segments
  are now disabled

### Fixes
- Printable view assembly issues addressed in Image Analysis Vulnerability
  and Compliance views—charts now render correctly in portrait mode
- The alerts banner is now subject to RBAC and will not appear if the
  fetch alert permission is not detected
- Clipping issues resolved in the creation date popup in the Policy Bundle view
- Supporting libraries have been updated in order to improve security,
  performance, and also to remove deprecation warnings from browser
  and server output logs

Additional minor bug fixes and enhancements

### Upgrading
* [Upgrading Anchore]({{< ref "/docs/installation/upgrade" >}})