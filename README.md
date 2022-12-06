# Final Report
## CSCI 5900- Independent Study (Fall 2022)
## Sagar Pathare
## Date: December 6, 2022

## Title: Building metadata index and applying query filter

## Motivation
- The input files to GIGGLE may contain additional metadata columns (importance, score, etc.) with arbitrary information (columns count, data types, and sizes). 
- Currently, GIGGLE ignores this metadata. 
- The only way to access the metadata after indexing is to open the original input files. 
- Could we store this metadata as a part of GIGGLE index while indexing the input files and load it while searching? 
- We would no longer need access to the input files after indexing
- We would no longer need the offset index used to access the input files
- This would be useful for STIX

## Method/implementation
- Designed metadata.conf file format to accept the metadata columns information- a list of `<column name, data type and size>`
- Stored the metadata types information in the header of metadata_index.dat file
- Stored the actual metadata information from the input files in the rest of the file
- Created a query parser which uses the metadata index to parse a simple query of the format `<column><operator><value>`
  - Query example- score>=4.59

## Results

### Time taken for Index (seconds)
| Operation              | Mean        | User       | System     |
|------------------------|-------------|------------|------------|
| Index without metadata | 2.301400604 | 2.18434688 | 0.11592476 |
| Index with metadata    | 3.217897652 | 2.90494334 |  0.3117602 |

### Time taken for Search Query (milliseconds)
| Operation                                                                        | Mean        | User        | System      |
|----------------------------------------------------------------------------------|-------------|-------------|-------------|
| Search using index without metadata                                              | 59.05192191 | 52.30880857 | 6.741488163 |
| Search using index with metadata without loading metadata                        | 58.88397734 |    53.15284 |     5.73212 |
| Search using index with metadata with loading metadata                           | 59.28336942 | 54.07237961 | 5.203470588 |
| Search using index with metadata with loading metadata and applying query filter | 61.46501216 | 53.66404174 | 7.794006087 |

### Time taken for Search Region (milliseconds)
| Operation                                                                        | Mean        | User        | System       |
|----------------------------------------------------------------------------------|-------------|-------------|--------------|
| Search using index without metadata                                              | 3.400331697 | 2.852696838 | 0.5706614229 |
| Search using index with metadata without loading metadata                        | 3.360303457 | 3.007352598 | 0.5151494662 |
| Search using index with metadata with loading metadata                           | 3.542046965 | 3.007217495 | 0.5688710866 |
| Search using index with metadata with loading metadata and applying query filter | 3.506091068 | 2.974596693 | 0.5560202362 |

**Note**: The time duration values are approximate as they are affected by other applications running in the background.
## Discussion
The time taken for indexing was increased by approximately 39%, which is fine for a one-time task. Even after building the metadata index, we still have the option not to load it, which doesn't affect the performance. Loading the metadata and applying the filter takes slightly increases the time by ~5%.
