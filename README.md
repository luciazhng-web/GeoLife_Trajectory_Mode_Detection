# GeoLife_Trajectory_Mode_Detection
ITS course project source code

**Introduction**
The initial motivation for this project is to encourage more sustainable transportation methods by classifying transportation modes for individuals based on their daily mobility activities. This approach was
inspired by Ant Forest, a Chinese public participation project that gamifies carbon footprint reduction by rewarding users with ”green energy” points for choosing low-carbon travel options.
During the implementation of this project, the scope was narrowed down to transportation mode detection based on GPS trajectories, utilizes the GeoLife GPS dataset. The aim of this project is to develop a robust pipeline for cleaning, enriching, and classifying mobility segments.

**Objectives**
The primary objective of this project is to gain a deep understanding of trajectory data, its inherent noise characteristics, and the methodologies required to transform raw sensor readings into actionable
mobility insights. Specifically, the project aims to:
1. Develop a data processing pipeline that transforms raw GPS points into semantically enriched movement segments.
2. Implement filtering and smoothing techniques to mitigate GPS sensor noise.
3. To engineer statistical and spatial features that distinguish between different transportation modes.
2.2 Hypothesis
H1: Statistical moments (skewness, kurtosis) of speed profiles are stronger predictors of motorized vs. non-motorized transport than simple mean speed.
H2: Transportation modes like ”Bus” and ”Taxi” can be differentiated by their proximity to Public Transport (PT) infrastructure extracted from OpenStreetMap (OSM).
H3: Each trajectory file is recorded on the same device

**About the dataset**
According to the User Guide for Geolife GPS trajectory dataset, there are total 17,621 trajectories from
182 users. But the labeled dataset I could get from Mircosoft website only contains 2240 trajectories
from 24 users.

Based on the objectives of this project, above labeled data points are enough for training the trans-portation mode classifier.

**System Design**
The system design was born from a series of fundamental questions regarding data integrity and the ”Garbage In, Garbage Out” principle. The architecture is built on four pillars: Segmentation, Calibration,
Enrichment, and Simplification.

**Initial Design Considerations and Clarifications**
Before implementing the pipeline, the following design constraints were resolved:
Handling Unlabeled Data: While only labeled data is used for training, unlabeled data is essential during the preprocessing phase. These points represent stay points and movements between labeled
trips; removing them early would break the continuity required to accurately identify stops and segment boundaries.
Label Simplification: To reduce class ambiguity, labels were merged based on physical characteristics:

• Car + Taxi → Car

• Train + Railway → Train

Sampling Frequency Uniformity: The dataset contains varying sampling rates 2 - See Figure 1. As 91% of the data is recorded at high density (1-5s or 5-10m), and the features are based on speed
and acceleration which means they are normalized by time intervals so no re-sampling will be incorporated in the pipeline.

**Programming language and Tools**
The implementation of this project relies on the Python ecosystem, known for its robust libraries in data
science and geospatial analysis.
Development Environment : The project was developed and executed using Jupyter Notebooks.
Infrastructure Transition : While initial prototyping was conducted on Google Colab, the large dataset size and the high RAM requirements for trajectory smoothing and spatial joins required migration. The final pipeline was deployed on a local runtime to leverage higher RAM capacity
and dedicated computing resources, ensuring stable processing of the high-density GPS logs.
Key Libraries :
• Pandas and NumPy for data manipulation.
• GeoPandas and Shapely for spatial operations and coordinate handling.
• Scikit-learn for data preprocessing, train-test splitting, and evaluating baseline models, and
XGBoost for baseline and final models.
• Matplotlib/Seaborn for visual data exploration.

Only XGBoost was selected as the classifier for this project, as it’s already one of the state-of-the-art classifier for tabular trajectory features. Also given the project’s focus on the data pipeline
rather than model architecture, I did not compare with other classifier such as Random Forest orLightGBM.

Source Code : All source code and experimental logs are hosted in the project’s GitHub Repository,
and must be executed in the following order to maintain the data pipeline:
1. RawTrajectoryExtraction.ipynb Merge all trajectories into one data frame matching with modality labels, initial parsing of timestamps, and coordinate formatting.
2. BasicFeature&Segmentation.ipynb Extract basic features for segmentation, stop point detection (segmentation) and orientation drop, and Kalman filter.
3. FeatureExtraction&Cleaning.ipynb Statistical feature extraction after trajectory smoothing, outlier detection and GIS feature extraction.
4. Baseline&ModelSelection.ipynb The final classification stage using XGBoost, including model training, hyperparameter evaluation, and performance reporting.
