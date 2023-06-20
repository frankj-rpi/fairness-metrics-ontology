# Competency Questions

Each of these competency questions was encoded in DL and queried of the ontology, running in [Protege 5.5.0](https://protege.stanford.edu) with the [Pellet 2.2.0](https://github.com/stardog-union/pellet) reasoner plugin.

A basic set of 3 generic competency questions could take the following format:
- Which fairness notion(s) match the user's requirements?  
  *Example requirements*:
  - The fairness notion must work with a specific ML clustering algorithm or algorithm type.
  - The fairness notion must or must not be algorithm-agnostic.
  - The fairness notion must be based on either the group fairness notion or the individual fairness notion.
  - The fairness notion must be related to a specific existing fairness notion for clustering.
- Which fairness metric(s) can be used to measure a given fairness notion, and match the user's requirements?  
  *Example requirements*:
  - The fairness metric must work with a specific ML clustering algorithm or algorithm type.
  - The fairness metric must combine information from across different clusters or points using a specific method (i.e., max, average, etc.).
  - The fairness metric must use a specific sub-metric for the measurement of fairness for individual clusters, points, etc.
  - The fairness metric must specifically result in a cost to be minimized, or an objective to be maximized.
- Which bias mitigation method(s) can be used with a given fairness metric or notion, and match the user's requirements?  
  *Example requirements*:
  - The bias mitigation method learns using a specific ML clustering algorithm or algorithm type.
  - The bias mitigation method must operate at a specific point in the pipeline (pre-processing, in-processing, or post-processing).
  - The bias mitigation method should use a specific strategy for reducing bias (i.e., minimizing the most unfair cluster first, versus minimizing across the board).

We provide example DL queries that correspond to competency questions for [classification-specific](#classification-specific), [clustering-specific](#clustering-specific), and [regression-specific](#regression-specific) usage scenarios below. For each query, on the left, the actual description logic used to make the query is shown.
On the right, a line-by-line explanation of this description logic is shown, detailing the specific requirement each line corresponds to.
At the bottom, the results of running the query against the actual ontology are shown. Each DL statement queries for subclasses unless otherwise stated.

[todo]: <> (,with predicates in _italics_ and classes in **bold**)

## Classification-specific

### Usage Scenario 1

Consider the case study described in the [original paper](https://dl.acm.org/doi/10.1145/3514094.3534137). An ML developer has trained a loggistic regression classification model on synthetic patient data&mdash;data artificially generated to match real patient data but with identifying characteristics removed. In order to test the effectiveness of the model trained on synthetic data, the developer measures its results with Balanced Accuracy -- a statistical metric derived from sensitivity (true positive rate, or TPR) and specificity (true negative rate, or TNR). 

However, the developer is also concerned about whether or not the synthetic data, and by extension, the model, is unfair. Therefore, they also want to select a fairness notion that effectively works with TPR and TNR (or their inverses, FNR and FPR), which are used to calculate balanced accuracy, and some additional fairness notions to help interpret that result. 

### QUERY 1.1

Return fairness notions that are applicable to an ML model measured with balanced accuracy, as described in the [provided usage scenario](#usage-scenario-1). 

| Description logic statement    | Corresponding requirement |
| ------------------------------ | ------------------------- |
|'classification fairness notion'| Return only fairness notions for classification |
|and 'is derived from' some ('true positive rate' or 'false negative rate')| Must be derived from either TPR or FNR |
|and 'is derived from' some ('true negative rate' or 'false positive rate')| Must be derived from either TNR or FPR |

| Query 1.1 results |  |
| ------------------------------- | ---------------------------- |
| 'equalized odds' | 'total fairness' |

The developer will next examine superclasses of both equalized odds and total fairness, to see which other fairness notions are implied by each of them. This will help the developer select additional fairness notions to measure in order interpret the final result, as well as understand what notions must be required in order for equalized odds and total fairness to be satisfied.

### QUERY 1.2

Return classification fairness notions that are implied as a result of equalized odds, i.e., they are required to be true if equalized odds is true. Metrics measuring each of these notions individually can be used to help interpret the model result.

| Query for    | Description logic statement    | Corresponding requirement |
| -------------| ------------------------------ | ------------------------- |
| Subclasses   |'classification fairness notion'| Return only fairness notions for classification |
| Superclasses |'equalized odds'                | Return fairness notions that are implied by equalized odds|

| Query 1.2 results |  |
| ------------------------------- | ---------------------------- |
| 'equal opportunity' | 'predictive equality' |

These results tell the developer that both equal opportunity and predictive equality must be satisfied for equalized odds to be satisfied.

### QUERY 1.3

Return classification fairness notions that are implied as a result of total fairness, i.e., they are required to be true if total fairness is true. Metrics measuring each of these notions individually can be used to help interpret the model result.

| Query for    | Description logic statement    | Corresponding requirement |
| -------------| ------------------------------ | ------------------------- |
| Subclasses   |'classification fairness notion'| Return only fairness notions for classification |
| Superclasses |'total fairness'                | Return fairness notions that are implied by total fairness|

| Query 1.3 results |  |
| ------------------------------- | ---------------------------- |
| 'conditional use accuracy equality' | 'predictive equality' |
| 'equal negative predictive value' | 'predictive parity' |
| 'equal opportunity' | 'statistical parity' |
| 'equalized odds' | |

These results tell the developer that total fairness is an extremely stringent fairness notion with a lot of requirements, and would not only be difficult to satisfy, but difficult to interpret as well. The developer elects to use equalized odds instead.

### QUERY 1.4

The developer wants to understand how equal opportunity and predictive equality relate back to their original requirements of measurement of TPR/FNR and TNR/FPR. They use the following two queries to test for how these two notions relate back to these two statistical metrics.

#### Query 1.4.1

Which notions are derived from TPR or FNR?

| Description logic statement    | Corresponding requirement |
| ------------------------------ | ------------------------- |
|'classification fairness notion'| Return only fairness notions for classification |
|and 'is derived from' some ('true positive rate' or 'false negative rate')| Must be derived from either TPR or FNR |

| Query 1.4.1 results |  |
| ------------------------------- | ---------------------------- |
| 'balance for positive class'    | 'equalized odds' |
| 'equal opportunity'             | 'total fairness' |

This tells the developer that if equal opportunity is statisfied, then the TPR/FNR is fair.

#### Query 1.4.2

Which notions are derived from TNR or FPR?

| Description logic statement    | Corresponding requirement |
| ------------------------------ | ------------------------- |
|'classification fairness notion'| Return only fairness notions for classification |
|and 'is derived from' some ('true negative rate' or 'false positive rate')| Must be derived from either TNR or FPR |

| Query 1.4.2 results |  |
| ------------------------------- | ---------------------------- |
| 'balance for negative class'    | 'predictive equality' |
| 'equalized odds'                | 'total fairness' |

This tells the developer that if equal opportunity is statisfied, then the TPR/FNR is fair.

### Usage Scenario 1 Conclusion

The developer uses the difference from equalized odds to measure overall fairness, and the difference in equal opportunity and predictive equality to understand what kinds of unfairness are contributing to the equalized odds difference&emdash;whether it is due to inequality in TPR/FNR, inequality in TNR/FPR, or both. To see the actual results of the case study, see section 5.1 of [An Ontology for Fairness Metrics](https://dl.acm.org/doi/10.1145/3514094.3534137).

## Clustering-specific

### Usage Scenario 2

An ML developer wants to find a way to mitigate the unfairness of their clustering-based ML model, trained with some variation of the \textit{k}-center method. Their selected notion must work with any _k_-center algorithm, and should be a group fairness notion. Their selected metric must work with their selected notion, and minimize a cost by either looking at the maximum amount of unfairness in each cluster or by taking the average across all clusters. Lastly, as the developer already has a clustering-based ML algorithm implemented for their model, they require their bias mitigation method to be either pre-processing or post-processing.

The developer uses three queries to select a notion [Query 2.1](#query-21), metric [Query 2.2](#query-22), and bias mitigation method [2.3](#query-23). Note that the last row in each of Query 2.2 and Query 2.3 contains the copied text of the previous query, as the developer expands their search to metrics that work with any possible notion from Query 2.1, or mitigation methods that work with any possible notion from Query 2.2, but they could choose to replace this text with a specific notion or metric instead to further narrow down their results. 

Beyond the base results shown for each query, if the developer needs more information--such as whether a notion is algorithm-agnostic or algorithm-specific, or which specific algorithm the notion works with, they can examine the explanation for each result. Figure \ref{fig:explanations} shows the explanation generated in the Proteg\'e for one of the query 1 results--the ``Bounded Representation Fairness" fairness notion. Using this explanation, the user can determine why exactly this query result meets all of the requirements of the query; in the case of Bounded Representation Fairness, the explanation shows that it is an algorithm-agnostic notion, and that it is a group fairness notion because it is a notion related to the group-level Independence notion of classification. Using these explanations, the developer can also examine the results of queries 2 and 3, in order to determine exactly why they meet each of the requirements and which notions, metrics, algorithms, etc. they correspond to. 

### QUERY 2.1
Return fairness notions that match the requirements of the ML developer's existing model in the [provided usage scenario](#usage-scenario-2). 

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'clustering fairness notion' | Return only fairness notions for clustering |
|and 'group-level fairness notion' | Return only group fairness notions |
|and ('algorithm-agnostic fairness notion'  <br> or  'is attribute of' some ( 'is derived from' some (k-Center))) | Return either algorithm-agnostic fairness notions,  <br> or notions that correspond to the _k_-center algorithm |

| Query 2.1 results |  |
| ------------------------------- | ---------------------------- |
| 'bounded representation fairness' | 'cluster balance equality' |
| 'cluster entropy fairness' | 'cluster proportion preservation' |
| 'essential cluster fairness' |  'fair summary fairness' |
| 'max fairness cost' |  |

### QUERY 2.2
Return fairness metrics that measure a notion obtained via [Query 2.1](#query-21), and match the requirements in the [provided usage scenario](#usage-scenario-2).

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'clustering fairness metric' | Return only fairness metrics for clustering |
|and 'is derived from' some ('maximal value' or sum) | Return only fairness metrics that report a maximum over all<br>clusters, or sum from all points across all clusters (i.e., have a cost) |
| and 'is measurement value of' some (<br>&nbsp;&nbsp;'clustering fairness notion'<br>&nbsp;&nbsp;and 'group-level fairness notion'<br>&nbsp;&nbsp;and ('algorithm-agnostic fairness notion' or 'is<br>&nbsp;&nbsp;attribute of' some ('is derived from' some (k-Center)))<br>) | Measures a notion from Query 2.1<br>(description logic here contains entire copied text of Query 2.1) |

| Query 2.2 results               |                                |
| ------------------------------- | ------------------------------ |
| 'FairKM clustering objective'   | 'clustering entropy'           |
| 'group egalitarian clustering'  | 'group utilitarian clustering' |

### QUERY 2.3
Return a bias mitigation method that uses a metric obtained via [Query 2.2](#query-22), and match the requirements in the [provided usage scenario](#usage-scenario-2).

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'bias mitigation method'     | Return only bias mitigation methods |
| and 'pre-processing bias mitigation method' | Return only pre-processing bias mitigation methods |
| and 'is derived from' some (<br>&nbsp;&nbsp;'clustering fairness metric'<br>&nbsp;&nbsp;and 'is derived from' some ('maximal value' or sum)<br>&nbsp;&nbsp;and 'is measurement value of' some (<br>&nbsp;&nbsp;&nbsp;&nbsp;'clustering fairness notion'<br>&nbsp;&nbsp;&nbsp;&nbsp;and 'group-level fairness notion'<br>&nbsp;&nbsp;&nbsp;&nbsp;and ('algorithm-agnostic fairness notion'<br>&nbsp;&nbsp;&nbsp;&nbsp;or 'is attribute of' some (<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'is derived from' some (k-Center))<br>&nbsp;&nbsp;&nbsp;&nbsp;)<br>&nbsp;&nbsp;)<br>) | Return only mitigation methods that use a metric from Query 2.2<br>(description logic here contains entire copied text of Query 2.2) |

| Query 2.3 results       | 
| ------------------------|
| 'fairlet decomposition' |

### Usage Scenario 2 Conclusion

By examining the explanation for [Query 2.3](#query-23), the developer can see that fairlet decomposition was returned because it is a pre-processing bias mitigation methods that uses clustering entropy (a result given in [Query 2.2](#query-22)), and that clustering entropy is valid because it measures cluster entropy fairness (a result given in [Query 2.1](#query-21)). The developer elects to use fairlet decomposition to minimize clustering entropy, in order to develop a model that satisfies the cluster entropy fairness criterion.

## Regression-specific 

These three competency questions each try to cover separate areas newly introduced in FMO-R: the worldviews, the new statistical metrics that some fairness notions and metrics are derived from, and high-level notions. They are intended as models that can be adjusted to meet a user's specific needs.

### QUERY 3.1
Which notion of fairness is a member of the WAE
worldview and is derived from expected value?

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'regression fairness notion' | Return only fairness notions for regression |
|and 'is member of' some WAE | Return only fairness notions in the WAE worldview |
|and 'is derived from' some 'expected value' | Return only fairness notions that are derived from expected value |

| Query 3.1 results       | 
| ------------------------|
| 'mean difference' |


### QUERY 3.2
Which fairness metrics measure the high-level
notion of Independence and are derived from a
pairwise comparison between each individual?

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'regression fairness metric' | Return only fairness notions for regression |
|and 'is measurement value of' some 'independence-class fairness notion' | Return only fairness notions from the Independence high-level notipon |
|and 'is derived from' some 'pairwise comparison' | Return only fairness notions that are derived from a pairwise comparison of its members |

| Query 3.2 results               |                                |
| ------------------------------- | ------------------------------ |
| 'cross-pair group fairness'     | 'pairwise statistical parity'  |

### QUERY 3.3

Which bias mitigation methods are derived from
a metric that measures the high-level notion of loss (AKA, equal accuracy)?

| Description logic statement | Corresponding requirement |
| --------------------------- | ------------------------- |
|'bias mitigation method' | Return only bias mitigation methods |
|and 'is derived from' some ('regression fairness metric<br>&nbsp;&nbsp;and 'is measurement value of' some 'loss-class fairness notion'<br>) | Return mitigation methods that are derived from a metric<br>that measures some high-level notion of loss. |

| Query 3.3 results               |                                |
| ------------------------------- | ------------------------------ |
|'constrained optimization'       |'robust optimization'           |
