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



