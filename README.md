# Ensemble Integration (EI): Integrating multimodal data through heterogeneous ensembles

Ensemble Integration (EI) is a customizable pipeline for generating diverse ensembles of heterogeneous classifiers, as well as the accompanying metadata needed for ensemble learning approaches utilizing ensemble diversity for improved performance. It also fairly evaluates the performance of several ensemble learning methods including ensemble selection [Caruana2004], and stacked generalization (stacking) [Wolpert1992]. Though other tools exist, we are unaware of a similarly modular, scalable pipeline designed for large-scale ensemble learning. EI was developed to support research by Yan-Chak Li, Linhua Wang and Gaurav Pandey.

EI is designed for generating extremely large ensembles (taking days or weeks to generate) and thus consists of an initial data generation phase tuned for multicore and distributed computing environments. The output is a set of compressed CSV files containing the class distribution produced by each classifier that serves as input to a later ensemble learning phase. 

## Configurations

### Install Java and groovy.

This can be done using sdkman (https://sdkman.io/).

### Install python libraries:

	python==3.7.4
    scikit-learn==0.22
    xgboost==1.2.0
    numpy==1.19.5
    pandas==0.25.3
    argparse==1.1
    scipy==1.3.1


### Download weka.jar from github/or the link below:

	curl -O -L https://prdownloads.sourceforge.net/weka/weka-3-8-5-azul-zulu-linux.zip

## Data

Under the data path, 2 files and a list of feature folders are expected: 

1. classifiers.txt
This file specifies the list of base classifiers. See the sample_data/classifiers.txt as an example.

2. weka.properties
This file specifies the list of weka properties that are parsed to the training/testing of base classifiers. See the sample_data/weka.properties as an example.

3. Folders for feature sets
This is a list of folders under the main data path. Each of them originally contains only one file named as data.arff. The .arff files are the input feature matrices and labels for training/testing Weka base classifiers. Indices and labels of .arff files should be aligned across all feature sets. 

## Run the pipeline

### Train base classifiers

Arguments of train_base.py:

	--path, -P: Path of the multimodal data
	--queue, -Q: LSF queue to submit the job
	--node, -N: number of node requested to HPC
	--time, -T: number of hours requested to HPC
	--memory, -M: memory requsted in MB to HPC
	--classpath, -CP: Path of 'weka.jar' (default:'./weka.jar')
	--hpc: use HPC cluster or not
	--fold, -F: number of cross-validation fold
	--rank: Boolean of getting local feature ranking or not (default:False)

Option 1: Without access to Minerva, EI can be run sequentially.

	python train_base.py --path [path] --hpc False

Option 2: Run the pipeline in parallel on Minerva

	python train_base.py --path [path] --node [#node] --queue [queue] --time [hour:min] --memory [memory]

### Train and evaluate EI

Arguments of ensemble.py:

	--path, -P: Path of the multimodal data
	--fold, -F: cross-validation fold
	--rank: Boolean of getting local model ranking or not (default:False)


Run the follwoing command:

	python ensemble.py --path P

F-max scores of these models will be printed and written in the `performance.csv` file and saved to the `analysis`folder under the data path.

The prediction scores by the ensemble methods will be saved in `predictions.csv` file in `analysis` folder under the data path.

### Feature Ranking by EI


### More information about the implementation of EI
We used 10 standard binary classification algorithms, such as support vector machine (SVM), random forest (RF) and logistic regression (LR), as implemented in Weka to derive local predictive models from each individual data modality.

| Classifier Name | Weka Class Name |
|-----------------|-----------------|
|AdaBoost | weka.classifiers.meta.AdaBoostM1 |
| Decision Tree | weka.classifiers.trees.J48 |
| Gradient Boosting | weka.classifiers.meta.LogitBoost |
| K-nearest Neighbors | weka.classifiers.lazy.IBk |
| Logistic Regression | weka.classifiers.functions.Logistic |
| Voted Perceptron | weka.classifiers.functions.VotedPerceptron |
| Naive Bayes | weka.classifiers.bayes.NaiveBayes |
| Random Forest | weka.classifiers.trees.RandomForest |
| Support Vector Machine | weka.classifiers.functions.SMO |
| Rule-based classification | weka.classifiers.rules.PART |
