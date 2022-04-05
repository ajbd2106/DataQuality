# DataQuality


Introduction
The main aim of the Data Quality project is to provide a quantified, standardised, centralised report on data quality for production data in IDW tables and Data Products. With data being central to Optum's business, it is firstly paramount that the quality of the data is understood within the organisation; how this data quality is quantified is outlined in the Data Quality Scoring section below. Secondly, the introduction of a data quality score can provide direction: if a table's data quality score is poor, one can open up a line of inquiry as to investigate why this is the case. On the other hand, if the data quality score is high, it delivers confidence and peace-of-mind to any reports or insights derived from such a data set. If data is thought of as the raw materials going into internal strategic decision-making, analysts' internal reports, client-facing business reports, it is of huge benefit to get a sense of the degree in which the data is of a sound, trustworthy nature and how well it holds up under scrutiny; much in the same way one would like to know the quality of material going into the construction of a building.
The outputs of this project will initially be available for Internal use for the Optum Data Strategy team, with the evolution of the project seeing that Optum business users being included. 

 
Data Quality Scoring
Data quality is realised through a grading system, as outlined below:

 

This grading system is created through an algorithmic approach, which is outlined in greater detail here: algorithmic process for data quality scoring.
The implementation of the algorithm is developed in Azure with PySpark and Databricks. 
The current tasks and back log can be found here. 

 
Goals
•	Create capability for profiling production level tables from IDW, such as F_Claim_Transaction, as well as Data Products, and assign them a data quality score through algorithmic scoring, as shown above. 
•	Create an audit table to capture the evolution of each table's data quality. This will provide a means of monitoring whether or not a table's data quality is improving.
•	Provide dashboarding capabilities for the output of the Data Quality pipeline and eventually link in the pipeline with Collibra, a data catalog platform.

![image](https://user-images.githubusercontent.com/11782274/161700134-09c20809-70f4-4e4f-9329-f657223d2f26.png)


Data Quality Blueprint
Overview 

 
Data Quality

Data Quality is paramount for high-value high-veracity data for consumption, without Data Quality, even one single pricepoint denoted by an attribute on a dataset cannot be relied on. There are six traits outlining Data Quality.
1.	Clean - The dataset is prepared well and free of all errors
2.	Complete - There is no missing information in dataset
3.	Comprehensive - The data must cover all the functional requirements for the business users.
4.	Chosen - There is no irrelevant or confusing/contradictory data.
5.	Credible - The dataset must be collected/curated in a valid way from trusted sources.
6.	Calculable - The dataset must be usable by the business users.


Attribute Level Data Quality
What is this?

The Attribute-Level Data Quality flow is denoted in 5 sequential stages.
STAGE 1: Shape & Symmetry Validation
1.	Attribute Count Check:  Check if the number of attributes matches the expected based on the schema definition stored in the rule engine. Action is to stop the processing and send failure notification for intervention.
2.	Attribute Naming Check:  Check the attributes are named correctly as expected based on the schema definition stored in the rule engine. Action is to continue processing and overwrite or ignore by deferring to the rule engine value definitions.

STAGE 2: Duplication Validation
1.	Key-Attribute Duplicate Value Check: Check if any of the key-attributes (such as attributes that should only have unique values) have duplicate values. This is outlined by rule engine value definitions. Action is to stop the processing and send failure notification for intervention.
2.	Duplicate Row Check: Check if any rows are full duplicates (all attributes) are exact matches. This is outlined by rule engine definitions. Action is to continue processing and overwrite or ignore by deferring to the rule engine value definitions.

STAGE 3: Erroneous Validation
1.	Erroneous Datatypes Check: Check if any of the attributes are of the incorrect datatypes. This is outlined by rule engine value definitions. Action is to stop the processing and send failure notification for intervention.
2.	Erroneous Data Check: Check if any attributes contain erroneous data (matching a pattern or set of values) This is outlined by rule engine definitions. Action is to continue processing and overwrite or ignore by deferring to the rule engine value definitions.

STAGE 4: Nullability Validation
1.	Null Data Value Check: Check if any of the key-attributes (such as attributes that should only have values) have null or empty values. This is outlined by rule engine value definitions. Action is to stop the processing and send failure notification for intervention.

STAGE 5: Data Drift Validation
1.	Numerical Value Drift Check: Check if any of the numerical key-attributes (such as pricing, amounts etc) have a value that has drifted or changed beyond the expected value threshold. This is outlined by rule engine value definitions. Action is to stop the processing and send failure notification for intervention.
2.	Date Range Drift Check: Check if any rows are date key-attributes (such as submitted date) have a date that has drifted or changed beyond the expected date threshold. This is outlined by rule engine definitions. Action is to continue processing and overwrite or ignore by deferring to the rule engine value definitions.

Important to Note

In the context of Data Products, Data drift relates specifically to the difference in expected values of high priority attributes. Expected Values are denoted by the EVT (Expected Value Thresholds) for each attribute. For instance, an attribute such as AWP (Average Wholesale Price) can have an EVT of 1% thus if an existing record in the Base Claim Data Product had an AWP of $90.00 but during an incremental load this changed to $93.99 this would be above the threshold this would be a 4.43% change in that AWP thus would fail data quality. Data Drift should only be carried out on Business Critical attributes. These should be captured by the requirements gathering phase for the Data Product during the Data Factory Intake Process.

For date range drift, if the rule engine denotes that a date attribute is only for data in the last 12 months from current date but a date appears that is 14 months from current date, that is a date range drift.
Obviously, the more Data Drift checks the slower the process to upsert a data product during incremental loads ergo it is advised to only have data drift checks on the most critical of attributes in the Data Product. Many of the attributes are not applicable in the context of Data Drift such as surrogate keys, categoricals or primary/referential keys.

Asset Level DQ
What is this?

There are four grades for Asset Level Data Quality Grading. These are in sequence from worst to best; Bronze, Silver, Gold and Platinum. Each Grade has qualifiers that denote the level of quality available at each grade.
Quality	Grade	Characteristics	Qualifiers
1st	Platinum	Excellent	Published, Inventoried, Versioned, Trusted, Cataloged
2nd	Gold	Good	Published, Inventoried, Versioned, Trusted
3rd	Silver	Fair	Published, Inventoried
4th	Bronze	Poor	Published

Published
•	Dataset is Persisted and Accessible to multiple business users.
•	Example:
Inventoried
•	Dataset is documented in one of the following formats: Dataflow/ERD diagram (Visio, Miro, Draw etc), business context specification or confluence/wiki/sharepoint article
•	Example:
Versioned
•	Dataset has versioning in place on the dataset for auditing, remediation or snapshot reversion. CDC, Metadata storage or schema evolution (versioning) are potential versioning tools available.
•	Example:
Trusted
•	Dataset is governed with a data lineage tool and can be traced all the way back to source easily. For Strategic Data Solutions, this includes Rule Engine, Data Quality Application and Data Factory.
Cataloged
•	Dataset is cataloged as part of the Enterprise Data Catalog in line with Data Governance Standards.


