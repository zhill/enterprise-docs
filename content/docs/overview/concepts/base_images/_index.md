---
title: "Base Images"
linkTitle: "Base Images"
weight: 3
---

An image is considered base or ancestor if it's layers are a subset of another image. It is possible for an image to have more than one base image, wherein the image shares common layers with different base images to a varying degree. For instance consider an application image built on top of a webapp image that in turn is built on top of an scratch image. Both the webapp and scratch images are bases for the application. The webapp image is a maximal base since it has the most number of layers in common with the application while the scratch image is a minimal base since it has the least number of common layers           

### Comparison

Anchore Enterprise provides a mechanism to compare the policy checks and security vulnerabilities of an image with those of a base image. This allows clients to

- filter out results that are inherited from a base image and focus on the results relevant to the application image
- reverse the focus and examine the base image for policy check violations and vulnerabilities which could be a deciding factor in choosing the base image for the application    

To read more about the base comparison features, jump to 

- [Compare policy checks] ({{< ref "/docs/overview/concepts/base_images/checks" >}})
- [Compare vulnerabilities] ({{< ref "/docs/overview/concepts/base_images/vulnerabilities" >}})