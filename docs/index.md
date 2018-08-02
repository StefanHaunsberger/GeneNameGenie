---
title: GeneNameGenie Doc

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: Cypher
  - r: R
  - julia: Julia

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate' target="_blank">Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the _GeneNameGenie_ documentation. 

GeneNameGenie is a Neo4j graph database (GDB) which represents 47 different
molecular identifier types, such as official gene symbols, Ensembl IDs,
NCBI RefSeq IDs and UniProt IDs as well as their relationships in a most
flexible and visually approachable way.

This guide aims to show you how to investigate/translate molecular IDs in GeneNameGenie
using the Neo4j web browser console, R and Julia.

<a href="https://github.com/StefanHaunsberger/GeneNameGenieR" target="_blank">GeneNameGenieR</a> for the R and
<a href="https://github.com/StefanHaunsberger/GeneNameGenieJ" target="_blank">GeneNameGenieJ</a> for the Julia
programming language.

# Data content

The GeneNameGenie graph database (GDB) includes the following DBs:

* Human Ensembl (molecular IDs, chromosomal locations and more)
-> <a href="http://www.ensembl.org/index.html" target="_blank">Ensembl</a>
* A full instance of the Reactome GDB
-> <a href="https://reactome.org/dev/graph-database" target="_blank">Reactome GDB</a>
* miRNA identifiers from 22 different miRBase release versions
-> <a href="http://mirbase.org" target="_blank">miRBase</a>
* miRNA metadata from miRBase version 22.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Let's get started

> To load the package use the following code:

```r
library("GeneNameGenieR")
```

```julia
using GeneNameGenieJ
```

> We encourage to use qualified function calls:

```r
GeneNameGenieR::function
```

```julia
GeneNameGenieJ.function
```

> Web browser URL:

```shell
http://localhost:7474/browser/
```

In the case of the web browser console there are no further steps required to
be able to use the Java stored procedures from GeneNameGenie.

For R and Julia, please load the package with the language specific commands.

If you have a local GeneNameGenie-Neo4j GDB running, you're all set up and can
skip the next part. In the case you want to use a different host address, 
please check out the next paragraph.

<aside class="notice">
Please ensure you have a running _GeneNameGenie_ Neo4j instance.
</aside>

# Establish connection

> __To use custom connection settings you can use the following code:__  
> The following code, for example, sets the URL to: `http://address:7474/db/data/`

```r
GeneNameGenieR::setNeo4jConnection(host = "http://address")
```

```julia
GeneNameGenieJ.setNeo4jConnection(host = "http://address")
```

By default, the address is set to `host = localhost`, `port = 7474` and
`path = db/data/`.

Following parameters can be set:

Parameter | Default | Description
--------- | ------- | -----------
host | localhost | Host address of the DB server.
port | 7474 | Port on which the DBs service is exposed.
path | /db/data/ | Location of the DB files.

# Molecular-ID handling

## Get current official gene symbol - `getOfficialGeneSymbol`

> The following code translates the molecular IDs "AMPK", "ENST00000589955", "A0A024R2B3" and "596"
to their respective official gene symbol:

```shell
CALL rcsi.convert.table.getOfficialGeneSymbol(['AMPK', 'ENST00000589955', 'A0A024R2B3', '596'])

// Alternatively the `value` variable from the JSON object can be extracted and values 
reformatted, which results in a tabular output format (below only the JSON return value is shown):
CALL rcsi.convert.table.getOfficialGeneSymbol(['AMPK', 'ENST00000589955', 'A0A024R2B3', '596']) YIELD value
RETURN
	value.InputId AS InputId,
	value.InputSourceDb AS InputSourceDb,
   value.TargetId AS TargetId,
   value.TargetDb AS TargetDb
```


```r
> ids = c('AMPK', 'ENST00000589955', 'A0A024R2B3', '596')
> GeneNameGenieR::getOfficialGeneSymbol(ids)
```

```julia
# Implemented methods
getOfficialGeneSymbol(queryId::String; sourceDb::String, chromosomal::Bool=true)
getOfficialGeneSymbol(queryId::Vector{String}; sourceDb::String, chromosomal::Bool=true)


julia> ids = String["AMPK", "ENST00000589955", "A0A024R2B3", "596"]
julia> GeneNameGenieJ.getOfficialGeneSymbol(ids)
```

> The above code outputs:

<pre class="highlight text tab-r">
<code># The output is a data.frame object. Thereby, if warnings appear on the console,
affected rows are printed at the top of the results.
# A tibble: 2 x 2
  InputId      nEl
   chr         int
1 596            2
2 A0A024R2B3     2
          InputId            InputSourceDb OfficialGeneSymbol
1            AMPK        Gene Symbol Alias             PRKAA2
2 ENST00000589955 Ensembl Human Transcript               BCL2
3      A0A024R2B3      UniProtKB Gene Name               BCL2
4      A0A024R2B3         UniProtKB/TrEMBL               BCL2
5             596                 WikiGene               BCL2
6             596                NCBI gene               BCL2
Warning message:
In .postCheckInput(x, queryId) :
  Some input identifier(s) match to more than one input source database</code>
</pre>

<pre class="highlight text tab-julia">
<code>WARNING: Some input IDs have multiple mappings
6×3 DataFrames.DataFrame
│ Row │ InputId         │ InputSourceDb            │ OfficialGeneSymbol │
├─────┼─────────────────┼──────────────────────────┼────────────────────┤
│ 1   │ AMPK            │ Gene Symbol Alias        │ PRKAA2             │
│ 2   │ ENST00000589955 │ Ensembl Human Transcript │ BCL2               │
│ 3   │ A0A024R2B3      │ UniProtKB Gene Name      │ BCL2               │
│ 4   │ A0A024R2B3      │ UniProtKB/TrEMBL         │ BCL2               │
│ 5   │ 596             │ WikiGene                 │ BCL2               │
│ 6   │ 596             │ NCBI gene                │ BCL2               │</code>
</pre>

<pre class="highlight json tab-shell">
<code>[
   {
      "InputId": "AMPK",
      "OfficialGeneSymbol": "PRKAA2",
      "InputSourceDb": "Gene Symbol Alias"
   },
   {
      "InputId": "ENST00000589955",
      "OfficialGeneSymbol": "BCL2",
      "InputSourceDb": "Ensembl Human Transcript"
   },
   {
      "InputId": "A0A024R2B3",
      "OfficialGeneSymbol": "BCL2",
      "InputSourceDb": "UniProtKB Gene Name"
   },
   {
      "InputId": "A0A024R2B3",
      "OfficialGeneSymbol": "BCL2",
      "InputSourceDb": "UniProtKB/TrEMBL"
   },
   {
      "InputId": "596",
      "OfficialGeneSymbol": "BCL2",
      "InputSourceDb": "WikiGene"
   },
   {
      "InputId": "596",
      "OfficialGeneSymbol": "BCL2",
      "InputSourceDb": "NCBI gene"
   }
]</code>
</pre>

The `getOfficialGeneSymbol` function can be used to retrieve the official gene symbol
for any given molecular identifier without having to provide the input ID type.

### Function parameters

Parameter | Default | Mandatory | Description
--------- | ------- | --------- | -----------
queryIds | - | true | Molecular input IDs. For R and Julia it can either be a single String or a String vector. For the stored procedure input it needs to be a String array.
sourceDb | - | false | DB name of the input IDs.
chromosomal | true | false |  If `true`, only features located on one of the 23 chromosomes ({1,2,...,X,Y}) are considered. If `false`, features located on human alternative sequences will also be considered. Alternative sequences are sequences regions which differ from the genomic DNA on the primary assembly, such as HSCHR17_2_CTG4. For further information on alternative chormosomes please see <a href="http://www.ensembl.info/2011/05/20/accessing-non-reference-sequences-in-human/" target="_blank">Ensembl blog</a>.

## Convert any molecular input ID to 47 different types

> The following code retrieves the `"Uniprot/SWISSPROT"`, `"Uniprot/SPTREMBL"` and
`"EntrezGene"` IDs for the molecular input ID `"AMPK"`:

```shell
// The section from `YIELD value ...` on is only due to formatting reasons
CALL rcsi.convert.table.convertIds(["AMPK"], ["GeneSymbolDB", "GeneAliasDB", "RefSeq_mRNA"])
YIELD value
RETURN
	value.InputId AS InputId,
   value.InputSourceDb AS InputSourceDb,
   value.TargetDb AS TargetDb,
   value.TargetId AS TargetId
```

```r
# 1) With parameter `longFormat = true` (default)
> GeneNameGenieR::convertFromTo("AMPK", targetDb = c("GeneSymbolDB", "GeneAliasDB", "RefSeq_mRNA"))
# 2) With parameter `longFormat = false`
> GeneNameGenieR::convertFromTo(
      "AMPK",
      targetDb = c("GeneSymbolDB", "GeneAliasDB", "RefSeq_mRNA"),
      longFormat = FALSE)
```

```julia
# Implemented methods
convertFromToExtended(queryId::String; targetDb::Union{String, Vector{String}} attributesUnion{String, Vector{String}}, sourceDb::String, longFormat::Bool, chromosomal::Bool)
convertFromToExtended(queryId::Vector{String}; targetDb::Union{String, Vector{String}}, attributesUnion{String, Vector{String}}, sourceDb::String, longFormat::Bool, chromosomal::Bool)


# 1) With parameter `longFormat = true` (default)
julia> GeneNameGenieJ.convertFromTo("AMPK", targetDb = ["GeneSymbolDB", "GeneAliasDB", "RefSeq_mRNA"])

# 2) With parameter `longFormat = false`
julia> GeneNameGenieJ.convertFromTo(
         "AMPK",
         targetDb = ["GeneSymbolDB", "GeneAliasDB", "RefSeq_mRNA"],
         longFormat = false)
```

> The above code outputs:

<pre class="highlight text tab-r">
<code># 1) Output for `longFormat = true`:
  InputId     InputSourceDb             TargetDb  TargetId
1    AMPK Gene Symbol Alias          RefSeq mRNA NM_006252
2    AMPK Gene Symbol Alias    Gene Symbol Alias    AMPKa2
3    AMPK Gene Symbol Alias    Gene Symbol Alias     PRKAA
4    AMPK Gene Symbol Alias    Gene Symbol Alias      AMPK
5    AMPK Gene Symbol Alias Official Gene Symbol    PRKAA2

# 2) Output for `longFormat = false`, hence, wide table format:
  InputId     InputSourceDb RefSeq.mRNA Gene.Symbol.Alias Official.Gene.Symbol
1    AMPK Gene Symbol Alias   NM_006252 AMPKa2,PRKAA,AMPK               PRKAA2</code>
</pre>

<pre class="highlight text tab-julia">
<code># 1) Output for `longFormat = true`:
5×4 DataFrames.DataFrame
│ Row │ InputId │ InputSourceDb     │ TargetDb             │ TargetId  │
├─────┼─────────┼───────────────────┼──────────────────────┼───────────┤
│ 1   │ AMPK    │ Gene Symbol Alias │ RefSeq mRNA          │ NM_006252 │
│ 2   │ AMPK    │ Gene Symbol Alias │ Gene Symbol Alias    │ AMPKa2    │
│ 3   │ AMPK    │ Gene Symbol Alias │ Gene Symbol Alias    │ PRKAA     │
│ 4   │ AMPK    │ Gene Symbol Alias │ Gene Symbol Alias    │ AMPK      │
│ 5   │ AMPK    │ Gene Symbol Alias │ Official Gene Symbol │ PRKAA2    │

# 2) Output for `longFormat = false`, hence, wide table format:
1×5 DataFrames.DataFrame
│ Row │ InputId │ InputSourceDb     │ RefSeq_mRNA │ Gene_Symbol_Alias │ Official_Gene_Symbol │
├─────┼─────────┼───────────────────┼─────────────┼───────────────────┼──────────────────────┤
│ 1   │ AMPK    │ Gene Symbol Alias │ NM_006252   │ AMPKa2,PRKAA,AMPK │ PRKAA2               │</code>
</pre>

<pre class="highlight text tab-shell">
<code>InputId	    InputSourceDb               TargetDb	 TargetId
 "AMPK"	"Gene Symbol Alias"	         "RefSeq mRNA"  "NM_006252"
 "AMPK"	"Gene Symbol Alias"	   "Gene Symbol Alias"	   "AMPKa2"
 "AMPK"	"Gene Symbol Alias"	   "Gene Symbol Alias"	    "PRKAA"
 "AMPK"	"Gene Symbol Alias"	   "Gene Symbol Alias"	     "AMPK"
 "AMPK"	"Gene Symbol Alias"	"Official Gene Symbol"	   "PRKAA2"</code>
</pre>

With the `convertFromTo` (Julia and R)/ `convertIds` (Cypher) function we can
convert any given identifier to any supported target database. Supported DBs can
be retrieved using the
[`getValidDatabases`](#getvaliddatabases-list-all-supported-dbs) function.

### Function parameters

Parameter | Default | Mandatory | Description
--------- | ------- | --------- | -----------
queryIds | - | true | Molecular input IDs. For R and Julia it can either be a single String or a String vector. For the stored procedure input it needs to be a String array.
targetDb | "GeneSymbolDB" | false | Target DB ID(s) (for valid DB IDs please see [getValidDatabases](#getvaliddatabases-list-all-supported-dbs))
sourceDb | - | false | DB name of the input IDs.
chromosomal | true | false |  If `true`, only features located on one of the 23 chromosomes ({1,2,...,X,Y}) are considered. If `false`, features located on human alternative sequences will also be considered. Alternative sequences are sequences regions which differ from the genomic DNA on the primary assembly, such as HSCHR17_2_CTG4. For further information on alternative chormosomes please see <a href="http://www.ensembl.info/2011/05/20/accessing-non-reference-sequences-in-human/" target="_blank">Ensembl blog</a>.

# miRNA ID handling

## Translate miRNA input Ids to current version and retrieve metadata

> Following parameter interfaces are implemented:

```julia
# Implemented methods
convertToCurrentMirbaseVersion(queryId::String; species::String, metadata::Union{String, Vector{String}})
convertToCurrentMirbaseVersion(queryId::Vector{String}; species::String, metadata::Union{String, Vector{String}})
```

> __Example 1:__ The following code translates the mature miRNA ID hsa-miR-29a to its
current name in miRBase version 22:

```shell
CALL rcsi.mirna.convertToCurrentMirbaseVersion(["hsa-miR-29a"])
YIELD value
RETURN
	value.InputId AS InputId,
   value.Accession AS Accession,
   value.CurrentMirna AS CurrentMirna,
   value.CurrentVersion AS CurrentVersion
```

```r
> GeneNameGenieR::convertToCurrentMirbaseVersion("hsa-miR-29a")
```

```julia
julia> GeneNameGenieJ.convertToCurrentMirbaseVersion("hsa-miR-29a")
```

> __Output for example 1:__

<pre class="highlight text tab-shell">
<code>InputId	Accession	CurrentMirna	CurrentVersion
"hsa-miR-29a"	"MIMAT0000086"	"hsa-miR-29a-3p"	22.0</code>
</pre>

<pre class="highlight text tab-r">
<code>      InputId    Accession   CurrentMirna CurrentVersion
1 hsa-miR-29a MIMAT0000086 hsa-miR-29a-3p             22</code>
</pre>

<pre class="highlight text tab-julia">
<code>1×4 DataFrames.DataFrame
│ Row │ InputId     │ Accession    │ CurrentMirna   │ CurrentVersion │
├─────┼─────────────┼──────────────┼────────────────┼────────────────┤
│ 1   │ hsa-miR-29a │ MIMAT0000086 │ hsa-miR-29a-3p │ 22.0           │</code>
</pre>

> __Example 2:__ In this case we translate the mature miRNA ID hsa-miR-29a to its
current name in the miRBase version 22:

```shell
CALL rcsi.mirna.convertToCurrentMirbaseVersion(
      ["hsa-miR-29a", "MI0000474"],
      "",     // neglect species parameter (stored procedures do not support named arguments)
      ["sequence", "chromosome"])
YIELD value
RETURN
	value.InputId AS InputId,
   value.Accession AS Accession,
   value.CurrentMirna AS CurrentMirna,
   value.CurrentVersion AS CurrentVersion,
   value.Sequence AS Sequence,
   value.Chromosome AS Chromosome
```

```r
> GeneNameGenieR::convertToCurrentMirbaseVersion(
     c("hsa-miR-29a", "MI0000474"),
     metadata = c("sequence", "chromosome"))
```

```julia
julia> GeneNameGenieJ.convertToCurrentMirbaseVersion(
            ["hsa-miR-29a", "MI0000474"],
            metadata = ["sequence", "chromosome"])
```

> __Output for example 2:__

<pre class="highlight text tab-shell">
<code>InputId	Accession	CurrentMirna	CurrentVersion	Sequence	Chromosome
"MI0000474"	"MI0000474"	"hsa-mir-134"	22.0	"CAGGGUGU ... CCAACCCUC"	"14"
"hsa-miR-29a"	"MIMAT0000086"	"hsa-miR-29a-3p"	22.0	"UAGCACCAUCUGAAAUCGGUUA"	null</code>
</pre>

<pre class="highlight text tab-r">
<code>      InputId    Accession   CurrentMirna CurrentVersion               Sequence Chromosome
1   MI0000474    MI0000474    hsa-mir-134             22 CAGGGUGU ... CCAACCCUC         14
2 hsa-miR-29a MIMAT0000086 hsa-miR-29a-3p             22 UAGCACCAUCUGAAAUCGGUUA         NA</code>
</pre>

<pre class="highlight text tab-julia">
<code>2×6 DataFrames.DataFrame
│ Row │ InputId     │ Accession    │ CurrentMirna   │ CurrentVersion │ Sequence                │ Chromosome │
├─────┼─────────────┼──────────────┼────────────────┼────────────────┼─────────────────────────┼────────────┤
│ 1   │ MI0000474   │ MI0000474    │ hsa-mir-134    │ 22.0           │ CAGGGUGU ... ACCAACCCUC │ 14         │
│ 2   │ hsa-miR-29a │ MIMAT0000086 │ hsa-miR-29a-3p │ 22.0           │ UAGCACCAUCUGAAAUCGGUUA  │ missing    │</code>
</pre>

The `convertToCurrentMirbaseVersion` function takes a single or multiple miRNA IDs
and returns the current miRNA name for each input value respectively (where possible).
  
Moreover, metadata for mature as well as precursor miRNAs, such as comments and reads,
can be retrieved by passing the parameter value `metadata` to the function.
The function accepts mature miRNA identifiers (miRNA name and MIMAT-accession) as well as
precursor identifiers (pre-miRNA name and MI-accession). Hence, some metadata values
only apply to precursor miRNAs, such as genomic location and strand.

### Function parameters

Parameter | Default | Mandatory | Description
--------- | ------- | --------- | -----------
queryIds | - | true | Molecular input IDs. For R and Julia it can either be a single String or a String vector. For the stored procedure input it needs to be a String array.
targetDb | "GeneSymbolDB" | false | Target DB ID(s) (for valid DB IDs please see [getValidDatabases](#getvaliddatabases-list-all-supported-dbs))
sourceDb | - | false | DB name of the input IDs.
chromosomal | true | false |  If `true`, only features located on one of the 23 chromosomes ({1,2,...,X,Y}) are considered. If `false`, features located on human alternative sequences will also be considered. Alternative sequences are sequences regions which differ from the genomic DNA on the primary assembly, such as HSCHR17_2_CTG4. For further information on alternative chormosomes please see <a href="http://www.ensembl.info/2011/05/20/accessing-non-reference-sequences-in-human/" target="_blank">Ensembl blog</a>.


# List valid databases and supported parameter values

## `getValidDatabases`: List all supported DBs

> The following code returns all supported DB types:

```shell
CALL rcsi.params.getValidDatabases()
```

```r
GeneNameGenieR::getValidDatabases()
```

```julia
GeneNameGenieJ.getValidDatabases()
```

## `getValidMirnaMetadataValues`: List all valid miRNA metadata parameter values

> The following code returns all valid miRNA metadata parameter values:

```shell
CALL rcsi.params.getValidMirnaMetadataValues()
```

```r
GeneNameGenieR::getValidMirnaMetadataValues()
```

```julia
GeneNameGenieJ.getValidMirnaMetadataValues()
```

> The above code outputs

```text
             Parameter
1           confidence
2                 type
3             sequence
4             comments
5          previousIds
6                  url
7           chromosome
8          regionStart
9            regionEnd
10              strand
11 communityAnnotation
12        nExperiments
13               reads
14        evidenceType
```

The values returned by the `getValidMirnaMetadataValues` function are valid
input parameters for the `metadata` value of the `convertToCurrentMirbaseVersion`
function. All values were retrieved from the current miRBase release version 22.