---
title: "Trouble Shooting"
linkTitle: "Trouble Shooting"
weight: 7
---

This guide will walkthrough some general troubleshooting tips with your Anchore Engine instance. When troubleshooting Anchore Engine, the recommend approach is to first verify all Anchore services are up, use the event subsystem to narrow down particular issues, and then navigate to the logs for specific services to find out more information.

If you are running into issues performing certain Anchore operations (examples: images are failing analysis or cannot access a private registry) please consult the [FAQs]({{< ref "/docs/troubleshooting/faqs" >}}) document.

Throughout this guide, Anchore CLI commands will be executed to assist with troubleshooting. For more information on the Anchore CLI, please reference the CLI section.

* [Verifying Services]({{< ref "#verifying-services" >}})
* [Events]({{< ref "#events" >}})
* [Logs]({{< ref "#logs" >}})
* [Removing a Repository and Images]({{< ref "#removing-repo-images" >}})

## Verifying Services

Verify that the following Anchore Engine services are up

- Service analyzer
- Service policy_engine
- Service catalog
- Service apiext
- Service simplequeue

You can do so by running: `anchore-cli system status`

```
anchore-cli system status
Service analyzer (dockerhostid-anchore-engine, http://anchore-engine:8084): up
Service policy_engine (dockerhostid-anchore-engine, http://anchore-engine:8087): up
Service catalog (dockerhostid-anchore-engine, http://anchore-engine:8082): up
Service apiext (dockerhostid-anchore-engine, http://anchore-engine:8228): up
Service simplequeue (dockerhostid-anchore-engine, http://anchore-engine:8083): up

Engine DB Version: 0.0.9
Engine Code Version: 0.3.4
```

**Note:** If specific services are down, you can investigate the logs for the services. See [Logs Section]({{< ref "#logs" >}})

### The --debug and --json options

Passing the `--debug` option to any Anchore CLI can often help narrow down particular issues.

```
# Example system status with --debug
root@4c0a95557659:/anchore-engine# anchore-cli --debug system status
INFO:anchorecli.clients.apiexternal:As Account = None
DEBUG:urllib3.connectionpool:Starting new HTTP connection (1): localhost:8228
DEBUG:urllib3.connectionpool:http://localhost:8228 "GET / HTTP/1.1" 200 5
INFO:anchorecli.clients.apiexternal:As Account = None
DEBUG:anchorecli.clients.apiexternal:GET url=http://localhost:8228/system
DEBUG:anchorecli.clients.apiexternal:GET insecure=True
DEBUG:urllib3.connectionpool:Starting new HTTP connection (1): localhost:8228
DEBUG:urllib3.connectionpool:http://localhost:8228 "GET /system HTTP/1.1" 200 2672
DEBUG:anchorecli.cli.utils:fetched httpcode from response: 200
Service analyzer (dockerhostid-anchore-engine, http://anchore-engine:8084): up
Service policy_engine (dockerhostid-anchore-engine, http://anchore-engine:8087): up
Service catalog (dockerhostid-anchore-engine, http://anchore-engine:8082): up
Service apiext (dockerhostid-anchore-engine, http://anchore-engine:8228): up
Service simplequeue (dockerhostid-anchore-engine, http://anchore-engine:8083): up
```

Passing the `--json` option to any Anchore CLI commands will output the data in JSON format.

```
# Example system status with --json
root@4c0a95557659:/anchore-engine# anchore-cli --json system status
{
    "service_states": [
        {
            "base_url": "http://anchore-engine:8087",
            "hostid": "dockerhostid-anchore-engine",
            "service_detail": {
                "available": true,
                "busy": false,
                "db_version": "0.0.9",
                "detail": {},
                "message": "all good",
                "up": true,
                "version": "0.3.4"
            },
            "servicename": "policy_engine",
            "status": true,
            "status_message": "available",
            "version": "v1"
        },
        ...
    ]
}
```

## Events

If you've successfully verified that all of the Anchore Engine services are up, but are still running into issues operating Anchore a good place check is the event log.

The event log subsystem provides users with a mechanism to inspect asynchronous event occurring across various Anchore Engine services. Anchore events include periodically triggered activities such as vulnerability data feed sync in the policy_engine service, image analysis failures originating from the analyzer service, and other informational or system fault events. The catalog service may also generate events for any repositories or image tags that are being watched, when Anchore Engine encounters connectivity, authentication, authorization or other errors in the process of checking for updates.

The event log is aimed at troubleshooting most common failure scenarios (especially those that happen during asynchronous engine operations) and to pinpoint the reasons for failures, that can be used subsequently to help with corrective actions. Events can be cleared from Anchore Engine in bulk or individually.

### Viewing Events

Running the following command will give a list of recent Anchore events: `anchore-cli event list`

```
# Viewing list of recent Anchore events

root@4c0a95557659:/anchore-engine# anchore-cli event list
Timestamp                          Level        Service              Host                                                           Event                                  ID                                      
2019-04-28T13:04:16.203425Z        INFO         policy_engine        dockerhostid-anchore-engine                                    feed_sync_complete                     16590324ffe443dfa8b8352a0e63bd14        
2019-04-28T13:04:04.202101Z        INFO         policy_engine        dockerhostid-anchore-engine                                    feed_sync_start                        7d0c3d0015e74302a5242f74b03092ec        
2019-04-28T07:04:03.946414Z        INFO         policy_engine        dockerhostid-anchore-engine                                    feed_sync_complete                     eb8a7f70c0eb4f92a8941ead61d3a5ef        
2019-04-28T07:03:53.112932Z        INFO         policy_engine        dockerhostid-anchore-engine                                    feed_sync_start                        1277941
```

### Details about a specific event

If you would like more information about a specific event, you can run the following command: `anchore-cli event get <event-id>`

```
# Details about a specific Anchore event

root@4c0a95557659:/anchore-engine# anchore-cli event get 7f6dc74c8c8348ecad97f2f54ad488d6
details:
  sync_feed_types:
  - vulnerabilities
level: INFO
message: Feed sync started
resource:
  type: feeds
  user_id: admin
source:
  base_url: http://localhost:8087
  hostid: anchore-quickstart
  servicename: policy_engine
timestamp: '2019-02-06T04:15:11.372306Z'
type: feed_sync_start
```

**Note:** Depending on the output from the detailed events, looking into the logs for a particular servicename (example: policy_engine) is the next troubleshooting step.  

## Logs

Anchore logs can be accessed by executing into the Anchore container and navigating to `/var/log/anchore`. From this location you can access logs for specific services (if co-located each of the service logs will be available for access under this directory).

```
# Example logs
# Co-located Anchore Engine installation

root@4c0a95557659:/var/log/anchore# ls
anchore-api.log                 anchore-simplequeue.log     anchore-catalog.log             anchore-policy-engine.log  anchore-worker.log
```

As stated above, if you are running into issues performing certain Anchore operations (examples: images are failing analysis or cannot access a private registry) please consult the [FAQs]({{< ref "/docs/troubleshooting/faqs" >}}) document.

## Removing a Repository and Images

There may be a time when you wish to stop a repository analysis when the analysis is running (e.g., accidentally watching an image with a large number of tags).  There are several steps in the process which are outlined below.  We will use `docker.io/library/alpine` as an example.

**Note:** Be careful when deleting images. In this flow, Anchore deletes the image, not just the repository/tag combo.  Because of this, deletes may impact more than the expected repository since an image may have tags in multiple repositories or even registries.

### Check the State

Take a look at the repository list.

```
# anchore-cli repo list
Repository                      Watched        TagCount       
docker.io/library/alpine        True          29
```

Also look at the image list.

```
# anchore-cli image list | grep 'docker.io/library/alpine'
docker.io/library/alpine:20190228              sha256:1dd6a46eca0d7025920a8b3e3db7fdc33ad5c4e2e317c314e125d4141ce14a0f        not_analyzed         
docker.io/library/alpine:20190408              sha256:00c76f80fd9298c831c4c5e799df6d7164b2a2692b10318c00ab217b381ba659        not_analyzed         
docker.io/library/alpine:20190707              sha256:c04b643dedaccae53e036f2bf72b0e792870f51708aff6ceaa6895de60e46257        not_analyzed         
docker.io/library/alpine:3.1                    sha256:25fd8fc1aefcc8ae46aae23daefcd7dcb97f676fa0bc72bb0bf7cfb75df4f22e        not_analyzed         
docker.io/library/alpine:3.10.1                sha256:57334c50959f26ce1ee025d08f136c2292c128f84e7b229d1b0da5dac89e9866        not_analyzed         
docker.io/library/alpine:3.5                    sha256:f7d2b5725685826823bc6b154c0de02832e5e6daf7dc25a00ab00f1158fabfc8        not_analyzed         
docker.io/library/alpine:3.6.5                  sha256:36c3a913e62f77a82582eb7ce30d255f805c3d1e11d58e1f805e14d33c2bc5a5        not_analyzed         
...
...
```

### Removing the Repository from the Watched List

Unwatch `docker.io/library/alpine` to prevent future automatic updates.

```
# anchore-cli repo unwatch docker.io/library/alpine
Repository                      Watched        TagCount       
docker.io/library/alpine        False          29
```

### Delete the Repository

Delete the repository.  This may need to be done a couple times if the repository still shows in the repository list.

```
# anchore-cli repo del docker.io/library/alpine
Success
```

### Forcefully Delete the Images

Delete the analysis/images.  This may need to be done several times to remove all images depending on how many there are.

```
# for i in `anchore-cli image list | grep 'docker.io/library/alpine' | awk '{print $1}' | sort | uniq`
> do
> anchore-cli image del ${i} --force
> done
Success
Success
Success
...
...
```

### Verify the Repository and All Images are Deleted

Check the repository list.

```
# anchore-cli repo list
<no output>
```

Check the image list.

```
# anchore-cli image list | grep 'docker.io/library/alpine'
<no output>
```
