---
title: "Tensorboard"
description: ""
lead: ""
date: 2021-04-07T15:35:16-07:00
lastmod: 2021-04-07T15:35:16-07:00
draft: false
images: []
menu:
  docs:
    parent: "recipes"
weight: 400
toc: true
---

KubeDL can attach a tensorboard to a running tensorflow job. 
Users can measure and visualize the tensorflow job with the tensorboard.

To use tensorboard, users must ensure that the tensorflow job logs are created and stored in a kubernetes volume(emptyDir hostPath and local are not supported), and the tensorboard can reuse the volume.

Users can set the tensorboard config in the job's annotation with key `kubedl.io/tensorboard-config` as below. After that, users can access the tensorboard through this URL `http://<ingress host>/<ingress pathPrefix>/<job namespace>/<job name>`.

```yaml
    apiVersion: "kubeflow.org/v1"
    kind: "TFJob"
    metadata:
      name: "mnist"
      namespace: kubedl 
      annotations:
 +      kubedl.io/tensorboard-config: '{"logDir":"/var/log/training","ttlSecondsAfterJobFinished":3600,"ingressSpec":{"host":"locahost","pathPrefix":"/tb"}}'
    spec:
      cleanPodPolicy: None 
      tfReplicaSpecs:
        ...
```

A full list of supported options are:

```json5
{
    "logDir": "xxx",            // the path of the tensorflow job logs (required).
    "ttlSecondsAfterJobFinished": 3600,     // the TTL to clean up the tensorboard after the job is finished (required).
    "image": "xxx",             // the image of the tensorboard, default value is the job's image (optional).
    "ingressSpec": {            // the ingress of the tensorboard (required).
        "host": "xxx",          // the ingress host (required).
        "pathPrefix": "xxx",    // the pathPrefix will set to ingress path with the pattern: <pathPrefix>/<job namespace>/<job name> (required).
        "annotations": {        // the annotations of the ingress (optional).
            "xxx": "xxx"
        }
    }
}
```
