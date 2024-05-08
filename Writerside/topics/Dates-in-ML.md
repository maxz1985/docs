# Dates in ML

When building ML prediction models, unique values such as date-time stamps are not very useful since model training depends on value's frequency of occurrence.

Such values are a source for *feature engineering*.

*Feature engineering* is a process of transforming raw values into data that can be used in supervised learning.

For example, a date can be transformed to features such as:
* year
* month
* season
* quarter
* week of year
* beginning/end of week (month, quarter, etc.) ex. pay days
* days
* days to/from particular event (distance)
* hour (time of day)

To suggest the correct feature transform, consider a question you're trying to answer.

Ex. — We would like to consider market trader activity.

`What day of the week and time of day most trades under $5000 will be made?`

Our features are: `day of week`, `hour of day`, `trade value`.
