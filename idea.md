# Ideas

In this document, we investigate query planning optimization using the [shape-index specification](https://github.com/constraintAutomaton/shape-index-specification).

## Query Planning Optimizations

Optimizations possible with shape indexes can be categorized into:

- **Source selection**: Determining which sources should participate in a join.
- **Cardinality estimation**: Estimating the number of results a query pattern will produce.
- **Choice of join algorithms**: Selecting the most efficient join strategy based on available data and constraints.


## Source Selection

Source selection appears could have a high impact and is conceptually intuitive.

### Research Questions  

#### 1. Can join source selection in LTQP reduce query execution time?  
How much does join source selection impact query performance, and under what conditions is it beneficial?  

#### 2. How does source pruning affect the impact of join source selection?  
Does the effectiveness of join source selection impacted by prior pruning of irrelevant sources in the link queue?  

#### 3. What is the optimal query performance achievable with perfect join source selection?  
By using an oracle to select the best sources, we determine the upper bound of possible performance improvements.  

#### 4. What is the overhead of using an adaptive execution plan compared to a static plan?  
We compare an adaptive approach to a static, precomputed source assignment to assess the trade-offs in efficiency and complexity.  


### Approach

#### Triple pattern assignation

Consider that LTQP can be treated as a federated query system with a dynamic federation. At any given time $i$, the federation is defined as:

$$ F_i = \{f_j \mid 1 \le j \le  n_i \} $$

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

#### Join Assignment

The previous section focused on the acquisition of triple patterns rather than the join operation. However, a similar and potentially more impactful optimization can be applied to joins.

The join between two solution mappings, $\sigma_i$ and $\sigma_j$, is defined as:

$$
\sigma_i \Join_k \sigma_j
$$

Given that each solution mapping $\sigma$ is associated with a set of triple patterns ($tp$s), we introduce two related sets:  
- **$prov$**: Represents the provenance of the triple, a set of $f$ elements indicating where-provenance.  
- **$nFtp$**: Contains $f$ elements known to be **triple-pattern-irrelevant**.  

If all elements of $prov$ are present in $nFtp$, then the join operation will produce no results and can be safely ignored.

### Current State

- Each $f_j$ is assigned indiscriminately to every $tp$.  
- There is no visibility into the actual source each $tp$ is assigned to.  
- The most performant version of LTQP relies on the aggregated store, but there is currently no method to determine the provenance of triples within it.  
- There is no mechanism to adapt the query plan dynamically.  


### Next Steps

1. Implement a mechanism to assign sources to triple patterns in LTQP and in intermediary query solutions (might already exist).
    - Maybe some sort of "virtual QuerySources"
    - Being able to dynamically add QuerySources to join
2. Integrate source visualization within the LTQP physical plan.
3. Manually create an optimal source assignment for comparison.
4. Evaluate performance using SolidBench queries.
5. Design and implement an algorithm to automate source assignment using a shape index.
6. Evaluate the performance using SolidBench queries.

#### Paper

Potential name: **A Dynamic Federation Perspective on Link Traversal Querying: Leveraging RDF Data Shapes for Source Selection in Joins**