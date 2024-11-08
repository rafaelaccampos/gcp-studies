You have been asked to deploy, configure, and test a new Kubernetes Engine cluster that will be used for application development and pipeline testing by the Orca development team.

As per the organization's security standards you must ensure that the new Kubernetes Engine cluster is built according to the organization's most recent security standards and thereby must comply with the following:

The cluster must be deployed using a dedicated service account configured with the least privileges required.
The cluster must be deployed as a Kubernetes Engine private cluster, with the public endpoint disabled, and the master authorized network set to include only the ip-address of the Orca group's management jumphost.
The Kubernetes Engine private cluster must be deployed to the orca-build-subnet in the Orca Build VPC.
From a previous project you know that the minimum permissions required by the service account that is specified for a Kubernetes Engine cluster is covered by these three built in roles:

roles/monitoring.viewer
roles/monitoring.metricWriter
roles/logging.logWriter
These roles are specified in the Google Kubernetes Engine (GKE)'s Harden your cluster's security guide in the Use least privilege Google service accounts section.

You must bind the above roles to the service account used by the cluster as well as a custom role that you must create in order to provide access to any other services specified by the development team. Initially you have been told that the development team requires that the service account used by the cluster should have the permissions necessary to add and update objects in Google Cloud Storage buckets. To do this you will have to create a new custom IAM role that will provide the following permissions:

storage.buckets.get
storage.objects.get
storage.objects.list
storage.objects.update
storage.objects.create
Once you have created the new private cluster you must test that it is correctly configured by connecting to it from the jumphost, orca-jumphost, in the management subnet orca-mgmt-subnet. As this compute instance is not in the same subnet as the private cluster you must make sure that the master authorized networks for the cluster includes the internal ip-address for the instance, and you must specify the --internal-ip flag when retrieving cluster credentials using the gcloud container clusters get-credentials command.

All new cloud objects and services that you create should include the "orca-" prefix.

Your final task is to validate that the cluster is working correctly by deploying a simple application to the cluster to test that management access to the cluster using the kubectl tool is working from the orca-jumphost compute instance.