# Does claw size affect mate choice in fiddler crabs?
Code by Lesley Morrell

![image](https://upload.wikimedia.org/wikipedia/commons/0/08/Uca_perplexa_male_waving.gif)

*Uca perplexa* male waving, source: https://commons.wikimedia.org/wiki/File:Uca_perplexa_male_waving.gif

Reaney did a series of 4 choice trials, where females have a choice of a larger or a smaller crab. Each trial has different combinations of crab sizes. She recorded the crab that each of ~20 females chose in each trial. She tested the hypothesis that females prefer the larger of the two crabs, but this depends on both the size difference and the absolute sizes of the two crabs. 

Reaney, LT (2009) Female preference for male phenotypic traits in a fiddler crab: do females use absolute or comparative evaluation? Animal Behaviour 77: 139-143

Here are her data:
```
T1small<-2
T1large<-18

T2small<-3
T2large<-17

T3small<-4
T3large<-16

T4small<-7
T4large<-13
```
Because I have a count of the number of females choosing each of two crabs, I can carry out a binomial test. A binomial test tests whether the proportion of females choosing the larger crab differs significantly from 0.5 (half of all females). I carry out a separate binomial test for each trial:

```
binom.test(T1large,T1large+T1small,0.5)
binom.test(T2large,T2large+T2small,0.5)
binom.test(T3large,T3large+T3small,0.5)
binom.test(T4large,T4large+T4small,0.5)
```
#To plot the figure, I need to put all the counts together into a single matrix
```
choice<-matrix(c(18,17,16,13,2,3,4,7),nrow=2,byrow=TRUE,dimnames=list(chosen=c("larger","smaller"),
	ratio=c("Trial 1","Trial 2", "Trial 3", "Trial 4")))
```

Then I plot my data as a barplot of the number of females choosing each type of male. The names.arg argument labels the x axis with the claw sizes rather than the trial numbers:
```
barx<-barplot(choice,beside=TRUE,col=c("black","white"),ylim=c(0,25), ylab="Times chosen by female", xlab="Claw size (mm)",names.arg=c("23","17","23","19","17","14","19","17"))
mtext(text=c("Trial 1","Trial 2", "Trial 3", "Trial 4"), side=1,at=colMeans(barx),line=2) #add the trial numbers to the x axis too
box() #draws a box around my figure
```
The variable barx provides the midpoint of each bar on the x-axis. This is useful if I want to draw lines/significance stars on my graph
```
barx #this shows me what the values are
```
For trial 1, I draw a line from the middle of the black bar to the middle of the white bar, at y = 19 (1 value higher than the number of females choosing the preferred crab). Then I add the p value above the line (at y = 20 and in the middle of the midpoints of the bars). I picked y=19 and y=20 because it looks clear and aesthetically pleasing.
```
segments(1.5,19,2.5,19,lty="solid") 
text(2,20,"p < 0.001")
```
![image](https://media.giphy.com/media/QykvUEm4yFU1a/giphy.gif)


In a second experiment, I want to test the hypothesis that females prefer larger crabs using observational evidence in the field. I measure every crab in a population of 100 males, mark them with little numbers, then release 40 females individually and record which crab they mate with. Once mated, a male will remain in his burrow guarding the female, so males can only mate once during my experiment.

As size is a measurement, it is likely to be normally distributed. Crabs (claws) have a mean size of about 18mm generally. First, I will create size for 40 chosen males and 60 males that were not chosen.
```
chosensize<-rnorm(n=40,mean=20,sd=4)
notchosensize<-rnorm(n=60,mean=18,sd=4)

hist(chosensize)
hist(notchosensize)

#combine these into a single variable
populationsize<-c(chosensize,notchosensize)
```

Now I will create a variable to show whether the males were chosen or not. Remember the first 40 in my overall variable population size were chosen, the rest were not.
```
lucky<-rep(1,40)
unlucky<-rep(0,60)
chosen<-c(lucky,unlucky)

#make a data frame
mydata<-data.frame(populationsize,chosen)
```

What I want to know is whether choice is affected by size: are chosen males larger than those that are not chosen? Choice is a yes/no variable (a male is either chosen, which I have designated as 1, or not chosen, which is designated as 0). This is clearly not normally distributed data, so I need a binomial error distribution, and a general linear model.

```
m1<-glm(chosen~populationsize,family=binomial)
summary(m1)
```
Alternatively, if this doesn't make sense to me, I can go back to the idea that size IS normally distrubuted, and use a t-test to examine whether the chosen and not chosen males differ from each other in size.
```
t.test(chosensize,notchosensize)
```
Then I want to make a nice plot. For the t-test, I would use a boxplot
```
boxplot(chosensize,notchosensize,names=c("Chosen","Not Chosen"))
#then I would add some nice labels
```

For the GLM I would plot
```
plot(populationsize,chosen)
```

Then I would add the fit line predicted from the model, which should be an s-shaped curve
```
xsize<-seq(5,30,1)
ypreds<-predict(m1,list(populationsize=xsize),type="response")
lines(xsize,ypreds)

```
This shows that as the crabs get bigger, they are more likely to be chosen. The key is now to make sure you put this into a wider context - why would females prefer larger males?
