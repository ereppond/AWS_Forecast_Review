
# Walk-Through of Amazon Forecast
This notebook is an overview of the functionality of the Amazon Forecast tool. Click [here](https://aws.amazon.com/forecast/) for the homepage. 
![How it works](https://d1.awsstatic.com/r2018/r/seer/diagrams/Seer_HowitWorks_Final.44b02658b17d05e9242b450b220f6e0ca4065638.png)

<br> Features include:
* Automated machine learning
* Based on technology used at Amazon.com
* Simple evaluation of the accuracy of models
* Visualizations of forecasts
* Integration with existing tools
* Generation of probabilistic forecasts

Access the tool using:
* AWS CLI
* Python Notebook
    * Demonstration of Amazon Forecast notebooks can be found [here](https://github.com/aws-samples/amazon-forecast-samples).
* Console
    * GUI of the tool

## Using the Console

### Step 1 : Import Training Data
1. Create dataset group
> This step was easy to follow. I used the domain called "Metric", which represented revenue and sales data.
![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/dataset_group.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/dataset_group.PNG).

2. Specify dataset details
> Although the explantion of each field is clear, the data being used for the model must follow the schema as shown in the example, which means the order of the columns must be the same as the order of the schema.
![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/dataset_details.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/dataset_details.PNG).

3. Import the time series data from S3 bucket
> This page was nice because it was straight forward in that it allows you to simply reference the path to the data in your S3 bucket and the Role ARN to retrieve the data. You also have the option of having the tool create an IAM role for the purpose of forecasting on this page if one is not already created. 
> * IAM role – Keep the default Enter a custom IAM role ARN.
> * Alternatively, you can have Amazon Forecast create the required IAM role for you by choosing Create a new role from the drop-down menu and following the on-screen instructions.
![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/import_time_series_data.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/import_time_series_data.PNG).

4. Start the import
> Clicking start import begins the importing process, which took about 3 minutes for a dataset of ~10,000 rows. 
<br> Once the data has been imported, the following banner should appear. ![](https://docs.aws.amazon.com/forecast/latest/dg/images/gs-step1-imported-banner.png)


5. OPTIONAL - add metadata and/or related time series data
> If desired, you may include additional data for the model to train on, but unfortunately you must upload it as an entirely separate dataframe from the original time series data. <br>
> * item metadata refers to some feature associated with the 'metric_name' attribute
> * related time series data refers to another dataset with a timestamp object that is not the target data

### Step 2 : Train a predictor

1. Specify the parameters of the model
> Documentation on this step is clear. 
> * algorithm selection allows you to either have the automated algorithm, or choose your own algorithm from the following: 
     * [ARIMA](https://docs.aws.amazon.com/forecast/latest/dg/aws-forecast-recipe-arima.html)
     * [DeepAR+ Algorithm](https://docs.aws.amazon.com/forecast/latest/dg/aws-forecast-recipe-deeparplus.html)
     * [Exponential Smoothing Algorithm](https://docs.aws.amazon.com/forecast/latest/dg/aws-forecast-recipe-ets.html)
     * [Non-Parametric Time Series Algorithm](https://docs.aws.amazon.com/forecast/latest/dg/aws-forecast-recipe-npts.html)
     * [Prophet](https://docs.aws.amazon.com/forecast/latest/dg/aws-forecast-recipe-prophet.html)
![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/train_predictor.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/train_predictor.PNG).

> **Note**: This can take a long time to train depending on the algorithm and amount of data. For a dataset of ~10,000 rows using the AutoML approach, it took around 30-40 minutes. 

### Step 3 : Create a forecast

At this point, your dashboard for this dataset group should look like this: ![](https://docs.aws.amazon.com/forecast/latest/dg/images/gs-step3-trained.png)

![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/forecast_details.PNG)

1. Create a forecast
> Once you've clicked the 'Start' button, you will be brought to this page:
![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/forecast_details.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/forecast_details.PNG).


> Here your predictor is likely either an AutoML, Prophet, or one of the other modeling options you built your forecast with. Once you click 'Create a forecast', the forecast should be done within minutes. <br> This image will appear on your screen with the forecast is done. ![](https://docs.aws.amazon.com/forecast/latest/dg/images/gs-step3-created-banner.png)

### Step 4 : Forecast Lookup
> Since we want to look at an item in our data that has a lot of data, we will get the forecast for the most common value. ![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/value_counts.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/value_counts.PNG).

> At this step, you can see the prediction of the data. The details required for this step are specified here: ![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/forecast_lookup_details.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/forecast_lookup_details.PNG).

> Even though the Start date and End date boxes have any date as an option to choose from, this page will not submit without times that work for your data. The timeframe in which you can see is very limited. 

> ![](https://raw.githubusercontent.com/ereppond/AWS_Forecast_Review/master/pictures/forecast_visuals.png)
For link to image, click [here](https://github.com/ereppond/AWS_Forecast_Review/blob/master/pictures/forecast_visuals.PNG).

### Understanding the Forecast
> At a first glance, the visual representation of the forecast is not simple to understand because there is not any explanation of what **P10**, **P50**, and **P90** mean. After looking through the documentation, here is what I found: 


**wQuantileLoss[0.1]**: For the P10 prediction, the true value is expected to be lower than the predicted value 10% of the time.

For example, suppose that you're a retailer and you want to forecast product demand for winter gloves that sell well only during the fall and winter. If you don't have a lot of storage space and the cost of invested capital is high, or if the price of being overstocked on winter gloves concerns you, you might use the P10 quantile to order a relatively low number of winter gloves. You know that the P10 forecast overestimates the demand for your winter gloves only 10% of the time, so 90% of the time you'll be sold out of your winter gloves.

**wQuantileLoss[0.5]**: For the P50 prediction, the true value is expected to be lower than the predicted value 50% of the time. In most cases, the point forecasts that you generate internally or with other forecasting tools should match the P50 forecasts. If τ = 0.5, both weights are equal and the wQuantileLoss[0.5] reduces to the commonly used Mean Absolute Percentage Error (MAPE):


where ŷi,t = qi,t(0.5).

Forecast uses the scaling factor of 2 in the wQuantileLoss formula to cancel the 0.5 factor to obtain the exact MAPE expression.

Continuing the winter gloves example, if you know that there'll be a moderate amount of demand for the gloves and aren't concerned about being overstocked, you might choose to use the P50 quantile to order gloves.

**wQuantileLoss[0.9]**: For the P90 prediction, the true value is expected to be lower than the predicted value 90% of the time.

If you determine that being understocked on gloves will result in huge amounts of lost revenue—for example, the cost of not selling gloves is extremely high or the cost of invested capital is low—you might choose to use the P90 quantile to order gloves.

The following figure of a forecast that has a Gaussian distribution, shows the quantiles that divide the forecast into four regions of equal probability.

![pic12](https://docs.aws.amazon.com/forecast/latest/dg/images/metrics-gaussian.png)

> Unfortunately, even with the explanation of the forecasts, the visual representation of the forecast does not show us how the model performed on the validation dataset. 

> Overall, this tool would be useful if the end results were clearer, but since the forecasts don't help explain the data well, I would not use this tool again. 


