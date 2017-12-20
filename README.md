# West Nile Virus Prediction
Author: Haiming Tian
## Problem Statement
West Nile virus is a kind of fatal virus that would spread to humans through mosquitos that carry the virus. In North America, cases of WNV occur during mosquito season, which starts in the summer and continues through fall. WNV cases have been reported in all of the continental United States. There are no vaccines to prevent or medications to treat WNV. Though most people will not develop any symptoms after mosquitos bite, some people will and it could be fatal.

The Chicago Department of Public Health has established a comprehensive surveillance and control program. Every week in the mosquito season, mosquitos captured in traps across the city are tested for the virus. The results of these tests influence when and where the city will spray airborne pesticides to control adult mosquito populations.

Weather data, training data and test data are given. The challenge here is for us to predict when and where different species of mosquitos will test positive for West Nile virus. A more accurate method of predicting outbreaks of West Nile virus in mosquitos will help the City of Chicago and CPHD more efficiently and effectively allocate resources towards preventing transmission of this potentially deadly virus.

## Approach Summary
My approach contains the following steps:
1. Data cleaning.
2. Feature selection and engineering.
3. Building models.
4. Predicting.
### Data Cleaning
Weather data:

![1.1](https://github.com/hming9527/West-Nile-Virus-Prediction/blob/master/Screen%20Shot%202017-12-19%20at%2005.03.44.png)

Training data:

![1.2](https://github.com/hming9527/West-Nile-Virus-Prediction/blob/master/Screen%20Shot%202017-12-19%20at%2005.02.25.png)

For this challenge, there are values that are missing in the weather data, we need to perform some cleaning jobs as well as format the date attribute for future joining operation.

### Feature Selection and Engineering
Basic features:
1. Position, which is represented by longitude and latitude.
2. Date.
3. Species of mosquitos.
4. Temperature related features, this includes maximum temperature, minimum temperature and average temperature.
5. Wind related features, this includes wind speed and direction.
6. Humidity related features, this includes precipitation, dew point and wet bulb point.

Additional features:
1. Duplicate rows with same trap, date and species.
2. Number of mosquitos per trap.
3. Lagged weature features.

For weather data, intuitively, we can be positive that temperature, wind and humidity are related to the probability of the presence of WNV in mosquitos. The warmer it is, the more mosquitos there will be. Wind speed and direction also needs to be taken into account, as mosquitos have very small size, wind will have a great effect on their distribution. Attributes like dew point, wet bulb and precipitation could also have some effects, as we know that the larva of mosquitos live in water, humidity will affect the development of wigglers greatly. Attributes like standard pressure, snow fall, etc, could be dropped early. Furthermore, the time series nature of weather data reminds us to also build lagged features, and use both current and lagged features when we train our model. The lags for each weather data attribute were chosen at 1, 2, 4, 8, 16 days.

For training data, the data was organized in such a way that when the number of mosquitoes exceeded 50, they were split into another record. This nature of data representation generated duplicate rows for some date, trap, species combination in the data. Moreover, WNV could be recorded as "present" in one duplicated row, and "absent" in another which introduced variation in the distribution of WNV even conditional on date, location and the type of mosquitoes in the trap. Duplicated rows could be used to substantially improve the accuracy of predictions since traps with large number of mosquitoes could be detected based on whether there are duplicates. Since duplicated rows were observed in both train and test data, a very useful feature could be generated: number of duplicated rows for a particular date-trap-species combination. Training data contained the number of mosquitoes per trap, we also want to use it as a feature in the model. However this information is missing in test data, I used the number of mosquitoes per date-trap-species combination in training data to generate meta-features by constructing out-of-fold predictions for number of mosquitoes.

### Model Training and Prediction
Based on the out-of-fold prediction result on number of mosquitoes from 3 different regression models, meta-features are generated and used to create a stacked dataset. Then use gradient tree boosting to train on stacked data and obtain prediction on test dataset.

Prediction:

![1.3](https://github.com/hming9527/West-Nile-Virus-Prediction/blob/master/Screen%20Shot%202017-12-19%20at%2012.12.39.png)

However, this competition works differently from previous ones I've seen. The way this competition works is to submit predictions to Kaggle and then feedback will be given, and the competition is ended, I can't get any feedbacks on my prediction. I should've realized that before I decided to work on this project. But essentially, I believe my approach will work.
