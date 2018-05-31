# discover_feature_relationships

Attempt to discover 1D relationships between all columns in a DataFrame using scikit-learn (RandomForests). 

The goal is to see if we can better understand the data in a DataFrame by learning which features (1 column at a time) predict each other column. This code attempts to learn a predictive relationship between the Cartesian product (all pairs of columns) of all columns.

Rather than just learning which column(s) predict a target column, we might want to know what other relationships exist (e.g. during Exploratory Data Analysis) and whether some predictive features are driven by other less-predictive features (to help us find new & better features or data sources). We might also sense-check out data by checking that certain relationships exist.

By default it assumes every target column is a regression challenge. You can provide a list of columns to treat as classification challenges.

## Titanic example

* Embarked (classification) is predicted well by Fare, also by Age
* Pclass (regression) is predicted by Fare
* Fare (regression) is poorly predicted by Pclass
* Sex (classification) is predicted well by Survived
* Survived (classification) is predicted well by Sex, Fare, Pclass, SibSpParch
  * Predicting this feature at circa 0.62 is equivalent to "no information" as 0.62 is the mean of Survived
* SibSpParch is predicted by both SibSp and Parch (SibSpParch is the sum of both - it is an engineered additional feature) - it is also predicted by Fare
* SibSp and Parch are also predicted by Fare (but less well so than by SibSpParch)

![alt text](example_titanic_output.png)

This is generated using:
```
df = pd.read_csv("titanic_train.csv")
...

import discover
df_results = discover.discover(cols, classifier_overrides, df)

df_results.pivot(index='target', columns='feature', values='score').fillna(1) \
.style.background_gradient(cmap="viridis", low=0.3, high=0.0, axis=1) \
.set_precision(2)
```

## Boston example

* CRIM is predicted by NOX
* DIS is predicted by INDUS and NOX
* INDUS is very well predicted by NOX
* NOX is reasonable well predicted by INDUS, also by TAX
* PTRATIO is predicted by INDUS and NOX
* RAD seems to be very highly predicted by INDUS and NOX and TAX and almost as well by PTRATIO - why is this?
* TAX is highly predicted by INDUS and NOX, also PTRATIO and RAD
* target is lightly predicted by LSTAT, NOX, RM, TAX and PTRATIO



# Requirements

* scikit-learn (0.19+)
* pandas
* jupyter notebook

# TODO

* how many nans dropped?
* use auc not accuracy for classifier
* for Titanic graph some of these relationships
* add `all_1s_` additional column to simulate "no information"
* use CV rather than train_test_split

# Note to Ian

Environment: `. ~/anaconda3/bin/activate discover_feature_relationships`

