---
title: "AnchoreCTL"
linkTitle: "AnchoreCTL"
weight: 3
---

### Introduction

In this section you will learn how to install and configure AnchoreCTL, the Anchore Enterprise CLI.
It currently shares some functionality with anchore-cli is designed specifically for use with Anchore Enterprise. 
AnchoreCTL is published as simple binary that can be installed by downloading it or using provided packages for installation in different platforms. 
Using AnchoreCTL, users can manage and inspect images, manage their false-positive management settings, manage their runtime inventory settings, interact with the runtime compliance API, and even generate/upload image SBOMs.

### Getting Started
You can install AnchoreCTL using either archives or provided packages downloaded from the release site.  

#### macOS .dmg
```shell script
curl -o anchorectl.dmg https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_darwin_amd64.dmg
```

#### macOS Tar
```shell script
curl -o anchorectl.dmg https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_darwin_amd64.tar.gz
```


#### Debian

```shell script
curl -o anchorectl.deb https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_linux_amd64.deb
```

#### RPM

```shell script
curl -o anchorectl.rpm https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_linux_amd64.rpm
```

#### Linux Tar

```shell script
curl -o anchorectl.tar.gz https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_linux_amd64.tar.gz
```

#### Windows

```shell script
curl -o anchorectl.zip https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl_0.1.2_windows_amd64.zip
```


### Configuration
AnchoreCTL can be configured via a config file at the following locations, with the following precedence:
1. Environment Vars (i.e. ANCHORECTL_ANCHORE_USER)
2. Config Path override (note: if this file is not found, config SHOULD fail)
3. .anchorectl.yaml or anchorectl.yaml
4. .anchorectl/config.yaml
5. ~/.anchorectl.yaml
6. ~/anchorectl/config.yaml

To get a release-matched version of this configuration file, you can retrieve it as follows:
```shell script
curl -o anchorectl.yaml https://anchorectl-releases.s3-us-west-2.amazonaws.com/v0.1.2/anchorectl.yaml
```
Note that it has detailed comments above each configuration value so you know what each does.

#### Required
At the very least, AnchoreCTL needs the following information
* Anchore Connection details (including authentication information)
* `enterpriseEnabled: true`. This is the default value, but it _must_ be specified in order for the enterprise-related features to work. 
