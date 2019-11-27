---
title: "Frequently Asked Questions"
linkTitle: "FAQs"
weight: 2
---

# Anchore Troubleshooting FAQs

For each of the following frequently asked questions, read the [General Troubleshooting Approach Guide]({{< ref "/docs/troubleshooting" >}}) first, then following the data from there, follow the query specific answers below.


* [Unauthorized error when using the Anchore CLI]({{< ref "#unauthorized-error-when-using-the-anchore-cli" >}})
* [No vulnerability results for an analyzed image]({{< ref "#no-vulnerability-results-for-an-analyzed-image" >}})
* [My image will not analyze]({{< ref "#my-image-will-not-analyze" >}})
* [Unable to access private registry]({{< ref "#unable-to-access-private-registry" >}})
* [Anchore Engine container keeps exiting]({{< ref "#anchore-engine-container-keeps-exiting" >}})

**Note:** As stated in the [General Troubleshooting Approach Guide]({{< ref "/docs/troubleshooting" >}}), passing the `--debug` option to any Anchore CLI command can often help narrow down particular issues.

## Unauthorized error when using the Anchore CLI

If you run into an `"Unauthorized"` error, verify you have configured the Anchore CLI correctly, as this error is most commonly seen when the Username, Password, or Service URL are improperly set.

By default the Anchore CLI will try to connect to the Anchore Engine at `http://localhost:8228/v1` with no authentication. The username, password and URL for the server can be passed to the Anchore CLI as command line arguments.

```
--u   TEXT   Username     eg. admin (default)
--p   TEXT   Password     eg. foobar (default)
--url TEXT   Service URL  eg. http://localhost:8228/v1
```

Rather than passing these parameters for every call to the cli they can be stores as environment variables.

```
ANCHORE_CLI_URL=http://myserver.example.com:8228/v1
ANCHORE_CLI_USER=admin
ANCHORE_CLI_PASS=foobar
```

**Note:** When passing the parameters through the command line, order matters. For example, `anchore-cli --url http://localhost:8228/v1 --u admin --p foobar system status`

## No vulnerability results for an analyzed image

In order to return vulnerability results on analyzed images feed data must be synced. Verify that feed data has successfully synced and indicate record counts with the following:

### Verifying feed data

The following command will report a list of feeds synced by Anchore: `anchore-cli system feeds list`

```
anchore-cli system feeds list
Feed                   Group                  LastSync                          RecordCount        
nvdv2                  nvdv2:cves             2019-11-25T22:19:53.961641        133810             
vulnerabilities        alpine:3.10            2019-11-25T22:19:52.819559        1725               
vulnerabilities        alpine:3.3             2019-11-25T22:19:46.581789        457                
vulnerabilities        alpine:3.4             2019-11-25T22:19:50.728927        681                
vulnerabilities        alpine:3.5             2019-11-25T22:19:52.619737        875                
vulnerabilities        alpine:3.6             2019-11-25T22:19:44.783531        1051               
vulnerabilities        alpine:3.7             2019-11-25T22:19:50.055020        1395               
vulnerabilities        alpine:3.8             2019-11-25T22:19:46.352638        1486               
vulnerabilities        alpine:3.9             2019-11-25T22:19:47.608825        1558               
vulnerabilities        amzn:2                 2019-11-25T22:19:47.785204        282                
vulnerabilities        centos:5               2019-11-25T22:19:47.987641        1325               
vulnerabilities        centos:6               2019-11-25T22:19:48.756978        1362               
vulnerabilities        centos:7               2019-11-25T22:19:50.939557        911                
vulnerabilities        centos:8               2019-11-25T22:19:45.016264        129                
vulnerabilities        debian:10              2019-11-25T22:19:51.971215        21630              
vulnerabilities        debian:11              2019-11-25T22:19:50.288326        18371              
vulnerabilities        debian:7               2019-11-25T22:19:47.026591        20455              
vulnerabilities        debian:8               2019-11-25T22:19:47.389327        22889              
vulnerabilities        debian:9               2019-11-25T22:19:49.366756        21793              
vulnerabilities        debian:unstable        2019-11-25T22:19:49.624134        22744              
vulnerabilities        ol:5                   2019-11-25T22:19:49.845383        1241               
vulnerabilities        ol:6                   2019-11-25T22:19:44.572298        1469               
vulnerabilities        ol:7                   2019-11-25T22:19:46.786958        1059               
vulnerabilities        ol:8                   2019-11-25T22:19:48.983817        116                
vulnerabilities        ubuntu:12.04           2019-11-25T22:19:46.110109        14948              
vulnerabilities        ubuntu:12.10           2019-11-25T22:19:45.242311        5652               
vulnerabilities        ubuntu:13.04           2019-11-25T22:19:50.510165        4127               
vulnerabilities        ubuntu:14.04           2019-11-25T22:19:45.684811        20218              
vulnerabilities        ubuntu:14.10           2019-11-25T22:19:45.917066        4456               
vulnerabilities        ubuntu:15.04           2019-11-25T22:19:53.048077        5860               
vulnerabilities        ubuntu:15.10           2019-11-25T22:19:51.405542        6513               
vulnerabilities        ubuntu:16.04           2019-11-25T22:19:51.697377        17330              
vulnerabilities        ubuntu:16.10           2019-11-25T22:19:44.354528        8647               
vulnerabilities        ubuntu:17.04           2019-11-25T22:19:52.156023        9157               
vulnerabilities        ubuntu:17.10           2019-11-25T22:19:43.293815        7935               
vulnerabilities        ubuntu:18.04           2019-11-25T22:19:51.209554        11582              
vulnerabilities        ubuntu:18.10           2019-11-25T22:19:48.233665        8394               
vulnerabilities        ubuntu:19.04           2019-11-25T22:19:53.291181        8123               
vulnerabilities        ubuntu:19.10           2019-11-25T22:19:52.414912        6354
```

### Waiting for Anchore feed data to sync

**Note:** Upon a fresh installation of Anchore Engine, the system will take some time to bootstrap. CVE data for Linux distributions such as Alpine, CentOS, Debian, Oracle, Red Hat and Ubuntu will be downloaded. The initial sync may take several hours, depending on the speed of your network connection.

You can run the following command to wait until Anchore Engine is available and ready. This can be useful when waiting for vulnerability data to sync on intial installation. `anchore-cli system wait`

```
# Blocking operation that will return when anchore-engine is available and ready

root@4c0a95557659:/anchore-engine# anchore-cli system wait
Starting checks to wait for anchore-engine to be available timeout=-1.0 interval=5.0
API availability: Checking anchore-engine URL (http://localhost:8228)...
API availability: Success.
Service availability: Checking for service set (catalog,apiext,policy_engine,simplequeue,analyzer)...
Service availability: Success.
Feed sync: Checking sync completion for feed set (vulnerabilities)...
Feed sync: Success.
```

### Logs

If you are running into feed sync failures a good place to begin investigation is the the policy engine service logs (`/var/log/anchore/anchore-policy-engine.log`).

## My image will not analyze

Image analysis is performed as a distinct, asynchronous, and scheduled task driven by queues that analyzer workers periodically poll. Image records have a small state-machine as follows:

**Note:** In order for an image to move from 'not_analyzed' to 'analyzing', you need a healthy catalog, simplequeue, and analyzer service up and running. See the verifying services section in the [General Troubleshooting Approach Guide]({{< ref "/docs/troubleshooting" >}}) for more information.

### Logs

If you run into issues with images failing analysis a good place to start inspecting is the analyzer logs (`/var/log/anchore/anchore-worker.log`)

The analyzer is the only component that can set an image state to 'analysis_failed', so you should be able to see a record of what happened.

## Unable to access private registry

Anchore Engine will attempt to download images from any registry without requiring further configuration.
However if your registry requires authentication then the registry and corresponding credentials will need to be defined.

### The --insecure option

Anchore Engine will only pull images from a TLS/SSL enabled registry. If the registry is protected with a self signed certificate or a certificated signed by an unknown certificate authority then the `--insecure` option can be passed which instructs the Anchore Engine not to validate the certificate.

`anchore-cli registry add REGISTRY USERNAME PASSWORD --insecure`

### The --skip-validate option

Anchore Engine attempts to perform a credential validation upon registry addition, but there are cases where a credential can be valid but the validation routine can fail (in particular, credential validation methods are changing for public registries over time).  If you are unable to add a registry but believe that the credential you are providing is valid, or you wish to add a credential to anchore before it is in place in the registry, you can bypass the registry credential validation process using the `--skip-validate` option to the 'registry add' command.

`anchore-cli registry add REGISTRY USERNAME PASSWORD --skip-validate`

## Anchore Engine container keeps exiting

The first step here should be to inspect the logs for the exited container. For Docker, running `docker logs <exited container id> ` should return the reason the container exited.

If you see `[MainThread] [anchore_manager.cli.service/start()] [ERROR] Error: cannot locate configuration file (/config/config.yaml)` in the logs, you should verify you have set up the directory for your Anchore installation correctly.

If you are following the Anchore engine quickstart guide, your directory structure should look like the following:

```
cd ~/aevolume
# find .
.
./config
./config/config.yaml
./db
./docker-compose.yaml
```

If you still see the above error, take a look at the `docker-compose.yaml` file and confirm that the config volume mount definition is correct.

Example:

```
volumes:
   - ./config:/config/:z
```

**Note:** If you are following the quickstart guide, you should not need to make any modifications to the configuration files, they are designed to work out of the box.
