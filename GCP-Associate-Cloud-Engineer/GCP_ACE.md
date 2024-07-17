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

# ~~~~ GCP Instance groups ~~~~

Group of VM instances managed as a single entity

* Manage group of similar VMs having a similar lifecycle as ONE UNIT

* Two types of Instance groups (Like AWS auto scaling groups)
    * Managed: Identical VMs created using a template:
        * Features: Auto scaling, auto healing and managed releases
    * Unmanaged: Different configuration for VMs in same group:
        * Does NOT offer auto scaling, auto healing & other services
* Location can be zonal or regional

### Managed instance groups

* Identical VMs created using an instance template
* Instance template is mandatory
* Importan features:
    * Maintain certain number of instances
        * if an instance crashes, MIG launches another instance
    * Detect application failures using health checks(Self healing)
    * Increase and decrease instances based on load(Auto scaling)
        * Max number of instances
        * Min number of instances
        * Autoscaling metrics: CPU utilization target or load balancer utilization
        target or Any other metric from stack driver:
            * Cooldown perdiod: How long to wait before looking at auto scaling metrics again
            * Scale in Controls: Prevent a sudden drop in no of VM instances
                * Example: Don't scale in by more than 10% or 3 instances in 5 minutes
* Add Load Balancer to distribute load
* Create instances in multiple zones (regional MIG)
* Release new application versions without downtime
    * Rolling updates: release new version step by step (gradually). Update
    a percentage of instances to the new version at a time
    * Canary Deployment: Test new version with a group of instances before
    releasing it across all instances

___Rolling Update___

Gradual update of instances in an instance group to the new instance template
* Specify new template
    * Optional -> Specify a template for canary testing
* Specify how yuo want the update to be done:
    * When should the update happen?
        * Start the update immediately (Proactive) or when instance group is resized
        later (Opportunistic)
    * How should the update happen?
        * Maximum surge: How many instances are addded at any point in time?
        * Maximum unavailable: How many instances can be offline during the update
* Rolling restart/replace: Gradual restart or replace of all instances in the group
    * No change in template but replace/restart existing VMs
    * Configure Maximum surge, Maximum unavailable and What you want to do? (Restart/Replace)

### Gcloud managed instances

___Create___

* gcloud compute instance-groups managed
    * Create instance group: create
        * gcloud compute instance-gropups managed create my-mig --zone us-central1-a --template
        my-template --size 1
            * --health-check=HEALTH_CHECK: how you do decide if an instance is healthy?
            * --initial-delay= How much time should you give an instance to start?
* Setup auto scaling: set-autoscaling/stop-autoscaling
    * gcloud compute instance-groups managed set-autoscaling my-mig --max-num-replicas=10
        * --cool-down-perdiod(default 60s): how much time should auto scaler wait after initiating an autoscaling action?
        * --scale-based-on-cpu --target-cpu-utilization
        * --scale-based-on-load-balancing --target-load-balacing-utilization
        * --min-num-replicas --mode(off/on/only-stale-out)
    * gcloud compute instance-groups managed stop-autoscaling my mig
* Update existing MIG policies (ex: auto healing policies):
    * gcloud compute instance-groups managed update my-mig
        * --initial-delay: how much time should you give to the isntance to start before marking ita s unhealthy?
        * --health-check: how do you decide if an instance is healthy?

___Update___

* Resize the group:
    * gcloud compute instance-groups managed resize my-mig --size=5
* Recreate one or more instances(delete and recreate instances):
    * gcloud compute instance-groups managed recreate-instances my-mig --instances=my-instance-1, my-instance-2
* Update specific instances:
    * gcloud compute instance-groups managed update-instances my-mig --instances=my-instance-3,my-instance-4 (Update specific instances from the group)
        * --minimal-action=none(default)/refresh/replace/restart
        * --most-disruptive-allowed-action=none(default)/refresh/replace/restart
* Update instance template:
    * gcloud compute instance-groups managed set-instance-template my-mig --template=v2-template

___Rolling___

New release without downtime from v1 to v2 (After updating instance template)

* gcloud compute instance-groups managed rolling-action
    * Restart: gcloud compute instance-groups managed rolling-action restart my-mig
        * --max-surge=5% or 10% (Max no of instances updated at a time)
    * Replace: gcloud compute instance-groups managed rolling-action replace my-mig
        * --max-surge=5% or 10% (Max no of instances updated at a time)
        * --max-unavailable=5% or 10% (Max no of instances that can be down for the update)
        * --replacement-method=recreate/substitute(substitute) create instances with new names, recreate reuses names
    * Update instances to a new template:
        * Basic version (Update all instances slowly step by step): gcloud compute instance-groups managed rolling-action start-update my-mig --version=template=v1-template


___Important___

* **Create -> gcloud compute instance-gropups managed create**
* **AutoScaling -> gcloud compute instance-groups managed set-autoscaling/stop-autoscaling my-mig**
* **Updatepolicies -> gcloud compute instance-groups managed update my-mig**
* **Resize -> gcloud compute instance-groups managed resize**
* **Recreate -> gcloud compute instance-groups managed recreate-instances**
* **Updateinstance -> cloud compute instance-groups managed update-instances**
* **Update instance template -> gcloud compute instance-groups managed set-instance-template**


# ~~~~ GCP Cloud load balancing ~~~~

Distributes user traffic across instances of an application in single region or multiple regions

* Fully distributed, software defined managed service
* Importante features:
    * Health checks - Route to healthy instances
        * Recover from failures
    * Auto Scaling
    * Global load balancing with single anycast IP
        * Supports internal load balancing
* Enables:
    * High availability
    * Auto scaling
    * Resiliency

### SSL/TLS Termination/Offloading

* Client to Load balancer: Over internet
    * HTTPS recommended
* Load Balancer to VM instance: Through Google internal network
    * HTTP is ok. HTTPS is preferred
* SSL/TLS Termination/Offloading
    * Client to Load balancer: HTTPS/TLS
    * Load balancer to VM instance: HTTP/TCP

___Choosing load balancer___


* External HTTPs Load balancer
    * Global, external, HTTP or HTTPS
    * Proxy pass
    * Destination - HTTP 80,8080 ports or HTTPS 443 Port
* Internal HTTPs Load balancer
    * Regional, internal, HTTP or HTTPS
    * Proxy pass
    * Destination - HTTP 80,8080 ports or HTTPS 443 Port
    * SSL/TLS termination - yes
* SSL proxy
    * Global, External, TCP with SSL offload
    * Proxy pass
    * A big list of destinations
* TCP proxy
    * Global, External, TCP without SSL offload
    * Proxy pass
    * A big list of destinations
    * SSL/TLS termination - no
* Eternal network TCP/UDP
    * Regional,External TCP or UDP
    * Pass-through
    * Any destination
* Internal TCP/UDP
    * Regional,internal TCP or UDP
    * Pass-through
    * Any destination


* Internal
    * TCP Traffic && UDP traffic - **Internal TCP/UDP Load balancing**
    * HTTP or HTTPS - **Internal HTTPS Load balancing**
* External
    * HTTP or HTTPS:
        * Regional - **Regional External HTTPS load balancer**
        * Non-regional - **External HTTPS Load Balancer (Global(preferred) or Classic)**
    * TCP Traffic: 
        * Offloading - **SSL Proxy**
        * No-Offloading
            * Global LB or IPv6 - **TCP Proxy**
            * No-Global LB or IPV6
                * Preserve Client IP - **External NetworkLoad balancing**
                * No-preserve Client IP - **TCP Proxy**
    * UDP - **External NetworkLoad balancing**

# ~~~~ GCP Managed Services ~~~~

* Compute engine
    * High performance and general purpose VMs that scale globally - IaaS
* GKE   
    * Orchestrate containerized microservices on Kubernetes, Needs advanced cluster configuration
    and monitoring - CaaS
* App Engine
    * Build highly scalable applications on a fully managed platform using open and
    familiar languages and tools - Paas
* Cloud Functions
    * Build event driven applications using simple, single-purpose functions - Faas, Serverless
* Cloud Run
    * Develop and deploy highly scalable containerized applications,
    does not need a cluster - CaaS

# ~~~~ GCP App Engine ~~~~

Simplest way to deploy and scale applications in GCP (Similar to AWS Beanstalk)

* Provides end-to-end application management
* Supports:
    * Go, Java, .NET, Node.js, PHP, Python, Ruby using pre-configured runtimes
    * Use custom run-time and write code in any language
    * Connect to a variety of Google Cloud storage products (Cloud SQL, etc)
* No usage charges - Pay for resources provisioned
* Features
    * Automatic load balancing & Auto scaling
    * Managed platform updates & Application health monitoring
    * Application versioning
    * Traffic splitting

___Environments Standard vs Flexible___

* Standard: Applications run in language specific sandboxes
    * Complete isolation from OS/Disk/Other Apps
    * V1: Java, Python, PHP, Go (Old versions)
        * ONLY for Python and PHP runtimes:
            * Restricted network access
            * Only white-listed extensions and libraries are allowed
        * No restrictions for Java and Go runtimes
    * V2: Java, Python, PHP, Node.Js, Ruby, Go (New versions)
        * Full network access and no restrictions on Language Extensions
* Flexible: Application instances run within docker containers
    * Makes use of compute engine virtual machines
    * Supports ANY runtime
    * Provides access to background proceses and local disks

Feature| Standard    | Flexible |
|------| -------- | ------- |
| Pricing  | Instance hours    | vCPU, Memory & Persistent Disks
| Scaling | Manual, basic, automatic     | Manual, automatic
| Scaling to zero    | Yes    | No, Minimum one instance
| Instance startup  | Seconds    | Minutes
| Rapid Scaling | Yes     | No
| Max requests timeout   | 1 to 10 minute    | 60 minutes
| Local Disk  | Mostly (Except for Python, PHP) can write to tmp  | Yes, Ephimeral, New disk on startup
| SSH for Debugging | No     | Yes


___Application Component Hierarchy___

* Application: One App per project
* Services: Multiple microservices or App components
    * You can have multiple services in a single application
    * Each service can have different settings
    * Earlier called modules
* Versions: Each version associated with code and configuration
    * Each version can run in one or more instances
    * Multiple versions can co-exist
    * Options to rollback and split traffic

### Scaling Instances

* Automatic - Automatically scale instances based on the load:
    *  Recommended for continously running workloads
        * Auto scale based on:
            * Target CPU utilization - Configure a CPU usage threshold
            * Target Throughput utilization - Configure a throughput threshold
            * Max concurrent requests - Configure max concurrent requests an instance can receive
        * Configure max instances and min instances
* Basic - Instances are created as and when requests are received
    * Recommended for AdHoc workloads
        * Instances are shutdown if ZERO requests
            * Tries to keep costs low
            * High latency is possible
        * NOT supported by App engine Flexible environment
        * Configure Max instances and Idle timeout
* Manual - Configure Specific number of instances to run:
    * Adjust number of instances manually over time

### App engine app.yaml Reference example

```yaml
runtime: python39 # Name of the runtime
api_version: 2 # String representing the app's version
instance_class: F1
service: service-name

inbound_services:
- warmup

env_variables:
    MY_CFG_VAR: "val1"

handlers:
    - url: /
      script: home.app

# Scaling options, choose 1, flexible cannot handle basic scaling
automatic_scaling:
    target_cpu_utilization: 0.65
    min_instances: 5
    max_instances: 100
    max_concurrent_requests: 50
basic_scaling:
    max_instances: 11
    idle_timeout: 10m
manual_scaling:
    instances: 5
```

### App Engine - Request routing

* We can use a combination of three approaches:
    * Routing with URLs:
        * https://PROJECT_ID.REGION_ID.r.appspot.com (default service called)
        * https://SERVICE-dot-PROJECT_ID.REGION_ID.r.appspot.com (specific service called)
        * https://VERSION-dot-SERVICE-dot-PROJECT_ID.REGION_ID.r.appspot.com (specific service version called)
        * Replace -dot- with "." if using custom domain
* Routing with a dispatch file:
    * Configure dispatch.yaml with routes
    * gcloud app deploy dispatch.yaml
* Routing with Cloud Load Balancing:
    * Configure routes on Load Balancing instance

### App Engine - Deploying with zero downtime

* Option 1: Deploy & Shift all trafic at once:
    * Deploy and shift all traffica t once from v1 to v2: gcloud app deploy
* Option 2: Manage migration from v1 to v2
    1. Deploy v2 without specific traffic shifting (--no-promote)
        * gcloud app deploy --no-promote
    2.  Shift traffic to V2:
        * Option 1: (All at once migration): Migrate all at once to V2
            * gcloud app services set-traffic s1 --splits v2=1
        * Option 2: Gradual migration: Gradually shift traffic to v2. Add --migrate option
            * Gradual mgiration is not supported by the flexible environment
        * Option 3: Splitting: Control the pace of migration
            * gcould app services set-traffic s1 --splits=v2=.5,v1=.5
            * Useful to perform A/B testing

### App engine traffic splitting

* IP splitting - Based on requests IP Address
    * IP addreses can cahnge causing accuracy issues
    * If all requests originate from a corporate vpn with single IP, this can cause all requests to go to the same version
* Cookie splitting - Based on a cookie (GOOGAPPUID)
    * Cookies can be controlled from the application
    * Cookie splitting accurately assigns users to versions
* Random - Do it randomnly

To split, include --split-by option in gcloud app services set-traffic command, value must be one of, cookie, ip, random

### App Engine Cron Job

```yaml
cron:
- description: "daily summary job"
  url: /tasks/summary
  schedule: every 24 hours
```

* Allows to run scheduled jobs at pre-defined intervals
* Use cases:
    * Send a report by email every day
    * Refresh cache data every 30 minutes
* Configured using cron.yaml
* Run this command - gcloud app deploy cron.yaml
    * Performs a HTTP GET requests to the configured URl on schedule

### App Engine Other important yaml files

* dispatch.yaml - override routing rules

```yaml
dispatch:
- url: "*/mobile/*"
  service: mobile-frontend
- url: "*/work/*"
  service: static-backend
```

* queue.yaml - manage task queues

```yaml
queue:
- name: fooqueue
  rate: "1/s"
  retry_parameters:
    task_retry_limit: 7
    task_age_limit: 2d
```


### App engine important to remember

* gcloud app browse/create/deploy/describe/open-console
    * gcloud app create --region=us-central
    * gcloud app deploy app.yaml
        * --image-url: only for flexible environments. Deploys a docker container
        * --promote, --no-promote (Should the new version receive traffic or not)
        * --stop-previous-version, --no-stop-previous-version
        * --version, assign a version, otherwise a number is generated
    * gcloud app browse --service="myservice" --version="v1" - gives the link for the service and version
    * gcloud app open-console --service="myservice" --version="v1"
    * gcloud app open-console --logs
* gcloud app delete/describe/list/scp/ssh
    * gcloud app instances delete i1 --service=s1 --version=v1
    * gcloud app instances descvribe --service=s1 --version=v1
    * gcloud app instances list
    * gcloud app instances scp --service=s1 --version=v1 --recurse local_dir i1:remove_dir
    (Copy files to/from App Engine Flexible instances)
    * gcloud app instances ssh --service=s1 --version=v1 i1 (SSH into the VM of an App Engine Flexible Instance)
* gcloud app services browse/delete/describe/list/set-traffic
    * gcloud app services list
    * gcloud app services browse myService --version="v1"
    * gcloud app services delete servic1 service2
    * gcloud app services describe service1
    * gcloud app services set-traffic APP1 --splits=v1=0.9,v2=0.1
        * --split-by ip,cookie,random
* gcloud app versions browse/delete/describe/list/migrate/start/stop
    * gcloud app versions list
        * --hide-no-traffic (Only show versions that are receiving traffic)
    * gcloud app versions browse/delete/describe v1 --service="myService"
    * gcloud app versions migrate v2 --service="myService"
    * gcloud app versions start/stop v1
        * --service=myService Only start v1 of service myService

# ~~~~ GCP Google Kubernetes Engine ~~~~

Managed Kubernetes service

* Minimize operations with auto-repair (repair failed nodes) and auto-upgrade (user latest version of K8S always) features
* Provides Pod and Cluster autoscaling
* Enabled Cloud loggging and Cloud Monitoring with simple configuration
* Use container-optimized OS. a hardened OS built by google
* Provides support for persistent disks and Local SSD

___Components___

* Cluster: Group of compute engine instances:
    * Master node - Manages the cluster
    * Worker node - Run the workloads (pods)
    * Node Pool - Group of nodes in a cluster with the same configuration
* Master Node - Components:
    * API Server - Handles all communication for a K8S cluster
    * Scheduler - Decides the placements of the pods
    * Control Manager - Manages Deployments and Replicasets
    * etcd - Distributed database storing the cluster state
* Worker Node - Components:
    * Runs pods
    * Kubelet - Manages communication with the master node/nodes

___GKE Cluster Types___

* Zonal Cluster:
    * Single zone - Single control plane. Nodes running in the same zone
    * Multi Zone - Single control plane but Nodes run in multiple zones
* Regional Cluster:
    * Replicas of the control plane runs in multiple zones of a given region. Nodes also run in same zones where control plane runs
* Private Cluster:
    * VPC-native cluster. Nodes only have internal IP Addresses
* Alpha Cluster:
    * Cluster with alpha APIs - early feature APIs used to test new K8S features

### PODS

* Smallest deployable unit in K8S
* A pod contains one or more containers
* Each pod is assigned an ephemeral IP Address
* All containers in a pod share:
    * Network
    * Storage
    * IP Address
    * Ports
    * Volumes (Shared persistent disks)
* POD Statuses: Running/Pending/Succeeded/Failed/Unknown

### Deployments && Replica set

* A deployment is created for each microservice:
    * kubectl create deployment m1 --image=m1:v1
    * Deployment represents a microservice (with all its releases)
    * Deployment manages new releases ensuring zero downtime
* Replica set ensures that a specific number of pods are running for a specific microservice version
    * kubectl scale deployment m2 --replicas=2
    * Even if one of the pods is killed, replica set will launch a new one
* Deploy V2 of microservice - Creates a new replica set
    * kubectl set image deployment m1 m1=m1:v2
    * V2 Replica set is created
    * Deployments updates V2 Replica Set and V2 replica set based on the release strategies
    
### Service

* Each pod has its own IP Address:
    * A pod fails and is replaced by replica set
    * A new release happens and all existing pods of old release are replaced by ones of new release
* Create service
    * kubectl expose deployment name --type=LoadBalancer --port=80
        * Expose PODs to outside world using a stable IP Address
        * Ensures that the external world does not get impacted as pods go down and come up
* Three Types:
    * ClusteIP: Exposes services on a cluster-internal IP
        * use-case: Expose microservice only to be available inside the cluster (intra cluster communication)
    * LoadBalancer: Exposes service externally using a cloud provider's load balancer
    * NodePort: Exposes service on each Node's IP at a static port (the NodePort)

### Container Registry - Image Repository

Fully managed container registry provided by GCP, alternative to Docker Hub

* Can be integrated to CI/CD tools to publish images to registry
* Secure container images. Analyze for vulnerabilities and enforce deployment policies
* Naming: HostName/ProjectID/Image: Tag - gcr.io/projectname/helloworld:1

### Important to remember

* Replicate master nodes accross multiple zones for high availability
* Some CPU on the nodes is reserved by the control plane:
    * 1st core - 6%, 2nd core - 1%,  3rd/4th - 0.5, rest 0.25
* Creating Docker image for services(Dockerfile):
    * Build image: docker build -t name:tag .
    * Test locally: docker run -d -p localport:containerport name:tag
    * Push it to containe repository: docker push name:tag
* Kubernetes supports Stateful deployments like Kafka, Redis, Zookeper:
    * StatefulSet - Set of pods with unique, persistent identities and stable hostnames
* Services on Cluster Nodes:
    * DaemonSet - One pod on every node (background services)
* Integrates with Cloud Monitoring and Cloud logging:
    * Cloud logging system and Apllication logs can be exported to Big Query or Pub/Sub
* Execute untrusted third-party code in Kubernetes cluster:
    * Create a new node pool with GKE SandBox. Deploy untrusted code to sandbox node pool

# ~~~~ GCP Cloud Functions ~~~~

(Similar to AWS Lambda functions)
Run code in response to multiple or single events 

* Enter Cloud Functions:
    * Run code in response to events:
        * Write own business logic in multiple languages
        * Do not worry about servers, scaling or availability
    * Pay what you use:
        * Number of invocations
        * Compute time of the invocations
        * Memory and CPU provisioned
    * Time bound - Default 1 min and MAX 60 minutes (3600 seconds)
    * 2 Product versions:
        * Cloud functions 1st gen: First version
        * Cloud functions 2nd gen: New version built on top of Cloud Run and Eventarc

### Cloud functions 2nd Gen

This is the recommended version to use for Cloud functions

* Key enhancements:
    * Longer request timeout: Up to 60 minutes for HTTP-triggered functions
    * Larger instance sizes: Up to 16GiB RAM with 4 vCPU (v1 up to 8GiB RAM with 2 vCPU)
    * Concurrency: Up to 1000 concurrent requests per function instance (v1: 1 concurrent request per function instance)
    * Multiple Function revisions and traffic splitting: Supported (v1: Not supported)
    * Support for 90+ Event types - enabled by Eventarc (v1: only 7)

___Scaling and Concurrency___

* Typical serverless functions architecture
    * Autoscaling - As new invoctaions come in, new function instances are created
    * One function instance handles ONLY ONE request AT A TIME
    * 3 concurrent function invocations => 3 function instances
        * If a 4th function invocation occurs while existing invocations are in progress, a new function instace will be created
        * A function instance that completed execution may be reused for future requests
    * Cold start:
        * New function instance initialization from scratch can take time
        * Solution - Configure min number of instances (increases cost)
* 1st Gen uses the typical serverless functions architecture
* 2nd Gen Adds very important features:
    * One function instance can handle multiple requests AT THE SAME TIME:
        * Concurrency: How many concurrent invocations can one function instance handle? (Max 1000)
        * Code should be safe to execute concurrently

___Deploying using gcloud___

* gcloud functions deploy
    * --docker-registry: registry to store the function's docker image
        * Default -container-registry
        * Alternative -artifact-registry
    * --docker-repository: repository to store the function's Dockeri mages
    * --gen2: use 2nd Gen
    * --runtime: Nodejs, python, java...
    * --service-account: Service account to user
    * --timeout: function execution timeout
    * --max-instances: function execution exceeding max-instances time out
    * --min-instances: avoid cold starts at higher cost
    * --source
        * Zip file from google cloud storage
        * Source repository
        * Local file system
    * --triger-bucket, --trigger-http, --trigger-topic, --trigger-event-filters (ONLY in gen2 - Eventarc matching criteria for the trigger)


### Concepts && Important to remember

* Event: Upload object to cloud storage
* Trigger: Respond to event with a Function call
    * Trigger: Which function to trigger when an event happens?
    * Functions - Take event data and perform action
* Events are triggered from:
    * Cloud Storage
    * Cloud pub/sub
    * HTTP POST/GET/DELETE/PUT/OPTIONS 
    * Firebase
    * Cloud Firestore
    * Stackdriver logging

* No server management
* Automatically spin up and back down in response to events, scalling horizontally
* Cloud Functions are recommended for responding to events:
    * Cloud functions are not ideal for long running processes

# ~~~~ GCP Cloud Run ~~~~

Container to production in seconds

* Built on top of an open standar - Knative
* Fully managed serverless platform for containerized applications
    * Zero infrastructure management
    * Pay-per-use
    * Can split traffic for multiple versions/revisions
* Fully integrated end-to-end developer experience:
    * No limitations in languages, binaries and depdendencies
    * Easily portable because of container based architecture
    * Cloud code, Cloud Build, Cloud Monitoring & Cloud logging integrations
* Anthos - Run kuberentes clusters anywhere
    * Cloud, Multi Cloud and On-Premise
* Cloud Run for Anthos: Deploy workloads to Anthos clusters running on-premises or on google cloud

# ~~~~ GCP Cloud KMS ~~~~

* Data at rest: Stored on a device or a backup
    * Examples: data on a hard disk, in a database, backups and archives
* Data in motion: Being transferred across a network
    * Also called data in transit
    * Examples:
        * Data copied from on-premise to cloud storage
        * An application talking to a database
    * Two types:
        * in and aout of cloud (from internet)
        * within cloud
* Data in use: Active data processed in a non-persistent state
    * Example: Data in RAM

* Typical use of encryptions
    * Encrypt data at rest
    * Encrypt data in transit
    * Encrypt data in use
* It is not sufficient to encrypt data at rest
    * Encrypt data in transit (between networks) is necessary to avoid intruders

___Asymmetric Key Encryption___

* Two Keys: Public Key and Private key
* Also called Public key Cyptography
* Encrypt data with Public key and depcrypt with Private Key
* Share public key with everybody and keep private key PRIVATE

___KMS___

* Create and manage Cryptographic keys (Symmetric and asymmetric)
* Control their use in application and GCP services
* Provides and API to Encrypt, decrypt or sign data
* Use existing cryptographic keys created on premises
* Integrates with almost all GCP services that need data encryption
    * Google-managed key: No configuration required
    * Customer-managed key: Use Key from KMS (Created in Cloud KMS)
    * Cusomer-supplied key: Provide your own key

# ~~~~ GCP Block and File Storage ~~~~

Types of storage handled by GCP

* Type of storage of a hard disk
    * Block storage
* File sharing storage type
    * File Storage

### Block Storage

* Hardisks attached to the computers
* Typically, ONE block storage device can be connected to ONE virtual server
    * We can attach read only block devices with multiple virtual servers and certain cloud providers are exploring multi-writer disks as well
* Can connect to multiple different block storage devices to one virtual server
* Used as:
    * Direct-attached storage DAS - Similar to a hard disk
    * Storage Area Network SAN - High-speed network connecting a pool of storage devices

* Types
    * Persisent disks: Network Block Storage
        * Zonal: Data replicated in one zone
        * Regional: Data replicated in multiple zone
    * Local SSDs: Local Block Storage


___Local SSDs___

Are physically attached to the host of the VM instances:

* Provide very high IOPS and very low latency
* Ephemeral storage - Temporary data (Data persists as long as the instance is running)
    * Enable live migration for data to survive maintenance events
* Data automatically encrypted
    * Cannot configure encryption keys
* Lifecycle tied to VM instance
* Hold temporary data
* ONLY some machine types support local SSDs
* Supports SCSI and NVMe interfaces
* Choose NVMe-enabled and multi-queue SCSI images for best performance
* Larger Local SSDs (more storage), More vCPUs(attached to the VM) => Even better performance


Advantages

* Very fast I/O (10-100x compared to Persistent disks)
* Ideal when needing high IOPs while storing temporary information

Disadvantages

* Ephemeral storage
    * Lower durability, lower availability, lower flexibility compared to PDs
    * Cannot detach and attach it to another VM

___Persistent Disks___

* Network block storage attached to the VM instance
* Provisioned capacity
* Flexible:
    * Increase size when need it - when attached to VM instance
    * Performance scales with size:
        * For higher performance, resize or add more PDs
* Independent lifecycle from VM instance:
    * Attach or detach from one VM instance to another
* Options: Regional/Zonal:
    * Zonal PDs replicated in a single zone. Regional PDs replicated in 2 zones in same region
    * Typically Regional PDs are 2x the cost of Zonal PDs

Networking storage

* More durable
* Lifecycle NOT tied to VM instance
* Permanent storage
* No VM needed

___PDs Disk Comparison table___

Feature| Standard    | Balanced | SSD |
|------| ----------- | -------- | --- |
| Underlying storage  | Hard Disk Drive | Solid State Drive | Solid State drive
| Referred to as | pd-standard | pd-balanced | pd-ssd
| Performance - Sequential IOPS | Good    | Good | Very good
| Performance - Random IOPS | Bad    | Good | Very good
| Cost | Cheapest | In between | Expensive
| Use cases   | Big Data (Cost efficient) | Balance between cost and performance | High performance


___PDs Snapshots___

* Take point-in-time snapshots of PDs
* Schedule snapshots:
    * Can auto-delete snapshots after X days
* Snapshots can be multi-regional and regional
* Can Share snapshots across projects
* Can create new disks and instances from snapshots
* Snapshots are incremental
    * Deleting a snapshot only deletes data which is NOT needed by other snapshots
    * Do not hesitate to delete unnecessary snapshots
* Keep similar data together on a Persistent Disk
* Separate operating system, volatile data and permanent data
* Attach multiple disks if needed
* This helps to better organize snapshots and images

* Avoid taking snapshots more often than once an hour
* Disk volume is available for use but snapshots reduce performance
    * Schedule snapshots during off-peak hours
* Creating snapshots from disk is faster than creating from images
    * Creating disk from image is faster than creating from snapshot
    * If repeatedly creating disk from snapshot
        * Create an image from snapshot and use the image to create disks


### Images

* Machine image is different from image
    * Image contains an operating system used to boot up a VM
    * Machine image is more than an image
* Multiple disks can be attached with a VM:
    * One boot disk
    * Multiple data disks
* An image is created from the boot persistent Disk
* A Machine Image is created from a VM instance:
    * Machine image contains everything needed to create a VM instance:
        * Configuration
        * Metadata
        * Permissions
        * Data from one or more disks
* Recommended for disk backups, instance cloning and replication

### File Storage

* Media workflows need huge shared storage for supporting processes like video editing
* Enterprise users need a quick way to share files in a secure and organized way
* These files shares are shared by several virtual servers
* Performance scales with capacity

* Types
    * Filestore: High performance file storage
    * Supports NFSv3 Protocol
    * Provisioned capacity

* Suitable for high performance workloads
    * Up to 320 TB with throughput of 16GB/s and 480k IOPS
* Supports HDD (General purpose) and SDD (Performance-critial workloads)
* Use cases
    * File share
    * Media workflows
    * Content management

# ~~~~ GCP Cloud Storage ~~~~

Similar to AWS S3 (Buckets)

* Most popular, very flexible & inexpensive storage service
    * Serverless: Autoscaling and infinite scale
* Storage large objects using a key-value approach:
    * Treats entire object as a unit (Partial updates not allowed)
        * Recommended when you operate on entire object most of the time
        * Access control at Object level
    * Also called Object storage
* Provides REST API to access and modify objects
    * Also provides CLI (gsutil) & Client Libraries (C++, C#, Java, Node.js, PHP, Python & Ruby)
* Store all file types - text, binary, backup & archives
    * Media files and archives, application packages and logs
    * Backups of databases or storage devices
    * Staging data during on-premise to cloud database migration

___Objects and Buckets___

* Objects are stored in buckets
    * Bucket names are gobally unique
    * Bucket names are used as part of object URLs => can contain Only lower case letters, numbers, hyphens, underscores and periods
    * 3-63 characters max, Can't start with goog prefix or should not contain google
    * Unlimited objects in a bucket
    * Each bucket is associated with a project
* Each object is identified by a unique key
    * Key is unique in a bucket
* Max object size is 5 TB
    * But can store unlimited number of such objects

___Storage Classes___

* Different kinds of data can be stored in Cloud Storage
    * Media files and archives
    * Application packages and logs
    * Backups of databases or storage devices
    * Long term archives
* Huge variations in access patterns
* Storage classes help to optimize costs based on access needs
    * Designed for durability 99.99999999999999%
* Low latency
* Unlimited storage
    * Auto scaling
    * No minimum object size
* Same APIs across storage classes
* Commited SLA is 99.95% for multi region and 99.9% for single region for Standard, Nearline and Coldline storage classes
    * No commited SLA for archive storage


StorageClass| Name    | Min Storage Duration | Availability | Use case
|------| ----------- | -------- | --- | ------------------- |
| Standard  | STANDARD | None | > 99.99% in multi region and dual region, 99.99% in regions | Frequently used data for a short period of time
| Nearline storage | NEARLINE | 30 Days | 99.95% in multi region and dual region, 99.9% in regions | Read or modify data once a month on average
| Coldline storage | COLDLINE    | 90 Days | 99.95% in multi region and dual region, 99.9% in regions | Read or modify at most once a quarter
| Archive storage | ARCHIVE    | 365 Days | 99.95% in multi region and dual region, 99.9% in regions | Less than once a year


___Uploading and Downloading Objects___

Option | Recommended for Scenarios |
|------| ----------- |
| Simple Upload  | Small files (that can be re uploaded in case of failuers) + No object metadata | 
| Multipart Upload | Small files (that can be re uploaded in case of failuers) + object metadata |
| Resumable upload | Larger files. RECOMMENDED for most use cases (even for smal files - cost one additional HTTP request) |
| Streaming transfers | Upload an object of unknown size |
| Parallel composite uploads | File divided up to 32 chunks and uploaded in parallel, Significantly faster if network and disk speed are not limiting factors |
| Simple Download | Downloading objects to a destination |
| Streaming Download | Downloading data to a process |
| Sliced object Download | Slice and download larger objects |

___Object versioning___

* Prevents accidental deletion & provides history
    * Enabled at bucket level
        * Can be turned on/off at any time
* Live version is the latest version
    * If we delete live object, becomes noncurrent object version
    * If delete noncurrent object version, it is deleted
* Older versions are uniquely identified by (object key + a generation number)
* Reduce costs by deleteing older (noncurrent) versions

___Object lifecycle management___

* Files are frequently accessed when they are created
    * Generally usage reduces with time
* Identify objects using conditions based on:
    * Age, CreatedBefore, IsLive, MatchesStorageClass, NumberOfNewerVersions, etc...
    * Set multiple conditions: all conditions must be satisfied for action to happen
* Two kinds of actions
    * SetStorageClass actions (change from one storage class to another)
    * Deletion actions (delete objects)

___Encryption___

* Cloud storage always encrypts data on the server side
* Configure server-side encryption: Encryption performed by cloud storage:
    * Google-managed encryption key: Default (No configuration required)
    * Customer-managed encryption keys: Created using Cloud Key Management Service (KMS) by customer in KMS
        * Cloud storage service account should have access to keys in KMS for encrypting and decrypting using the Customer-Managed encryption key
* Client side encryption (OPTIONAL): Encryption performed by customer before upload
    * GCP does NOT know about the keys used

___Gsutil___

* gsutil is the CLI for Cloud Storage not gcloud
    * Cloud Storage (gsutil)
        * gsutil mb - Create cloud storage
        * gsutil ls -a - List current and non-current object version
        * gsutil cp - Copy objects
        * gsutil mv - Rename/Move Objects
        * gsutil rewrite - Change storage class for objects
        * gsutil cp - Upload and Download objects
            * gsutil cp LOCAL_LOCATION gslocation Upload
            * gsutil cp gslocation LOCAL_LOCATION Download
        * gsutil versioning set on/off
        * gsutil uniformbucketlevelaccess set on/off
        * gsutil acl ch (Set access permissions)
        * gsutil iam ch (Set up IAM role)
        * gsutil signurl -d 10m (Set temporary signed URL for temporary access)

# ~~~~ GCP Cloud Identity and Access Management (IAM) ~~~~

* Authentication: Right user?
* Authorization: Access to the desired resource?
* Identities can be
    * A GCP user (Google account or externally authenticated user)
    * A Group of GCP users
    * An application running in GCP
    * An application running in the datacenter
    * Unauthenticated users
* Provides granular control
    * Limit a single user:
        * to perform a single action
        * on a specific cloud resource
        * from a specific IP address
        * during a specific time window

* Important generic concepts
    * Member: Anyone
    * Resource: Like a cloud storage bucket
    * Action: Upload/Delete object
* In Google Cloud IAM:
    * Roles: A set of permissions to perform specific actions on specific resources
        * Roles do NOT know about members, its all about permissions
    * Policy: Assing or bind a role to a member
1. Choose a Role with right permissions (Ex: Storage Object Admin)
2. Create a policy binding memeber (anyone) with role (permissions)

IAM in AWS is very different from GCP (NEVER RELATE EACHOTHER)

### IAM Roles

Roles in GCP are completely different from AWS roles

* Roles are permissions
    * Perform some set of actions on some set of resources
* Three types
    * Basic roles (primitive roles) - Owner/Editor/Viewer
        * Viewer - Read only actions
        * Editor - Viewer + Edit actions
        * Owner - Editor + Manager Roles and Permissions + Billing
        * EARLIEST VERSION: Created before IAM
        * DO NOT USE IN PRODUCTION
    * Predefined Roles - Fine grained roles predefined and managed by Google
        * Different roles for different purposes
        * e.g. Storage Admin, Storage Object Admin, Storage Object Viewer, Storage Object Creator
    * Custom Roles - When predefined roles are NOT sufficient, we can create our own roles

### IAM Policy

* Roles: Permissions, What actions?, What Resources?
* Policy: Assing Permissions to Members
    * Map roles, members and conditions
    * Permissions are not diretly assigned to member
        * Permissions are represented by a Role
        * Member gets permissions through role
* A role can have multiple permissions
* Can assign multiple roles to a Memeber
* Members are also named PRINCIPAL or IDENTITY
* Roles are assigned to users through IAM policy documents
* Represented by a policy object
    * Policy object has list of bindings
    * A binding, binds a role to a list of members
* Member type is identified by prefix:
    * Example: user, serviceaccount, group or domain

* gcloud:
    * gcloud compute project-info describe - Describe current project
    * gcloud auth login - Access the cloud platform with google user credentials
    * gcloud auth revoke - Revoke access credentials for an account
    * gcloud auth ist - List active accounts
    * gcloud projects
        * gcloud projects add-iam-policy-binding - Add IAM policy binding
        * gcloud projects get-iam-policy - Get IAM policy for a project
        * gcloud projects remove-iam-policy-binding - Remove IAM policy binding
        * gcloud projects set-iam-policy - Set the IAM policy
        * gcloud projects delete - Delete a project
    * gcloud iam
        * gloud iam roles describe - Describe an IAM role
        * gloud iam roles create - Create an IAM role --project, --permissions, --stage
        * gloud iam roles copy - Copy IAM Roles

### Service Accounts

* Scenario: An application on a VM needs access to cloud storage
    * Don't want to use personal credentials to allow access
* Use service accouts
    * Identified by an email address (e.g. id-compute@developer.gserviceaccount.com)
    * Does not have a password:
        * Has a private/public RSA key-pairs
        * Can't login via browser or cookies
* Service account types:
    * Default service account - Automatically created when some services are used
        * Not recommended: Has Editor role by default
    * User managed - User created
        * Recommended: Provides fine grained access control
    * Google-managed service accounts - Created and managed by Google
        * Used by GCP to perform operations on user's behalf
        * In general, we do not need to worry about them

___Use Cases___

VM <-> Cloud Storage

1. Create a Service Account role with the right permissions
2. Assing a service account role to VM instance

Uses Google cloud-managed keys:

* Key generation and use are automatically handled by IAM when we assign a service account to the instance
* Automatically rotated
* No need to store credentials in config files
* Do not delete service accounts used by running instances:
    * Applications running in those instances will lose access

On prem <-> Cloud Storage

* Cannot assing Service Account directly to an On Prem Acc
1. Create a Service account with right permissions
2. Create a Service  Account user managed key
    * gcloud iam service-accounts keys create
    * Download the service account key file
        * Keep it secure (it can be used to impersonate service account)!
3. Make the service account key file accessible to the application
    * Set the environment variable GOOGLE_APPLICATION_CREDENTIALS
        * export GOOGLE_APPLICATION_CREDENTIALS="/PATH_TO_KEY_FILE"
4. Use google cloud client libraries
    * Google Cloud client libraries use a library - Application Default Credentials (ADC)
        * ADC uses the service account key file if env var GOOGLE_APPLICATION_CREDENTIALS exists

On prem <-> Google Cloud APIs

* Make calls from outside GCP to Google cloud APIs with short lived permissions
    * Few hours or shorter
    * Less risk compared to sharing service account keys
* Credential Types:
    * OAuth 2.0 access tokens
    * OpenID connect ID tokens
    * Self-signed JSON Web Tokens(JWTs)
* Examples
    *  When a memeber needs elevated permissions, he can assume the service account role (Create OAuth 2.0 access token for service account)
    * OpenID connect ID tokens is recommended for service to service authentications
        * A service in GCP needs to authenticate itself to a service in other cloud

### ACL (Access control lists)

* ACL: Define who has access to your buckets and objects, as well what level of access they have
* How is this different from IAM
    * IAM Permissions apply to all objects within a bucket
    * ACLs can be used to customize specific accesses to different objects
* User gets access if he is allowed by either IAM or ACL
* Use IAM for common permissions to all objects in a bucket
* User ACLs if you need to customize access to individual objects

* Two types of access controls:
    * Uniform (recommended) - Uniform bucket level access using IAM
    * Fine-grained - use IAM and ACLs to control access
        * Both bucket level and individual object level permissions
* Use uniform access when all users have same level of access across all objects in a bucket
* Fine grained access with ACLs can be used when you need to customize the access at an object level
    * Give a user specific access to edit specific objects in a bucket

### Cloud Storage - Signed URL

If we want to allow a user limited time access to the objects we use Signed URLs

* Users do not need Google accounts
* Use Signed URL functionality
    * A URL that gives permissions for limited time duration to perfomr specific actions
* To create a Signed URL:
    * Create a key (KEY) for the service account/user with the desired permissions
    * Create Signed URL with the key:
        * gsutil signurl -d 10m "key":gs//BUCKET_NAME/OBJECT_PATH

### Cloud Storage - Static website

1. Create a bucket with the same name as website name (Name of bucket should match DNS name of the website)
    * Verify that the domain is owned by us
2. Copy the files to the bucket
    * Add index and error html files for better user experience
3. Add memeber allUsers and grant Storage Object Viewer option
    * Select Allow Public Access

# ~~~~ GCP Databases ~~~~

Basic concepts of Databases

* Measuring how quickly we can recover from failure
    * RPO (Recovery point objective): Maximum acceptable period of data loss
    * RTO (Recovery time objective): Maximum acceptable downtime
* Achieving minimum RTO and RPO is expensive

* Databases performance is impacted:
    * Vertically scale the database - increase CPU and memory
    * Create a database cluster (Distribute the database) - Typically database clusters are expensive to setup
    * Create read replicas - run read only applications against read replicas
        * Reduces the load on master databases
        * Can create read replicas in multiple regions
        * Take snapshots from read replicas

___Consistency___

* Strong consistency - Synchronous replication to all replicas
    * Will be slow if you have multiple replicas and standbys
* Eventual consistency - Asynchronous replication. A little lag - few seconds - before the change is available in all replicas
    * In the intermediate period, different replicas might return different values
    * Used when scalability is more important that data integrity
    * Examples: Social Media Posts - Facebook status messages, Twitter tweets, Linked in posts
* Read-after-Write consistency - Inserts are immediately available
    * However, updates would have enventual consistency

___Cloud Firestore (Datastore) vs Cloud BigTable___

* Cloud Datastore - Managed serverless NoSQL document database
    * Provides ACID transactions, SQL-like queries, indexes
        * Designed for transactional mobile and web applications
    * Firestore (next version of datastore) adds:
        * Strong consistency
        * Mobile and Web client libraries
    * Recommended for small to medium databases
* Cloud BigTable - Managed, Scalable NoSQL wide column database
    * Not serverless (need to create instances)
    * Recommended for data size > 10 Terabytes to several petabytes
    * Recommended for large analytical and operational workloads
        * NOT recommended for transactional workloads (Does not support multi row transactions, supports ONLY single row transactions)

### In memory databases

* Retrieveing data from memory is much faster than retrieving data from disk
* In memory databases like redis deliver microsecond latency by storing persistent data in memory
* Recommended GCP managed service
    * Memory store
* Use cases: Caching, session management, gaming leader boards, geospatial applications

### Cloud SQL

* Fully managed Relational Database service
    * Configure needs and do not worry about managing the database
    * Supports MySQL, PostgreSQL, and SQL server
    * Regional Service providing High Availability (99.95%)
    * Use SSDs or HDDs (For best performance: use SSDs)
    * Upto 416 GB of RAM and 30 TB of data storage
* Use Cloud SQL for simple relational use cases
    * To migrate local MySQL, PostgreSQL, and SQL server databases
    * To reduce maintenance cost for a simple relational database
    * Use Cloud Spanner (Expensive $$$) instead of Cloud SQL if:
        * Huge volumes of data is involved (TBs)
        * Need infinite scaling for growing applications (TBs)
        * Need global distributed database across multiple regions
        * Higher Availability (99.999%)

___Important Cloud SQL features___

* Automatic encryption (tables/backups), maintenance and updates
* High availability and failover:
    * Create a standby with automatic failover
    * Pre requisites: Automated backups and Binary logging
* Read replicas for read workloads:
    * Options: Cross-zone, Cross-region and External (NON Cloud SQL DB)
    * Pre requisites: Automated backups and Binary logging
* Automatic storage increate without downtime - for newer versions
* Point in time recovery: Enable binary logging
* Backups (Automated and On-demand backups)
* Supports migration from other sources
    * Use database migration service (DMS)
* Can export data from UI (console) or gcloud with formats
    * SQL (Recommended if import data into other Databases) and CSV

___Cloud SQL High Availability___

* Create a High availability (HA) Configuration
    * Choose primary and secondary zones within a region
    * Two instances: Primary and Secondary instances
* Changes from primary are replicated synchronously to secondary
* In case of Zonal failure, automatic failover to secondary instance
    * If primary zone becomes availiable, failover does not revert automatically
* High Availability setup CANNOT be used as a Read Replica

___Import and Export___

* From console/gloud/REST API
    * SQL and CSV formats
* Large databases, use serverless mode
    * Reduces performance impact of export on the live database

### Cloud Spanner

* Fully managed, mission critical, relational (SQL), globally distributed database with VERY high Availability (99.999%)
    * Strong transactional consistency at global scale
    * Scales to PGs of data with automatic sharding
* Cloud Spanner scales horizontally for reads and writes
    * Configure no of nodes
    * In comparison, Cloud SQL provides read replicas:
        * BUT cannot horizontally scale write operations with Cloud SQL
* Regional and Multi-regional configurations
* Expensive compared to Cloud SQL - Pay for nodes and storage
* Data Export: Use Cloud console to export data
    * Other option is to use flow to automate export
    * No gcloud export option
* To import and export use Cloud Data Flow
* The recommended maximum CPU utilization for single-region Spanner instances is 65%

### Cloud Datastore and Firestore

* Datastore - Highly scalable NoSQL Document Database:
    * Automatically scales and partitions data as it grows
    * Recommended for upto a few TBs of data
        * For bigger volumes, BigTable is recommended
    * Supports Transactions, Indexes and SQL like queries (GQL)
        * Does NOT support joins or Aggregate (sum or count) operations
    * For use cases needing flexible schema with transactions
    * Structure: Kind - Entity (Use namespaces to group entities)
    * Can export data ONLY from gcloud (NOT from cloud console)
        * Export contains a metadata file and a folder with the data
* Firestore = Datastore++: Optimized for multi device access
    * Offline mode and data synchronization across multiple devices - mobile, IOT etc
    * Provides client side libraries - Web, iOS, Android and more
    * Offers Datastore and Native modes
* To import and Export to/from Cloud Storage
    * From console/gcloud/REST API

### Cloud BigTable

* Petabyte scale, wide column NoSQL DB (HBase API compatible)
* Designed for huge volumes of analytical and operational data
    * IOT Streams, Analytics, Time series Data etc
* Handle millions of read/write TPS at very low latency
* Single row transactions (multi-row transactions not supported)
* NOT Serverless - need to create a server instance (SSD or HDD)
    * Scale horizontally with multiple nodes (No downtime for cluster resizing)
* Cannot export data using cloud console or gcloud
    * Either use a Java application
    * Use HBase commands
* Use cbt command line tool to work with BigTable (NOT gcloud)
    * cbt createtable my-table
* Can configure the project with cbt in .cbtrc file
* To import and Export to/from Cloud Storage
    * Create Dataflow Jobs
    * Formats: AVRO,PARQUET,SequenceFiles
* Ensure that service accounts have access to Cloud Storage Buckets
    * ACL
    * Roles Storage Admin or Storage Object Admin or Storage Object creator

### Cloud MemoryStore

* In-memory datastore service: Reduce access times
* Fully managed
    * Highly available with 99.9% availability SLA
    * Monitoring can be easily setup using Cloud Monitoring
* Support for Redis and Memcached
    * Use memcached for caching
        * Reference data, database query caching, session store etc
    * Use Redis for low latency access with persistence and high availability
        * Gaming Leader boards, Player Profiles, in memory stream processing, etc

### BigQuery - Datawarehouse

* Exabyte scale modern Datawarehousing solution from GCP
    * Relational database (SQL, schema, consistency, etc)
        * Use SQL-Like commands to query massive datasets
    * Traditional (Storage + Compute) + modern (Realtime + serverless)
* Importing and exporting data and formats becomes very important
    * Load data from a variety of sources, incl. streaming data
        * Variety of import formats
    * Export to Cloud Storage (long term storage) & Data studio (visualiztion)
        * Multiple formats - CSV,JSON, AVRO
* Automatically expire data
* Query external datasources without storing data in BigQuery
    * Cloud Storage, Cloud SQL, BigTable, Google Drive
    * Use permanent and temporary external tables
* Access big query
    * Cloud console
    * bq command-line tool (NOT gcloud)
    * BigQuery REST API OR
    * HBase API based libraries
* BigQuery queries can be expensive if run against large datasets
* Always estimate BigQuery queries before running
    * Use UI/bg - Get scanned data volume estimate
    * Use pricing calculator: Find price for scanning 1 MB data. Calculate cost

___Import and Export___

* From console/bq
    * Formats - CSV,JSON,AVRO
* Variety of options to import data:    
    * Load data from Cloud Storage
    * Batch Loading with BigQuery Data Transfer Service
    * Use Dataflow to setup streaming pipeline

___Remember___

* BigQuery, Datastore, Firebase does NOT need VM configuration (Serverless)
* Cloud SQL and BigTable need VM configuration
* Relational Databases
    * Small local databases - Cloud SQL
    * Highly scalable global databases - Cloud Spanner
    * Datawarehouse - Bigquery
* NoSQL Databases
    * Transactional database for a few Terabytes of data - Cloud Datastore
    * Huge volumes of IOT or streaming analytics data - Cloud BigTable

# ~~~~ GCP Pub/Sub ~~~~

___Sync communication___

* Application on the webserver make synchronous calls to the logging service
* What if it goes down?
    * Will applications go down?

___Async communication___

* Create a topic and have applications put log messages on the topic
* Logging service picks them up for processing when ready
* Advantages:
    * Decoupling: Publisher (apps) don't care about whos listening
    * Availability: Publisher(apps) up even if a subscriber is down
    * Scalability: Scale consumer instances under high load
    * Durability: Message is not lost even if subscriber is down

### Pub/Sub

Reliable, scalable, fully-managed asynchronous messaging service

* Backbone for highly available and Highly scalable solutions
    * Auto scale to process billions of messages per day
    * Low cost (pay for use)
* Usecases: Event ingestion and delivery for streaming analytics pipelines
* Supports push and pull message deliveries (Like combination of AWS SNS + SQS)

* Publisher - sender of a message
    * Publisher send messages by making HTTPS requests to pubsub.googleapis.com
* Subscriber - Receiver of the message
    * Pull - Subscriber pulls messages when ready
        * Subscriber makes HTTPS requests to pubsub.googleapis.com
    * Push - Mesasges are sent to subrscribers
        * Subscribers provide a web hook endpoint at the time of registration
        * When a message is received on the topic, A HTTPS POST request is sent to the web hook
        endpoints
* Very flexible publishers and subscribers relationships
    * Many to Many, One to One, Many to One, One to Many

___Message processing___

1. Topic is created
2. Subscriptions are created
    * Subscribers register to the topic
    * Each subscription represents discrete pull of messages from a topic
    * Multiple clients pull same subscription => messages split between clients
    * Multiple clients create a subscription each => each client will get every message
3. Publisher sends a message to the Topic
4. Message individually delivered to each and every subscription
    * Subscribers can receive messages either by
        * Push: Pub/sub sends the message to subscriber
        * Pull: Subscribers poll for messages
5. Subscribers send acknowledgements
6. Messages are removed from subscriptions message queue
    * Pub/Sub ensures the message is retained per subscription until it is acknowledged


# ~~~~ GCP VPC ~~~~

 * In a corporate network or an on-premises data center:
    * Can anyone on the internet see the data exchange between the application and the database?
        * No
    * Can anyone from the internet directly connect to the database?
        * Typically no
        * Need to connect to the corporate network and then access applications or databases
* Corporate network provides a secure internal network protecting resources, data and communication from external users
* How to create own private network in the cloud?
    * Enter VPC

* VPC is our own isolated network in GCP Cloud
    * Network traffic within a VPC is isolated from all other cloud VPCs
* Can control all the traffic coming in and going outside a VPC
* Best Practice - Create all GCP resources within a VPC
    * Secure resources from unauthorized access
    * Enable secure communication between cloud resources
* VPC is a global resource and contains subnets in one or more regions
    * VCP is NOT tied to a region or a zone. VPC can be in any region or zone

* By default, every project has a default VPC
* Can create our own VPCs:
    * OPTION 1: Auto mode VPC network
        * Subnets are automatically created in each region
        * Default VPC created automatically in the project uses auto mode
    * OPTION 2: Custom mode VPC network
        * No subnets are automatically created
        * Have complete control over subnets and their IP ranges
        * Recommended for production

___Shared VPC___

In an organization that has multiple proyects we want the resources from both projects to talk to each other

* Shared VPC
    * Created at organization or shared folder level (Access needed: Shared VPC Admin)
    * Allow VPC network to be shared between projects in same organization
    * Shared VPC contains one host project and multiple service projects
        * Host project - Contains shared VPC network
        * Service Projects - Attached to host projects
* Helps to achieve separation of concerns
    * Network administrators responsible for Host projects and Resource users use Service project

___VPC Peering___

Connect VPC networks accross different organizations

* Enter VPC peering
    * Networks in same project, different projects and across projects in different organizations can be peered
    * All communication happens using internal IP addresses
        * Highly efficient because all communication happens inside google network
        * Highly secure because not accessible from internet
        * No data transfer charges for data transfer between services
    * Network administration is NOT changed
        * Admin of one VPC do not get the role automatically in a peered network


___Cloud VPN___

* Connect on-premise network to the GCP network
    * Supports private IPs routing
    * Implemented using IPSec VPN tunnel
    * Traffic through internet (public)
    * Traffic encrypted using Internet Key Exchange protocol
* Two types of Cloud VPN solutions
    * HA VPN (SLA of 99.99% service availability with two external IP addresses)
        * Only dynamic routing (BGP) supported
    * Classic VPN (SLA of 99.9% service availability, a single external IP address)
        * Supports static routing (policy-based, route-based) and dynamic routing using BGP

___Cloud Interconnect___

* High speed physical connection between on-premise and VPC networks:
    * Highly available and high throughput
    * Two types of connections possible
        * Dedicated interconnect - 10 Gbps or 100 Gpbs configurations
        * Partner interconnect - 50 Mbps to 10Gbps configurations
* Data exchange happens through private network
    * Communicate using VPC networks internal IP addresses from on-premise network
    * Reduces egress costs
        * As public internet is NOT used
* Supported Google APIs and services can be privately accessed from on-premise
* Use only for high bandwidth needs
    * For low bandwidth, Cloud VPN is recommended

### Subnets

* Different types of resources are created on cloud - databases, compute, etc
    * Each type of resource has its own access needs
    * Load balancers are accessible from internet (public resources)
    * Databases or VM instances should NOT be accessible from the internet
        * ONLY application within the network VPC should be able to access them (private resources)
* How do we separate public resources from private resources inside a VPC?
    * Create separate subnets
* We can also create multiple subnets to distribute resources accross regions for high availability
* Create different subnets fo public and private resources
    * Resources in a public subnet CAN be accessed from internet
    * Resources in a private subnet CANNOT be accesses from internet
    * BUT resources in public subnet can talk to resources in private subnet because they are in the same VPC
* Each subnet is create in a region
* Example: VPC - demo-vpc => Subnets - region, us-central1, europe-west1 or us-west1
* While creating a subnet
    * Enable Private Google Access - Allows VMs to connect to Google APIs using private IPs
    * Enable FlowLogs - To Troubleshoot any VPC related network issues

### CIDR (Classless Inter-Domain Routing) blocks

* Resources in a network use continous IP addresses to make routing easy:
    * Example: Resources inside a specific network can use IP addresses from 62.208.0.0 to 62.208.0.15
* How do we express a range of addresses that resources in a network can have?
    * CIDR Block
* A CIDR block consists of a starting IP address (69.208.0.0) and a range (/28)
    * Example: CIDR block 69.208.0.0/28 represents addresses from 69.208.0.0 to 69.208.0.15 - a total of 16 addresses
* Quick Tip: 69.208.0.0/28 indicates that the first 28 bits out of 32 are fixed
    * Last 4 bits can change => 2 to the power 4 = 16 addresses
* Examples:
    * 69.208.0.0/26
        * 2 to the power of 6 => 64 addresses
    * 69.208.0.0/30
        * 2 to the power of 2 => 4 addresses
    * Difference betweeon 0.0.0.0/0 and 0.0.0.0/32
        * 0.0.0.0/0 represents all IP Addresses (256) 0.0.0.0/32 represents just one address

### Firewall rules

* Configure firewall rules to control traffic going in our out the VPC network
    * Stateful
    * Each firewall rule has priority (0-65535) assigned to it
    * 0 has the highest priority and 65535 has least priority
    * Default implied rule with lowest priority 65535
        * Allow all egress
        * Deny all ingress
        * Default rules can't be deleted
        * You can override default rules by defining new rules with priority 0-65534
    * Default VPC has 4 additional rules with priority 65534
        * Allow incoming traffic from VM instances in the same network
        * Allow incoming TCP traffic on port 22
        * Allow incoming TCP traffic on port 3389
        * Allow incoming ICMP from any source on the network
* Ingress rules - Incoming traffic from outside to GCP targets
    * Target (defines the destination): All instances or instances with TAG/SA
    * Source (defines where the traffic is coming from): CIDR instances with TAG/SA
* Egress rules - Outgoing traffic to destination from GCP targets
    * Target (defines the source) - All instances or instances with TAG/SA
    * Destination: CIDR Block
* Along with each rule, we can also define
    * Priority - Lower the number, higher the priority
    * Action on match - Allow or deny traffic
    * Protocol - ex TCP, UDP or ICMP
    * Port - Port
    * Enforcement status- Enable or Disable the rule

# ~~~~ GCP Cloud Operations ~~~~

### Cloud Monitoring

* To operate cloud applications effectively, you should know:
    * Is my application healthy?
    * Are the users experiencing any issues?
    * Does my database has enough space?
    * Are my servers running in an optimum capacity?

* Cloud Monitoring - Tools to monitor your infrastructure
    * Measures key aspects (Metrics)
    * Create visualizations (Graphs and Dashboard)
    * Configure Alerts (when metrics are NOT healthy)
        * Define Alerting policies:
            * Condition
            * Notifications - Multiple channels
            * Documentation

* We can use cloud Monitoring to monitor one or more GCP projects and one or more AWS accounts
* We can group all the information from multiple GCP projects or AWS accounts via Workspaces
* Workspaces are needed to organize monitoring information
    * A workspace allows to see monitoring information from multiple projects
        1. Create a worskapce in a specific project (Host project)
        2. Add other GCP projects (or AWS accounts) to the workspace

* Default metrics monitored include:
    * CPU utilization
    * Disk traffic metrics
    * Network traffic
    * Uptime information
    * Install Cloud Monitoring agent on the VM to get more disk, CPU, network and process metrics
        * Collectd-based daemon
        * Gathers metrics from VM and sends them to Cloud Monitoring


### Cloud Logging

* Real time log management and analysis tool
* Allows to store, search, analyze and alert on massive volume of data
* Exabyte scale, fully managed service
    * No server provisioning, patching etc
* Ingest log data from any source
* Key features:
    * Logs explorer - Search, sort & analyze using flexible queries
    * Logs Dashboard - Rich visualization
    * Logs Metrics - Capture metrics from logs (using queries/matching strings)
    * Logs router - Route different log entries to different destinations

* Most GCP managed services automatically send logs to Cloud Logging:
    * GKE
    * App Engine
    * Cloud Run
* Ingest logs from GCE VMs
    * Install Logging agent (based on fluentd)
    * Recommended - Run Logging Agent on all VM instances
* Ingest logs from on-premises
    * Recommended - Use the bingplane tool from blue medora
    * Use the Cloud Logging API

___Audit and Security logs___

* Access Transparency log: Captures actions performed by GCP team on your content (NOT supported by all services)
    * ONLY for organizations with Gold support level and above
* Cloud Audit logs: Answers who did what, when and where
    * Admin activity logs
    * Data access logs
    * System Event Audit logs
    * Policy Denied Audit logs


Feature | Admin activity logs | Data access logs | System Event Audit logs | Policy Denied Audit logs
|-------| ------------------- | ---------------- | ----------------------- | ------------------------|
| Logs for | API calls that modifty the configuration of resources | Reading configuration of resources | Google Cloud Administrative actions | When user or service account is denied access 
| Default enabled | Yes | No | Yes | Yes
| VM examples| VM creation, Patching, Change IAM permissions | Listing resources | On host maintenance instance preemption | Security policy violation logs
| Cloud storage | Modify bucket or object | Modify/read bucket or object | |
| Access Needed | Logging/logs viewer or project/viewer | Logging/Private logs viewer or project owner | Logging logs viewer or project viewer | Logging/logs viewer or Project vi

___Controlling & Routing___

* How do you manage your logs?
    * Logs from various sources reaches Log Router
    * Log Router checks against configured rules
        * What to ingest? What to discard?
        * Where to route?
* Two types of logs buckets:
    * _Required: Holds Admin Activity, System Events & Access Transparency logs (Retained for 400 days)
        * Zero charge
        * Cannot delete the bucket
        * Cannot change retention period
    * _Default: All other logs retained for 30 days
        * Billed based on Cloud Logging pricing
        * Cannot delete the bucket
            * can disable the _Default log sink route to disable ingestion

___Export___

* Logs are ideally stored in Cloud Logging for limited period
    * For long term retention (Compliance, Audit) logs can be exported to
        * Cloud storage bucket
        * Big query dataset
        * Cloud pub/sub topic (base64 encoding log entries)
* How to export logs?
    * Create sinks to these destinations using Log Router
        * Can create include or exclude filters to limit the logs

### Cloud Trace

* Distributed tracing system for GCP: Collect latency data from
    * Supported Google Cloud services
    * Instrumented applicatons (using tracing librearies) using Cloud Trace API
* Find out
    * How long does a services take to handle requests?
    * What is the average latency of requests?
    * Howare we doing overtime?
* Supported for
    * Compute Engine, GKE, App engine
* Trace client libraries available for
    * Go, Java, Node.js, PHP, Python & Ruby

### Cloud Profiler

* Identify bottlenecks in production
* Cloud profiler - Statistical, low-overhead profiler
* Continously gathers CPU and Memory usage from production systems
* Connect profiling data with application source code
* Two major components
    * Profiling agent
    * Profiler interface

### Error reporting

How to identify production problems in real-time

* Real time exception monitoring
    * Aggregates and displays errors reported from cloud services (using stack traces)
    * Centralized error management console
        * Identify & manage top errors or recent errors
    * Use Firebase crash reporting for errors from androir & iOS client applications
    * Supported for Go, Java, Node, PHP, Python and Ruby
* Errors can be reported by
    * Sending them to Cloud Logging OR
    * By calling Error Reporting API
* Error reporting can be accessed from desktop
    * Also available in the Cloud console mobile app for iOS and Android

# ~~~~ GCP Organizations and IAM ~~~~

Resource Hierarchy in GCP

* Well defined hierarchy
    * Organization -> Folder -> Project -> Resources
* Resourcea are created in projects
* A folder can contain multiple projects
* Organization can contain multiple projects

___Recommendation for Enterprises___

* Create separate projects for different environments
    * Complete isolation between test and production environments
* Create separate folders for each department
    * Isolate production applications of one department from another
    * We can create a shared folder for shared resources
* One project per application per environment
    * Let's consider two apps: A1 and A2
    * Let's assume we need two environements: DEV and PROD
    * Create Four projects, A1-DEV, A1-PROD, A2-DEV, A2-PROD:
        * Two Folders: A1, A2
        * Isolets environments from each other
        * DEV changes will NOT break PROD
        * Grant all developers complete access (create, delete, deploy) to DEV projects
        * Provide production access to operations team only

### Billing accounts

* Billing account is mandatory for creating resources in a project
    * Billing Account contains the payment details
    * Every project with active resources should be associates with a Billing Account
* Billing account can be associated with one or more projects
* Can have multiple billing accounts in the organization
* Create billing acccounts representing the organization structure
    * Startups can have one billing account
    * Large enterprises can have a separate billing account for each department
* Two types
    * Self serve: Billed directly to Credit Card or Bank Account
    * Invoiced: Generate Invoices (Used by large enterprises)
* Setup a Cloud billing budget to avoid surprises
    * Configure alerts
    * Default alert thresholds set at 50%, 90% and 100%
        * Send alerts to Pub Sub (Optional)
        * Billing admins and Billing Account users are alerted by e-mail
* Billing Data can be exported (on a schedule)
    * Big query (if we want to query or visualize)
    * Cloud storage (for history/archiving)

### IAM Best practices

* Principle of least privilege - Give least possible privilege needed for a role
    * Basic Roles are NOT recommended
        * Prefer predefined roles when possible
    * Use Service accounts with minimum privileges
        * Use different service accounts for different apps/purposes
* Separation of duties - Involve atleast 2 people in sensitive tasks
    * Example: Have separate deployer and traffic migrator roles
        * AppEngine provides AppEngine deployer and App Engine Service Admin roles
            * Deployed can deploy new version but cannot shift traffic
            * Service Admin can shift traffic but cannot deploy new version
* Constant monitoring: Review Cloud Audit Logs to audit changes to IAM policies and access to Service account keys
    * Archive Cloud Audit logs in Cloud Storage buckets for long term retention
* Use groups when possible
    * Makes it easy to manage users and permissions

### User Identity management

* Email used to create free trial account - "Super Admin"
    * Access to everything in the GCP organizaiton, folders and projects
    * Manage access to other users using their Gmail accounts
* Not recommended for enterprises
* Option 1: Enterprise is using Google Workspace
    * Use Google workspace to manage users (Groups, etc)
    * Link Google Cloud Organization with Google Workspace
* Option 2: Enterprise uses an Identity provider of its own
    * Federate Google Cloud with Identity Provider

___Corporate Directory Federation___

* Federate Cloud Identity or Google Workspace with External Identity Provider (idP) such as Active Directory or Azure Active Directory
* Enable Single Sign On
    1. Users are redirected to an external IdP to authenticate
    2. When users are authenticated, SAML assertion is sent to Google Sign-In
* Examples
    * Federate Active Directory with Cloud Identity by using Google Cloud Directory Sync (GCDS) and Active Directory Federation Services (AD FS)
    * Federating Azure AD with cloud Identity

### IAM Members/Identities

* Google Account - Represents a person (email address)
* Service account - Represents and application account (Not person)
* Google group - Collection - Google & Service Accounts
    * Has unique email address
    * Helps to apply access policy to a group
* Google Workspace domain: Google Workspace (formely G suite) provides collaboration services for enterprises
    * Tools like Gmail, Calendar, Meet, Chat, Drive, Docs etc are included
    * If enterprise is using Google Workspace, we can manage permissions using Google Workspace Domain
* Cloud Identity domain - Cloud Identity is an Identity as a Service (IDaaS) solution that centrally manages users and groups
    * We can use IAM to manage access to resources for each Cloud Identity account

### Organization policy service

Enable centralized contrainsts on all resources created in an Organization

* Configure Organization Policy
    * Example: Disable creation of Service Accounts
    * Example: Allow/Deny creation of resources in specific regions
* Needs Organization Policy Administrator Role
* IAM focuses on Who
    * Who can take specific actions on resources?
* Organization policy focuses on What
    * What can be done on specific resources?

### Resource Hierarchy & IAM Policy

* IAM Policy can be set at any level of hierarchy
* Resources inherit the policies of all parents
* The effective policy for a resource is the union of the policy on that resource and its parents
* Policy inheritance is transitive
    * Organization policies are applied at resource level
* Cannot restrict policy at a lower level if permission is given at an higher level
* Organization -- Folder -- Project -- Resource

### Exploring Roles 

___Organization, Billing and Project roles___

* Organization administrator
    * Define resource Hierarchy
    * Define Access management policies
    * Manage other users and roles
* Billing Account creator - Create Billing Accounts
* Billing account administrator - Manage Billing Accounts (payment instruments, billing exports, link and unlink projects, manage roles on billing account)
    * Cannot create billing account
* Billing Account user - Associate projects with billing accounts
    * Typically used in combination with Project creator
    * These two roles allow user to create new project and link with billing account
* Billing Account viewer - See All billing account details

___Compute Engine Roles___

* Compute Engine Admin - Complete control of compute - instances, images, Load balancers, Network, etc
* Compute Instance Admin - Create, modify and delete virtual machine instances and disks
* Compute Engine Network Admin - Complete access to networking resources and READ ONLY access to firewall rules and SSL certificates
* Compute Engine Security Admin - Complete access to firewall rules and SSL certificates
* Compute Engine Storage Admin - Complete access to disks, images, snapshots
* Compute Engine Viewer - Read ONLY access to everything in compute
* Compute OS Admin Login - Log in to a Compute Engine Instance as an administrator user
* Compute OS Login - Log in to a Compute Engine Instance as a standard user

___App Engine Roles___

C - Create
R - Read
U - Update
D - Delete

* App Engine Creator - application(C,D) Responsible for creation/deletion an application
* App Engine Admin - applications(R,U) services/instances/versions(CRUD), operations
* App Engine Viewer - applications/services/instances/versions R, operations
* App Engine Code Viewer - appengine.versions.getFileContents (Only role that can view code)
* App Engine Deployer -  versions(CRD), applications/services/versions(R)
    * Cannot change traffic between versions
    * Can deploy app versions (With Service Account User role)
* App Engine Service Admin - versions(RUD), applications(R), services/instances(CRUD)
    * Can change traffic between versions
    * Cannot deploy app versions

* App Engine Roles Do not allow to
    * View and Download application logs
    * View Monitoring charts in the Cloud Console
    * Enable and Disable billing
    * Access configuration or data stored in other services

___GKE IAM Roles___

* Kubernetes Engine Admin - Complete access to Clusters and Kubernetes API Objects
* Kubernetes Engine Cluster Admin - Provides access to management of clusters, Cannot access Kubernetes API Objects
* Kubernetes Engine Developer - Manage Kubernetes API objects, READ cluster info
* Kubernetes Engine Viewer - get/list cluster and kubernetes api objects

___Cloud Storage Roles___

* Storage Admin - Complete access to buckets and objects
* Storage Object Admin - Complet access to Objects only
* Storage Object Creator - Can create objects
* Storage Object Viewer - Can get and list objects
    * Container Registry stores container images in Cloud Storage buckets
        * Controll access to images in Container Registry using Cloud Storage permissions

___Big Query roles___

* BigQuery Admin - Complete access to big query
* BigQuery Data Owner - Access to datasets, models, routines, tables, no access to jobs (Where you run the queries)
* BigQuery Data Editor - Fewer access to datasets,models, routines, tables, no access to jobs
* BigQuery Data Viewer - get/list to datasets, models, routines, tables
* BigQuery Job user - Can create jobs, run queries
* BigQuery User - BigQuery Data viewer + get/list jobs, can also run some queries and create datasets

* To see data we need BigQuery User or BigQuery Data Viewer
    * Cannot see data with BigQuery Job User role


___Logging IAM Roles and Service Account Roles___

* Logs viewer - Read all logs except Access Transparency logs and Data access audit logs
* Private Log viewer - Logs viewer + Read Access Transparency logs
* Loggin Admin - All permissions related to logging
* Security Admin - Get and set any IAM policy
* Security Viewer - List all resources & IAM policies
* Organization Role Admin - Admin all custom roles in the organization and projects below it
* Organization Role Viewer - Read all custom roles in the organization and the projects below it
* Role Admin - Provides access to all custom roles in the project
* Role Viewer - Provides read access to all custom roles in the project
* Browser - Read access to browse the hierarchy for a project, including the folder, organization and IAM policy

* Service accounts
    * Service Account Admin - Create and manage Service Accounts
    * Service Account User - Run operations as the service Account
    * Service Account Token Creator - Impersonate service accounts (Create OAuth2 access tokens, sign blobs, JWTs, etc)
    * Service Account Key Admin - Create and manage (and rotate) service account keys


# ~~~~ GCP Price calculator ~~~~

* Estimating the cost of a Google Cloud solution is NOT easy
* We would need to take a number of factors into account
* How do we estimate the cost of the GCP solution?
    * Use Google Cloud Pricing Calculator
* Estimates for 40+ Services
    * Compute Engine
    * Google Kubernetes Engine
    * Cloud Run
    * App Engine
    * Cloud Storage
    * etc
* These are estimates

# ~~~~ GCP Important Services ~~~~

### Deployment Manager / Cloud Foundation Toolkit

Centralized place where we can deploy multiple services at once with a configuration file, YAML file

* Can deploy the same configuration in multiple environments
* Automate deployment and modification of Google Cloud resources in a controlled, predictable way
* Avoid configuration drift
* Avoid mistakes with manual configuration
* Like a version control of environments
* Always modify the resources created by Deployment Manager using Deployment manager

* All configuration is defined in a YAML file
* Deployment manager understands dependencies
    * Creates VPCs first, then subnets and then the database
* Automatic rollbacks on errors
    * If creation of database fails, it would automatically delete the subnet and VPC
* Version control of configuration file and make changes to it over time
* Free to use - Pay only for the resources provisioned
    * Get an automated estimate for the configuration

___Terminology___

* Configuration file - YAML file with resource definitions for a single deployment
* Templates - Reusable resource definitions that can be used in multiple configuration files
    * Can be defined using
        * Python (preferred)
        * JinJa2
    * Templates are stored in Cloud Source Repositories as a best practice
        * Designed for hosting and versioning source code
* Deployment - Collection of resources that are deployed and managed together
* Manifests - Read-only object containing original deployment configuration (included imported templates)
    * Generated by Deployment Manager
    * Includes fully-expanded resource list
    * Helpful for troubleshooting

### Cloud Marketplace

Central repository of easily deployable apps and datasets

* Similar to app store/play store for mobile applications
* Can search and install a complete stack
    * Commercial solutions - SAP HANA
    * Open source packages - LAMP, Wordpress, Cassandra, Jenkins, etc
    * OS Licenses - BYOL, Free, Paid
    * Categories - Datasets/Developer tools/OS etc
* When selecting a solution we can see
    * Components - Software, infrastructure needed, etc
    * Approximate price

### Cloud DNS

To set up a website with a specific domain (www.mysuperwebsite.com)

1. Buy the domain name
2. Setup the webiste content (Website hosting)
3. Route requests to the website host server (DNS)

* Cloud DNS = Global Domain Name System (Step 3)
    * Setup DNS routing for the website
        * Route www.mysuperwebsite.com to the IP address of api server
        * Route static www.mysuperwebsite.com to the address of the http server
        * Route email to the mailserver
    * Public and Private managed DNS zones (container for records)
        * To create records, we need managed Zones

### Cloud Dataflow

Streaming realtime batch processing service

* Example pipelines
    * Pub/sub -> Dataflow -> BigQuery(Streaming)
    * Pub/sub -> Dataflow -> Cloud storage (Streaming - files)
    * Cloud storage -> Dataflow -> BigTable/CloudSpanner/Datastore/BigQuery  (Batch load data into databases)
    * Bulk compress files in Cloud Storage (Batch)
    * Convert files formats between Avro, Parquet & CSV (batch)
* Streaming and Batch Usecases
    * Realtime Fraud Detection, Sensor Data Processing, Log Data Processing, Batch processing (Load data, convert formats)
* Use pre-built templates
* Based on Apache Beam (supports Java, Python, Go)
* Serverless (Autoscale automatically)

### Cloud Dataproc

Cloud managed services for Spark and Hadoop

* Variety of jobs are supported
    * Spark, PySpark, SparkR, Hive, SparkSQL, Pig, Hadoop
* Perform complext batch processing
* Multiple cluster Modes
    * Single node / Standard / High Availability (3 masters)
    * Use regular / preemptible VMs
* Use case: Move Hadoop and Spark clusters to the cloud
    * Perform machine learning and AI development using opensource frameworks
* Cloud dataproc is a data analysis platform
    * Can export cluster configuration but not Data
* BigQuery (Alternative) - When we run SQL queries on petabytes
    * Go for Cloud Dataproc when we need more than queries (Batch Processing, Machine Learning, AI Workloads)

### Secret Manager

Store API keys, passwords, certificates, and other sensitive data

* First-class versioning
    * Secret data is immutable and most operations take place on secret versions. With Secret Manager, you can pin a secret to specific versions like "42" or floating aliases like "latest."
    * Data is encrypted in transit with TLS and at rest with AES-256-bit encryption keys.

# ~~~~ GCP Exam Findings ~~~~

### Transfer Appliance

Is a high-capacity storage device that enables you to transfer and securely ship your data to a Google upload facility, where we upload your data to Cloud Storage

The following features are provided by Transfer Appliance

* Tamper resistant: Bad actors cannot easily open Transfer Appliance's physical case. We also apply tamper-evident tags to the shipping case, so that you can visually inspect each appliance's integrity prior to opening the package.
* Ruggedized: Transfer Appliance's shipping container is ruggedized, ensuring your data arrives safely.
* Trusted Platform Module (TPM) chip: We validate the TPM's Platform Configuration Registers to ensure that the immutable root filesystem and software components haven't been tampered with.
* Hardware attestation: We use a remote attestation process to validate the appliance before you can connect it to your device and copy data to it. If anything is amiss, we work with you to quickly send you a new appliance.
* AES 256 encryption: Your data is encrypted with industry-standard encryption to keep it safe.
* Customer-managed encryption keys: We use encryption keys that you manage using Cloud Key Management Service (Cloud KMS), enabling you to control and secure your data prior to shipping an appliance back to us.
* NIST 800-88 compliant data erasure: We securely erase your data from Transfer Appliance after uploading your data to Cloud Storage. You can request a wipe certificate to verify that we've wiped your data.


### Transfer Appliance

Transfer Appliance is the most cost-effective solution for data migration of large size. Obtaining a Transfer Appliance, copying the data to it, and shipping it to Google allows for a faster and more efficient migration. The Transfer Appliance is a physical device that can securely transfer large amounts of data offline

### Exam question information

___Connect Compute Engine VM to GKE internal in different VPC___

* Creating a LoadBalancer Service type using the application's Pods as the backend and adding the annotation "cloud.google.com/load-balancer-type: Internal" ensures that the load balancer is only accessible internally within the VPC. Peering the two VPCs together allows the Compute Engine instance in one VPC to access the application in the other VPC.

___Create GKE Resources from Deployment manager file___

* Adding the cluster's API as a new Type Provider in Deployment Manager allows you to directly use Kubernetes API resources within your Deployment Manager templates.

___When users get access to certain resources?___

*  Admin Activity logs show when users were given roles to a certain resource

___Google Kubernetes Engine (GKE). You want to keep costs under control. What is the best way to do that?___

* Configure Autopilot in GKE to monitor node utilization and eliminate idle nodes.

___How to move projects from a different org to your org. How can you accomplish this with minimal effort?___

1. Move the project to the other organization using the projects.move method.
2. Update the billing account of the project to that of your organization.

___The app will be deployed in 3 separate environments namely development, test, and production. How can you deploy and manage these environments to ensure that they are consistent?___

* Create one deployment template that will work for all environments using the Cloud Foundation Toolkit (CFT), and deploy with Terraform.

___Your team is trying to migrate a business-critical application to Google Kubernetes Engine. What steps would you recommend to optimize the cluster for reliability?___

*  Establish a GKE Autopilot cluster and choose to enroll the cluster in the stable release channel.

___Your webapp needs a custom utility to build and you need to make sure it is in the default execution path and persists across sessions. Where should you store it?___

* ~/bin
