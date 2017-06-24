---
layout: post
title: "To bridge the gaps - An overview of WSO2 Machine Learner"
category: technology
---

How effectively can we bring purely mathematical and mere conceptual models/mechanisms into the utilization of common man? Is it plausible to make the prerequisite technical know-how for dealing with such models
indifferent among multiple ranges of intellectual levels? That said, are these mechanisms capable of functioning with a variety of applications and data. To which extent can these scale?
The advent of tools for emulating learning theory has trasnpired amidst these concerns. 

Frameworks which perform computational learning tasks had started to emerge approximately at the same time as the inception of Machine Learning as a another branch of Artificial Intelligence. At early stages,
these frameworks basically emulated the exact algorithms with small samples of data and almost all of them were CLI (Command Line Interface) based frameworks. Majority of those were used only in laborotaries in
very specific applications and restricted to researchers. But our society has evolved much and progressed through a myriad of milestones along the path of artificial intelligence and nowadays almost all the organizations require some amount of
data analytics and machine learning to keep abreast with the ever-growing technology. This trend has as become exigent due to the massive amounts of data being collected and stored each and every day. With this inclination,
the demand for the tools that conduct the entire range of workflows of data analytics has been elevated colossaly.

As an open-source middleware based organization, WSO2 has been able to establish its' name among a vast amount of communities which seek different types of solutions in their variety of application domains. A few years ago,
WSO2 entered into the arena of data analytics by providing a rich platform for batch analytics and real time data analytics ([WSO2 Data Analytics Server]() and [WSO2 Complex Event Processor](http://wso2.com/products/complex-event-processor/)). In order to fortify the
hegemony that WSO2 has been able to build around data analytics, [**WSO2 Machine Learner**](http://wso2.com/products/machine-learner/) has been introduced with the intention of providing a rich, user friendly, flexible and scalable predictive analytics
platform which is compatible with the standard machine learning methodologies and with the other products of WSO2 as well. At the humble nascent of the first release of WSO2 Machine Learner (version 1.0.0), this discussion will
provide an elaborative overview on its' ambition of bridging the gaps that hinder prevailing predictive analytics tasks due to various reasons such as complexity, lack of skill in data analytics, inability to scale, real time prediction requirements, rapid increase of not analytized data storages, proprietary rights of software, etc.

In the subsequent sections, you will be able to understand how WSO2 Machine Learner (WSO2 ML from this point onwards) can fit in and what sets it apart from other machine learning
tools and frameworks available with respect to the following concerns.

1. Functionality
2. HCI aspects and userbility
3. Scalability
4. Lisence - Availability to users
5. Compatibility and use cases


# 1. Functionality

![ML key concepts](https://docs.wso2.com/download/attachments/45949448/Ml-overview.png?version=3&modificationDate=1443422195000&api=v2 "Overview functionality of WSO2 Machine Learner"){: .center-image }

The above diagram shows the the key concepts and terminology of WSO2 ML. To put it simply, WSO2 ML supports the entire workflow of data mining: 

### 1. Data sources and data formats

WSO2 ML supports datasources such as csv/tsv files from local file system, files from HDFS file system, and tables from WSO2 Data analytics server.

### 2. Data exploration

Analyzing  and visualizing the characteristics and structure of the data is considered one of the most imparative tasks in data science. Most often, choosing the **right** machine learning algorithm depends 
on the understanding a user has about the data that he/she is dealing with.
![Know thy data](https://docs.google.com/drawings/d/1tZ1Sv7k1NK78Obe5GY9ievctmjaBJar5VA106jftCy4/pub?w=499&h=380 "Know Thy Data!"){: .center-image }
WSO2 ML provides a plenty of tools to visualize data and get to know the data more.
![data exploration](https://docs.google.com/drawings/d/1soCBzrrpB1A7QXvoh3Mj4VsLvdpF2vRsV2u7fx8XreA/pub?w=960&h=720 "Data exploration!"){: .center-image }

In order to identify relations among the features of datasets, know statistical metrics such as mean, standard deviation and skewness, indentify class imbalancies and frequencies, and to visualize how data is structured,
WSO2 ML provides the following graphs which are considered as standard methods of data visualizaiton.

- Scatter plots
- Parallel sets
- Trellis charts
- Cluster diagrams
- Histograms and pie charts

See more about [exploring data at WSO2 ML documentation](https://docs.wso2.com/display/ML100/Exploring+Data).

### 3. Data preprocessing and feature engineering

This is considered as the key of building a successful machine learning model within the data analytics community. WSO2 ML version 1.0.0 provides some primitive techniques of data proprocessing such as feature
selection and missing value handling with mean imputation and discarding.

### 4. Learning algorithms

This initial release of WSO2 ML supports supervised learning and unsupervised learning. It has a set of algorithms for numerical prediction, classification and clustering.

- Numerical prediction - Linear Regression, Ridge Regression, Lasso Regression
- Classfication - Logistic Regression, Naive Bayes, Decision Tree, Random Forest and Support Vector Machines
- Clustering - K-Means

For each of these learning algorithm, user can set the corresponding parameters that specify how algorithm should behave with respect to convergence, regularization, information gain, etc. These are called hyper-parameters. 

### 5. Prediction

Users can use the trained models to predict the values. Feature values can be provided via the UI or a file that contains feature values can be uploaded and the predictions can be obtained for the entire set of records
in the file.

### 6. Model Evaluation and comparison

WSO2 ML allows you to retain a fraction of the training data and evaluate your trained models against that data. Model evaluation is a crucial task when determining the **right learning algorithm** and the **right hyper-parameters**.
It helps the users to get an insight about the models they have trained and make decisions based on the interpretations of the results. Some of the evaluation metrics are:

- Accuracy
- Area under ROC curve
- Confusion Matrix
- Predicted vs. Actual graphs
- Feature importances

![Model evaluation](https://docs.wso2.com/download/attachments/45949563/screencapture-10-100-7-51-9443-ml-site-analysis-view-model-jag-1443421639332.png?version=1&modificationDate=1443466811000&api=v2 "Model Evaluation"){: .center-image }

Moreover, WSO2 ML allows to compare the models you have trained and lets you to choose the best fit among them.

Read more about [model evaluation at WSO2 ML documentation](https://docs.wso2.com/display/ML100/Evaluating+Models).

### 7. Additional functionalities

In addition to standard machine learning tasks, WSO2 ML has the abilities such as downloading trained models, sending e-mail notifications to users when model building is complete, publishing trained models into registry, etc.


# 2. HCI aspects and userbility

Even though Information technology has become an ubiquitous science in the modern community, there is still a huge gap between those who are with data and those who can analyze them.

![gap](https://docs.google.com/drawings/d/1iRK5_3eN_KJU0nCu0YOHLVM6zJxQtvUDcJjT2_NOBDE/pub?w=537&h=255 "Gap"){: .center-image }

Most of the industries are suffering from the scarcity of knowledge workers of data analytics and it is considered as one of the most expensive skills in the current society.

###**_“ I keep saying the sexy job in next ten years will be the statisticians “_**
                                    
**- Hal Varian, Google chief economist, 2009**

There are solid evidences that analytics skills will be one of the most sought after requirements in the future industrial communities. But gaining such immense knowledge and experience in a short period of time is not plausible. 
WSO2 ML is here to help you. For novice data analysts, it provides a very verbose and highly interactive guided process. Therefore anyone with basic understanding of software can use WSO2 ML with ease.

![Guided process](https://docs.google.com/drawings/d/18kSP3WoqtwYH45nR4nT1ERiNTpIs6GwpqUvaH6tEMJQ/pub?w=960&h=720 "Guided Process"){: .center-image }

It is a well known fact that software frameworks will be failures without proper human computer interaction capabilities. WSO2 ML is designed in a way such that anyone can quickly grasp what to do with it and how to get what you want
out of it. It has made the workflow of data mining easier by providing rich graphical user interfaces and essential assistance such as descriptions of visualization techniques and tool tips which explain the effectivity of
hyper-parameters of learning algorithms. Moreover WSO2 ML presents a very elaborative and easy-to-follow [quick start guide in the documentation](https://docs.wso2.com/display/ML100/Quick+Start+Guide).

![Help and Tooltips](https://docs.google.com/drawings/d/1ENic1CExtNyZK87A_eLy5-Ui6Y6v7Z5HCoWPL62Xlt4/pub?w=472&h=274 "Help and Tooltips"){: .center-image }

For data scientists who are with greater experience and knowledge with data analytics, WSO2 ML provides advanced data visualizations and preprocessing techniques which can still be usefull in critical decision making. With the
evaluations and feedback it provides, data analysts can identify the discripancies in their models and improve those to obtain the best results.

# 3. Scalability

In the modern community, scalability of software frameworks is the most decisive factor when selecting solutions for different industrial/enterprise domains due to high availability of data. Data analytics also need to adhere to
the scalability requirements of industries in order to provide best support.
WSO2 ML is built on top of [Apache Spark](http://spark.apache.org/) which is one of the most successful frameworks built for large scale data processing. This allows WSO2 ML to extend its' scalability in different directions.

### 1. Standalone deployment

This mode only allows to start WSO2 ML with a single JVM instance and all the processing is done locally in the same machine. This setting is recommended for small scale tasks like learning WSO2 ML, analyzing small datasets and
analyzing sample datasets. It is not recommended to use the standalone mode for precessor intensive or memory intensive tasks with large datasets. WSO2 ML provides other deployments patterns to support such cases.


### 2. With external Spark cluster

WSO2 ML with external Spark cluster allows to bring the maximum efficiency of the machine learning tasks and utilize the resources to reach the highest achievable performance. By allocating sufficient resources to the Spark cluster,
WSO2 ML can be used to perform predictive analytics with any preferred sizes of datasets without having to worry about performance. This setting is recommended for working with very large datasets and CPU intensive algorithms.

### 3. With WSO2 Data Analytics Server as Spark cluster

This setting is similar to working with external Spark cluster, but when the entire WSO2 data analytics platform is available, it is preferred to use WSO2 Data Analytics server as the Spark cluster for WSO2 ML. This avoids the trouble
of setting an external Spark cluster and extra overheads associated with that.

You can read more about the [deployment patterns at WSO2 ML documentation](https://docs.wso2.com/display/ML100/Deployment+Patterns).

# 4. Lisence - Availability to users

One of the prominent aspects that makes WSO2 ML distinct is that it is free and open source under [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). Therefore, any interested person can get
the code, play with it and add or change anything they want. Interested developers can even contribute to WSO2 ML by resolving issues and improving it.

(Refer to [WSO2 ML Jira](https://wso2.org/jira/browse/ML) and [WSO2 Carbon Jira](https://wso2.org/jira/browse/CARBON))

# 5. Compatibility and use cases

Some of the ways(but not restricted to) that WSO2 ML can be used can be indicated as follows:

### REST API

WSO2 ML has a rich restful API which allows users to call all the functionalities of ML externally. This API can be used to perform tasks starting from
uploading data up to predicting and model evaluations. You can get a more sound idea of what this REST API is capable of by referring to the [REST API guide of WSO2 ML](https://docs.wso2.com/display/ML100/REST+API+Guide).

### Working with tables in WSO2 Data Analytics Server

It is a well known fact that most of the data accumulated in todays' industries are not capable of being stored in typical file systems in properly structured ways. Most of the times, the data we have may have to
be stored in distributed environments. WSO2 Data Analytics Server provides this capability to store data in tables and allows to perform SQL operations on the uploaded data. WSO2 ML can directly import these tables as its'
datasets. Read more on how to use [WSO2 DAS tables in WSO2 ML documentation](https://docs.wso2.com/display/ML100/Integration+with+WSO2+Data+Analytics+Server).

### Real time prediction with WSO2 Complex Event Processor

The most vital duty of predictive machine learning models is to make predictions on unprecedented data and with all the smart technolgoies we have today, it has become a major requirement to analyze data in real time. Hence,
making predicitons on real time data can be considered as the most eminent usage of machine learning models nowadays. WSO2 ML provides a feasible solution for this taks with its' extension for WSO2 Complex Even Processor. Read
more about [real time prediction with WSO2 CEP extension at WSO2 ML documentation](https://docs.wso2.com/display/ML100/WSO2+CEP+Extension+for+ML+Predictions).

# What's next?

WSO2 ML is still at its' embryonic infancy and it has a long journey ahead with many directions to venture. Some the features that are expected to be incorporated with future versions of WSO2 ML are as follows:

1. PMML support
2. Deep Learning
3. Advanced data preprocessing methods and data wrangler support
4. Recommendation systems
5. Anomaly detection algorithms
6. Dimensionality reduction methods
7. Ensemble methods
8. Natural Language Processing
and many more!