---
title: "Base Comparison"
linkTitle: "Base Comparison"
weight: 7
---

{{% alert title="Info" color="info" %}}
This feature is available only in Anchore Enterprise >= v2.4.0
{{% /alert %}}

Base comparison provides a mechanism to compare the policy checks for an image with those of a base image. An image is considered an ancestor or base if it's layers are a subset of another image. You can read more about the different types of ancestor images and how to find them [FIX-THIS]({{< ref "/docs/using/ui_usage/notifications" >}}). Base comparison uses the same policy bundle and tag to evaluate both images to ensure a fair comparison. The API yields a response similar to the policy checks API with an additional element within each triggered gate check to indicate whether the result is inherited from the base image.

### Usage

This functionality is currently available via the Enterprise UI and API. Watch this space as we add base comparison support in other tools

#### Enterprise UI
       
To learn more about exercising base comparison via the Enterprise UI, go to [FIX-THIS]({{< ref "/docs/using/ui_usage/notifications" >}})

#### API          

The JSON definition for the Enterprise API specification for your specific instance can be downloaded from a running Anchore Enterprise service at the following URI:

`http://{servername:port}/v1/enterprise/swagger.json`

API route for base comparison is `GET /enterprise/images/{imageDigest}/check`. This API exposes similar path and query parameters as image policy check API `GET /images/{imageDigest}/check` plus an optional query parameter for supplying the digest of the base image. If the base digest is omitted, the system falls back to evaluating image policy checks without comparing the results to the base image.   

Example request using curl to retrieve policy check for an image digest sha256:xyz and tag p/q:r and compare the results to a base image digest sha256:abc

```
curl -X GET -u {username:password} "http://{servername:port}/v1/enterprise/images/sha256:xyz/check?tag=p/q:r&base_digest=sha256:abc"
```

Example output:   
 
```json
[
  {
    "sha256:xyz": {
      "p/q:r": [
        {
          "detail": {
            "result": {
              "base_image_digest": "sha256:abc",
              "result": {
                "123": {
                  "result": {
                    "final_action": "stop",
                    "header": [
                      "Image_Id",
                      "Repo_Tag",
                      "Trigger_Id",
                      "Gate",
                      "Trigger",
                      "Check_Output",
                      "Gate_Action",
                      "Whitelisted",
                      "Policy_Id",
                      "Inherited_From_Base"
                    ],
                    "row_count": 2,
                    "rows": [
                      [
                        "123",
                        "p/q:r",
                        "41cb7cdf04850e33a11f80c42bf660b3",
                        "dockerfile",
                        "instruction",
                        "Dockerfile directive 'HEALTHCHECK' not found, matching condition 'not_exists' check",
                        "warn",
                        false,
                        "48e6f7d6-1765-11e8-b5f9-8b6f228548b6",
                        true
                      ],
                      [
                        "123",
                        "p/q:r",
                        "CVE-2019-5435+curl",
                        "vulnerabilities",
                        "package",
                        "MEDIUM Vulnerability found in os package type (APKG) - curl (CVE-2019-5435 - http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5435)",
                        "warn",
                        false,
                        "48e6f7d6-1765-11e8-b5f9-8b6f228548b6",
                        false
                      ]
                    ]
                  }
                },
                ...
              },
              ...
            },
            ...
          },
          ...
        }
      ]
    }
  }
]
```

Note that header element `Inherited_From_Base` is a new column in the API response added to support base comparison. The corresponding row element in each item of `rows` uses a boolean value to indicate whether the gate result is present in the base image. In the above example

- `Dockerfile directive 'HEALTHCHECK' not found, matching condition 'not_exists' check` is triggered by both images and hence `Inherited_From_Base` column is marked `true`
- `MEDIUM Vulnerability found in os package type (APKG) - curl (CVE-2019-5435 - http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5435)` is not triggered by the base image and therefore the value of `Inherited_From_Base` column is `false`



