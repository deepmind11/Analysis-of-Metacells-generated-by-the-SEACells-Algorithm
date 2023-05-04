# COMS4761, Spring2023, Group 18 Final Project Repository

## Notebook 1

This notebook, borrowing from https://github.com/dpeerlab/SEACells/blob/main/notebooks/SEACell_computation.ipynb, implements code that
* Builds the convex hull of SEACells metacells
* Projects single cells to the edges of this convex hull
* Projects single cells to the planes of this convex hull
* Creates the hash tables with intermediary results
* Creates the Pickle objects (downloadable) for further analysis offline.

In particular, there are the following sections of interest:
### Pickle Code
Creates Pickle objects, which can be downloaded/uploaded for further analysis in other systems or offline.
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
