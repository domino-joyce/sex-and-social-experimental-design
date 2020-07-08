# Experimental design - zebra finches

Watch this video to get an idea of the background for this experimental design
https://www.youtube.com/watch?v=50N7EvuuzGY

We want to design an experiment to test whether beak colour affects mate choice in a bird species. For the purposes of this assessment, we don't need to actually carry out the experiment, so this gives us a bit of freedom. In the assessment, we want to see that you have thought about your design carefully, and know what data you are going to collect, what it will look like, and how you will analyse it. You are going to simulate the data in R, and submit the code to both simulate the data of the type you expect, and subsequently analyse it. We will run this code when we mark your assessment. This is an example of the type of code you might submit.

In our design, we will have two groups of male birds, one group have been given a diet supplement with extra caroteinoid, and one group have been given a normal diet.

We will then allow 100 female birds a free choice to mate with the males, and we will use DNA paternity testing to quantify the number of offpsring collected for each male. If beak colour is important, we predict that the mean number of offspring will be higher for birds with brighter beaks.

![image](https://upload.wikimedia.org/wikipedia/commons/c/c2/Zebra_finch_group.png)
image: Liza Gross https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.0040270


First, lets check that the diet supplement did indeed have an affect on beak brightness. Assume we can measure beak brightness using a spectrophotometer to measure the wavelength of light reflected. Orange/red beaks reflect longer wavelength light.

Beak brightness is measured in nm and the mean is about 550 nm, I expect this measurement to have a normal distribution because it is continuous.

```
#50 males in normal diet treatment, mean beak wavelength is 550 nm, and the standard deviation is 10 so the points are spread quite far from the mean.
  normal_diet<-rnorm(n=50, mean=550, sd=10)
  normal_diet

  special_diet<-rnorm(n=50, mean=560, sd=10)
  special_diet
```
Lets visualise those two data sets we have created by plotting histograms


```
par(mfrow=c(2,1))
hist(normal_diet)
hist(special_diet)
```
Next we want to turn the variables we have generated for each of the treatments into a dataframe, labelled with the treatment group each male was from.

```
dat<-data.frame(brightness = c(normal_diet, special_diet), treatment=rep(c('normal','special'), each=50))
dat #just shows you the data to make sure it looks as you are expecting
```
These are normally distributed data, and we now want to test whether there is a difference in mean brightness between the treatments.

```
t.test(dat$brightness ~ dat$treatment)
```

The output tells us that there is a significant difference in beak brightness between the two groups, so we can be sure that our different diets had a significant effect. In order to visualise this difference, lets make a quick boxplot
```
boxplot(dat$brightness ~ dat$treatment)
```
When you are producing your own script, make sure you annotate each line of code, and paste in your outputs so that you can explain how you will interpret your results.

OK, so now we know that the two treatments have different beak brightness, we will see if the number of offspring that each male has is significantly different in the brighter beak group. We predict that there will be a difference, so lets generate data assuming we're right. 

What kind of data will we get? Mean number of offspring per male is probably about 3, so lets say 3 for the normal group but 4 for the bright group. These data are not measurements, they are counts, so they are unlikely to be normally distributed. We need poisson distributed data.

```
par(mfrow=c(2,1))                   #creates a window with two spaces for plots one above the other
special <- rpois(n=50, lambda=5)    #creates data following a poisson distribution for 50 birds, with a medium skew
hist(special, xlim=c(0, 10))        #shows the data in a histogram
abline(v=4, lwd=3, col='red')       #puts a vertical red line at the mean value (4)
normal <-rpois(n=50, lambda=3)      #creates data following a poisson distribution for 50 birds, with a lesser skew
hist(normal, xlim=c(0, 10))         #shows the data in a histogram
abline(v=3, lwd=3, col='red')       #puts a vertical red line at the mean value (3)
```
We now want to make these values into a dataframe:

```
dat2<-data.frame(offspring=c(special, normal), diet=rep(c('special','normal'), each=50))
dat2
tapply(dat2$offspring, dat2$diet, mean) #shows a summary table of the data
```
A summary figure which would display the results would be useful
```
with(dat2, boxplot(offspring ~ diet))
```
and of course you would add code which would label it clearly in your final submission. 

We now want to see if there is a significant difference between the mean offspring number for the birds on a special diet, with brighter beaks, vs those on a normal diet. This is not normally distributed data so I need to use a non-parametric test

```
with(dat2, wilcox.test(offspring ~ diet))
```
You can then add your output to the code and explain how you would interpret it - and don't forget to put this into a wider context by explaining why you would expect beak brightness to affect female choice.

![image](https://media.giphy.com/media/K5mhAi01uxnos/giphy.gif)

Now let's add some more detail. Do females allocate more resources to their eggs when mate to an attractive male (i.e. males with brighter beaks)? We would predict that females mated to brighter males will produce heavier eggs. We need to generate a relationship between male beak brightness and female average egg weight.

For simplicity, we will have a simple linear relationship of the y = mx + c (+ error) variety. We first need to generate the relationship, and then add error.

We have already made the beak brightness data
```
(dat$brightness)
```
Let's assume eggs weigh about 1 g (with a range of about 0.8 g to 1.2 g). We are going to need to produce a range of egg weights spanning 0.4 g

Let's find what the range of beak brightnesses we generated is
```
range(dat$brightness)
```
The range of egg weights divided by the range of beak brightnesses gives is the factor by which to multiply brightness (about 0.008). We can work out the m value in the relationship - the amount of extra weight you get per increase in nm brightness

```
head((dat$brightness-min(dat$brightness)*(0.008)) 
```
To each, we need to add the minimum egg weight, which is our c value, and we called this 0.8 g

```
dat$egg.predicted <- (dat$brightness-529)*(0.008) + 0.8
```
We now have a set of predicted egg weights which increase with brightness. However, if you plot the data, you will see that it doesn't look at all realistic!
```
with(dat, plot(egg.predicted ~ brightness))
```
Clearly you'd never get data like this in nature! We need to add error. Each egg will be randomly heavier or lighter than predicted, according to a bell curve, due to genetic and environmental factors that we haven't measured.

For this we need to decide what the distribution of this error variable is. For measurement data like egg weight, distribution is typically normal. So we add a random normally distributed error variable with mean 0 (because there's no reason to assume eggs are systematically lighter or heavier). We adjust the standard deviation according to what looks reasonable.

```
dat$egg.error <- rnorm(n=100, mean=0, sd=0.2)
```
Now we have generated our error, we just have to add it to the egg weight data
```
dat$eggweight <- dat$egg.predicted + dat$egg.error
```
and have a look at the results
```
with(dat, plot(eggweight ~ brightness))
```
Now, if we had collected these data for real, we would need to analyse them to see if there is a significant relationship between egg weight and beak brightness
```
cor.test(dat$eggweight, dat$brightness)
```
And we can use a linear model to find the parameters of that relationship (i.e. what R thinks are the m and c values)
```
lm0 <- lm(eggweight ~ brightness, data=dat)
anova(lm0)
summary(lm0)
```

You can now compare what R thinks the m and c values are, with what you told it when you generated the data, to see how the error affected the parameters.


How was that? There is a lot of information here, and your assessment needn't be as detailed as this - we just wanted to include a variety of code to give you an idea of what is possible. Hopefully you can start to think about the types of experiment you might do, the types of data you will generate, and how you will analyse them, using and adapting some of this code where it helps. You will need to collect realistic background information (for example here, we used realistic egg weights and beak brightnesses).

If you would like to work through another design and dataset, have a look at the fiddler crab experiment in design-instructions2.md

