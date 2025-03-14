# ~~~~ GCP Data Engineering overview ~~~~

* Data pipelines
* How data flows

General Steps

Ingestion -> Storage -> Process and Analyze -> Explore and Visualize

___Ingestion___

* Gather data from multiple resources
* Data gather from app
    * Event log, Click stream data, E-commerce transaction
* Streaming ingest
    * Pub/Sub
* Batch ingest
    * Different transfer services
    * GCS - gsutil

___Store___

* Cost efficient & Durable storage

* Structured Data
    * Transactional
        * Cloud SQL
        * Cloud Spanner (Global availability)
    * Analytical
        * Big query

* Semi-structured Data
    * Fully indexed
        * Cloud datastore
    * Row key
        * Cloud Bigtable

* Unstructured Data
    * Cloud storage

___Process & Analyze___

* What kind of outcome we want
* What analysis do we want to perform
* Convert data into meaning
* Analyze data with big query
* Apply ML with
    * BigQueryML
    * SparkML with Dataproc
    * Vertex API
        * Build ML model with Auto ML/Custom Model

___Explore & Visualize___

* Google data studio - Easy to use BI engine
    * Dashboard and Visualization
* Datalab
    * Interactive jupyter notebook
    * Support for all Datascience library
* ML Pre-built API
    * Speech API
    * Vision API


### GCP Types of data

___Structured Data___

* Tabular Data
* Represented by Rows & Columns
* SQL can be used to interact with data
* Fixed Schema
* Each row has same number of columns
* Relational Database are structured
* MySQL, OracleSQL, PostreSQL, etc...
* CloudSQL, Cloud spanner

___Semi-Structured Data___

* Each record has a variable number of properties
* No fixed schema
* Flexible structure
* NoSQL kind of data
* Store data as key-value pair
* JSON - Java script object notation are base way to represent semi structure data
* MongoDB, Cassandra, Redis, Neo4j
* Cloud Bigtable, Cloud Datastore(firestore), Memorystore(Redis & Memcached)

___Unstructured Data___

* Image
    * Video data
    * Natural language is an example of unstructured data
* Google cloud storage, File store inside GCP


### GCP Batch Data vs Streaming Data

* Batch Data Processing
    * Defined start & end data - Data Size is known
    * Process High volume of data after certain periodic interval
    * Long time to process data
    * Example: Payment processing
* Streaming Data
    * Unbounded, no end defined
    * Data is processed as it arrives
    * Size is unknown
    * No much heavy processing - take millisecond to seconds to process data
    * Example: Stock data processing

### GCP Container registry

* Artifact registry is a redefined version of the GCP container registry

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
* Data is geo - redundant
    * Multi regional, Dual-region storage

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

Since objects are immutable, these are commonly versioned (Disabled by default)

* Prevents accidental deletion & provides history
    * Enabled at bucket level
        * Can be turned on/off at any time
* Live version is the latest version
    * If we delete live object, becomes noncurrent object version
    * If delete noncurrent object version, it is deleted
* Older versions are uniquely identified by (object key + a generation number)
* Reduce costs by deleteing older (noncurrent) versions
* If no version provided, we will always get the latest version of the object

___Object lifecycle management___

* Files are frequently accessed when they are created
    * Generally usage reduces with time
* Identify objects using conditions based on:
    * Age, CreatedBefore, IsLive, MatchesStorageClass, NumberOfNewerVersions, etc...
    * Set multiple conditions: all conditions must be satisfied for action to happen
* Two kinds of actions
    * SetStorageClass actions (change from one storage class to another)
        * Example: Standard to Nearline
    * Deletion actions (delete objects)

___Encryption___

* Cloud storage always encrypts data on the server side (encrypted at rest)
* Configure server-side encryption: Encryption performed by cloud storage:
    * Google-managed encryption key: Default (No configuration required)
        * Fully managed
    * Customer-managed encryption keys: Created using Cloud Key Management Service (KMS) by customer in KMS
        * Keys should be managed by the customer (rotation)
        * Cloud storage service account should have access to keys in KMS for encrypting and decrypting using the Customer-Managed encryption key
        * If key is deleted or destroyed, object can no longer be acessed.
    * Cusomer-supplied encryption-key:
        * Generated by the customer, needs to be supplied each time we want to read the object
* Client side encryption (OPTIONAL): Encryption performed by customer before upload
    * GCP does NOT know about the keys used


___Gsutil OLD___

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

___Gcloud storage CLI NEW___

* gcloud storage is the new CLI for Clouse storage (gcloud storage)
    * gcloud storage buckets list - List current and non-current object versions
    * gcloud storage create - Create a bucket
    * gcloud storage cp - Copy objects
    * gcloud storage rm - Remove one or multiple objects
    * gcloud storage mv - Rename/Move objects

___Storage Location___

Region

* Lowest latency within a single region
* Replicated data across multiple zone in single region

Dual-Region

* High availability and low latency across 2 regions (Paired region)
* Auto-failover

Multi-Region

* Highest availability across regions continent area - US, EU, Asia
* Autofailover

___Controlling Access___

* Who can do what on Google Cloud storage at what level
* Apply at bucket level
    * Uniform level access (It also uses IAM)
        * No object level permission
        * Apply uniformly at all objects inside the bucket
    * Fine grained permission (In addition to bucket level permissions IAM)
        * Access Control List - ACL for each object separately
* Apply at Project level
    * IAM (Mostly for users and Service Accounts)
    * Different  predefined role
        * Storage Admin
        * Storage Object Admin
        * Storage Object Creator
        * Storage Object Viewer
* Assing bucket level role
    * Select bucket & assign role
    * To user
    * To other GCP services or products

### ACL (Access control lists, Kind of old)

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
* Max period the URL can be valid is 7 days
* Use Signed URL functionality
    * A URL that gives permissions for limited time duration to perfomr specific actions
* To create a Signed URL:
    * Create a key (KEY) for the service account/user with the desired permissions
    * Create Signed URL with the key:
        * gsutil signurl -d 10m "key":gs//BUCKET_NAME/OBJECT_PATH

### GCP - Storage Transfer Service

* From On-Premises to Google Cloud Storage (GCS)
    * gsutil -- gcloud storage
    * Online mode of transfer -- Online
    * Transfer service for on-premises data
        * Quickly and securely move data from private data centers into Google cloud storage
            * Two step process
                1. Install agent
                2. Create transfer job
    * Transfer appliance (Google request) -- Offline
        * Physical device which securely transfer large amounts of data to Google cloud platform
        * Used when data exceeds 20TB or would take more than a week to upload

* Transfer service cloud data
    * From one bucket to another bucket inside same GCP
    * From other public cloud Amazon S3, Azure container to GCS
    * Create Transfer job
    * One time run or recurring

### GCP - Block Storage

* Hardisks attached to the computers (VMs) -- High throughput, Low latency
* Typically, ONE block storage device can be connected to ONE virtual server
    * We can attach read only block devices with multiple virtual servers and certain cloud providers are exploring multi-writer disks as well
* Can connect to multiple different block storage devices to one virtual server
* Used as:
    * Direct-attached storage DAS - Similar to a hard disk SSD
        * Physically attached to the VM
        * Very high performance
        * Costlier than persistent disks
        * Can only attach to a single VM, cannot re attach to other VM
        * If VM is destroyed, disk is destroyed
        * Cannot take snapshots
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


___Persistent Disks___

* Network block storage attached to the VM instance
* Provisioned capacity
* Supports snapshots
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

* More durable - Almost permanent storage
* Lifecycle NOT tied to VM instance
* Permanent storage
* No VM needed

### GCP - File Storage

* More expensive than cloud storage
* Fully managed, High performance filestore
* Network attached storage (NAS), for compute engine and GKE
* Supports HDD and SSD
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

___Storage, which storage to use when___

* Cloud storage (BlOB)
    * Unstructured data storage
    * Video stream, images
    * Staging environment
    * Compliance
    * Backup
    * Data lake
* Persistent Disk (Block storage)
    * Attack disk with VM and Containers
    * Share read-only disk with multiple VM
    * Database storage
* Local Disk (Block storage, High performance)
    * Temporary High performance attach disk
* Filestore (High performance)
    * Performance predictable
    * Lift-Shift millions of files

### GCP - Database Concepts

___OLTP___

* Online transaction processing
* Simple query
* Large number of small transactions
* Traditional RDBMS
* Database modification
* Databases - MySQL, PostgreSQL, Oracle, MSSQL
* ERP, CRM, Banking application
* GCP - Cloud SQL, Cloud spanner

___OLAP___

* Online analytical processing
* Data warehousing
* Data is collected from multiple sources
* Complex query
* Data analysis
* Google Cloud big query - Petabyte Data warehouse
* Reporting application, Web click analysis, BI Dashboard app

___RTO_RPO___

* RTO - Recovery time objective
    * Maximum time for which the system can be down
* RPO - Recovery point objective
    * Maximum time for which organization can tolerate dataloss

___Durability_Availability___

Durability

* If we lose data it means
    * Business is down
    * No business afford to lose data
* How healthy and resilient our data is
* Object storage provider measure durability in terms of number of 9's

Availability

* If a region goes down, where data is stored (access)
    * Replicate data across many regions
* How much of time data is up/available
* Data replicated across multiple regions, means higher availability
* SLA - service level agreement
* SLA - 99.99%

# ~~~~ GCP Database products ~~~~

### Cloud SQL

* Fully managed Relational Database service
    * Configure needs and do not worry about managing the database
    * Supports MySQL, PostgreSQL, and SQL server
    * Regional Service providing High Availability (99.95%)
    * Use SSDs or HDDs (For best performance: use SSDs)
    * Up to 416 GB of RAM and 30 TB of data storage
    * Up to 96 cores
    * Ondemand backup
    * Schedule backup
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
* No Ram, CPU limits
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
* To import and export use Cloud Dataflow
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
* In the project you can either use Datastore or Firestore not both

### Cloud MemoryStore

* In-memory datastore service, fully managed: Reduce access times
* Only internal IP address
* Fully managed
    * Highly available with 99.9% availability SLA
    * Monitoring can be easily setup using Cloud Monitoring
* Support for Redis and Memcached
    * Use memcached for caching
        * Reference data, database query caching, session store etc
    * Use Redis for low latency access with persistence and high availability
        * Gaming Leader boards, Player Profiles, in memory stream processing, etc

### Cloud BigTable

* Petabyte scale, wide column NoSQL DB (HBase API compatible)
* Column are grouped into column family (Disk performance)
* Designed for huge volumes of analytical and operational data
    * IOT Streams, Analytics, Time series Data etc
* Handle millions of read/write TPS at very low latency
* Single row transactions (multi-row transactions not supported)
* NOT Serverless - need to create a server instance (SSD or HDD)
    * Scale horizontally with multiple nodes (No downtime for cluster resizing)
    * Scale to huge volume of data
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
* Used for 
    * Financial Data
    * Time series Data
* Seamless integration with
    * Big query - Warehouse
    * Machine learning products

# ~~~~ GCP Data Processing ~~~~

### BigQuery - Datawarehouse

* Exabyte scale modern Datawarehousing solution from GCP (serverless)
    * Relational database (SQL, schema, consistency, etc)
        * Use SQL-Like commands to query massive datasets
    * Traditional (Storage + Compute) + modern (Realtime + serverless)
* Importing and exporting data and formats becomes very important
    * Load data from a variety of sources, incl. streaming data
        * Variety of import formats
    * Export to Cloud Storage (long term storage) & Data studio (visualiztion)
        * Multiple formats - CSV,JSON,AVRO
* Automatically expire data
* Not a transactional purpose database
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

* BigQuery data organization
    * Projects -- Datasets -- Tables -- Jobs
* Projects are top level containers in GCP
* Datasets hold multiple tables, projects contain 1 or more datasets
* Each table must belong to a dataset
* Assing Role at the organization, project and dataset level
* Tables contain data -- Schema
* Types of tables
    * Native tables
    * External tables
    * View
* Jobs can manage asynchronous tasks
    * Load job
    * Query job
    * Extract job
    * Copy job

___Time travel___

Time travel in BigQuery allows you to query data stored in a table at any point in the past within the past seven days. This feature is helpful in recovering from a corruption event as you can access the historical data before the corruption occurred.

___Omni Connection___


BigQuery Omni accesses Amazon S3 data through connections. Each connection has its unique Amazon Web Services (AWS) Identity and Access Management (IAM) user. You grant permissions to users through AWS IAM roles.


___Import and Export___

* From console/bq
    * Formats - CSV,JSON,AVRO
* Variety of options to import data:    
    * Load data from Cloud Storage
    * Batch Loading with BigQuery Data Transfer Service
    * Bigquery transfer service can only transfer data between clouds with internet access
    * Use Dataflow to setup streaming pipeline

___Big query data transfer service___

* Bigquery transfer service can only transfer data between clouds with internet access
* Can transfer between GCP services 
    * Cloud Storage → BigQuery
    * Google Ads → BigQuery
    * Google Analytics 360 → BigQuery
    * Search Ads 360 → BigQuery
* Cannot transfer data in real time
* Cannot extract Data directly from Cloud SQL or Spanner (Use Datastream or Dataflow instead)
* Cannot extract data from pub/sub (use dataflow)

___Remember___

* BigQuery, Datastore, Firebase does NOT need VM configuration (Serverless)
* User big query only if
    * When workload is analytical
    * When data does not change in database, as bigquery use built in cache
    * Complex queries
    * When querying takes more execution time
    * Large volumes of data
    * No joins -- Denormalized data
* Expensive querying with BigQuery, dry run before running any query
* Cloud SQL and BigTable need VM configuration
* Relational Databases
    * Small local databases - Cloud SQL
    * Highly scalable global databases - Cloud Spanner
    * Datawarehouse - Bigquery
* NoSQL Databases
    * Transactional database for a few Terabytes of data - Cloud Datastore
    * Huge volumes of IOT or streaming analytics data - Cloud BigTable

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
    * Write to Google cloud storage
        * Can create a GCS subscription and create a new object per message
    * Write to BigQuery
        * Can write to a databaser and table schema, dead lettering recommended
* Very flexible publishers and subscribers relationships
    * Many to Many, One to One, Many to One, One to Many
* Max size of a message is 10MB
* Max days pub/sub can retain messages is 31 days

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

### Pub/Sub Lite

* Partition base messaging service
* Its not global it is zonal or regional
* Lower cost, lower reliability
* No automation, manual provision required for storage & Throughput
* Regional lite has same SLA as Pub/Sub
    * Regional lite topics replicate data to a other zone asynchronously
    * Where as Pub/sub synchronously replicate data
* Zonal lite has no SLA
    * Stored in only one zone
* Need to pay for capacity provisioned
* No dead lettering
* No Exactly once delivery
* No CMEK
* No cross-project subscriptions
* No message filtering
* No message Schema validation
* No REST endpoints
* Only standard subscriptions supported
* Only Java, Python, Golang support

### Cloud Dataflow

Streaming realtime batch processing service

Advanced unified programming model to implement batch and streaming data processing jobs that run on various execution engines/runners

* Fully managed
* Horizontal autoscaling of workers
* Example pipelines
    * Pub/sub -> Dataflow -> BigQuery(Streaming)
    * Pub/sub -> Dataflow -> Cloud storage (Streaming - files)
    * Cloud storage -> Dataflow -> BigTable/CloudSpanner/Datastore/BigQuery  (Batch load data into databases)
    * Bulk compress files in Cloud Storage (Batch)
    * Convert files formats between Avro, Parquet & CSV (batch)
* Streaming and Batch Usecases
    * Realtime Fraud Detection, Sensor Data Processing, Log Data Processing, Batch processing (Load data, convert formats)
* Use pre-built templates
* Cloud version of Apache Beam (supports Java, Python, Go)
* Serverless (Autoscale automatically)
* Jobs created with
    * Pre-defined templated
    * Notebook instance
        * Write data pipeline job in Java, Python SQL

* Flow
    1. Write job in Java, Python or Go
    2. Unified API for both batch + stream processing
        * No need to separately handle batch & streaming data
    3. Execution
        * Direct runner
            * Scaling issue (testing locally)
        * Apache flink
        * Apache spark
        * Cloud Dataflow (inside GCP)
* Pipeline
    * A pipeline is a graph of transformations that a user constructs that defines the data processing they want to do
* Io-Transform
* Pcollectiopn
    * Fundamental data type in Beam
        * One Pcollection -to- Pcollection
* Ptransform
    * The operations executed within a pipeline
* Runner - execution engine

### Cloud Dataproc

Cloud managed services for Spark and Hadoop

* Variety of jobs are supported
    * Spark, PySpark, SparkR, Hive, SparkSQL, Pig, Hadoop
* Perform complext batch processing
* Multiple cluster Modes
    * Single node / Standard / High Availability (3 masters)
        * Standard (1 master, N workers)
        * Single node (1 master, 0 workers) -- testing
        * High availability (3 masters, N workers)
    * Use regular / preemptible VMs
* Worker node regular VM or Preemtible VM (Cost reduction)
* Use case: Move Hadoop and Spark clusters to the cloud
    * Perform machine learning and AI development using opensource frameworks
* Cloud dataproc is a data analysis platform
    * Can export cluster configuration but not Data
* BigQuery (Alternative) - When we run SQL queries on petabytes
    * Go for Cloud Dataproc when we need more than queries (Batch Processing, Machine Learning, AI Workloads)

### Cloud Data Fusion

Fully managed cloud native solution to quickly build data pipelines

* Code free, Drag-n-drop tool
* 150+ preconfigured connectors & transformations
* Built with Open-source CDAP
* 3 editions available
    * Developer -- $250 per month
    * Basic -- $1100 per month
    * Enterprise -- $3000 per month

### Cloud Composer

Fully managed apache airflow instance in GCP

* Airflow is a workflow & orchestration engine
* With airflow, one can programmatically schedule and monitor workflows
* Workflows are defined as directed acyclic graphs (DAGs)
* DAGs are written in Python 3.x
* Built-in integration for other GCP services
    * Google BigQuery
    * Cloud Dataflow & Dataproc
    * Cloud Datastore
    * Cloud Storage
    * Cloud Pub/Sub, and Cloud ML Engine

### Data Loss Prevention API

* Fully managed service designed to help discover, classify and protect most sensitive data
* PII data
    * Person's name, Credit Card Number, SSN
* Apply API on Cloud storage, BigQuery Data
* DLP work upon Free form text, Structured & Unstructured data (image)
* What to do with this Data
    * Identify sensitive data
    * De-identify data
        * Masking and Encryption
    * Re-identify (In case we want to recover original data)

* Match Likelyhood
    * LIKELIHOOD_UNSPECIFIED - Default value; same as POSSIBLE
    * VERY_UNLIKELY - It is very unlikely that data matches the given InfoType
    * UNLIKELY - It is unlikely that data matches the given InfoType
    * POSSIBLE - It is possible that data matches the given InfoType
    * LIKELY - It is likely that data matches the given InfoType
    * VERY_LIKELY - It is very likely that data matches the given InfoType

___De-Identification of Data___

* Redacation - Remove sensitive data
* Replacement - Replace with some tokens (Like info_type)
* Masking - Replace one or more characters with some other character
* Encryption - Encrypt sensitive data

___Templates___

* Configuration which define for
    * Inspection of jobs
    * De-identification of jobs
* Once Template defined, can be reused for other jobs


___Infotypes___

* What to scan for like:
    * Credit cards
    * Social Security Number (SSN)
    * Age
* Can create custom infotypes or use one of the 120+ defined Built-in infotypes

### Data Catalog

Most organizations today are dealing with a large and growing number of data assets

* Data stakeholders (consumers, producers, and administrators) face a number of challenges:
    * Searching for insightful data
    * Understanding data
    * Making data useful
* Data catalog
    * A fully managed and highly scalable data discovery and metadata management service
    * Single place to discover all data, asset across all projects
* Using Data catalog
    * Search data asserts
    * tag data
* Technical metadata
    * For BigQuery, Pubsub these metadata resides inside individual products
    * Technical meta data being registered by catalog automatically
* Business Metadata
    * Attach tag to existing data asset
    * Define some tag template and attach metadata

# ~~~~ GCP Data Analysis and ML ~~~~

### Machine learning basics

* Design spam email classification system
* How to design?
* What rules we will need inside the system?
    * If message coming from some specified list of senders, spam it
    * If message contain word like lottery, promotion, spam it
* But how many rules?
* Very diffucult & cumbersome task to desing in such way
* Intelligent approach needed
* ML is the solution behind it

Machine learning learns from data

* Training + testing kind of system

___Types of ML systems___


### Supervised learning

* Supervised learning
    * Label has been given
    * Regression
    * Classification
* Unsupervised learning
    * No labels
    * Find Structure within data


___Regression___

* Output prediction is continous in nature
    * Example: House price predition
* Regression ML Algorithm
    * Linear Regression
    * SVR
    * Decision Tree Regressor

___Classification___

* Output prediction is discrete in nature
    * Example: Sentiment analysis of review
        * This product is very much helpful: +ve
    * Example: Is it orange?
        * Yes/No
* Classification Algorithm
    * Logistic Regression
    * SVM
    * KNN
    * Decision Tree Classification

### Unsupervised learning

* No label given
* Find structure within data
* Clustering is a type of unsupervised learning
* Some clustering Algorithms
    * K-Means
    * Hierarchical


### Machine learning workflow

1. Ingesting data
2. Data preparation
3. Split data (Training + Testing)
4. Apply ML Algorithm to the training data
5. Testing Data
6. Model
7. Ready for predictions


___GCP Machine learning services___

* GCP ML Solutions
* Pre-trained model
    * Generic use cases
    * Vision API, Speech API, Language API
* Auto ML
    * Upload our Data and rest, GCP will take care of it
* Custom Training
    * Tensorflow, PyTorch, Scikit-learn
    * SparkML
    * BigQuery ML

### GCP Dataprep (Data preparation)

* Intelligent data preparation tool
* Visually explore, clean , and prepare data for analysis and machine learning
* Build by Trifacta - Third Party Tool, not cloud native
* This is a no-code tool
* Dataprep is serverless and it works at any scale
* No infrastructure to deploy or manage
* Automatically detect schema, anomalies
* Do all time consuming task easily
* Concern - Need to share data with Trifacta


### GCP ML Pre-trained Model

* Google has huge amounts of data
* Google has already trained ML/AI algorithms to build models
* For generic use cases like
    * Object recognition/detection - Vision API
    * OCR
    * Speech to Text
    * Language Translation
    * NLP API - to get insight from natural language
* No training required from customer
* Use already created Rest API for above cases

___Vision API___

* Derive insights from images
    * Detect printed and handwritten text
    * Detect objects
    * Identify popular places and product logos
    * Moderate content
    * Celebrity recognition
* How to use
    * Web UI - Just for testing
    * gcloud - CLI
    * Python SDK

___Natural Language API___

* Derive insights from structured text using Google machine learning
    * Identify entities within documents
    * Sentiment analysis
    * Content classification
* How to use
    * gcloud - CLI
    * Python SDK

___SpeechToText -> TextToSpeech API___

* Speech to text API
    * Accurately convert speech into text using an API powered by Google's AI technologies
    * Supports 125 languages
    * Streaming speech recognition
    * Content filtering
    * Automatic punctuation

* Text to speech API
    * Convert text into natural-sounding speech using an API powered by Google's AI technologies

### GCP Auto machine learning (Auto ML)

* AutoML - Auto machine learning
* Case is not generic
* There is a custom requirement
* Vision API - recognize shoes
* AutoML - Different types of shoes detection
    * Adidas, Nike, etc...
* Upload our data & GCP will create the best model for us
* State-of art Transfer learning technology

### GCP Custom model

* We own
    * Dataset
    * Data scientist
* We want
    * Train our own model
    * Write our own algorithm based on
        * Scikit-learn, XGBoost
        * TensorFlow
        * PyTorch Framework
* Notebook instance
* Build logistic regression model for flower species recognition (example)
* Save model in pickle file
* Deploy model endpoint

### GCP BigQuery ML

* Create ML model in SQL
* No programming language needed
* No need to export data to other environments
* Model support
    * Linear Regression
    * Multiclass Logistic Regression
    * K-Means
    * XGBoost
    * TensorFlow - Import
* Use case demo
    * Flower species recognition
    * From BigQuery public dataset

___Main Bigquery Function___

* Create model
    * Model Type - Linear Reg, Logistic reg
    * Label Column
    * Learning Rate
* Evaluate model
    * ML.Evaluate
    * Provide Model & Test Data
    * Determine how good model perfomance on Test data
* Prediction
    * ML.Prediction
    * Apply live data to model to get prediction

### GCP Data Studio

* BI tool from google
* Connect our data from spreadsheets, Analytics, Google ads, BigQuery and many more connectors
* Drag & Drop, no code
* Create reports & Dashboards
* Free

# ~~~~ GCP Apache Beam (Dataflow continuation) ~~~~

* Apache beam was created as a MapReduce model
* Google developed many in-house products based on MapReduce
* Independently, Hadoop born based on MapReduce concept
* Hadoop is open source
* Apache Beam can be run ioFlume, Flick, Spark, Dataflow
* Top level Apache Open Source Projects
* Started 2016
* Apache Beam is a unified programming model that can build portable Big Data pipelines
* Unified API to process Both batch & Streaming Data
* Batch + Stream = Beam
* Beam supports Java, Python, Go
* Once pipeline is created, it can be executed on any execution framework
* Spark. Flink, Apex, Cloud Data Flow
* Is apache beam faster than Spark or Flink
    * Invalid comparison
* Beam is a programming model, not an execution engine
* Is spark faster than flink
    * Valid comparison


# ~~~~ GCP Datastream ~~~~

* Serverless data capture & replication service
* Stream real-time changes to big-query
* Captures (insert, update, delete) from source database and send changes to big query
* Supports MySQL, PostgreSQL, Oracle to bigquery and GCS
* Schema drift resolution


# ~~~~ GCP Cloud interconnect ~~~~

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

# ~~~~ GCP Cloud VPN ~~~~

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

# ~~~~ GCP Dataplex ~~~~

Dataplex is a platform designed for managing, analyzing, and monitoring data across various sources. It helps in organizing and governing data in a cost-effective manner while providing capabilities for data management, data lineage tracking, and data quality validation. By using Dataplex, the healthcare organization can address the challenges of a decentralized ecosystem and improve data management efficiency.

# ~~~~ GCP PDE Exam findings ~~~~

What is the option for Dataflow in order to process the jobs are not time-critical, and the CTO is willing to accept longer execution times if that can lower costs?

* Implement Dataflow FlexRS (Flexible Resource Scheduling)

Which of the following best corresponds to a single row in a relational database within the Firestore data model?

* Entity

You are training a neural network for a classification task, but the model's precision and recall metrics remain unsatisfactory. After verifying that overfitting is not a concern, which of the following strategies is most likely to improve the model's precision and recall?

* Increase the number of training instances (more data)

Which of the following types of indexes are automatically created for each field in a Cloud Firestore document?

* Single-field indexes for atomic values in descending order
* Single-field indexes for atomic values in ascending order

What should you do to create a BigQuery reservation that applies to the CDC process and ensures predictable costs?

* Create a BigQuery reservation for the project.

What should you do to capture late data in the appropriate window for accurate aggregation?

* Use watermarks to define the expected data arrival window. Allow late data as it arrives.

Question:
What should you do to ensure that data is streamed into BigQuery with exactly-once delivery semantics?

**Not part of question**
Quota limit might change with time. You do not need to worry about the quota limit numbers.

As of Nov 2024, the throughput quota has been updated to - 3 GB per second throughput in multi-regions; 300 MB per second in regions

* Use the BigQuery Storage Write API and ensure that your target BigQuery table is regional.

 You need to quickly adopt a cost-efficient solution that addresses the following requirements:

Data management and discovery

Data lineage tracking

Data quality validation

Question:
Which solution should you implement to address these requirements?

* Use Dataplex to manage data, track data lineage, and perform data quality validation.

You designed a data warehouse in BigQuery to analyze sales data. You need a self-service, low-maintenance, and cost-effective way to share the sales dataset with other business units in your organization.

Question:
What should you do to achieve this?

* Create an Analytics Hub private exchange, and publish the sales dataset.

You are designing a data transformation solution for BigQuery. Your developers are skilled in SQL and prefer to use the ELT (Extract, Load, Transform) development approach. They require an intuitive coding environment and the ability to manage SQL as code.

Question:
What solution should you choose to enable your developers to build and manage SQL pipelines effectively?

* Use Dataform to build, manage, and schedule SQL pipelines.


You are managing a Dataplex environment with raw and curated zones. A data engineering team uploads JSON and CSV files to a bucket asset in the curated zone, but Dataplex is not automatically discovering these files. You need to ensure Dataplex can automatically discover these files.

Question:

What should you do to make sure the JSON and CSV files are automatically discovered by Dataplex?

* Move the JSON and CSV files to the raw zone.