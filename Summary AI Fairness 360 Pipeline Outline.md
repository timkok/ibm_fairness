# AI Fairness 360: Pipeline Tutorial Summary
## Introduction

##### Key Terms [1]
- **Favorable Label** - a label whose value corresponds to an outcome that provides an advantage to the recipient
- **Protected Attribute** - an attribute that partitions a population into groups that have parity in terms of benefit received
- **Privileged Value** - indicates a group that has historically been at a systematic advantage
- **Group Fairness** - the goal of groups defined by protected attributes receiving similar treatments or outcomes
- **Individual Fairness** -  the goal of similar individuals receiving similar treatments or outcomes
- **Bias** - a systematic error
- **Fairness Metric** -  a quantification of unwanted bias in training data or models
- **Bias Mitigation Algorithm** -  a procedure for reducing unwanted bias in training data or models

## The Pipeline
![AI Fairness 360 Pipeline example](https://pantelis.github.io/cs634/docs/common/projects/fairness/images/ibm-fair-pipeline.png#center)
Figure 1: Generic Pipeline for Bias Mitigation Example

##### Key:
- **Rectangles** - output that represents a new dataset that shares, at least, the same protected attributes as other datasets in the pipeline
- **Trapezoids** -  represent learned models that can be used to make predictions on test data
- **Transitional Arrows** - a transformation that may modify the features or labels or both between its input and output
##### Description:
There can be one of three paths taken to get a result of a fair prediction, all of which are detailed below. Each relates to one category of bias mitigation algorithms that has an input and an output after processing. 

## Classes
### Dataset Class
> The `Dataset` class and its subclasses are a key abstraction that handle all forms of data. Training data is used to learn classifiers. Testing data is used to make predictions and compare metrics. [1]

All the associating protected attributes, features, labels, etc for each instance or record are presented and accessible from each instance of a class. The `Dataset` class also serves as a structure that holds data to be used by bias mitigation algorithms or metrics and allows for equality comparisons between two datasets using python's `==` operator. For a subset of fields, `temporarily_ignore` must be used such as:
``` 
with transf.temporarily_ignore(‘labels’):
    return transf == pred
# returns True if the two datasets transf and pred differ only in labels.
```
The `split` method can also be applied to partition a dataset into subsets for training, testing, validation, etc. In addition, metadata is tracked for each instance in a transparent way: 
>After each modification by an algorithm, a new object is created and a pointer to the previous state is kept in the metadata along with details of the algorithm applied.[1]

##### Types of Dataset Classes[1]:
- `StructuredDataset` - The basic type of dataset class.
- `BinaryLabelDataset` - A `StructuredDataset` with a single label variable per instance that can only be favorable or unfavorable.
- **Unstructured data** - Data supported by a parallel class to the `StructuredDataset` class. It does not affect existing classes or algorithms that are not associate with unstructured data.
- `StandardDataset` - Standardizes the process of loading a dataset from CSV format and populating the necessary class attributes. It also allows for user-defined data pre-processing, provides a simple interface for the user to specify the columns of a Pandas `DataFrame` that correspond to various features, labels, and protected attributes. 

### Metrics Class
The `Metrics` class and related subclasses are responsible for computing and calculating metrics in order to determine the presence of bias. 

The `DatasetMetric` class takes as input `StructuredDataset`, along with its subclass of `BinaryLabelDatasetMetric` taking the input of `BinaryLabelDataset`, to determine the group fairness measures of **disparate (DI)**, **statistical parity difference (SPD)**, and the individual fairness measure consistency. Relating this to the Figure 1. mentioned above, this class would typically be applied during **pre-processing**, or just directly to the original or transformed datasets. 

The `ClassificationMetric` class has two inputs: the original/transformed dataset with *true labels*, the predicted/fair-predicted dataset with *predicted labels*. Its purpose is to check for accuracy and fairness.

The `SampleDistortionMetric` class has two inputs: a single point that exists in both the original and transformed datasets. Its purpose is to check for changing distances between the two versions of the same point. 

### Explainer Class
The `Explainer` class and related subclasses are responsible for providing insights, details, and reasoning in conjunction with the results that come from applying the `Metrics` class. These reports are done in a "pretty print" JSON formatted output. 

##### Reporting Subclasses
`TextExplainer` returns a plain text string with a metric value, for both privileged and unprivileged insstances. 
`JSONExplainer` extends the former and produces a JSON output with attributes:
1. meta-attributes (i.e name, description, etc.)
2. statistical attributes (i.e derived and raw values)
3. plaintext explanation from `TextExplainer`

##### Fine-grain Localization
In order to determine the source of bias at a fine granularity, the class can find values where a particular metric is diminished or enhanced compared to the entire group in the protected attribute space, and find objectionable fairness metrics across all values in a feature space. 

### Algorithms Class
>Bias mitigation algorithms attempt to improve the fairness metrics by modifying the training data, the learning algorithm, or the predictions. These algorithm categories are known as pre-processing, in-processing, and post-processing, respectively (d’Alessandro et al., 2017) [1]

The three categories are thus based on the stage in the machine learning pipeline in which the algorithm can intervene. The IBM Fairness pipeline has 9 algorithms spanning these three categories and can be implemented by inheriting from the `Transformer` class, which acts on a `Dataset` instance and returns a new `Dataset` object. 

#### Pre-Processing - modification of training data
The pre-processing methods are often used at instances where the training data can be updated, and the outcome understood by metrics. 

##### The four main pre-processing bias mitigation algorithms are:
1. **Reweighing (Kamiran & Calders, 2012)** - This algorithm weighs the training examples in each combination of groups and labels in a different way. 
2. **Optimized preprocessing (Calmon et al., 2017)** - This algorithm transforms the data features and labels in a likely way based on group, individual, and overall fidelity considerations.
3. **Learning fair representations (Zemel et al., 2013)** - This algorithm encodes the data whilst blurring protected attributes.
4. **Disparate impact remover (Feldman et al., 2015)** - This algorithm keeps rank ordering but changes features to increase group fairness. 

#### In-Processing - modification of learning model 
The pre-processing methods are often used at instances where the learning model can be updated, and the outcome understood by metrics. 

##### The two main in-processing bias mitigation algorithms are:
1. **Adversarial debiasing (Zhang et al., 2018)** - This algorithm attempts to maximize prediction accuracy while reducing the ability of an adversary to determine the protected attribute, which leads to a more fair classifier. 
2. **Prejudice remover (Kamishima et al., 2012)** - This algorithm simply adds a term to the learning objective which aims to regulate discrimination that it is aware of.

#### Post-Processing - no modification of prior two
The post-processing methods are often used at instances where the training data or the learning models can-not be updated. The outcome of the bias mitigation can be understood by the metrics.

##### The three main post-processing bias mitigation algorithms are:
1. **Equalized odds post-processing (Hardt et al., 2016)** - The algorithm equalizes the odds by changing the labels to produce an unbiased outcome for privileged and unprivileged group on the protected variable. 
2. **Algorithms Calibrated eq. odds postprocessing (Pleiss et al., 2017)** - Calibrated algorithm extends the equalized odd based on the probability of the classifier output.
3. **Reject option classification (Kamiran et al., 2012)** - The algorithm works on the option of classification of threshold. The algorithm mitigates bias by manipulating the outcomes of the privileged and unprivileged group at the threshold before the prediction. 

## Citations
1. https://arxiv.org/pdf/1810.01943.pdf
2. https://pantelis.github.io/cs634/docs/common/projects/fairness/
