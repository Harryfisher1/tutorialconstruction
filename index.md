<center><img src="{{ site.baseurl }}/Banner.png" alt="Img"></center>

# Intro to model statistics and comparison 
#### Created by Harry Fisher 24/11/2020
--------------------------------------------------
## Tutorial Aims and Steps:
#### <a href="#section1"> 1. What is a model?</a>
#### <a href="#section2"> 2. R-squared values with linear models</a>
#### <a href="#section3"> 3. Linear Model assumptions</a>
#### <a href="#section4"> 4. Genarilized linear models and when to use them</a>
#### <a href="#section5"> 5. Information Criteria and AICs</a>
#### <a href="#section6"> 6. Challenge yourself and furthur tutorial

### All the files you need for this tutorial can be downloaded from [this repository](https://github.com/Harryfisher1/CC-model-statscomp). Click on `Clone/Download/Download ZIP` and unzip the folder, or clone the repository to your own Github account.

# __Introduction__
__The use of statistics and models in science is crutial when looking into the relationship between two variables, assessing which have the strongest effect and how much we can trust these is at the front of Science right now. There are other tutorials looking into how to build [linear models](https://ourcodingclub.github.io/tutorials/modelling/) and [model design](https://ourcodingclub.github.io/tutorials/model-design/), but this tutorial can be used as an introduction to the statistics behind these tutorials so you can feel more confident behind how models work, and how they can be compared.__ 

<a name="section1"></a>

# 1.) What is a model?
There are tutorials to explain the different types of models we can build, but here I want to strip it right back and explain what a model actually is, in order to understand its assumptions and how we can compare them. The goal of a model is to provide a very basic summary of a data-set. Often in ecology, models are used to determine the relationship between two variables, and are can be visualized by a straight line, what we call a __linear model__. The model recognizes all the data points you give it, finds the X value (Predictor variable), and all the Y values (explanatory variable) and creates a relationship between them, so that it could find a y value for every possible x value there could be. This line does not often pass through every actual data point, but makes a line of best fit that explains the relationship based on the data points available. This is an important thing to remember when looking at your model, it has not captured perfectly how your data is behaving, but it can give us a good idea, as Famous statistician George Box once said `All models are wrong, but some are useful`.

A lot of statistics is based on the distance between the actual data points and the line, this is called the __residual__ a term I imagine you've come across! The line that has the least  over all distance between all the points in the data, describes the relationship the best. If there is very little difference between the line fitted, and the data points, then you can describe the trend of the line with more statistical confidence. If there is a lot of variation between your data points and the line fitted, then this relationship has weaker statistical significance.

<a name="section2"></a>

## R-squared values with linear models
To find how well the line you have plotted fits your data, we use the R-squared value of the model. The R-squared value is calculated first by finding the variance of the mean, we will call this Var(mean) here. This is often called the sum of squares, this is calculated by finding the difference between each data point and the mean of the data points, and squaring it (we square it so the points that are below the mean don't cancel out the points that are above the mean). Now that we have done this, we find the variance of data from our line we have fitted we will call this Var(Line) here, this is done the same way as the Var(mean), but instead of finding the difference between data points and the mean, we find the difference between the data points and where our model expects them to be, on the line. Now we have Var(mean) and Var(line) we can begin calculating R-squared! The equation is: 

<center><img src="{{ site.baseurl }}/R2_equation.png" alt="Img"></center>

By dividing by the variance in the mean means the value will be somewhere between 0 and 1, as the variation from the line will never be greater than the variation around the mean , and it will never be less than 0. With it being between 0 and 1, it means we can treat R-squared as a percentage. If the R-squared value is 0.85 then, there is 85% less variation around the line the model fitted, than the mean, we describe this as the relationship between the explanatory variable and response variable explains 85% of the variation. The acceptable value for R-squared is often disputed and depends on your data, if  you get a R-squared value of 0.4, then your explanatory variable explains 40% of the variation, that's good if there are 100s of other things accounting for the other 60% but not good if there's just one thing making up that variation. 

You need to be careful however! If we had just two data points then the line would go from one point to the other, meaning there would be no distance between your datapoints and the line, meaning no residuals, meaning an R-squared value of 1.0! Great, we have found there to be a 100% relationship between our variables and are ready  to rock the scientific communities world! Not quite. Obviously this cant be trusted, and to prove whether we can trust our R2 value we look at the P-value of our model. The p-value takes the number of samples into consideration, and gives you a value between 0 and 1. To have confidence in our model and the R squared value we have obtained we have to meet a certain criteria, which within the scientific community this is __a p-value below 0.05__. 


Lets look at the example of the heights and weights of two people. We will be using `ggplot2` to plot our linear models, see [here](https://ourcodingclub.github.io/tutorials/datavis/) for more tutorials on ggplot2. So if you havent got it already installed onto your device do that first!


```r
#If you haven't already, install ggplot2
install.packages(ggplot2)
#load the library
library(ggplot2) 

#Remeber to make sure your working directory is set to where you saved the files first 
setwd("C:/User/CC-model-statscomp")

twopoint <- read.csv("2point.csv")
twopointlm <- lm(WeightLB ~ HeightIN, data = twopoint)
summary(twopointlm)

ggplot(twopoint, aes(x = WeightLB, y = HeightIN)) + #Assigning our x and y variables
  geom_point() + 
  stat_smooth(method = "lm", col = "red") # adding our linear models line, with the colour being red

```
<center><img src="{{ site.baseurl }}/2point.png" alt="Img"></center>

<center><img src="{{ site.baseurl }}/2pointsum.png" alt="Img"></center>

From the our plot it shows there to be a decreasing trend with height compared to weight, and with an R-squared value of 1 we can say that height is responsible for all of the variation in weight. The p-value in the summary table is N/A though, meaning there isnt even enough data points to show this relationship is significant. 

So we decided to measure 2 more individuals
```r
#Load dataset with 4 measurements
fourpoint <- read.csv("4point.csv")
fourpointlm <- lm(WeightLB ~ HeightIN, data = fourpoint)
summary(fourpointlm)

ggplot(fourpoint, aes(x = WeightLB, y = HeightIN)) + 
  geom_point() +
  stat_smooth(method = "lm", col = "red")
```
<center><img src="{{ site.baseurl }}/4point.png" alt="Img"></center>

Now we see there is a possitive relationship between height and weight, with a R-squared value is 0.81, and which is still a really strong relationship, but with height still with a p value of greater than 0.05 we can not trust the value.

Lets try the full data set that included 100 inividuals.

```r
#Load dataset with 100 measurements
hundredpoint <- read.csv("100point.csv")
hundredpointlm <- lm(WeightLB ~ HeightIN, data = hundredpoint)
summary(hundredpointlm)

ggplot(hundredpoint, aes(x = WeightLB, y = HeightIN)) + 
  geom_point() +
  stat_smooth(method = "lm", col = "red")
```
<center><img src="{{ site.baseurl }}/100point.png" alt="Img"></center>

Now we can see there is infact a possitive relationship between height and weight with a p-value close to 0 and a R-squared value of 0.62. It is still a strong relationship, but the decrease from the smaller samples shows there are other factors that cause variation in weight. 

<a name="section3"></a>

# Linear model assumptions

To use a linear model there are assumptions that need to be met first, they are touched on in the [Basics to statistical modelling tutorial](https://ourcodingclub.github.io/tutorials/modelling/#linear) but here we will go in more detail on each to understand why these assumptions need to be met.

Numbers can be misleading, this is illustrated well by the Anscombe's quartet below. Each of the data sets look completely different, yet all have the same regression line and mean value for X and Y.


<center><img src="{{ site.baseurl }}/Anscombe.png" alt="Img"></center>

Therefore the assumptions for linear models mean that when linear models are constructed they are done so they truely give valuable insight to the data.

### 1.Linearity: The relationship between X and the mean of Y is linear.

Models wil be misleading if the data isnt apporixmately linear. One way to solve this is to make a sctterplot of your data (tutorials on this [here](https://ourcodingclub.github.io/tutorials/datavis/#scatter)), doing this can give you and idea of your data and can help identify any outliers that can be removed. This should be done with consideration however, outliers shouldnt be removed to change the outcome of your model. They can be removed if you think they they are obviously incorect, does not change the outcome or if there is no significance anyway. You can find more on dropping outliers [here](https://www.theanalysisfactor.com/outliers-to-drop-or-not-to-drop/). 

### 2.Homoscedasticity: The variance of residual is the same for any value of X.

One of the assumptions of linear models is that the data is homoscedastic, or simply put, the explanatory variable does not have an effect on the margin of error around a data set. Again imagine our straight line running through data points, as we go along the x axis and the distance between the the data point and the line remains constant, we would describe the data as homoscedastic, as all the error is the same. However, if as X increases we see saw that the data points appear to be getting further and further from the line, then we would describe the data as heteroscedastic, as the error is not consistent at all points for our model. It is also important to remember this does not just happen when there is a high value of X, heteroscedasticity can occur at any given value.


<center><img src="{{ site.baseurl }}/Homoscedasticity-300x196.png" alt="Img"></center>

<center><img src="{{ site.baseurl }}/Heteroscedasticity-300x193.png" alt="Img"></center>
### 3.Independence: Observations are independent of each other.

Residuals should not have any correlation amoung themselves. Ultimetly we are trying to avoid the second error correlating with the first error point.

### 4.Normality: For any fixed value of X, Y is normally distributed.
The residuals should be close to the regression line, with few outliers. To check this the qqplot is used, to pass this assumption there should be most of the residual plots following the line especially through the center of the line

<a name="section4"></a>

# Generalised linear models
Now that we have covered how to evaluate your linear models, we will now look at generalized linear models. R2 values are calculated when normal distribution is assumed, however this is not the case with generalized linear models. 

### When to use a Generalised linear model
Generalized linear models are used for when we have Poisson or Binomial distribution, you can learn more about these distributions [here](https://ourcodingclub.github.io/tutorials/modelling/#distributions), but essentially, poisson distribution is used for count data (ie data that can only be whole numbers and cant be negative), and Binomial is used for proportion data (ie data with a limited number of outcomes, in the case of binomial this is 2). The reason we can't use R-squared for poisson is because one of the assumptions is that the variance in each point is equal to the mean of the data. Therefore it is not the distribution of the data itself that needs to appear normally distributed with poisson, but the error. However problems start to occur when you get to the lower end of the data in these poissons. For example imagine you are looking at the number of crows sitting on houses. Say the average number was 5, this means that for poisson test of normality the distribution in error must be 5, but with count data the values can not be less than 0! So for values of 1 crow, the distribution in error around this could not be 5 as this would become negative. See the problem? So to get around this we need to make the data behave as if it is continuous, even if it is discrete. One way to do this is log transforming the data. This is what separates a linear model and a generalized linear model, the predictor values are log transformed and therefore exponential.

Now we have done this, using the standard R-squared value is no longer applicable, as it assumes normal distribution which no longer applies to Poisson and instead we use Information criteria. Information Criteria is a measure of the quality of a model, that takes into account how well the the model fits the data, and how complex the model is. The  most commonly used metrics of this is AICs in which we will be using in this tutorial (other uses can include BIC, AICc, DIC, WAIC, LOO-CV, if you want to research more on these!). Both of these work around the same framework of finding a balance between how well your model fits the data and how complex your model is, and the lower the value you obtain the better your model fits the data

To look at GLMs and how to compare them, we will be using a fake dataset constructed for this tutorial, looking at how Grouse abundance varies across different habitat types, carbon concentrations and vegetation cover. First lets import this 

```r
#Load data ----
#Remeber to make sure the working directory is set to where the files are saved
Grouse <- read.csv("Grouse_data.csv")
#Use 'head' to have a quick look around the data
head(Grouse)
```
This shows us the headings for each column as well as the first 6 rows. The Grouse column shows the number of Grouse found at each site, and we will be looking at how the other columns change this number. To get a feel for te abundance, we can use the `hist`  function to plot a simple histogram.

```r
hist(Grouse$Grouse)
```

<center><img src="{{ site.baseurl }}/hist_grouse.png" alt="Img"></center>

The data seems to be slightly left skewed, but fairly normally distributed.

Now we have done this we can start constructing our Generalised linear models to see how our different variables impact grouse abundance. First we will construct a a model with each variable seperately, allowing us to asses which model can explain our variation the most. In ecological and environmental studies, it is important that this is not the approach used when analysising your data. Always try to construct models by using the parameters you set out at the start of your study, and make the most ecological sense. Trying every variable in every combination to obtain a significant result is a hot topic in the world of statistics, and is refered to as p-hacking. So keep this in mind when using these model comparisons, they are good to explore your data and see how the variables interact within your data sets, but models should be built off of existing ecological knowledge. Anyway, now the disclaimer is out of the way, we can plot our models to investigate the different impact of our variables.

```r
#First we will build a model to see how abundance varies with habitat type
model1 <- glm(Grouse ~ Habitat, data = Grouse, family = poisson)
#Now lets call on our model
model1
```
Here we use the glm function in r to fit a generalized linear model, followed by our dependent variable `Grouse`, then `~ Habitat` this lets our model know this is our independent variable. `data = Grouse` lets the model know what data set we are going to be using, this is usefull as often you can have multiple datasets in one R session. Finally we have `family = poisson`, this is used to let the model know what kind of probability distribution we want to use for the dependent variable. We went over earlier the need for poisson distribution for count data, as it allows us to add a log link preventing our model from producing negative values, which is important as reporting negative grouse would be a little tricky.

When we run `model1` it calls on our model, and gives us values for our coefficients and our total degrees of freedom, which is our sample units minus one, and our residual degrees of freedom, which is the number of samples minus the number of parameters we have set, in our case 2. You might notice that we have not estimated any variance or error, this is beacuse we are using the poisson distributions, where the variance is estimated to be approximately the mean. However if your variance is greater than the mean you have what is called __over dispersion__ (this word is given to models that show greater variability in a datset than is expected), to check for this we find our `residual deviance` and divide it by our `residual degrees of freedom`, if the value is greater than 1 then the model suffers from over dispersion. 


<center><img src="{{ site.baseurl }}/Mod1.png" alt="Img"></center>
`19.47` / `23` = `0.846`. Hurray our model doesnt suffer from over dispersion! 

Now we make models using the other variables as the independent variable, checking each as we go.

```r
model2 <- glm(Grouse ~ Carbon, data = Grouse, family = poisson) #Carbon content as the independent variable
model3 <- glm(Grouse ~ Vegetation, data = Grouse, family = poisson) #Vegetation cover as the independent variable
model2
model3 # call on our new models 
```
Now we have created models looking at our variables, we can create what is called a `null model`, this is done through only estimating the intercept. In R this is done through making our independent variable `1`, this doesnt mean anything its just how R works, doing this will create an intercept model, or null model, that can be used to compare how our independendt variables impact our dependent.

```r
modelnull <- glm(Grouse ~ 1, data = Grouse, family = poisson)
```
Now we have our models are constructed, we can mode on to comparing them. As we mentioned before the assumptions of a linear model have been broke, therefore the R2 value can no longer be used with confidence, so we move onto using information criteria to compare models.

<a name="section5"></a>

## Information Criteria & AICs

The formula behind this is : AIC = 2k - 2log(L), where k is the number of parameters, log is the natural logarithm, and L is essentially how well the model fits the data. This can be broken down into two parts, the first part being 2k and the second being -2log(L). The first part is a penalising factor for more complex mode with every parameter increasing this. The second part decreases as our model gets better at explaining our data. Therefore if you add more and more parameter you will get a higher first part, therefore you need to have it fitting your data reallllly well to get a lower value. 

The reason for this is to explain your data, the best way is to include every single parameter, and explain every single bit of variation. However this is not what we use models for, and therefore the AIC can be used for finding what parameters are best for explaining your data, and comparing models using the data against each other. However it should be remember that this its use, in helping strike a better balance between different models complexity and explanatory power, rather than telling you how well a particular model explains your data.

To use information criteria in R we use the `AIC()` with our models within the brackets to let the comparison take place. 

```r
AIC(modelNull, model1, model2, model3)
```

<center><img src="{{ site.baseurl }}/AIC1.png" alt="Img"></center>

This table shows the AIC values for each of the models. The actual value of the AIC isnt important, as this can be a range of values, even negative! Whats more important is the difference between the values, if the difference between the AICs is between 0-2, the models are eqivelent, 2-4 somewhat differnt models, 4-7 the models are considerably different, 7-10 the models are hugely different. From our results we can see that model 1 & 2 are below our null model, but model 3 is not, therfore vegetation can be disregarded as not explaining our variation. The model with the lowest AIC appears to be the model2 with carbon content as the independent variable, followed by habitat type. To look at these more we can combine them into the same multivariate model and run AIC on these. If you want to learn more about mixed models you can check out the tutorial [here](https://ourcodingclub.github.io/tutorials/mixed-models/#FERE).

```r
model4 <- glm(Grouse ~ Carbon + Habitat, data = Grouse, family = poisson)

model5 <- glm(Grouse ~ Carbon * Habitat, data = Grouse, family = poisson)

model6 <- glm(Grouse ~ Carbon + Habitat + vegetation, data = Grouse, family = poisson)

AIC(modelnull, model1, model2, model3, model4, model5, model6)
```
Here we have added an additional variables in our model to see if this lowers our AIC value. We do this through a `+` which adds the secong variable as an independednt, `*` however adds an interaction term, this lets the model know there is some connection between these variables and lets them interact. From the results we can see that all of these models give lower AIC models than our null model, but none as low as our model with just Carbon as our independent variable. From model6 we can see how the AIC operates, you would expect adding all the variables would use the best performing modeln however we can see here that adding the extra variable actually increases the AIC due to its penalising formula for over complicated models.

Now we can use the summary function to look at model2 to see a bit more about it 
```r
summary(model2)
```
To look into the relationship between Grouse populations and carbon content in the habitat we can use ggplot again
```r
#plot using the ggplot function to look at the relationship between grouse populations and carbon content
ggplot(Grouse, aes(x = Carbon, y = Grouse)) + 
  geom_point() +
  stat_smooth(method = "glm", col = "red")
```

<center><img src="{{ site.baseurl }}/glmplot.png" alt="Img"></center>

There you have it, you have conduced a model comparison on an abundance count, and found Carbon to be the independent variable to explain the most variation in your data. You can now go onto use this information to pose a more specific hypothesis looking into how carbon content in a habitat influences grouse populations.

<a name="section6"></a>
# 6.Challenge yourself
Well done on finishing this tutorial! I hope now your understanding of models, the difference in linear and generalized linear, and how to use R-squared and AIC values, and had fun and made memories along the way!

Satistics and models can be a scary thing to start, but hopefully know you feel a bit more comftorable and confident in learning more. 

Take the next step and try our tutorial on [distributions and linear models](https://ourcodingclub.github.io/tutorials/modelling/) where you can learn more about the topics we started today, and introduce ANOVAs as a method of model comparison. 

Or if ypu enjoyed making the graphs, try the introduction tutorial to [ggplot and data visulisation](https://ourcodingclub.github.io/tutorials/datavis/)!


------------------------------------------------------
#### Check out our <a href="https://ourcodingclub.github.io/links/" target="_blank">Useful links</a> page where you can find loads of guides and cheatsheets.

#### If you have any questions about completing this tutorial, please contact us on ourcodingclub@gmail.com

#### <a href="INSERT_SURVEY_LINK" target="_blank">We would love to hear your feedback on the tutorial, whether you did it in the classroom or online!</a>

<ul class="social-icons">
	<li>
		<h3>
			<a href="https://twitter.com/our_codingclub" target="_blank">&nbsp;Follow our coding adventures on Twitter! <i class="fa fa-twitter"></i></a>
		</h3>
	</li>
</ul>

### &nbsp;&nbsp;Subscribe to our mailing list:
<div class="container">
	<div class="block">
        <!-- subscribe form start -->
		<div class="form-group">
			<form action="https://getsimpleform.com/messages?form_api_token=de1ba2f2f947822946fb6e835437ec78" method="post">
			<div class="form-group">
				<input type='text' class="form-control" name='Email' placeholder="Email" required/>
			</div>
			<div>
                        	<button class="btn btn-default" type='submit'>Subscribe</button>
                    	</div>
                	</form>
		</div>
	</div>
</div>

