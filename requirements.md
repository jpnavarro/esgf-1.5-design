# ESGF-1.5 Requirements and Acceptance Criteria

This document outlines the requirements and acceptance criteria for the ESGF2-US's ESGF-1.5 deployment. ESGF-1.5 is a new version of the US-Dept. of Energy's (US-DOE) ESGF catalog and data archive for CMIP6 and prior ESGF data. (CMIP7 and beyond will instead use the ESGF-NG system.) ESGF-1.5 will allow US-DOE to continue providing access to legacy ESGF data by migrating to new, more easily-maintainable systems.

## Changes from ESGF-1.0

- **Discovery** - Replace Apache Solr search indices with Globus Search indices.
- **Discovery** - (Eventually) Consolidate the separate indices at Lawrence Livermore, Argonne, and Oak Ridge national laboratories into a single US-DOE index.
- **Data Access** - Replace legacy HTTP/S servers (for data access) with Globus Connect Server (providing both HTTP/S and bulk data transfer)
- **Data Access** - Update wget script service to use Globus HTTP/S and begin a deprecation process
- **Applications** - Replace COG web application with MetaGrid
- **Applications** - Add intake-esgf command-line interface

## Globus Search indices

**Phase I:** Initially, each US-DOE laboratory (LLNL, ANL, ORNL) will replace its Solr ESGF index with a distinct Globus Search ESGF index.

**Phase II:** As a separate follow-on activity, the three laboratories will consolidate the three indices into a single US-DOE index that references data wherever it is available.

### Index contents

In Phase I, each index MUST contain metadata for data stored at the corresponding site (ANL, LLNL, ORNL).

In Phase II, the consolidated index MUST contain metadata for data stored at ANY US-DOE site (ANL, LLNL, ORNL).

In Phase II, each dataset will have a single entry in the consolidated index, and the entry will refer to ALL US-DOE locations where the data may be accessed.

- **Q:** Will either the Phase I or Phase II indices need to refer to any data access options beyond the three US-DOE sites?

- **Q:** LLNL's Solr index currently includes datasets that are not stored at LLNL. These datasets are stored at other data nodes that do not have their own indices. How will these datasets be represented in each of the two phases mentioned above?  Is it possible that we might prefer to operate an additional (separate) index for this data in Phase I or Phase II?

- **Q:** Argonne's E3SM modeling team plans to publish new datasets between now and the time ESGF-NG is available. These datasets will be stored at Argonne. Will LLNL or ORNL replicate these datasets? How will the publication be managed?

- **Q:** It is expected that there will be "CMIP6 Plus" datasets published between now and the time ESGF-NG is available. Where will these datasets be stored? Will they be replicated at any other physical sites, and if so, how will that publication and replication be managed?

### ESGF metadata schema

The ESGF dataset metadata schema is based on WCRP specifications and will not change in either Phase I or Phase II.

It is expected that the metadata schema will change in Phase II to allow the expression of multiple data nodes (physical storage locations) for a given dataset entry.

- **Q:** Have we settled on the specific changes mentioned above yet?

- **Q:** What process(es) will we follow to announce the Phase II changes to the ESGF community?

### File-level entries

In Phase I, file-level entries will be included in the Globus Search indices.

- **Q:** Could we remove file-level entries in Phase I and replace them with a file manifest (file list including pathnames and checksums for each file in the dataset) in the dataset entry? This would have operational benefits and also simplify clients by reducing the number of entries they must explore to retrieve data.

In Phase II, the consolidated index will NOT include file-level entries in the Globus Search index. Clients will not need to inspect additional index entries to learn about individual files in a dataset. Instead, each dataset entry will contain a new file manifest field: a file list including pathnames and checksums for each file in the dataset. Clients can use that manifest to request individual files via HTTP/S. (Clients that prefer bulk transfer can use the base path in the dataset as the source path for transfer requests.)

- **Q:** What is the maximum number of files in any existing ESGF (CMIP6 or earlier) dataset? (We need to confirm that no manifest is too large to include in a dataset entry.)

File checksums in manifests will use the same algorithm(s) that was/were previously used in file entries and, in fact, will not be recomputed.

## Transition API for search clients

To simplify the transition from Solr to Metagrid, we will design, develop, and deploy a transition API that mimics the Apache Solr search API using the new Globus Search ESGF indices to fulfill requests.

The transition API MUST be feature complete with respect to known uses of ESGF Solr indices.

- **Q:** What remaining design and/or development is still needed for the transition API?

- **Q:** Who will complete any remaining design/development/testing of the transition API?

- **Q:** Will the transition API mimic file-level entries when they're removed from the Globus Search indices?

- **Q:** In Phase I, will each lab need to deploy and operate a distinct transition API, or will one lab provide the transition API for all three labs?

- **Q:** In Phase II, who will be responsible for deploying and operating the transition API for the consolidated index?

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

Intake-esgf MUST be updated to allow use of a Globus Search index instead of a Solr index. The transition API for search clients described above is a key step in making this happen.

- **Q:** Will intake-esgf be updated to use Globus Search natively, or will it use the transitional API mentioned above?

- **Q:** Are there any more changes to intake-esgf needed to allow use of a Globus Search index?

- **Q:** Are there changes to intake-esgf needed to implement the wget service changes mentioned above?

- **Q:** Would it be useful for intake-esgf to support bulk transfers with Globus rather than only HTTP/S access? If so, does it already have this support or is there work needed to make this possible?
