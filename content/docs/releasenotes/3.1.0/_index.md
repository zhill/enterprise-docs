---
title: "Anchore Enterprise Release Notes - Version 3.1.0"
linkTitle: "3.1.0"
weight: 65
---

## Anchore Enterprise 3.1.0

This release adds new capabilities for runtime inventory automated scanning, checking running container compliance, a new 
vulnerability scanner option in tech preview, a new enterprise CLI, and other improvements and fixes. 

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

The new anchorectl command line tool provides a new Enterprise-focused CLI experience with support for local analysis of images to import
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
By default, the analyzer will filter packages that are determined by at analysis time to be "owned" by a parent package when that package 
is an rpm, deb, apk, or similar distro package that installs all the files of the child package. That behavior can be disabled by setting this configuration 
value to "false". The filtering removes false positives associated with packages installed by yum, apt, or apkg, that also 
install language packages like python, npms, or gems that have backports applied by the distro maintainer but no corresponding 
language package version change other than the parent package version. However, if you package your own applications as 
rpms, debs, or similar and need to ensure all included packages are scanned directly against NVD sources, then you can 
disable this behavior.

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
  watching repositories that do not have a latest tag
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

### Improved

### Fixes

Additional minor bug fixes and enhancements


### Upgrading

* [Upgrading Anchore]({{< ref "/docs/installation/upgrade" >}})