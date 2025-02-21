# ESGF-1.5 Changes

** Preliminary work in progress list of what is changingn between ESGF-1.0 and ESGF-1.5 **

## Metadata and Search Indexes

1. The metadata _index_node_ attribute will be set to `us-index` in all metadata entries
2. A single public ESGF2-US wide Globus Search index replaces three public Solr indexes at LLNL, ORNL, and ANL
3. New private staging Globus Search indexes used by publishers
4. New delay begtween publishing to staging indexes and visibility in public ESGF2-US wide Globus Search index
5. Search will no longer return metadata from Solr indexes outside the US
6. Projects with read-only metadata: CMIP3, CMIP5, CREATE-IP, E3SM-supplement, GeoMIP, LUCID, TAMIP

## Applications

### esg-publisher
1. Change: modified to work with Globus Search
2. Change: modified to publish to a configurable staging Globus Search index

### metagrid
1. Change: modified to work with Globus Search
2. New: search returns datasets, files and replicas hosted on all US based data nodes

### intake-esgf
1. Change: modified to work with Globus Search
2. New: search returns datasets, files and replicas hosted on all US based data nodes
3. Removed: no longer can return XML results

### esg-fastapi
1. New: interface to Globus Search used by (...)
2. New: search returns datasets, files and replicas hosted on all US based data nodes

### esg-wget API
1. Change: modified to work with Globus Search
2. New: search returns datasets, files and replicas hosted on all US based data nodes

### Metadata synchronization tool
1. New: synchronizes staging Globus Search indexes with the public ESGF2-US wide Globus Search index
