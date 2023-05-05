# COMS4761, Spring2023, Group 18 Final Project Repository

## Notebook 1

This notebook, borrowing from https://github.com/dpeerlab/SEACells/blob/main/notebooks/SEACell_computation.ipynb, implements code that
* Builds the convex hull of SEACells metacells
* Projects single cells to the edges of this convex hull
* Projects single cells to the planes of this convex hull
* Creates the hash tables with intermediary results
* Creates the Pickle objects (downloadable) for further analysis offline.

Everything has been built and tested in Google Collaboratory.

In particular, there are the following sections of interest:
### Pickle Code
Creates Pickle objects, which can be downloaded/uploaded for further analysis in other systems or offline. These can be found and downoaded from the pickle folder
### Edge Projections
Implements:
* MB_graph(M matrix @ B matrix) -> complete graph representing the convex hull
* project_cells_MB(single cell coordinates, MB graph) -> project cells to the closest edge. Returns: the closest edge assignment, minimum distance to the closest edge, relative position on the closest edge (expressed in range(0,1)), list of distances to all edges of the MB graph.
* metacells_distance(MB graph) -> computes the distance between each two vertices of the convex hull (between each two metacells)
### Dictionaries
The following section builds dictionaries that are subsequently downloaded as Pickle objects for further, faster and more optimized, processing.
* metacell_dictionary(MB matrix) -> generates the metacells hash table with SEACell assignment as key and location as the value.
* edge_dictionary(graph of MB) -> generates the edge hash table with edge number as the key, tuple(length and 2 Metacells) as the value.
* cell_dictionary(hard SEACells Assignment , A matrix, M matrix, graph_MB, dictionary range start value, dictionary range end value) -> generates the hash table with cell number as the key, tuple(hard SEACells Assignment, closest edge, distance to all edges, relative position on the edge, weight) as the value.
### 3D Projections and Examples
* create_3combinations_matrix(MB matrix) -> simplifies the procedure of computing N choose 3 by producing a matrix whose 3 consecutive rows represent the output of N choose 3 function. Returns this matrix as well as a list which keeps track of original indices of each of the matrix rows.
* plane_graph(M matrix, original indices) -> builds a graph in which each vertex represents a plane of the N choose 3 SEACells metacells. Edges in this graph would represent the intersection between two given planes [NOT IMPLEMENTED].
* project_cell_to_graph(single cell coordinates, graph of planes) -> project the single cell to planes in the graph of planes. Returns the closest plane assignment, distances to all the planes in the graph, as well as the position on the closest plane.


## Notebook2 Analysis of Projections

This notebook contains scripts to generate statistics on the projections of the cells onto the convexhull. Borrowing from scripts in Notebook 1.
To run this script, the following pickle files need to be imported:

* intermediate_results.pickle
* edge_metacell_dicts.pickle (the edge and metacell dictionaries)
* celld.pickle

### Using the above objects it generates:
* Edge-length Distribution Histogram
* Cell projection distance Distribution  Histogram
* It finds the outlier edges for each of the cells. These are stored in the celld dictionary, under the 'zcore_3' heading.

### To generate statistics:
It creates the following dataframes:

#### cell_outlier_edge_df: columns=['cell','metacell','closest_edge','metacell_in_edge','zscore_3','zscore_len','count','cells_in_metacell']

* cell: The cell id
* metacell: Metacell Assignment
* closest_edge: Edge to which the cell is projected on
* metacell_in_edge (Boolean): Does the above edge contain the assigned MetaCell
* zscore_3: List of outlier edges
* zscore_len: Number of outlier edges
* count: How many outlier edges contain the assigned MetaCell
* cells_in_metacell: How many cells have the given Metacell Assignment


#### metacell_in_edge_df: columns=['cellid','max_weight','metacell_in_edge']

* cellid: The cell id
* max_weight: The weight of the Assigned Metacell (indicative of the confidence of assignment) 
* metacell_in_edge(Boolean): Does the above edge contain the assigned MetaCell

### Further Analysis

* It implements a function 'angle_between_vectors(v1, v2)' to get the angle in degree between two vectors

* To check if certain outlier edges are common between cells, we performed pairwaise hypergeometric test on all the possible pairs. This was computationally intensive, therefore we save the output as a pickle file: hyper_geom_test_matrix.pickle, which is a 6881 by 6881 matrix. This file needs to be imported to run the code that follows.



## Supplement to Notebook 3 

Scripts to generate the 'gene_scores.pickle' file. It also creates a new rna and atac ad object with new Metacell assignments (This is important, as in the original script these are hardcoded, we need the metacell assignment to match our current convexhull) 

This is generated by borrowing from https://github.com/dpeerlab/SEACells/blob/main/notebooks/SEACell_ATAC_analysis.ipynb

To run this script, the following pickle files need to be imported:
* celld.pickle

As output it generates:
* gene_scores.pickle
* rna_meta_ad.pcikle
* atac_meta_ad.pickle

For the existing instance, importing the 'gene_peak_cors.pickle' to this notebook should directly allow you to get the gene_scores. But for every new instance of Convexhull the whole process needs to be repeated.


## Notebook 3 Regulatory And Structural Correlations

Contains scripts to create the approprite dataframes and generate statistics to infer correlation between the structure of the convex hull and regulatory network of the metacells. 

To run this script, the following pickle files need to be imported:

* intermediate_results.pickle
* edge_metacell_dicts.pickle (the edge and metacell dictionaries)
* celld.pickle
* gene_scores.pickle
* rna_ad.pickle

Using rna_ad it computes the 1500 highly variable genes. For further analysis we compare gene_scores only for these genes.

It creates the following dataframes:

#### metacell_pairwise_del_score

* metacell_pair: Which two Metacells are we comparing?
* <Gene Name>: 1500 columns that store the difference in gene score for said gene among the two metacells.
* edge_distances: Lenght of Edge connecting the Metacells


#### max5_delta

The metacell_pairwise_del_score df serves as input for the construction of the max5_delta, which serves as the basis for computing the correlation between edge lenght and regulatory differences.

* Max1_delscore: For a given Metacell Pair, which gene score is the furthest apart (We just store the numeric value, not the gene name) 
* Max2_delscore: Similarly, for a given Metacell Pair, which gene score is the next most furthest apart  
* Max3_delscore: Third most
* Max4_delscore: Fourth most
* Max5_delscore: Fifth most
* Dist_metacells: Lenght of Edge connecting the Metacells

### Dictionary

#### pair_gene_score_diff

for each metacell_pair it stores the list of genes that have differ by a gene score of greater than 1.2, and also the corresponding scores.

* Key: Metacell_Pair
* Value: Another Dictionary with that stores 2 lists 'genes':genes that differ in the extent of regulation 'score_diff': the corresponding score_diff
 
Using the above dictionary, we perform hypergeomtric test to see if certain edges have common differentially regulated genes. The results are stored in the hyper_geom_test_matrix object.

To be more precise, we asked whether any two edges share the exact same geneset. We thought this would be a stronger indication of redundancy in the ConvexHull. The results of this are stores in equal_gene_set object.




