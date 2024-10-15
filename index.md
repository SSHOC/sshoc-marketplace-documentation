# sshoc-marketplace-documentation

## (data) source ingestion workflow
See https://marketplace.sshopencloud.eu/about/implementation#data-ingestion-workflow for the generic workflow. SSHOC [D7.3 (p.8)](https://doi.org/10.5281/zenodo.5871650) and [D7.2 (p. 25 and following)](https://doi.org/10.5281/zenodo.5783280) are also providing details about the process. The present documentation describes the ingestion workflow steps and their related documents.

### 1. Suggest and select a new source to harvest
Anyone can suggest a new source to harvest. Suggestions should be sent to the EB members and added to this internal waiting list: [mappings - sources to MP data model](https://docs.google.com/spreadsheets/d/17E6oJ_mXZFXNhQWhvOMmPLu5reJKImwI5OP44yudfe8/edit#gid=2053533970).

Considering the limited resources available for sources ingestion, a prioritisation of the sources of interest is performed. Based on [D7.3 report](https://doi.org/10.5281/zenodo.5871650), 5 criteria are taken into account to evaluate and prioritise the sources: 
1. quality (they should be useful additions), 
2. uniqueness (they should not consist of items mostly already in the Marketplace) 
3. technical interface (they should have metadata that can be relatively easily mapped to the Marketplace data model and should be harvestable via an API), 
4. how much they will enhance the representativity of the various SSH domains within the Marketplace, and how useful they will be to the users of the Marketplace. 
5. Additionally, it will be favourable if a new potential source will bring with it possible contextualization, i.e. relations to other items within itself or the Marketplace.

This prioritisation is performed by the Editorial Board, liaising with the PNSC and ACDH-CH teams in charge of running the DACE pipeline and/or ad-hoc ingestion scripts. For specific cases or difficulties in prioritising, the GB can also be contacted.  

### 2. Define mappings
A mapping from the source data model to the Marketplace data model has to be devised in each case. This mapping is prepared in a spreadsheet by one of the Moderators and reviewed by at least another. This mapping represents the prescription for the custom ingestion pipeline. 
A new tab is opened in this Gsheet - [mappings - sources to MP data model](https://docs.google.com/spreadsheets/d/17E6oJ_mXZFXNhQWhvOMmPLu5reJKImwI5OP44yudfe8/edit#gid=563796397) - based on the mapping template tab. Creating a mapping requires a good understanding of the MP data model, a few resources in that regard:
- [D7.2 p.11](https://doi.org/10.5281/zenodo.5783280) - data model v1.5  
- “Flat” representation of the data model in tabs [`data model`](https://docs.google.com/spreadsheets/d/17E6oJ_mXZFXNhQWhvOMmPLu5reJKImwI5OP44yudfe8/edit#gid=0) and [`properties`](https://docs.google.com/spreadsheets/d/17E6oJ_mXZFXNhQWhvOMmPLu5reJKImwI5OP44yudfe8/edit#gid=190660862) of the mapping spreadsheet.

But also a good understanding of the source data. The moderator preparing the mapping should add as much info as possible in its mapping tab.

### 3. Implement a custom pipeline via DACE or custom script
Based on the conceptual mapping defined in the previous step, a decision is taken by the ingest team to either use the DACE pipeline or to develop/adapt ad-hoc ingest scripts.
#### DACE
DACE is a custom ETL (extract, transform, load) pipeline, which fetches the data from the source, iteratively processes all the items and maps the structured data in the source format to the MP data model, ingesting the transformed items expressed as JSON-objects via the API of the Marketplace. 
- [Presentation slides of the DACE (Oct. 2024 by Ola Nowak)](https://docs.google.com/presentation/d/1LmlowOrpFhp_CKmNZ-N4fyqYnPxnppHquiPwRpzlhzA/edit#slide=id.g167e0717322_23_1) 
- DACE repo: https://gitlab.pcss.pl/dl-team/aggregation/dace and its instance at ACDH-CH https://github.com/SSHOC/data-ingestion/tree/sshoc-dace-to-acdh-ch-k8s
- Esp. list of sources processed: https://gitlab.pcss.pl/dl-team/aggregation/dace/-/wikis/SSHOC-Sources-Configuration
- Specs (json/jolt mappings): https://gitlab.pcss.pl/dl-team/aggregation/dace/-/tree/develop/processors/sshoc-records-processor/src/main/resources/specs
Documentation: https://gitlab.pcss.pl/dl-team/aggregation/dace/-/wikis/Aggregation-in-SSHOC 
- UX source manager: https://harvester-manager.acdh-dev.oeaw.ac.at/
- Jolt tutorial https://gitlab.pcss.pl/dl-team/aggregation/dace/-/wikis/JSON-to-JSON-Mapping-tutorial

#### Custom scripts
The ingestion custom scripts developed by Michael Kurzmeier are following a Python and Jupyter Notebooks approach. They are not primarily meant for continuous ingest., and well suited for the ingestion of a limited number of records (i.e. ~300 records for the sources processed so far). The scripts perform three main operations: source harvesting, transformation, and writing back to the SSH Open MP.
Although the transformation and writing back to the MP have been developed to be as standardised (and reusable) as possible, the heterogeneity of potential new MP sources requires that a complete new approach for the harvesting step is taken for each source.
- https://github.com/SSHOC/SSHOMP-Ingest 
- [Presentation slides (by M. Kurzmeier, Oct. 2024)](https://github.com/SSHOC/SSHOMP-Ingest/blob/aa3d8aac424f3743c2ca764b81dd98e0c4831690/mplibrary/presentation.html) 


### 4. Test ingest and review
After the mappings are defined, ingestion against a test instance and automatic checks are performed, as the basis for a review of the quality of the mapping. Identified problems inform refining of the mappings in an iterative process, until the test ingest passes the review (by the Moderators team). This work will be handled by the assigned Moderator, if necessary in cooperation with the Administrators. 

*Note:* at this stage, the checks are performed, but no curation or editing of the data is performed, only adjustments to the mapping. 

The **test ingest must be performed on the STAGE instance of the SSH Open Marketplace**, either using the `DACE_importer` user or the `system-importer` user (depending on the ingestion pipeline used). These users have a specific role - `administrator` role - that allows direct publication of the records on the stage instance (i.e. items are created or updated with a status approved). 

### 5. Production ingest
After the test ingest has been reviewed and approved, the assigned Moderator can invoke the ingestion against the production instance. 

This production ingest step is performed using either the `DACE_importer` user or the `system-importer` user (depending on the ingestion pipeline used). On production, these users have a `system-contributor` role that will create all the records with a status ingested  allowing moderators to review them before publication.

A bulk approval notebook has also been created in order to change all items status from ingested to accepted, in case there is no specific review to perform for the moderators before publication. 

### 6. Continuous ingest 
For sources, the content of which is expected to change substantially over time, the production ingest is configured as a recurring task. The ingestion pipeline is able to match the source items against Marketplace items and updates them accordingly, and processes previously unseen items to create new Marketplace items.

The continuous ingest mechanism is implemented as described below:

1. Collect all current items of a source in the MP
2. Gather the data from the source one by one and check if an item (identified by its source and its sourceItemId) was already ingested using  `api/items?sourceId={}&sourceItemId={}`
3. TO BE CONTINUED 


