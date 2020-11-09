# Anomaly Detection in Floodlights for Smart Campus

<img src="https://img.shields.io/badge/Category-Condition--Based%20Maintenance-blue"> <img src="https://img.shields.io/badge/Sub--Category-Anomaly%20Detection-yellowgreen"> <img src="https://img.shields.io/badge/Difficulty-Intermediate-yellow"> <img src="https://img.shields.io/badge/Analytical%20Method-Subspace%20Tracking (SST)-brightgreen"> <img src="https://img.shields.io/badge/Updated-Oct%202020-orange">

## Overview

Learn how to build a model for real-time detection of malfunctioning light groupings. These light groupings are part of the Smart Campus floodlight circuits located in a parking lot.

Key take-aways from the use case: 
* Learn how to design streaming model for real-time failure detection
* Learn how to use [Subspace tracking](/docs/SubspaceTracking.md) algorithm to detect anomalies
* Best practices for [Subspace tracking](/docs/SubspaceTracking.md) algorithm

The data set consists of energy consumption values captured every five minutes from six floodlight circuits, over a span of about three months. 

We will use the [Subspace tracking (SST)](/docs/SubspaceTracking.md) algorithm packaged in **SAS Event Stream Processing Studio** to detect outliers in real time using streaming data. It is a method to detect anomalies and system degradation in systems that generate high-frequency, high-dimensional data. It can be used for data containing a single measure for sensors from various devices operating under similar conditions (e.g., energy output from multiple panels in a Solar farm), or multiple measures for sensors from a single device operating under similar conditions (e.g., turbofan in an aircraft).

![](./docs/sst_floodlights.mp4)

### Prerequisites

List of required software offered as part of [**SAS Analytics for IoT**](https://www.sas.com/en_us/software/analytics-iot.html) 
*  [SAS Visual Analytics](https://support.sas.com/en/software/visual-analytics-support.html#documentation)
*  [SAS Event Stream Processing](https://support.sas.com/en/software/event-stream-processing-support.html#documentation)

## Getting Started

This examples takes you through the workflow to build anomaly detection model using tools offered as part of **SAS Analytics for IoT.**

<img src="/images/pipeline.png" width=900>

### Data Description 
This [data set](/data/parking_floodlights_data.csv) consists of energy consumption values that were captured every five minutes from six floodlight circuits. These circuits are located in a parking lot at a Smart Campus. 
Data is captured for a span of about three months. Each of these light circuits has a sensor that measures the energy consumption of the lights.

### Data Exploration
From analyzing first 16 days of the data in **SAS Visual Analytics**, we see that the energy consumption throughout the day is zero because the lights are off during the day.
**Light 2** has a dip in energy consumption for a couple of hours on the sixth night, indicating one of the lights controlled by the circuit was not functioning properly.
During day 15, all lights have a spike in energy consumption during the day. A storm occurred on that day, creating enough darkness to trigger the lights to come on briefly. There was no malfunction in the lights. 

<img src="/images/data1.png" >

From analyzing the most recent 16 days of the data we see that **Light 4** starts to have many dips in energy consumption in the last 11 nights. Several of the lights on this circuit are starting to fail.
Additionally, **Light 5** dips in energy consumption, especially on the second-last night. This indicates that problems are occurring in this circuit as well.

<img src="/images/data2.png" >

See [Explore Data](/docs/ExploreData.md) using **SAS Visual Analytics** for step by step description.

### Build and Test Streaming Model
To detect malfunctioning floodlights in real-time, we use the [Subspace tracking (SST)](/docs/SubspaceTracking.md) algorithm which is packaged with **SAS Event Stream Processing Studio (ESP Studio)** on streaming data.
It is frequently used in the IoT world where data is gathered from many sensors that are connected to each other and have high correlation. 
This approach converts a set of correlated variables to a set of linearly uncorrelated variables known as principal components. Because the first few principal components usually capture most of the variability in the data, they can be tracked over time to assess whether any changes have taken place in the subspace that is spanned by the data.
We can use SST to detect malfunctioning circuits in the floodlights by tracking angle changes between principal components or by using principal component distances away from the mean.

<img src="/images/esp_project_new.png" >

The following properties were used for window-based method:

| Name | Value |
| :------ | :------: |
| windowLength | 240 |
| maxPrincipal | 2 |  
| overlap | -1 |

A window length of 240 and overlap of -1 indicates that the procedure starts with the first 240 observations in the data table, calculates the principal components, moves ahead one observation and uses observations 2–241 to calculate the principal components, and continues in this manner until the entire data table has been used.

See instructions to [build and test model](/docs/BuildModel.md) using SST in **SAS Event Stream Processing Studio**.

### Result Interpretation

The output result is plotted using **SAS Visual Analytics**. It shows the first principal component that is obtained after applying the SST method over the sliding windows of data.

<img src="/images/result1.png" >

*  **Light 2** starts deviating from the first principal components that are associated with the other lights around the same time that the dips were observed on the sixth night for these lights.
*  **Light 4** starts deviating from the first principal components that are associated with the other lights again around the same time that the dips for this light were observed towards the end.

This plot can help determine which floodlight circuits are malfunctioning. SST can give you a relatively clear indication of which part of the system is getting out of control. Note that the dip in energy consumption on day 15 is not flagged as an anomaly since all floodlights were affected due to the storm. 

The chart below displays the angle change of the first principal component between consecutive windows.

<img src="/images/result2.png" >

* Value of angle change, indicated as vertical spikes in plot above, is relatively higher when an outlier enters or exits the window
* Checking value of angle change can detect when a light circuit is not functioning properly 

With this knowledge, decisions can be put into place to trigger maintenance activities when the angle change value is above an acceptable level. 

### Summary
We can build in-stream models using the [Subspace tracking (SST)](/docs/SubspaceTracking.md) algorithm packaged in **SAS Event Stream Processing Analytics**. 
It can monitor the system in real-time and the faulty light can be immediately identified, and admins alerted.
This method can be used in other systems that generate high-frequency, high-dimensional data to detect anomalies and degradation in real-time. 

**SAS Analytics for IoT** offers an optimized IoT Solution ecosystem and addresses the entire analytical lifecycle. Read more about **SAS Analytics for IoT** [here.](https://www.sas.com/en_us/software/analytics-iot.html) 

### Running
See detailed [step by step instructions](/docs) to build the process and learn more about SST.

| # | Description |
| :------: | :------ |
| 1 | [Explore Data](/docs/ExploreData.md) |
| 2 | [Build and Test Streaming Models](/docs/BuildModel.md) | 
| 3 | [Best Practices for SST](/docs/SubspaceTracking.md) | 

## Contributing

This repo is not open for external contributions.

## License

This project is licensed under the [Apache 2.0 License](LICENSE).

## Additional Resources

* [General information](https://www.sas.com/en_us/software/analytics-iot.html) about SAS Analytics for IoT
* Technical details about [how SAS Event Stream Processing implements SST](https://go.documentation.sas.com/?cdcId=espcdc&cdcVersion=6.2&docsetId=espan&docsetTarget=n0jveogr5iwzyxn1w7imhj73d4zf.htm&locale=en#p0dv9t241gp1ptn13vo75aol2d1ba)
* Reference information about the [MWPCA procedure](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.5&docsetId=casml&docsetTarget=casml_mwpca_overview.htm&locale=en) of SAS Visual Data Mining and Machine Learning
* Reference information about the [Subspace Tracking Package in TSMODEL procedure](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.5&docsetId=castsp&docsetTarget=castsp_sst_sect001.htm&locale=en) of SAS Visual Forecasting
* An [overview](https://support.sas.com/en/software/visual-analytics-support.html#documentation) of SAS Visual Analytics
* SAS Support Communities [website](https://communities.sas.com/)
* You can find additional IoT use cases on the [SAS for Developers website](https://developer.sas.com/guides/iot.html)
* SAS [Event Stream Processing Free Trial](https://www.sas.com/en_us/software/event-stream-processing.html)
