---
title: "The Unrecognized Platform"
date: 2018-12-01
tags: ["openshift", "kubernetes", "paas", "containers"]
draft: true
---
It has been almost 3 years when I started playing with OpenShift/k8s and a bit more with what we call now Linux Containers. During this period (mostly at Red Hat) I've helped Customers from many different industries to move their workloads to OpenShift. I had the opportunity to work on both greenfield and brownfield projects, move or create simple microservices to run in OpenShift and take advantage from the features the platform brings to accelerate the development process, or brake old big monolithics into small microservices and build a CI/CD ecosystem around them. These are, reduced to the minimal expression, the typical use cases so far when using OpenShift/k8s. I recently had the opportunity to work on diferent use cases where we sucessfuly demostrated that the platform limits and posibilities are still not discovered.

This blog post will try to explain and show new exciting posibilities already avilable or looking to be available in the near future for the OpenShift platform. We have already implemented some of these new capabilities in Customers where are key for their business development and the feedback has been really possitive. Customers are already thinking in OpenShift/k8s as their base platform to run all their workloads, something similar that happened not many years ago with RHEL. This is a massive opportunity for both Red Hat and the Comunnity continue leading the IT Enterprise direction.

From the initial days of OpenShift/k8s, the main idea was to orchestrate 'stateless' applications. These applications comming as a container images were going to be inmutable, as this is the nature and what makes a container 'special', and won't change over the time. They were not keeping any status as the industry thought at that time it was not necessary. As long the platform evolved, made popular and was globally adopted, new workload types aimed to be orquestrated and new features were asked, starting with applications that had the ability of record states, and react from those states once restarted, or applications that were sticked to some particular identity that must be preserved after restarts. This resulted in new supported API types called PetSet initially moved to StatefulSet afterwards. This provided the ability to run clusterized applications or applications that required using stable Network Identities.

As the Platform was getting more and more popular, new capabilities from different industries were asked. Not only the community but also big software and hardware vendors started working on solutions to cover these new requiremets. This resulted in a variaty of new projects, inicitives and capabilities added to the platform. As I mentioned before, I recently had the opportunity to work with Customers implementing these new funcionalities and also work closely with Red Hat and non Red Hat product teams to provide early feedback on these new capabilities and agree on how we can help Customers better and address uncoming requests as I will explain below.

Using GPUs in OpenShift/k8s is one of the new trending features we are trying to address now. This is implemented using [device plugins feature](https://docs.openshift.com/container-platform/3.11/dev_guide/device_plugins.html) which provides a solution for vendors to be able to advertise their resources to Kubelet and monitor them without writing custom Kubernetes code. The idea is to extend the existing k8s API so all these new capabilities could be added without modifying the Kubernetes project code. Check the (Device Manager Proposal)[https://github.com/kubernetes/community/blob/master/contributors/design-proposals/resource-management/device-plugin.md] for further understanding and direction from the community on this. This feature allowed us recently to move complex workloads for a Customer where they had many different server racks isolated






Windows containers along Linux container
GPU for calculation and rendering - AI focused
Big Data
Different storage types on the same cluster
KubeVirt for VMs orchestration using k8s
ARM
