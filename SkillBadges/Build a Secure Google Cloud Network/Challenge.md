You need to create the appropriate security configuration for Jeff's site. Your first challenge is to set up firewall rules and virtual machine tags. You also need to ensure that SSH is only available to the bastion via IAP.

For the firewall rules, make sure that:

The bastion host does not have a public IP address.
You can only SSH to the bastion and only via IAP.
You can only SSH to juice-shop via the bastion.
Only HTTP is open to the world for juice-shop.
Tips and tricks:

Pay close attention to the network tags and the associated VPC firewall rules.
Be specific and limit the size of the VPC firewall rule source ranges.
Overly permissive permissions will not be marked correct.

 ![Build Secure Google Cloud Network](images/build-secure-google-cloud-network.png)