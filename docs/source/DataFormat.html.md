---
title: BigSMILES Data Format

language_tabs: # must be one of https://git.io/vQNgJ
  - json: BigSMILES Data

toc_footers:
  - <a href='#'>BigSMILES Project</a>
  - <a href='line_notation.html'>BigSMILES Line Notation</a>
  - <a href='DataFormat.html'>BigSMILES Data Format</a>
  - <a href='https://github.com/olsenlabmit/bigSMILES'>GitHub</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:

search: true
---

# Introduction

The [BigSMILES line notation](./BigSMILES_LineNotation.md) provides a compact representation scheme in plain text for expressing the composition and connectivity of polymers and the chemical structures of their constituent repeating units. However, while the text representations provide comprehensive descriptions on how distinct repeating units interconnect to form macromolecular fragments, each BigSMILES representation specifies only the unweighted ensemble of molecular states (individual molecular connectivity realizations) permissible by the connectivity rules defined within the string, and the bare BigSMILES strings do not explicitly contain any information on the relative weight or probability of each molecular state. Therefore, to fully specify a polymeric system, additional quantifications on the distributional properties of the polymer, such as the degree of polymerization or the dispersity, must be provided in addition to the BigSMILES string. 

This document provides the official documentation of the BigSMILES data format, a data standard that accompanies the BigSMILES line notation. 

<aside class="success">This page is mainly meant for developers. For BigSMILES users that are looking to generate BigSMILES data files, please go to the <a href="https://olsenlabmit.github.io/BigSMILES_DataForm/">BigSMILES Data Form</a> for a more user friendly interface.
</aside>



# The BigSMILES Data Format

> A BigSMILES data file stores data in the following format

```json
{	
    "BigSMILES" : "CC{[$]$CC$,$CC(C)$[$]}",
	"log" : [log1,log2,...],
	"id" : "XXXX-XXX",
	"src" : ["source-of-data"],
	...
	"data" : [data1,data2,...]
}
```

> where the terms *log1*, *log2*, *data1*, *data2* are placeholders for JSON objects that contain log entries and individual data with characterization of the structural properties of a polymer. Their exact syntax will be introduced in later sections.
>
> The following example provides an  expanded version with explicit objects, 

```json 
{	
    "BigSMILES" : "CC{[$]$CC$,$CC(C)$[$]}{[<]>OCC<[>]}O",
	"log" : [{"author_id":"921312906","date":"2019-10-16","msg":"file first created."},
             {"author_id":"921312906","date":"2019-10-17","msg":"new examples"} ],
	"id" : "polymer-100.1",
	"src" : ["doi:10.1021/acscentsci.9b00476","doi:10.1021/acs.macromol.8b01676"],

	"data" : [
              {
               "target":"{[$]$CC$,$CC(C)$[$]}",
               "atom_idx" :[3,4,5,6,7],
               "Mn" : {"value":4.4,
                       "unit":"kDa",
                       "uncertainty":0.5,
                       "uncertainty_src":"multiple samples from the same batch",
                       "method": "GPC" },
               "D" : {"value": 1.1, 
                      "uncertainty": 0.1, 
                      "uncertainty_src": "uncertainty in GPC calibration"} 
              },
              {
               "target":"{[<]>OCC<[>]}O",
               "atom_idx" :[8,9,10,11],
               "Mw" : {"value":10,
                       "unit":"kDa",
                       "uncertainty":0.5,
                       "uncertainty_src":"multiple samples from the same batch",
                       "method": "GPC" },
               "D" : {"value": 1.5} 
              } 
    		 ]
}
```

The major aim of the BigSMILES data format is to provide a standard language for the storage of chemical structure related characterization for polymers. In the data file, the data entries are stored in JSON format.

In JSON, multiple name-value pairs are saved in a comma-delimited list. Unless otherwise specified, all lists within the BigSMILES data format are considered unordered (even for arrays). This improves flexibility and minimize overhead burden in transforming existing records into the standard data format.

An illustrative BigSMILES data JSON file example is shown in the code block. In the rest of the document, a JSON file/object that describes a single polymer is referred to as the ***main JSON object***.

<aside class="success">Remember — Each BigSMILES JSON file contains data and characterization for exactly one polymer. Multiple polymers that appear within one literature should be encoded within separate files. </aside>

In BigSMILES data format, relevant data is stored within different entries according to their nature. On the highest level, data are classified into two major different categories:

- **metadata** associated with the data file itself, such as the document identifier, the source of the data, or other information relevant to the maintenance of the data file itself, and
- **data** or parameters that provide characterization of the chemical structure of the polymer

where the name-value pairs of the metadata, such as the BigSMILES string of the polymer, the author, the document identifier, etc., are directly stored on the top level of the JSON file (more details on the definition of some standard metadata entries will be provided shortly), and the physicochemical properties relevant to the characterization of the chemical structure are encapsulated and compiled within the "data" entry. 

<aside class="notice">The order of which each individual entry appears does not matter; as long as each entry is denoted by a comma-delimited list of name (specified by a string within quotation marks) and value pairs separated by colons, they are considered equally valid.</aside>

# Metadata 

> The metadata section of a BigSMILES data file has the following format

```json
{	
	"BigSMILES" : "CC{[$]$CC$,$CC(C)$[$]}",
	"log" : [{"author_id":"921312906","date":"2019-10-16","msg":"file first created."},
             {"author_id":"921312906","date":"2019-10-17","msg":"new examples"} ],
	"id" : "XXXX-XXX",
	"src" : ["source 1", "source 2", "source3"],
	"other metadata1" : "value1",
	"other metadata2" : 2.0,
	"other metadataN" : ["valueN1","valueN2","valueN3"]
}
```

A list of useful metadata entries are provided in this section. However, it should be noted that the provided list is very basic and not intended to be comprehensive. Additional metadata entries beyond the provided list can be incorporated depending on the specific needs of individual projects. Standardization are provided for these to serve as a guideline for recording the essential information. 

| Name                    | Type   | Description                                                  |
| ----------------------- | ------ | ------------------------------------------------------------ |
| [BigSMILES](#bigsmiles) | string | The BigSMILES string for the polymer.                        |
| [id](#id)               | string | An identifier for the document to serve as document reference id in database or file systems. |
| [src](#src)             | array  | The source of the data.                                      |
| [log](#log)             | array  | An array that contains the revision log of the document.     |

<aside class="notice">It is strongly recommended that these provided entries are incorporated within every data file, because they provide basic information that allows for verification of the data entries for each created polymer, as well as serving as digital trails for document curation purposes.</aside>
## BigSMILES

> Syntax and usage

```json
"BigSMILES" : "{<OCC>;>[H],<O}"
"BigSMILES" : "CC{[$]$CC$,$CC(C)$[$]}"
```

BigSMILES holds the BigSMILES string generated by the [BigSMILES line notation](BigSMILES_LineNotation.md) that describes the basic chemical connectivity pattern between constituent units for a polymer.

**Type**

string



## id

> Syntax and usage

```json
"id" : "1100583"
"id" : "Polymer-0-05"
```

id holds a string that serves as an identifier for the document. It should correspond to the unique identifier in a database or the file identifier in a file system or data repository.

**Type**

string



## src

> Syntax and usage

```json
"src" : ["10.1021/acscentsci.9b00476","10.1021/acs.macromol.8b01676"]
```

src holds an array of strings that specifies the source(s) of the raw data. 

In academic settings, the DOI number associated with the reference literatures could be a good choice of unambiguous specification of the source; for industry or individual organizations, DOI can be replaced by the internal document identifier of the relevant documents.

**Type**

array of strings



## log

> Syntax and usage
>
> Author represented as numerical identifiers

```json
"log" : [
    {"author_id":["921312906","9527"],"date":"2019-10-16","msg":"file first created."},
    {"author_id":["921312906"],"date":"2019-10-17","msg":"new examples"}, 
    {"author_id":["921312906"],"date":"2019-10-19","msg":"more examples"} 
]
```

> or authors with their names explicitly included

```json
"log" : [
    {"author_id":["Tzyy-Shyang Lin"],
     "date":"2019-10-07",
     "msg":"document first created"},
    {"author_id":["Tzyy-Shyang Lin","Bradley Olsen"],
     "date":"2019-10-10",
     "msg":"first revision, modified kinetics section"}, 
    {"author_id":["MONET Team"],
     "date":"2019-10-20",
     "msg":"first publishable version"}
]
```

log holds the log of the revisions of the document. 

The log consists of an array of *log objects*. Each log object is a JSON object that contains the following entries:

- author_id (array of string)
  A list of the identifier for the authors (represented as strings) of the revision of the document. Depending on the application, the authors can be represented by 
  - ORCID
  - employee id
  - ... or any other suitable identifier (such as names of the author)
- date (string)
  represented by a string in the "yyyy-mm-dd" format
- msg (string)
  a string logging necessary information or message associated with the revision

**Type**

array of *log object*

<aside class="notice">Using names as author identifiers are strongly discouraged, because they are often not unique and difficult to track. Unique identifiers such as ORCID are preferred.</aside>


# Data and the Data Object

> The data objects are embedded within the main JSON object

```json
{	
	"BigSMILES" : "CC{[$]$CC$,$CC(C)$[$]}",
	"log" : [{"author_id":"921312906","date":"2019-10-16","msg":"file first created."},
             {"author_id":"921312906","date":"2019-10-17","msg":"new examples"} ],
	"id" : "XXXX-XXX",
	"src" : ["source 1", "source 2", "source3"],
	"other metadata1" : "value1",
	"data" : [data_obj1,data_obj2,...]
}
```

> Each data object follows the following syntax

```json
{
    "target":"{[$]$CC$,$CC(C)$[$]}",
    "atom_idx" :[3,4,5,6,7],
    "Mn" : {"value":4.4,
            "unit":"kDa",
            "uncertainty":0.5,
            "uncertainty_src":"multiple samples from the same batch",
            "method": "GPC" },
    "D" : {"value": 1.1, 
           "uncertainty": 0.1, 
           "uncertainty_src": "uncertainty in GPC calibration"} ,
	"kinetics" : {"parameter1":"value1", "description": "description-text"}
}
```

> The "target" and "atom_idx" entries are always required, whereas other entries related to properties are included depending on availability.  Note that although only two properties (number average molecular weight and dispersity) are included in the example, in general, all relevant properties should be included. 

In the BigSMILES data format, the data associated with the molecular structures of the polymers are encapsulated within the "data" entry with an array of ***data objects*** in the following syntax

`"data" : [data_obj1, data_obj2, data_obj3,...]`

and placed within the major JSON object, as illustrated in the example. Each data object corresponds to a single segment of the polymer or a polymeric precursor related to the polymer of interest, and encapsulates the information on properties or parameters relevant to the characterization of the weights of each molecular state within the ensemble. The syntax for the data object is illustrated in the example panel.



Each data object is consisted of the following three types of entries:

1. indexing entries (the "target" and "atom_idx" entries) that specify the structure of the polymer segment or precursor on which the physicochemical measurements are actually performed, and how they are related to the polymer of interest
2. measurable properties that provide characterization of the chemical structure of the polymer, such as number average molecular weight, dispersity, ... etc.
3. properties or parameters that provide information that could be used to infer the chemical structure of a polymer, but are model dependent and become meaningless outside of the context of a specific physicochemical model

The entries in the first two categories are directly listed within the data object (analogous to the metadata entries in the main JSON object), whereas the parameters that are model dependent are encapsulated within a "kinetics" entry.  

<aside class="notice">Remember - The order of the entries do not matter!</aside>
## Indexing a Polymeric Segment

> For each data object, the "target" and "atom_idx" entries must be included

```json
data_obj = 
{	
	"target" : "{[<]>OCC<[>]}Br",
	"atom_idx" : [1,2,3,null],
	"Mn" : 	{"value":4.4, ...},
    "D" : 	{"value": 1.1, ...},
	"kinetics" : {...}
}
```

> To illustrate the indexing scheme, consider the following examples.
>
> First, consider the a diblock copolymer formed via iodoacetyl sulfhydryl conjugation 

```json
"BigSMILES" : "{[>]<OCC>[<]}NC(=O)CS{[$]$C(c1ccccc1)C$[$]}"
```

> To specify characterization on the PEG precursor, the following entries could be used

```json
"target" : 	"{[>]<OCC>[<]}NC(=O)CI"
"atom_idx": [1,2,3,4,5,6,7,null]
```

> for the polystyrene precursor, similar encoding applies

```json
"target" : 	"{[$]$CC(c1ccccc1)$[$]}S"
"atom_idx": [16,9,10,11,12,13,14,15,8]
```

> Next, consider a polycysteine grafted with iodoacetyl terminated PEG 

```json
"BigSMILES" : "{[>]<NC(CS)C(=O)>,<NC(CSCC(=O)N{[>]<CCO>[<]})C(=O)>[<]}O"
```

> To characterize the entire polymer, the BigSMILES string can be directly used in the target entry

```json
"target" : 	"{[>]<NC(CS)C(=O6)>,<NC(CSCC(=O)N14{[>]<CCO>[<]})C(=O)>[<]}O"
"atom_idx": [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
```

> Characterization of the grafts would require the specification of iodoacetyl terminated PEGs

```json
"target" : 	"ICC(=O)N{[>]<CCO>[<]}"
"atom_idx": [null,11,12,13,14,15,16,17]
```

> If the backbone was characterized prior to grafting, the characterization for the polypeptide would require the following indexing

```json
"target" : 	"{[>]<NC(CS)C(=O6)>[<]}O"
"atom_idx": [[1,7],[2,8],[3,9],[4,10],[5,18],[6,19],20]
```

> Note that in this case some of the atoms on the precursor peptide polymer correspond to more than one atoms on the graft polymer, therefore the corresponding elements contain an array of multiple indices instead of one index.

In each data object, the exact element or component of the polymer that the properties are associated with is given by the ***target*** and ***atom_idx*** entries. 

| Entry Name | Type   | Description                                                  |
| ---------- | ------ | ------------------------------------------------------------ |
| target     | string | The BigSMILES string for the polymer/molecule that characterizations are actually performed on. |
| atom_idx   | array  | The map between the atoms on the target polymer and the polymer described by the overall data file. |

In principle, the BigSMILES data format is designed to store quantifications obtained directly from experimental measurements. The "target" entry is designed to contain the BigSMILES string of the exact polymer molecule that the measurements are performed on. 



In the case that all relevant characterizations are performed on the polymer of interest (the polymer with which the data file is associated), the target entry should be identical to the "BigSMILES" entry in the metadata section.



However, for polymers, it is common that characterizations are performed on precursors or polymers that are not exactly identical to the actual polymer of interest. This is especially common for polymers that undergo post-characterization functionalization, or polymers segments that have been grafted or tethered to other polymer segments. In these cases, the target polymer would not be the same as the polymer of interest, and may contain parts or atoms that could not necessarily be found in the polymer of interest.

<aside class="notice">In this case, the the BigSMILES string within the target field would not be substrings of the original BigSMILES string.</aside>
Meanwhile, to unambiguously specify which of the atom(s) on the polymer of interest do each atom on the target polymer correspond to, a map between the atoms on the target polymer and the atoms on the polymer of interest must be provided. This is encapsulated within the "atom_idx" entry, which is an array that has the same number of elements as the number of atoms (number of explicit atom symbols) in the target string. 

Within the atom_idx array, the *n*th element corresponds to the *n*th atom within the target string. (The labelling goes from left to right, with the leftmost atom being the 1st atom, the next one the 2nd, and so on.) The element within the array contains the numeric indices of the atom(s) (labelled in the same way) in the original polymer of interest that the *n*th atom in the target polymer corresponds to. 



For example, consider a file that were to describe an azide-functionalized poly(ethylene glycol). Its BigSMILES string reads

`"BigSMILES" : "{[<]>OCC<[>]}N=[N+]=[N-]"`

The numeric label for each atom would be: (leftmost) O = 1, (left) C = 2, (right) C = 3 and 4, 5, 6 for the three nitrogen atoms.



Now, consider the scenario where the characterization of the PEG were all performed on the precursor bromide terminated PEG without end functionalization, then the target field would be

`"target" : "{[<]>OCC<[>]}Br"`

In this case, the first oxygen atom corresponds to the first atom in the polymer of interest; therefore, the first element for the atom_idx array would be 1. Likewise, the second and third elements are 2 and 3 respectively. The last element, which corresponds to the bromine atom, do not correspond to any of the atom found within the azide functionalized PEG. Therefore, the element would be *null*. Therefore, the atom_idx array for this precursor reads

`"atom_idx": [1,2,3,null]`

If the target string would be written in the reversed order

`"target" : "Br{[<]>CCO<[>]}"`

then the array would be changed accordingly to

`"atom_idx": [null,3,2,1]`

In some cases, if an atom on a target polymer corresponds to more than one atom, the corresponding element in the array should be replaced by an array with the indices of all the relevant atoms.

`"atom_idx": [...,1,null,2,[3,5],4,...]`

This can often be found on polymers that undergo post-characterization functionalization, as illustrated in the second example.



For more complex examples, please refer to the examples panel.

<aside class="warning">The target and atom_idx entries are required for every data object.</aside>

## Measurable Properties

> Relevant properties can be included within the list of entries in a data object

```json
data_obj = 
{	
	"target" : "{[<]>OCC<[>]}Br",
	"atom_idx" : [1,2,3,null],
	"Mn" : 	{"value":4.4, ...},
    "D" : 	{"value": 1.1, ...},
	"kinetics" : {...}
}
```

> For each property, please refer to corresponding sections for detailed syntax.



Within the data object, each measured property is stored within a separate entry. For each property, the parameters required within the corresponding JSON object is slightly different. Please click on the name of the property in the table to view the detailed syntax. 

| Property                              | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| [D](#d)                               | Dispersity                                                   |
| [Mn](#mn)                             | Number average molecular weight                              |
| [Mw](#mw)                             | Weight average molecular weight                              |
| [Mz](#mz)                             | Z average molecular weight                                   |
| [DPn](#dpn)                           | Number average degree of polymerization                      |
| [DPw](#dpw)                           | Weight average degree of polymerization                      |
| [DPz](#dpz)                           | Z average degree of polymerization                           |
| [skewness](#skewness)                 | The skewness of the molecular weight distribution            |
| [kurtosis](#kurtosis)                 | The kurtosis of the molecular weight distribution            |
| [MWD](#mwd)                           | The full molecular weight distribution                       |
| [GPC](#gpc)                           | The GPC measurement data for polymer molecular weight distribution |
| [comp](#comp)                         | The composition of a polymer characterized by the ratio of different repeat units. |
| [comp_diad](#comp_diad)               | The composition of a polymer characterized by the ratio of different repeat unit diads. |
| [comp_higher](#comp_higher)           | The composition of a polymer characterized by the ratio of different repeat unit multi-ads. |
| [tacticity_diad](#tacticity_diad)     | The tacticity of a polymeric segment described by diads      |
| [tacticity_triad](#tacticity_triad)   | The tacticity of a polymeric segment described by triads     |
| [tacticity_higher](#tacticity_higher) | The tacticity of a polymeric segment described by higher order multi-ads |
| [MSL](#msl)                           | The average length of *meso* sequences within the polymer molecule |
| [head_tail_config](#head_tail_config) | The head/tail configuration of asymmetric repeating units within a polymeric segment described by triads. |
| [p](#p)                               | The extent of reaction                                       |

<aside class="warning">BigSMILES data format is designed specifically for describing the structural features of polymers. Therefore, properties, such as the viscosity, that are not directly relevant to the structure should <b>NOT</b> be included within the data objects.</aside>
<aside class="warning">Unlike the metadata list, incorporating entries not contained within the supported properties list is strongly discouraged to ensure the maximal compatibility. If there are missing fields that you consider necessary, please submit a modification or update request, and we will incorporate the new entry into the standard list as soon as possible.</aside>
## Model Dependent Parameters

> The kinetic object can contain any user defined parameters that are relevant to the specific kinetic model. The only requirement is that the object contain either a description that details the exact definitions of each parameter and how they work with a specific kinetic model, or a reference to another document that contains such information.

```json
kinetic_obj = 
{	
	"src" : "source-to-model-description",
    "desc":	"description of the model",
    "parameter1" : "value1",
	"parameter2" : "value2",
	...
}
```



As discussed earlier, the data object should only explicitly contain properties that are not model dependent. Meanwhile, other parameters, such as the reactive rate constants (which are meaningful only under the context of a certain kinetic model, reaction mechanism and other assumptions), although not objectively quantitative, may also provide means to infer the relative weights of the molecular states in an ensemble. To include these information, the following syntax can be used:

`"kinetics": kinetics object`

However, since each kinetic model is highly specific and often vastly different from other models both in terms of the nature and number of parameters required, standardization for the ***kinetics object*** is currently not supported, and the user is free to define and include any parameter as deemed necessary. The only strict requirement is that the kinetics object must contain an entry that either clearly specifies the exact definitions of each parameter and how they work with a specific kinetic model to inform the molecular structures, or provide a reference to another document that details such information.



# List of Supported Data Entries

## D

> Syntax and usage

```json
"D" : {
    "value" : 1.1,
    "uncertainty" : 0.1,
    "uncertainty_src" : "uncertainty of GPC calibration",
    "method" : "GPC"
}
```

Dispersity of the selected polymeric segment.

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the dispersity                          |
| uncertainty     | number | optional | the uncertainty of the provided dispersity                   |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |



## Mn

> Syntax and usage

```json
"Mn" : {
    "value" : 4.4,
    "unit" : "kDa",
    "uncertainty" : 0.5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Number average molecular weight  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the molecular weight                    |
| unit            | string | required | the unit of the provided value                               |
| method          | string | optional | how the value is measured or obtained                        |
| uncertainty     | number | optional | the uncertainty of the provided value. The uncertainty must be provided in the same units as the value provided |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |

**Allowed Units**

- kDa
- Da



## Mw

> Syntax and usage

```json
"Mz" : {
    "value" : 4.4,
    "unit" : "kDa",
    "uncertainty" : 0.5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Weight average molecular weight  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the molecular weight                    |
| unit            | string | required | the unit of the provided value                               |
| method          | string | optional | how the value is measured or obtained                        |
| uncertainty     | number | optional | the uncertainty of the provided value. The uncertainty must be provided in the same units as the value provided |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |

**Allowed Units**

- kDa

- Da

  

## Mz

> Syntax and usage

```json
"Mz" : {
    "value" : 4.4,
    "unit" : "kDa",
    "uncertainty" : 0.5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Z average molecular weight  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the molecular weight                    |
| unit            | string | required | the unit of the provided value                               |
| method          | string | optional | how the value is measured or obtained                        |
| uncertainty     | number | optional | the uncertainty of the provided value. The uncertainty must be provided in the same units as the value provided |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |

**Allowed Units**

- kDa

- Da

  

## DPn

> Syntax and usage

```json
"DPn" : {
    "value" : 100,
    "uncertainty" : 5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Number average degree of polymerization of the selected polymeric segment. If multiple blocks are selected, this represent the sum of multiple blocks.

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the degree of polymerization            |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |

<aside class="notice">The end groups and everything not contained within stochastic objects are ignored.</aside>
## DPw

> Syntax and usage

```json
"DPw" : {
    "value" : 100,
    "uncertainty" : 5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Weight average degree of polymerization of the selected polymeric segment. If multiple blocks are selected, this represent the sum of multiple blocks.

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the degree of polymerization            |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |

<aside class="notice">The end groups and everything not contained within stochastic objects are ignored.</aside>
## DPz

> Syntax and usage

```json
"DPz" : {
    "value" : 100,
    "uncertainty" : 5,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

Z average degree of polymerization of the selected polymeric segment. If multiple blocks are selected, this represent the sum of multiple blocks.

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the degree of polymerization            |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |

<aside class="notice">The end groups and everything not contained within stochastic objects are ignored.</aside>
## skewness

> Syntax and usage

```json
"skewness" : {
    "value" : 1,
    "uncertainty" : 0.05,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

The skewness of the molecular weight distribution  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the skewness                            |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |



## kurtosis

> Syntax and usage

```json
"kurtosis" : {
    "value" : 1,
    "uncertainty" : 0.05,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "GPC"
}
```

The kurtosis of the molecular weight distribution  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the kurtosis                            |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |



## MWD

> Syntax and usage

```json
"MWD" : {
    "value" : [0.01,0.01,0.02,...],
    "mol_wt": [1,1.05,1.10,...],
    "unit"  : "kDa",
    "uncertainty" : [0.001,0.001,0.002,...],
    "uncertainty_src" : "GPC uncertainty",
    "method" : "GPC"
}
```

The full molecular weight distribution

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| value           | array of number | required | the relative probability densities at each molecular weight  |
| mol_wt          | array of number | required | the molecular weight with which the values are associated with. length of the value and mol_wt arrays should be identical |
| unit            | string          | required | unit for molecular weight. choice: 'kDa' or 'Da'             |
| method          | string          | optional | how the value is measured or obtained                        |
| uncertainty     | array of number | optional | the uncertainty of each provided value.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |

**Allowed Units**

- kDa
- Da



## GPC

> Syntax and usage

```json
"GPC" : {
    "I" : [0.01,0.01,0.02,...],
    "t": [1,1.05,1.10,...],
    "unit_t": "m",
    "calib" : "calibration-doc",
    "unit_I": "arbitrary unit",
    "uncertainty" : [0.001,0.001,0.002,...],
    "uncertainty_src" : "GPC-uncertainty",
    "method" : "GPC-spec..."
}
```

The GPC measurement data for polymer molecular weight distribution

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| I               | array of number | required | measured intensities                                         |
| t               | array of number | required | retention times. length of the I and t arrays should be identical |
| unit_t          | string          | required | unit for retention time                                      |
| calib           | string          | required | reference to calibration documentation                       |
| unit_I          | string          | optional | unit for intensity                                           |
| method          | string          | optional | specifications of the GPC                                    |
| uncertainty     | array of number | optional | the uncertainty of each intensity                            |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |

**Allowed Units for unit_t**

- s (seconds)
- m (minutes)



## comp

> Syntax and usage

```json
"comp" : {
    "seq" 	: ["A","B",["C","D"]],
    "ratio" : [0.5,0.3,0.2],
    "uncertainty" : [0.1,0.1,0.01],
    "uncertainty_src" : "error source",
    "method" : "some method"
}
```

The composition of a polymer characterized by the ratio of different repeat units.

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different repeating unit sequences. For stochastic object with *n* repeat units, the different repeat units (from the first to the last one) are represented by the symbols A,B,C,...,M,N. <br />For instance, repeat units for a polymer with three repeat units should be described by the array \["A", "B", "C"] (or any other permutation.) <br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element, e.g. [["A","B","C"],"D"]<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## comp_diad

> Syntax and usage

```json
"comp_diad" : {
    "seq" 	: ["AA","AB","BB"],
    "ratio" : [0.5,0.3,0.2],
    "uncertainty" : [0.1,0.1,0.01],
    "uncertainty_src" : "error source",
    "method" : "some method"
}
```

The composition of a polymer characterized by the ratio of different diads of repeat units.

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different repeating unit diad sequences. For stochastic object with *n* repeat units, the different repeat units (from the first to the last one) are represented by the symbols A,B,C,...,M,N. <br />For instance, repeat units for a polymer with three repeat units should be described by the array \["AA", "AB", "AC", ...] (or any other permutation.) <br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element, e.g. [["AA","AB"],"BB"]<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## comp_higher

> Syntax and usage

```json
"comp_higher_3" : {
    "seq" 	: ["AAA","AAB","ABB",...],
    "ratio" : [0.5,0.3,0.1,...],
    "uncertainty" : [0.1,0.1,0.05,...],
    "uncertainty_src" : "error source",
    "method" : "some method"
}
```

The composition of a polymer characterized by the ratio of different multi-ads of repeat units.

The name of the property follows the syntax

`"comp_higher_n" : {...}`

where *n* is an integer larger than 2 that specifies the exact order of the multi-ads.

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different repeating unit multi-ad sequences. For stochastic object with *n* repeat units, the different repeat units (from the first to the last one) are represented by the symbols A,B,C,...,M,N. <br />For instance, repeat units for a polymer with two repeat units and n=3 should be described by the array \["AAA", "AAB", ...] (or any other permutation.) <br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## tacticity_diad

> Syntax and usage

```json
"tacticity_diad" : {
    "r" : 0.5,
    "m" : 0.5,
    "uncertainty" : 0.1,
    "uncertainty_src" : "multiple samples from the same batch",
    "method" : "some method"
}
```

The tacticity of a polymeric segment described by diads  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| r               | number | required | the ratio of racemic diads                                   |
| m               | number | required | the ratio of racemic diads                                   |
| uncertainty     | number | optional | the uncertainty of the provided value.                       |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |

<aside class="notice">The two ratios should sum up to one.</aside>
## tacticity_triad

> Syntax and usage

```json
"tacticity_triad" : {
    "seq" 	: [["rr","mm"],"rm"],
    "ratio" : [0.3,0.7],
    "uncertainty" : [0.1,0.1],
    "uncertainty_src" : "error source,
    "method" : "some method"
}
```

The tacticity of a polymeric segment described by triads  

**Object Fields****

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different triads. permitted entries include "rm" (heterotactic triads), "mm" (isotactic triads) and "rr" (syndiotactic triads)<br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element, e.g. [["rr","mm"],"rm"]<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## tacticity_higher

> Syntax and usage

```json
"tacticity_higher_4" : {
    "seq" 	: ["rrr","mrm","rrm",...],
    "ratio" : [0.3,0.2,0.3,...],
    "uncertainty" : [0.1,0.1,0.1,...],
    "uncertainty_src" : "error source,
    "method" : "some method"
}
```

The tacticity of a polymeric segment described by multi-ads

The name of the property follows the syntax

`"tacticity_higher_n" : {...}`

where *n* is an integer larger than 3 that specifies the exact order of the multi-ads.

**Object Fields****

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different multi-ads. For instance, pentads include elements such as "mmmm","mrrm", ... etc.<br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## MSL

> Syntax and usage

```json
"MSL" : {
    "value" : 3,
    "uncertainty" : 0.5,
    "uncertainty_src" : "error source",
    "method" : "some method"
}
```

The average length of *meso* sequences within the polymer molecule  

**Object Fields**

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| value           | number          | required | the MSL value                                                |
| uncertainty     | array of number | optional | the uncertainty of the provided ratios.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |



## head_tail_config

> Syntax and usage

```json
"head_tail_config" : {
    "seq" 	: [["tt","hh"],"ht"],
    "ratio" : [0.1,0.9],
    "uncertainty" : [0.05,0.05],
    "uncertainty_src" : "error source,
    "method" : "some method"
}
```

The head/tail configuration of asymmetric repeating units within a polymeric segment described by diads

**Object Fields****

| Name            | Type            | Option   | Description                                                  |
| --------------- | --------------- | -------- | ------------------------------------------------------------ |
| seq             | array           | required | the array containing different head/tail placements. permitted entries include "ht" (head-to-tail), "hh" (head-to-head) and "tt" (tail-to-tail)<br />If characterization are determined in terms of the union of multiple units, the union can be specified by lumping multiple units into an array element, e.g. [["tt","hh"],"ht"]<br />The characterization for missing repeat units is assumed to be unknown. |
| ratio           | array of number | required | the ratio of different elements in the seq array.            |
| uncertainty     | array of number | optional | the uncertainty of the provided values.                      |
| uncertainty_src | string          | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string          | optional | how the value is measured or obtained                        |

<aside class="notice">The ratios should sum up to one.</aside>
## p

> Syntax and usage

```json
"p" : {
    "value" : 0.85,
    "method" : "IR quantification of residual unreacted functional groups",
    "uncertainty" : 0.05,
    "uncertainty_src" : "uncertainty in calibration between IR signal and extent of reaction"
}
```

The extent of reaction  

**Object Fields**

| Name            | Type   | Option   | Description                                                  |
| --------------- | ------ | -------- | ------------------------------------------------------------ |
| value           | number | required | the numeric value of the extent of reaction                  |
| uncertainty     | number | optional | the uncertainty of the provided value                        |
| uncertainty_src | string | optional | an explanation of what the provided uncertainty reflect and how the number/estimation is obtained |
| method          | string | optional | how the value is measured or obtained                        |

