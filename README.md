# Phase 2 Project

## Inroduction
This linear regression analysis of the King County housing dataset will serve to inform real estate construction companies in Washington looking to invest in building a new home or residential community. The goals are to discover which features of a home increase its sale value and to quantitatively define the relationship between those features and the price of a home.

The analysis for this project can be found in the [student.ipynb](https://github.com/ACB-prgm/Phase2Project/blob/master/student.ipynb) file in this repository.  The data and a rough explanation of the data can be found inside the [data](https://github.com/ACB-prgm/Phase2Project/tree/master/data) folder.  The presentation of my analysis and results can be found at [Presentation.pdf](https://github.com/ACB-prgm/Phase2Project/blob/master/Presentation.pdf).


## The Data
The King County dataset is a public dataset available on [Kaggle](https://www.kaggle.com/datasets/harlfoxem/housesalesprediction) as well the the King County Website.

This dataset contains over 15k clean data points representing house sales in King County between May 2014 - May 2015. Each data point includes data on amenities, number of bedrooms/bathrooms, square footage, renovations, location, views, and sale price.

## Analysis
### Data Cleaning and Preparation
I first imported and cleaned the data by dropping columns including NaN values and converting the ordinal string values to integers so that I could screen for correlations with price.
Before doing that though, I looked at the distribution of price, our target, and realized it was heavily skewed due to a few extraordinarily high priced outliers.  I removed the outliers using the IQR method, and the distribution of price normalized.
Then I ran pearson correlations with the entire dataframe and `price` and included all that had medium to strong correlations.  I also included two categorical variables, `view` and `waterfront`, as their scatter plots appeared linearly related to `price`.  I turned them into dummy variables, dropping the values with no view and no waterfront to serve as references.

<img src="https://user-images.githubusercontent.com/63984796/202476532-c3bfc563-8179-41f0-a78f-9590dbb1cd2d.png" alt="Heatmapfull" width="500" align="center"/> <img src="https://user-images.githubusercontent.com/63984796/202477228-9e3f8d38-ad89-4be1-a84b-350d52025bcc.png" alt="SmallHeatmap" width="500" align="center"/>



### Baseline Model
I created a baseline linear regression using these independant variables: 
 - `Bathrooms` - Number of bathrooms in the house
 - `Sqft_living` - Square footage of living space in the home
 - `Grade` - Construction and material quality (related to construction cost) on a scale of  1 to 13
 - `Latitude` - Latitude of the house (distance from southern parts of the county in this case)
 - `Sqft_living15` - Square footage of living space for the nearest 15 neighbors
 - `Floors` - Number of floors in the house
 - `View` - The views from the house on the following scale: NONE - FAIR - AVERAGE - GOOD - EXCELLENT
 - `Waterfront` - Whether or not the house is a waterfront property

All of the assumptions of linear regression were met (Linearity, ~Independance, Normality, and Homoscedasticity).  The model was statistically signifficant and abke to explain ~65% of the variance in the data.

### Final Model
To get to the final model, I went through several iterations of small adjustments. The Heterscedasticity plot and qq-plot of the baseline model looked slightly irregular and the target variable `price` had a slightly irregular distribution, so I log transformed `price`.  This improved the adj. R2 value to .668 (ie. explaining ~67% of the variance).  Then I identified that the number of floors had some interaction terms within it that had higher correlations than the whole, so I created dummy variables out of `floors` and added them to the independant variable.  This improved our adj. R2 to 0.677.  Adding the interaction terms actually decreased the R2 and added needless complexity to the interpretation of the model, so I backtracked to the previous model. Finally, I applied the natural log to `sqft_living` and `sqft_living15` as they seemed to have non-normal distributions and may have a non-linear relationship with `price`.  This slightly improved the model again, increasing the adj. R2 to 0.682.

## Results
<img src="https://user-images.githubusercontent.com/63984796/202478722-073b4baa-b53d-473a-a2c0-9b018179671d.png" alt="Predictors" width="300" align="right"/>

The model statistically signifficantly explains ~68% of the variance in sale price.

From the normalized results, we can see that `sqft_living`, `grade`, and `lat` have the most effect on our model. Of those, `sqft_living` and `grade` are the most actionable for the purposes of a construction company.

For each additional bathroom, we see an associated increase of ~1.31% in price.  
**For each additional grade, we see an associated increase of ~13.35% in price.**  
For each additional degree of latitude in King County, we see an associated increase of ~321.30% in price.  
**For each increase of 1% sqft_living, we see an associated increase of ~0.32% in price.**  
For each increase of 1% sqft_living15, we see an associated increase of ~0.17% in price.  
Houses on a waterfront have a ~42% higher price compared to those not on a waterfront.  
Houses with a "fair" view have a ~20.5% higher price tag than those with "no view".  
Houses with a "average" view have a ~18.6% higher price tag than those with "no view".  
Houses with a "good" view have a ~22.4% higher price tag than those with "no view".  
Houses with a "excellent" view have a ~31.4% higher price tag than those with "no view".  
Houses with 1.5 floors have a ~14.8% higher price tag than those with one floor.  
Houses with 2.5 floors have a ~11.1% higher price tag than those with one floor.  

## Recommendations
1. With respect to `sqft_living', It would be most profitable to maximize living squarefootage in any home constructed. This does not necessarily mean sacrificing non-living space, but is just a reccomendation to prioritize the size of areas such as living rooms and bedrooms over non-living areas.

2. With respect to `grade`, It would be most profitable to spend more on higher quality construction materials and have experienced contractors building the homes. This becomes an issue of diminishing marginal returns at a point that was not explained by the model. IE: at what point does it not become profitable to spend more on materials. This will need to be something decided by the individual construction company and their respective budgets.

## Discussion and Caveats
Though we have a decent model, several of the predictors are not particularly useful.  Construction companies can rarely control whether their houses will be waterfront properties, have excellent views, or be further from the equator.  I considered removing latitude as it did not seem logical to me, but I left it in as I don't know enough about the geography of King County, Washington to say definitively that northernmost areas are considered more desireable.  For instance, maybe northernmost houses are closer to big cities, or are nearer to better school districts.  Grade, while a useful variable describing the quality of contruction and materials, is not speciffic enough to generate any real insight other than "you get what you pay for".
