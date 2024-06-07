# Fairness Metrics Ontology for Clustering

The Fairness Metrics Ontology for Clustering (FMO-Clust) is an OWL2 ontology for the standardized representation of fairness metrics, fairness notions, and related machine learning concepts (including bias mitigation algorithms, machine learning models, and statistical metrics) for the clustering domain. Its primary goal is to guide AI/ML developers in the measurement and interpretation of clustering model fairness, as well as to aid ML fairness researchers by cataloguing emerging concepts in the fast-evolving field fair clustering research in a standardized and provenance-aware way. FMO-Clust has been designed from the ground up to fully support semantic technologies, including reasoning based on OWL2 restrictions, question-answering through description logic or SPARQL queries, and the instantiation of classes as individuals in an RDF knowledge graph to represent user-specific data.

_Version Information_: FMO-Clust is the newest addition to the fairness metrics ontology family: the other two ontologies are the fairness ontology for classification (FMO-Class or FMO-C) and the fairness ontology for regression (FMO-Reg or FMO-R), and one for clustering (FMO-Clust). FMO-Clust was released on April 18th, 2024. For more information, see [Version History](https://github.com/frankj-rpi/fairness-metrics-ontology/blob/main/README.md#version-history).

_Persistent URL_: To directly link to the latest OWL file, use [purl.org/twc/fmo-clust/latest](https://purl.org/twc/fmo-clust/latest). Currently, the latest version is [purl.org/twc/fmo-clust/2.0.0](https://purl.org/twc/fmo-clust/2.0.0).

_Canonical Citation_: Use the citation for the fairness metrics ontology for clustering paper (_pending publication_).

_Examples of Usage_: See [Competency Questions](https://github.com/frankj-rpi/fairness-metrics-ontology/blob/main/competency-questions.md) for examples of usage with description logic queries. For details on the FMO-Clust-based analysis of fair clustering models, see [the project report](https://frankj-rpi.github.io/fairness-metrics-ontology/clustering/Fairness%20Evaluation%20of%20Clustering%20Models.html) We are also currently in the process of updating our interactive [fairness metric explorer](https://inciteprojects.idea.rpi.edu/fairness/app/fairness/) to use clustering metrics: see [the explorer repository](https://github.com/tetherless-world/fairness) for examples of usage in a complex application via dynamically generated SPARQL queries.

For more information about the Fairness Metrics Ontology project as a whole, see [the main page](https://github.com/frankj-rpi/fairness-metrics-ontology/blob/main/README.md).

## FMO-Clust Structure

The following is a high-level overview of the fairness metrics ontology for clustering:

![High-level concept map](../diagrams/HL-concept-map.png "High-Level Concept Map")

Broadly, FMO-Clust consists of fairness metrics, fairness notions, and ML models. Each **Fairness metric** measures a conceptual **fairness notion** that is an attribute of an **ML model**. ML models are created via ML algorithms, including bias mitigation methods to mitigate bias according to a specific fairness metric or notion.

The full ontology is shown in diagram form below:

![High-level concept map](../diagrams/FairnessOntologyFull.png "Full Concept Map")

