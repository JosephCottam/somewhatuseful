---
tags:
  - graphs
  - Databases
title: "Neo4j: Export and Reload"
date: 2025-10-01
layout: post
teaser: Neo4j has opinion about how data is handled...
---
I had transfer a [Neo4j](https://neo4j.com/) database to another team. They already had a database so I wanted to provide flexibility in how they imported and merged it.  Below are notes on options explored. Ultimately, none were obviously better than the others for all cases.

| Method             | td/dr                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------- |
| Binary             | Good for a fresh database that you are an admin on.                                               |
| CSV <br>(one file) | Export easy, import hard.<br>Requires pre-processing to do import.                                |
| CSV (bulk)         | Export limitations, import easy. <br>Viable when nodes have few label combinations.               |
| JSON               | Export easy, import limitation.<br>Slower than CSV. <br>Best used when there are few node labels. |
| GML                | Silently drops data.                                                                              |
| XML                | Did not test...                                                                                   |

For context, here are the database stats:
- 2M nodes 
- 6M relationship
- \>3K distinct node labels
- \>10k distinct node label combinations 
- \>600 distinct edge types

## Binary export

**Export:** `neo4j-admin database dump neo4j --to-path /var/lib/neo4j`
**Import:** `neo4j-admin database load --from-path=datbase.dump neo4j` (Optionally, add `--overwrite-destination` if there is data.)

**Explanation:** As near as I can tell, this is basically a near-exact copy of the database as it exists on disk.  `neo4j-admin` is a command line tool for administering the database.  

**Pros:** 
- Binary export and import are fast (wall-clock time) and space efficient.  
- The commands for neo4j-admin are [well documented](https://neo4j.com/docs/operations-manual/current/backup-restore/offline-backup/) 
- It is a single command to both import and export
- Included in neo4j desktop tool in recent versions.

**Cons:** 
- Requires stopping the database for both export and import.  This requires platform-specific commands.
- Cannot merge into existing database
- Binary dumps are tied to specific versions, so if your infrastructure doesn't match it may not be viable.

**Conclusion:** If you have a fresh database, this a great option.
## CSV export (one-file)
**Export:** `CALL apoc.export.csv.all("file:/database.csv", {});`
**Import:** Process into separate nodes & relations files.
```cypher
CALL apoc.import.csv(  
    [{fileName: 'file:/database_nodes.csv', labels:[]}],  
    [{fileName: 'file:/import/database_relations.csv', types: ‘’}],  
    {ignoreBlankString: True, ignoreEmptyCellArray: True});
MATCH ()-[t]-() REMOVE t.__csv_type;
MATCH (n) REMOVE n.__csv_id;
```
**Explanation:** The export produces a single csv with all of the columns needed to do both nodes and edges.  I have a script that loads up that file, splits it into nodes and edges based on the `_id` field, reformats the list of node labels to be semi-colon separated and renames columns to use the right names.  Including `ignoreBlankString` and , `ignoreEmptyCellArray` keeps the import from making all properties on all nodes/relations.

**Pros:**
- Relatively quick
- Works when nodes have thousands of labels.
- The raw csv export and the post-processed csv are understood by other tools
- Some additional options in [apoc.export.csv.all](https://neo4j.com/docs/apoc/current/overview/apoc.export/apoc.export.csv.all/) [extended documentation](https://neo4j.com/docs/apoc/current/export/csv/)might make the process simpler. 

**Cons:**
- The processing before input can be extensive.  I have not included my script here because it is tailored to my specific dataset.
- There are issues around list-typed and empty-valued fields.  It can take some non-trivial work to get an identical import after export+pre-processing.

**Conclusion:** The post-processing has not been hard.  This is a flexible option if you are comfortable making it.

## CSV export (bulk)
**Export**: **Export:** `CALL apoc.export.csv.all("file:/database.csv", {bulkImport: true});`
**Import:** 
```cypher
CALL apoc.import.csv(  
    [{fileName: 'file:/database_<label1>.csv', labels:[]},
     {fileName: 'file:/database_<label2>.csv', labels:[]}],  
    [{fileName: 'file:/import/database_<type1>.csv', types: ‘’},
    {fileName: 'file:/import/database_<type2>.csv', types: ‘’}],
    {ignoreBlankString: True, ignoreEmptyCellArray: True})
```
**Explanation:** [apoc.export.csv.all](https://neo4j.com/docs/apoc/current/overview/apoc.export/apoc.export.csv.all/) with `bulkImport: true` creates a separate file for each node label combination and each relation type found in the database. You need to know those names at import time.

**Pros:**
- Fast export
- Data is immediately ready for import

**Cons:**
- No generic import command: you need to know the files produced.
- having nodes with many labels can fail because the files are split by label *combination* and the name length may exceed filesystem limits.

**Conclusion:** When it works, this is the simplest option.  Applicability depends on data characteristics.

## JSON export
**Export**:
```cypher
MATCH (n) SET n.labels = labels(n);
MATCH (n) CALL apoc.create.removeLabels([n], labels(n)) YIELD node FINISH;
MATCH (n) SET n:_IS_NODE;
CALL apoc.export.json.all("file:/database.json", {compression: "GZIP"});
```
**Import:**
```cypher
CREATE CONSTRAINT is_node_unique IF NOT EXISTS FOR (n:_IS_NODE) REQUIRE n.neo4jImportId IS UNIQUE;
CALL apoc.import.json("file:/database.json");
match (n) CALL apoc.create.addLabels(n, n.labels) YIELD node FINISH;
match (n) REMOVE n.labels;
match (n) REMOVE n:_IS_NODE;
MATCH (n) REMOVE n.neo4jImportId;
DROP CONSTRAINT is_node_unique IF EXISTS;
```

**Explanation:** JSON import requires a uniqueness constraint for every node label.  When working with many of labels, this is impractical.  For export, this method moves all labels to a property and creates on label used by all nodes.  The process is reversed on import.  The use of `FINISH` when manipulating lables reduces memory requirements.

**Pros:**
- Produces JSON which can be imported by a number of tools
- [apoc.export.json.all](https://neo4j.com/docs/apoc/current/overview/apoc.export/apoc.export.json.all/) can compress on export or input.  I compressed on export using but decompressed on the command line before import (as shown above).

**Cons:**
- Slowest import of all methods checked (5x slower than the CSV).  This is likely because it builds an index as it imports.
- The multi-step dance is easy to make a mistake during.  If you delete the labels property before successfully creating the node labels, it can cost a lot of time.

**Conclusion:** Its an OK process.  If there are only a few node labels, creating uniqueness constrains for each is preferable to the technique used here.

## Other notes:
- Neo4j `apoc.import.csv` supports "[id spaces](https://neo4j.com/docs/operations-manual/current/import/#import-tool-id-spaces)".  The original database was built from multiple different sources providing different node types.  Id spaces made it easier to merge those sources together since we did not need to ensure the ids were disjoint between sources.
- The GML export dropped significant portions of the data without warning.  I would not advise using this option.
- We did not test the XML option because we ran out of time!
