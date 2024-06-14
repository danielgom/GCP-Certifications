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
* SSL proxy
    * Global, External, TCP with SSL offload
    * Proxy pass
    * A big list of destinations
* TCP proxy
    * Global, External, TCP without SSL offload
    * Proxy pass
    * A big list of destinations
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
| Pricing  | Instance hours    | vCPU, Memory & Persisten Disks
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
    * Stack driver logging

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
* Fully integrated end-to-end developer experience:
    * No limitations in languages, binaries and depdendencies
    * Easily portable because of container based architecture
    * Cloud code, Cloud Build, Cloud Monitoring & Cloud logging integrations
* Anthos - Run kuberentes clusters anywhere
    * Cloud, Multi Cloud and On-Premise
* Cloud Run for Anthos: Deploy workloads to Anthos clusters running on-premises or on google cloud