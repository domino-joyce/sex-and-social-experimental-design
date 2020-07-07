# Experimental design - zebra finches

Watch this video to get an idea of the background for this experimental design.


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


