# Ideas

In this document, we investigate query planning optimization using the [shape-index specification](https://github.com/constraintAutomaton/shape-index-specification).

## Query Planning Optimizations

Optimizations possible with shape indexes can be categorized into:

- **Source selection**: Determining which sources should participate in a join.
- **Cardinality estimation**: Estimating the number of results a query pattern will produce.
- **Choice of join algorithms**: Selecting the most efficient join strategy based on available data and constraints.


## Source Selection

Source selection appears could have a high impact and is conceptually intuitive.

### Approach

Consider that LTQP can be treated as a federated query system with a dynamic federation. At any given time $i$, the federation is defined as:

$$ F_i = \bigcup_{j=1}^{n_i} f_j $$

where $n_i$ represents the number of federation members $f_j$.

Each $f_j$ contains a shape $S_j$, which is initially an open shape.
Suppose that a shape index $SI_k$ can be generated from the triples within a $f_j \in F_i$. 
This shape index $SI_k$ maps shapes $S$ to specific $f_j$ instances.

Using this mapping, the shape $S_j$ in $f_j$ can be updated accordingly.
Assuming the mapping is unique across all $SI_k$ instances.

Each triple pattern $tp$ is assigned to a set of $f_j$. Over time, the number of $f_j$ assigned to each $tp$ may increase.

To improve discrimination in the assignment process, we could:
- Group triple patterns as [graph star patterns (GSP)](https://github.com/constraintAutomaton/Traveling-with-a-Map).
- Check whether the shape $S$ in each $f_j$ subsumes the $GSP$.
- Upon validation, assign $f_j$ to $tp$.

### Current State

- Each $f_j$ is currently assigned to every $tp$ indiscriminately.

### Next Steps

1. Investigate whether source assignment can be derived from the shape index.
2. Explore the possibility of manually creating an optimal source assignment.
3. Evaluate performance improvements with the refined source assignment.

