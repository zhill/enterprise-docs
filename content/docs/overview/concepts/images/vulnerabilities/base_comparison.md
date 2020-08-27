---
title: "Base Comparison"
linkTitle: "Base Comparison"
weight: 5
---

{{% alert title="Info" color="info" %}}
This feature is available only in Anchore Enterprise >= v2.4.0
{{% /alert %}}

Base comparison provides a mechanism to compare the security vulnerabilities in an image with those of a base image. An image is considered an ancestor or base if it's layers are a subset of another image. You can read more about the different types of ancestor images and how to find them [FIX-THIS]({{< ref "/docs/using/ui_usage/notifications" >}}). The API yields a response similar to vulnerabilities API with an additional element within each result to indicate whether the result is inherited from the base image.

### Usage

This functionality is currently available via the Enterprise UI and API. Watch this space as we add base comparison support in other tools

#### Enterprise UI
       
To learn more about exercising base comparison via the Enterprise UI, go to [FIX-THIS]({{< ref "/docs/using/ui_usage/notifications" >}})

#### API          

The JSON definition for the Enterprise API specification for your specific instance can be downloaded from a running Anchore Enterprise service at the following URI:

`http://{servername:port}/v1/enterprise/swagger.json`

API route for base comparison is `GET /enterprise/images/{imageDigest}/vuln/{vtype}`. This API exposes similar path and query parameters as the security vulnerabilities API `GET /images/{imageDigest}/vuln/{vtype}` plus an optional query parameter for supplying the digest of the base image. If the base digest is omitted, the system falls back to retrieving security vulnerabilities in the image without comparing the results to the base image.   

Example request using curl to retrieve security vulnerabilities for an image digest sha:xyz and compare the results to a base image digest sha256:abc

```
curl -X GET -u {username:password} "http://{servername:port}/v1/enterprise/images/sha256:xyz/vuln/all?base_digest=sha256:abc"
```

Example output:   
 
```json
{
  "base_digest": "sha256:abc",
  "image_digest": "sha256:xyz",
  "vulnerability_type": "all",
  "vulnerabilities": [
    {
      "feed": "vulnerabilities",
      "feed_group": "alpine:3.12",
      "fix": "7.62.0-r0",
      "inherited_from_base": true,
      "nvd_data": [
        {
          "cvss_v2": {
            "base_score": 6.4,
            "exploitability_score": 10.0,
            "impact_score": 4.9
          },
          "cvss_v3": {
            "base_score": 9.1,
            "exploitability_score": 3.9,
            "impact_score": 5.2
          },
          "id": "CVE-2018-16842"
        }
      ],
      "package": "libcurl-7.61.1-r3",
      "package_cpe": "None",
      "package_cpe23": "None",
      "package_name": "libcurl",
      "package_path": "pkgdb",
      "package_type": "APKG",
      "package_version": "7.61.1-r3",
      "severity": "Medium",
      "url": "http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-16842",
      "vendor_data": [],
      "vuln": "CVE-2018-16842"
    },
    {
      "feed": "vulnerabilities",
      "feed_group": "alpine:3.12",
      "fix": "2.4.46-r0",
      "inherited_from_base": false,
      "nvd_data": [
        {
          "cvss_v2": {
            "base_score": 5.0,
            "exploitability_score": 10.0,
            "impact_score": 2.9
          },
          "cvss_v3": {
            "base_score": 7.5,
            "exploitability_score": 3.9,
            "impact_score": 3.6
          },
          "id": "CVE-2020-9490"
        }
      ],
      "package": "apache2-2.4.43-r0",
      "package_cpe": "None",
      "package_cpe23": "None",
      "package_name": "apache2",
      "package_path": "pkgdb",
      "package_type": "APKG",
      "package_version": "2.4.43-r0",
      "severity": "Medium",
      "url": "http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-9490",
      "vendor_data": [],
      "vuln": "CVE-2020-9490"
    }
  ]
}
```

Note that `inherited_from_base` is a new element in the API response added to support base comparison. The assigned boolean value indicates whether the exact vulnerability is present in the base image. In the above example

- CVE-2018-16842 affects libcurl-7.61.1-r3 package in both images, hence `inherited_from_base` is marked `true`
- CVE-2019-5482 affects apache2-2.4.43-r0 package does not affect the base image and therefore `inherited_from_base` is set to `false`



