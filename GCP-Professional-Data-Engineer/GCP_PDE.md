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
