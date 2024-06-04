# ~~~~ GCP Google Compute Engine ~~~~

Just like AWS EC2, Google CE or Google compute engine lets you create and manage lifecycle virtual machine instances VMs

* Load balancing and scaling for multiple VM instances
* Attach storage (& network storage) to your VM instances
* Manage network conectivity and configuration for your VM instances

### GCP Compute Engine Machine Family

What type of hardware do you want to run your workloads on?

* Different machine families for Different workloads:
    * General purpose (E2, N2, N2D, N1, C3, C3D): Best price-performance ratio
        * Web and application servers, small-medium databases, dev environments
    * Memory Optimized (M2, M1): Ultra high memory workloads
        * Large in-memory databases and in-memory analytics
    * Compute Optimized (H3, C2, C2D): Compute optimized family
        * Ultra high performance for compute intensive workloads
    * Storage Optimized (Z3): Good for storage intensive workloads
        * Flash-optimized databases, File servers
    * Accelerator Optimized (A3, A2, G2): Best for computing workloads
        * Optimized for accelerated high performance computing workloads
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

# ~~~~ GCP Optimizing costs and performance ~~~~

Automatic discounts: For running VM instances for significant portion of the billing month

* Example: If we use N1, N2 machine types, for more than 25% of a month we get between 20%
and to 50% discoun on every incremental minute
* Discount increases with usage
* No action required from the user

* Applicable for instances created by google kubernetes engine and compute engine
* Restrictions:
    * Does not apply on certain machines types (E2, A2)
    * Does not apply to VMs created by app engine flexible and dataflow

### Commited use discounts

Used for predictable resource needs, offers a greater discount compared to automatic discounts

* Commits between 1 and 3 years
* Up to 70% discount based on machine type and GPUs
* Applicable for instances created by google kubernetes engine and compute engine
* Restrictions:
    * Does not apply on certain machines types (E2, A2)
    * Does not apply to VMs created by app engine flexible and dataflow

### Preemtible VM (Like AWS Spot instances)

* Short-lived cheaper (upto 80%) compute instances
    * Can be stopped by GCP any time (preemted) within 24 hours
    * Instances get a 30 second warning (to save anything we want to save)
* Use preempt VM's if:
    * Application is fault tolerant
    * Very cost intensive applications
    * Workload is not immediate
    * Non immediate batch processing jobs
* Restrictions:
    * Not always available
    * NO SLA and cannot be migrated to regular VMs
    * No automatic restarts
    * Free tier credits not available

___Spot VMs___

Latest version of Preemtible VMs

* Key difference: Does not have a Maximum runtime
    * Compared to preemtible VMs which have a maximum runtime of 24 hours
* Other features similar to traditional preemtible VMs
    * May be reclaimed at any time with 30 second notice
    * Not always available
    * Dynamic pricing: 60-91% discount compared to on-demand VMs
    * Free tier credits not available

### GCP Billing

* You are billed by the second (after a minimum of 1 minute)
* You are not billed for compute when a compute instance is stopped
    * However, you will be billed for any storage attached to it
* (RECOMMEDNATION): Always create Budget alerts and make use of budget exports to stay on top
of billing
* Ways to save money?:
    * Choose the right machine type and image for the workload
    * Beware of the discounts available
    * Sustained use discounts
    * Commited use discounts
    * Discounts for preemtible/stop VM instances

### GCP Live migration & Availability policy

* How do we keep the VMs running when a host system needs to be updated (software or hardware updates)?

* Live migration
    * Running instances are migrated to another host in the same zone
    * Does not change any attributes or properties of the VM
    * Supported instances with local SSDs
    * NOT supported for GPUs and preemptible instances
* Important configuration - Availability policy:
    * On host maintenance: What should happen during periodic infrastructure maintenance?
        * Migrate(default): Migrate VM instance to other hardware
        * Terminate: Stop the VM instance
    * Automatic restart: Restart VM instances if they are terminated due to non-user
    initiated reasons (maintenance)

### GCP Custom machine Types

* Create custom images based on our needs (Custom machine Type)
* Custom machine type: Adjust vCPUs, memory and GPUs
    * Choose between E2, N2 or N1 machine types
    * Supports a wide variety of Operating systems: CentOS, CoreOS, Debian, Red Hat, Ubuntu
* Biller per vCPUs, memory provisioned to each instance
    * Example hourly price: $0.03 / vCPU + $0.005 / GB

### GCP GPUs

For accelerating math intensive and graphic intensive workloads for AI and ML

* Add GPU to a VM:
    * High performance for math intensive and graphic intensive workloads
    * Higher cost
    * Use images with GPU libraries (Deep learning) installed
        * Otherwise GPU will not be used
* GPU restrictions:
    * Not supported for all machine types
    * On host maintenance: can only have the value "Terminate VM instance"

### GCP Compute Engine VM Remember

* Associated with a project
* Machine type availability can vary from region to region
* Can only change the machine type (CPU and Memory) of a stopped instance
    * Cannot change this while instance is running
* VM's can be filtered by different properties
    * Name, Zone, MachineType, etc...
* Instances are Zonal (Run in a specific zone in a specific region)
    * Images are global (can provide access to other projects if needed)
    * Instance templates are global (unless we use zonal resources in templates)
* Automatic basic monitoring enabled:
    * Default metrics: CPU Utilization, Network Bytes, IOPS, etc

* Static IP addresses: Get a constant IP addresses for VM instances
* Instance templates: Pre-configured templates simplifying the creation of VM instances
* Sustained use discounts: Automatic Discounts for running VM instances for
significant portion of the billing month
* Committed for use discounts: 1 year or 3 year reservations for workloads with predictable
resource needs
* Preemtible/Spot VM: Short-lived cheaper (upto 80%) compute instances for non-time-critical workloads

___Best Practices___

* Choose zone and region based on:
    1. Compliance and legal
    2. Availability and latency
    3. Specific hardware needs (services)
    4. Costs
* Choose the right machine type for our needs
* Reserve for "commited discounts" for constant workloads
* Use preemtible/spot instances for fault-tolerant, NON time critical workloads
* Use labels to indicate environment, team, business unit

Important:
* If we want dedicated hardware for our compliance needs, licencing and management needs
    * Use sole tenant nodes
* To automate OS patch management for multiple VMs
    * Use VM manager