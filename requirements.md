# ESGF-1.5 Requirements and Acceptance Criteria

This document outlines the requirements and acceptance criteria for the ESGF2-US ESGF-1.5 deployment. ESGF-1.5 is a new version of the US-Dept. of Energy's (US-DOE) ESGF catalog and data archive for CMIP6 and prior ESGF data. (CMIP7 and beyond will instead use the ESGF-NG system.) ESGF-1.5 will allow US-DOE to continue providing access to legacy ESGF data by migrating to new, more easily-maintainable systems. Given that the ESGF-1.5 deployment targets legacy ESGF data, we will minimize the effort and cost to migrate from ESGF-1.0 by making the minimum necessary changes to maintain legacy data access. Ideas for improvements should target ESGF-NG.

## Changes from ESGF-1.0

- **Discovery** - Replace Apache Solr search indices with a single Globus Search index
- **Data Access** - Replace legacy HTTP/S servers (for data access) with Globus Connect Server (providing both HTTP/S and bulk data transfer)
- **Data Access** - Update wget script service to use Globus HTTP/S and begin a deprecation process
- **Applications** - Replace COG web application with MetaGrid
- **Applications** - Add intake-esgf command-line interface

## Globus Search Index

Replace Apache Solr search indices at Lawrence Livermore, Argonne, and Oak Ridge National Laboratories with a single consolidated US-DOE Globus Search index referencing all available data replicas.

### Index contents

Each dataset will have a single entry in the consolidated index, which will refer to ALL US-DOE locations where the data may be accessed.

- **Q:** Will the consolidated index need to refer to any data access options beyond the three US-DOE sites? **A:** YES, at NERSC and potentially other locations.

- **Q:** LLNL's Solr index currently includes datasets that are not stored at LLNL. These datasets are stored at other data nodes that do not have their own indices. How will these datasets be represented? **A:** as additional file replicas

- **Q:** Argonne's E3SM modeling team plans to publish new datasets between now and the time ESGF-NG is available. These datasets will be stored at Argonne. Will LLNL or ORNL replicate these datasets? How will the publication be managed?

- **Q:** It is expected that there will be "CMIP6 Plus" datasets published between now and the time ESGF-NG is available. Where will these datasets be stored? Will they be replicated at any other physical sites, and if so, how will that publication and replication be managed?

- **Q:** Are there concerns over the destructive potential of (~12) publishers affecting entries they do not own sufficient to warrent a mitigation approach? **A:** mitigated by having a single team consolidate Solr index metadata, and limited future publishing to the minimum required publishers.

- **Q** Can we streamline the transition by having a single person/team consolidating all the metadata in the Globus Search indexes? **A:** YES, tentatively.

### ESGF metadata schema

The ESGF **type=Dataset** metadata schema is based on WCRP specifications and will not change in ESGF-1.5.

The ESGF **Type=File** metadata schema will change to allow the expression of multiple Globus files in a given dataset, as follows:

- New url value "globus_file_attributes:" (informs a client to use the following new attributes)
- New attribute: mime_type
- New globus file attributes: collection (string), path (string), globus_type (string)
- Change index_node attribute value to "esgf2-us-globus-search" (or an index_id?)
- Unchanged file related atributes: data_node, title (contains filename), format, checksum, checksum_type, size, and others

- **Q:** What process(es) will we follow to announce the metadata changes to the ESGF community?

### File-level entries

File-level entries will be included in the Globus Search index to represent replica locations.

- **Q:** Could we remove file-level entries and replace them with a file manifest (file list including pathnames and checksums for each file in the dataset) in the dataset entry? This would have operational benefits and also simplify clients by reducing the number of entries they must explore to retrieve data. **A:** NO, as this change is not necessary and results in more transition effort.

- **Q:** What is the maximum number of files in any existing ESGF (CMIP6 or earlier) dataset? (We need to confirm that no manifest is too large to include in a dataset entry.)

File checksums in manifests will use the same algorithm(s) that was/were previously used in file entries and, in fact, will not be recomputed.

## Transition API for search clients

To simplify the transition from Solr to Metagrid, we will design, develop, and deploy a transition API that mimics the Apache Solr search API using the new Globus Search ESGF indix to fulfill requests.

The transition API MUST be feature complete with respect to known uses of ESGF Solr indices.

- **Q:** What remaining design and/or development is still needed for the transition API?

- **Q:** Who will complete any remaining design/development/testing of the transition API?

- **Q:** Will the transition API mimic file-level entries when they're removed from the Globus Search indices?

- **Q:** Who will be responsible for deploying and operating the transition API for the consolidated index?

- **Q:** How long must the transition API be available?

## Globus Connect Servers

ESGF requires anonymous HTTP/S read-only data access. ESGF-1.5 data MUST be housed in a Globus guest collection configured to allow anonymous HTTP/S read-only access.

## wget service

- **Q:** What do we need to change in the wget service for ESGF-1.5?

- **Q:** Who needs to run wget services? Is this something LLNL does for all three ESGF2-US data nodes, or something that ORNL and ANL will need to run as well?

- **Q:** How do we begin the process of deprecating the wget service?

## MetaGrid

Metagrid MUST be updated to allow use of a Globus Search index instead of a Solr index. The transition API for search clients described above is a key step in making this happen.

- **Q:** Will Metagrid ever be updated to use Globus Search natively?

- **Q:** Beyond configuring Metagrid to use the transitional API instead of a Solr index, are there any other changes to Metagrid needed to allow use of a Globus Search index? If not, we should explicitly state so here.

- **Q:** Are there changes to Metagrid needed to implement the wget service changes mentioned above?

## Intake-ESGF

Intake-esgf has been written to be agnostic about how it gets information about what datasets and files are available for download. It can use both the Solr index / REST API or Globus Search (via the python sdk) directly to populate intermediate data structures that are used to setup file transfers. If 1.5 were ready today, we would only need to update the index UUID in the configuration, and change a little code to parse out the correct globus information.

- **Q:** Will intake-esgf be updated to use Globus Search natively, or will it use the transitional API mentioned above? **A:** We have used Globus Search natively from inception. We do not require the intermediate API at all and do not plan to use it.

- **Q:** Are there any more changes to intake-esgf needed to allow use of a Globus Search index? **A:** No, we already use it.

- **Q:** Are there changes to intake-esgf needed to implement the wget service changes mentioned above? **A:** Intake-esgf is a programmatic interface into the ESGF holdings. We find it counter-intuitive to write a python script that generates a wget script that downloads your data. The python script simply downloads your data. In fact, we think this is a good alternative to wget scripts in the future.

- **Q:** Would it be useful for intake-esgf to support bulk transfers with Globus rather than only HTTP/S access? If so, does it already have this support or is there work needed to make this possible? **A:** `intake-esgf` already does this, see this [documentation](https://intake-esgf.readthedocs.io/en/latest/globus.html).
