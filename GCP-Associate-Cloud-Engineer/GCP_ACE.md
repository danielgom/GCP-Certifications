# ~~~~ GCP Google Compute Engine ~~~~

Just like AWS EC2, Google CE or Google compute engine lets you create and manage lifecycle virtual machine instances VMs

* Load balancing and scaling for multiple VM instances
* Attach storage (& network storage) to your VM instances
* Manage network conectivity and configuration for your VM instances

### GCP Compute Engine Machine Family

What type of hardware do you want to run your workloads on?

* Different machine families for Different workloads:
    * General purpose (E2, N2, N2D, N1): Best price-performance ratio
        * Web and application servers, small-medium databases, dev environments
    * Memory Optimized (M2, M1): Ultra high memory workloads
        * Large in-memory databases and in-memory analytics
    * Compute Optimized (C2): Compute intensive workloads
        * Gaming applications

How much CPU, memory or disk we want?

* Variety of machine types are available for each machine family
    * Example: e2-standard-2:
        * e2: machine family
        * standard - Type of workload
        * 2 - Number of CPUs
* Memory, disk and networking capabilities increase along with vCPUs

* What operating system and what software do you want on the instance?

* Type of image family:
    * Public images: Provided & maintained by Google or Open source communities or third party vendors
    * Custom images: Created by you for your projects

### Internal/External IP Addresses

* External (public) IP Addresses are Internet Addressable
* Internal (private) IP Addresses are internal to a corporate network
* Cannot have two resources with same public (external) IP address
    * However, two different corporate networks can have resources with the same internal IP Address
* All VM instances are assigned at least one internal IP Address
* Creation of external IP Addresses can be enabled for VM instances
    * External IP Address is lost on restart

___Static Addresses___

Quick and dirty way to assign an Static IP address to the VM

* Static IP can be switched to another VM instance in the same project
* Static IP remains attached even if you stop the instance. Must be manually detached
* Static IP is billed when we are not using it
    * Make sure to explicitly release an Static IP when we are not using it

### Bootstrapping with Startup script (same as UserData script in AWS EC2)

* Bootstrapping: Install OS patches or software when a VM instance is launched
* We can configure a startup script to bootstrap

### Instance templates

Why do we need to specify all the VM instance details (image instance type, etc) every time we launch an instance?

* Create instance template:
    * Define machine type, image, labels, startup script and other properties
* Used to create VM instances and managed instance groups
    * Provides a convenient way to create similar instances
* Cannot be updated
    * To make a change, copy an existing template and modify it
* (Optional) Image family can be specified (example - debian-9):
    * Latest non-deprecated version of the family is used

### Reducing launch time with custom image

Installing OS patches and software at launch of VM instances increases boot up time,
How about creating a custom image with OS patches and software pre-installed?

* Custom images can be created from an instance, a persistent disk, a snapshot, another image or a file in Cloud storage
* Can be shared across projects
* Depreacate old image -> recommendation
* Hardening an image: Customize images to your needs and corporate security standards
* Prefer using a Custom Image over a Startup script

