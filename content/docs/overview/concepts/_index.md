---
title: "Concepts"
weight: 2
---

### How does it work?

Anchore takes a data-driven approach to analysis and policy enforcement. The system essentially has discrete phases for each image analyzed:

- **Fetch** the image content and extract it, but never execute it
- **Analyze** the image by running a set of Anchore analyzers over the image content to extract and classify as much metadata as possible
- **Save** the resulting analysis in the database for future use and audit
- **Evaluate** policies against the analysis result, including vulnerability matches on the artifacts discovered in the image
- **Update** to the latest external data used for policy evaluation and vulnerability matches (we call this external data sync a feed sync), and automatically update image analysis results against any new data found upstream.
- **Notify** users of changes to policy evaluations and vulnerability matches
Repeat 5 & 6 on intervals to ensure latest external data and updated image evaluations

![alt text](HowItWorks.png)

The primary interface is a RESTful API that provides mechanisms to request analysis, policy evaluation, and monitoring of images in registries as well as query for image contents and analysis results. We also provide a CLI and its own container.

There are, generally speaking, two different ways to use Anchore, within its single API:

- Interactive Mode - Use the APIs to explicitly request an image analysis, get a policy evaluation and content reports, but the system only performs operations when specifically requested by a user
- Watch Mode - Use the APIs to configure Anchore Enterprise to poll specific registries and repositories/tags to watch for new images added and automatically pull and evaluate them, emitting notifications when a given tag's vulnerability or policy evaluation state changes

With these two modes of operation, integration into CI/CD with Interative Mode or less intrusive watching of production image repositories with Watch Mode, Anchore can be easily integrated into most environments and processes.

### Next Steps

Now let's get familiar with [Images]({{< ref "/docs/overview/concepts/images" >}}) in Anchore.
