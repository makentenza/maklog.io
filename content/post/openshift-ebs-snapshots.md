---
title: "Creating snapshots in AWS for OpenShift Persistent Volumes"
date: 2017-11-24
tags: ["aws", "openshift", "pvs", "pvc", "snapshots", "backup"]
draft: false
---
Even microservices where thought to be used as stateless services, the requirement to persist the data is growing exponentially. Kubernetes and OpenShift facilitate this through [persistent volumes](https://docs.openshift.com/container-platform/latest/architecture/additional_concepts/storage.html#persistent-volumes) and [persistent volume claims](https://docs.openshift.com/container-platform/latest/architecture/additional_concepts/storage.html#persistent-volume-claims).

We can use multiple volume plugins to automatically provision these volumes on different storage backends, giving users a way to request those resources without having any knowledge of the underlying infrastructure. This is really cool from a user/developer point of view, but the groups that take care of the PaaS environment would need to implement some backup strategies for the critical data.

If you use [EBS](https://docs.openshift.com/container-platform/latest/install_config/persistent_storage/persistent_storage_aws.html#install-config-persistent-storage-persistent-storage-aws) for persistent_storage, this post may be useful, as will show you how to create [Kubernetes Cron Jobs](https://docs.openshift.com/container-platform/latest/dev_guide/cron_jobs.html) in OpenShift to automatically create snapshots from the EBS volumes in AWS.

The following [repository](https://docs.openshift.com/container-platform/latest/dev_guide/cron_jobs.html) has all the required components to allow you to perform this operation.

The [cronjob-aws-ocp-snap.yaml](https://github.com/makentenza/aws-ocp-snap/blob/master/template/aws-ocp-snap.yaml) template creates several objects in OpenShift.

* A custom `BuildConfig` that will create the required container to run the job from
* A custom `ClusterRole` that defines the proper permissions to query PVCs in the Cluster
* A `ServiceAccount` we will use with 'aws cli' to perform the snapshots
* A `ClusterRoleBinding` that maps the `ServiceAccount` to the `ClusterRole`
* A `CronJob` to run the snapshots on a schedule
* A `Secret` to store the 'aws cli' required credentials and config to connect to your AWS account

To deploy this template, run the following:

1. Create a project in which to host your jobs.
	```
	oc new-project <project>
	```
2. Instantiate the template
	```
	oc process -f cronjob-aws-ocp-snap.yaml \
	  -p NAMESPACE="<project name from previous step>" \
	  -p AWS_ACCESS_KEY_ID="AWS Access Key ID (base64 format)" \
	  -p AWS_SECRET_ACCESS_KEY="WS Secret Access Key ID (base64 format)" \
		-p AWS_REGION="AWS Region where EBS objects reside (base64 format)" \
		-p NSPACE="Namespace where Persistent Volumes are defined (can be ALL)" \
		-p VOL="Persistent Volume Claim name (can be ALL)" \
		| oc create -f-

You should get a CronJob configured in your project:

  ```shell
  $ oc get cronjob
  NAME                                       SCHEDULE      SUSPEND   ACTIVE    LAST-SCHEDULE
  cronjob-ebs-snaphost                       00 23 * * *   False     0         Thu, 23 Nov 2017 23:00:00 +0000

  ```
And you will see Jobs coming in the schedule you selected:

  ```shell
  $ oc get jobs
  NAME                                                  DESIRED   SUCCESSFUL   AGE
  cronjob-ebs-snaphost-1511448600                       1         1            21h
  cronjob-ebs-snaphost-1511451060                       1         1            20h
  cronjob-ebs-snaphost-1511478000                       1         1            13h
  ```

If everything works as expected, you would see how the snaphots were created on your Pods' logs:

  ```shell
  $ oc logs cronjob-ebs-snaphost-1511478000-lbf67
  gogs-data            Bound     pvc-291b545e-bd52-11e7-8ca3-022931080584   10Gi      RWO       gp2       24d
  gogs-postgres-data   Bound     pvc-291c0ccc-bd52-11e7-8ca3-022931080584   5Gi       RWO       gp2       24d
  jenkins              Bound     pvc-5013abae-b989-11e7-bca0-022931080584   1Gi       RWO       gp2       29d
  Creating snapshot for EBS volume  vol-07671c0b8789108a5
  {
      "Description": "Automted Snapshot by aws-ocp-snap",
      "Encrypted": false,
      "VolumeId": "vol-07671c0b8789108a5",
      "State": "pending",
      "VolumeSize": 10,
      "StartTime": "2017-11-23T23:00:18.000Z",
      "Progress": "",
      "OwnerId": "715326621454",
      "SnapshotId": "snap-0af5d0fa731641b9f"
  }
  Creating snapshot for EBS volume  vol-0bde15d87eb1c720c
  {
      "Description": "Automted Snapshot by aws-ocp-snap",
      "Encrypted": false,
      "VolumeId": "vol-0bde15d87eb1c720c",
      "State": "pending",
      "VolumeSize": 5,
      "StartTime": "2017-11-23T23:00:18.000Z",
      "Progress": "",
      "OwnerId": "715326621454",
      "SnapshotId": "snap-0750eb9656549568e"
  }
  Creating snapshot for EBS volume  vol-0264af5d8dffc6b86
  {
      "Description": "Automted Snapshot by aws-ocp-snap",
      "Encrypted": false,
      "VolumeId": "vol-0264af5d8dffc6b86",
      "State": "pending",
      "VolumeSize": 1,
      "StartTime": "2017-11-23T23:00:19.000Z",
      "Progress": "",
      "OwnerId": "715326621454",
      "SnapshotId": "snap-0a86551ca6657c328"
  }
  ```

You should see same info in your AWS console:

![alt text](/static/aws-snap-01.png "EBS snapshots")

Enjoy!
