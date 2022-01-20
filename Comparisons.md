# Comparison of Pre-Processing Reweighting and In-Processing Adversarial Debiasing as Methods for Detecting and Removing Bias

One of the four main methods of pre-processing bias mitigation algorithms presented in the AIF360 package is **reweighing** (Kamiran & Calders, 2012) which weighs the training examples in each combination of groups and labels in a different way. In comparison, one of the main in-processing methods is **adversarial debiasing** (Zhang et al., 2018) which attempts to maximize prediction accuracy while reducing the ability of an adversary to determine the protected attribute, leading to a more fair classifier.

For our project, we have chosen these two methods to process the Statlog (German Credit Data) Data Set in order to identify and remove age bias presented in it. These two methods, although in the same AIF360 tool kit, act in different ways to perform their similarly goaled jobs, as can be seen by comparing the different fairness metrics resulting from running either algorithm. 

Overall, the accuracy shown by using the **adversarial debiasing (AD)** method was better than the accuracy displayed by pre-processing **reweighing (RW)**. However, we can try to measure the effectiveness of the bias mitigation algorithms by comparing the following metrics:
##### Equal Opportunity Difference
This metric represents the difference of the ratios of true positives to the total actual positives in a group, between the unprivileged and the privileged groups. 

`RW: Equal Opportunity Difference = -0.37`

`AD: Equal Opportunity Difference = 0.042`

A value 0 is a goal for this attribute. As we can observe that the value of this on reweighting is -0 which means benefit for the privileged group. After applying the adversarial debiasing we could see that it changed to 0.04 which means a value very close to 0 and also beneficial for the unprivileged group.
##### Average Odds Difference
This metric represents the difference between false positives divided by negatives and and true positives divided by positives, between unprivileged and privileged groups. 

`RW: Average Odds Difference = -0.17`

`AD:  Average Odds Difference = 0.12`

A value less than zero for this metric indicates a beneficial outcome for the privileged group. As we can see that by reweighting the value of this metric is -0.17 which after applying the adversarial debias improved to  0.12, closer to the ideal value of 0 and that too beneficial for the underprivileged group.
##### Disparate Impact
This metric represents the ratio between the rates of favorable outcome between the unprivileged and privileged groups.

`RW: Disparate impact = 0.56`

`AD: Disparate impact = 1.11`

A value lower than 1 means that the outcome is more positive for privileged group while value greater than 1 shows more positive outcomes for unprivileged group. As we can see that by applying adversarial debias we have achieved a value of 1 which is the goal for the metrics.
##### Theil Index
This metric represents inequality in benefit allocation for individuals in the data set by calculating the generalized entropy of benefit using α = 1.

`RW: Theil Index= 0.31`

`AD:  Theil Index = 0.19`

The ideal value for theil index metric is 0. We can see that applying the adversarial debiasing has a small but positive impact to mitigate bias.
##### Classification Accuracy and Balanced Classification accuracy
We could see that the classification accuracy is impacted negatively by a small percentage however the balanced classification summary has improved.

#### Conclusion
We can clearly see that the metrics for bias identification indicate a marked improvement by applying Adversarial Debiasing as compared to the Reweighting. All the metric values have improved to move towards the ideal goal for the metrics.
