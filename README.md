# sshoc-marketplace-documentation

https://harvester-manager.acdh-dev.oeaw.ac.at/ 

DACE repo: https://gitlab.pcss.pl/dl-team/aggregation/dace > https://github.com/SSHOC/data-ingestion/tree/sshoc-dace-to-acdh-ch-k8s 

Esp. list of sources processed: https://gitlab.pcss.pl/dl-team/aggregation/dace/-/wikis/SSHOC-Sources-Configuration 

Specs (json/jolt mappings): https://gitlab.pcss.pl/dl-team/aggregation/dace/-/tree/develop/processors/sshoc-records-processor/src/main/resources/specs 

Jolt tutorial https://gitlab.pcss.pl/dl-team/aggregation/dace/-/wikis/JSON-to-JSON-Mapping-tutorial 

SSH Open Marketplace Github: https://github.com/SSHOC 

esp. data ingest repo
https://github.com/SSHOC/data-ingestion/tree/sshoc-dace-to-acdh-ch-k8s 

Decisions summary
⇒ ingest workflow: 
ingest first on the stage instance with status “ingested” and “dace_importer” (=system_importer) user role. Once reviewed,
ingest on the production instance with status “ingested” with “dace_importer” (=system_importer) user role 
have a bulk approval via notebook 

