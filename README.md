### Alex Thiery - R training for bulk RNAseq


Here is a walk-through of the Streit lab R training sessions.

You can download this repository and run these sessions on your local machine by clicking on the "clone or download" button above.

With each session outlined below, you will also find an homework task and corresponding answers. This is a great way to test that you have understood the basic concepts highlighted in the sessions, and that will start to show you how to apply these to RNAseq analysis.

***

#### Week 1

[Homework](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week1_homework.R)
[Answers](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week1_homework_answers.R)

***

##### In this session we will look at basic R functions, basic datatypes, and how to access elements from dataframes and matrices

Installs packages from CRAN repository online
``` r
install.packages('pheatmap')
```

This will remove the package from your library - this will only take place once the current R session is restarted

``` r
remove.packages('pheatmap')
```

Loads installed package into current R session - you need to load the package every time
``` r
install.packages('pheatmap')
library(pheatmap)
```

Functions have brackets after them
c stands for concatenate/combine - it will combine the arguments you provide it with
``` r
c()
```

Functions take arguments which are separated by commas
``` r
a <- c(1, 4, 7)
```

We can also check the class of the data using the class() function
``` r
class(a)
```

Using quotation marks to set character data
``` r
b <- c("hello", "world")

class(b)
```

This is the case even if you're using inserting numbers as strings
``` r
c <- c("1", "2", "3")

class(c)
```

Vectors can only take one type of data - this will become a character vector
``` r
d <- c("hello", "world", 1, 3)

class(d)
```

You can convert data types - i.e. numeric characters can be converted to numeric data using as.numeric()

However, you cannot turn non-numeric characters into numeric data types - this will force NAs instead

``` r
as.numeric(c("hello", "world", 1, 3))

as.numeric(c("1", "2", "3"))
```


To access elements of a vector, use squared brackets
``` r
a[3]
```

Vectors are 1 dimensional
``` r
1:10
c(1,2,3,4,5,6,7,8,9,10)
```

Matrices and dataframes are 2 dimensional
``` r
mat_1 <- matrix(data = 1:10, ncol = 2)
```

To access elements of a matrix or dataframe - specify row and column position [row,col]
``` r
# access row 3, col 2
mat_1[3,2]

# access all rows from col 1
mat_1[,1]
```

Let's make a test dataframe

This dataframe has two columns named "names" and "heights"
``` r
test.heights <- data.frame("names" = c("Alex", "Scarlet", "Fereshteh"), "height" = c(200, 160, 155), stringsAsFactors = F)
```

You can see that columns of dataframes can contain different data types
``` r
class(test.heights[,1])
class(test.heights[,2])
```

As with vectors, you can subset columns and rows by positions as well as named matches
``` r
test.heights[,"names"]
```

One major difference between matrices and dataframes!

Matrices can only store 1 type of data...

Here I am setting 4 vectors (3 numeric - 1 character)
``` r
a <- 1:5
b <- 6:10
c <- 11:15
d <- c("Alex", "Scarlet", "Vida", "Ailin", "Owen")
```

cbind turns these into a matrix
``` r
test.matrix <- cbind(a, b, c, d)
```

Matrices can only store one class of data
``` r
class(a)
class(d)

test.matrix[,1]
class(test.matrix[,1])
```
***

##### Subsettting dataframes by a match in one column

One way to do this would be to assign the rownames as the column you are searching for - and then subset based on rowname match
``` r
test.dataframe <- data.frame(a,b,c,d)
rownames(test.dataframe) <- test.dataframe[,4]
test.dataframe["Vida",]
```

Another way would be to use boolean operators
``` r
test.dataframe <- data.frame(a,b,c,d)
"Vida" %in% test.dataframe[,4]

test.dataframe[,4] %in% "Vida"

test.dataframe[test.dataframe[,4] %in% "Vida",]

test.dataframe[test.dataframe[,4] %in% "Vida", 4]
```

<br/>
<br/>

***

#### Week 2

[Homework](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week2_homework.R)
[Answers](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week2_homework_answers.R)

***
##### In this session we will look at list data structures, and ordering and combining dataframes stored within lists
***

RDS files are saved R objects from a previous R session

Reading in bulk RNAseq data
``` r
RNAseq.data <- readRDS(file = 'test_data/fake_bulkdata.RDS')
```


Use summary() to access information as to the structure of the data
``` r
summary(RNAseq.data)
# RNAseq.data is a list
```


It contains 7 dataframes
``` r
length(RNAseq.data)
```
***

##### How do lists work?
Lists can store any combination of data types, including other lists (see "c" in list below) let's make an example list
``` r
mylist <- list("a" = c(1,2,3,4,5),
               "b" = matrix(data = 1:100, nrow = 10),
               "c" = list("a" = "b"))

length(mylist)
names(mylist)
```


##### Accessing elements of a list

You can subset a list by position, TRUE/FALSE vector, or by name
``` r
mylist["a"]
mylist[1]
mylist[c(T,F,T)]
```

Using single brackets subsets the list including the names for those list elements
This means that the object returned will still be a list (even if it is of length == 1)
``` r
mylist[1]
class(mylist[1])
```

In order to direcly access the CONTENTS of that list element and modify them, we need to use double square brackets
``` r
mylist[[1]]
class(mylist[[1]])
```

Going back to our test.data - we have 7 samples, each stored as a dataframe in a list if we look at the first dataframe in the list, we can see it contains gene name and gene counts
``` r
head(RNAseq.data$SOX8_1)
```

The order of the rows is clearly not the same between the datasets
``` r
head(RNAseq.data$SOX8_1)
head(RNAseq.data$SOX8_2)
```

Here we need to check if the genes in sample 1 are present in sample 2

To do this we can use the operator %in%. This returns TRUE or FALSE values for each S1 element (S1 %in% S2) depending on its presence in S2
``` r
c("a", "d") %in% c("a", "b", "c")
```

However the order in which you specify the arguments can give different outputs i.e this will give all T, even through 'e' is not present in the first vector
``` r
c("a", "b", "c", "d") %in% c("a", "b", "c", "d", "e")
```

Intersect compares both arguments against each other and only returns those which are shared
``` r
intersect(c("a", "b", "c", "d", "f", "h"), c("a", "b", "c", "d", "e", "z", "h"))
```

any() will check to see if any of the elements are true, all will check to see if ALL are true
``` r
any(c("a", "b", "c") %in% c("a", "b"))
all(c("a", "b", "c") %in% c("a", "b"))
```

Now we can check this in our actual dataset
all() genes are shared between the datasets, however they are not in the same order

``` r
all(RNAseq.data$SOX8_1[,"gene_name"] %in% RNAseq.data$SOX8_2[,"gene_name"])
```

***

##### Genes can have multiple ENSEMBL IDs and therefore once you lose this information you may end up with duplicated gene names
``` r
RNAseq.data$SOX8_1$gene_name[duplicated(RNAseq.data$SOX8_1$gene_name)]
```

You can make values unique using make.unique which will append a suffix if they are duplicated
``` r
make.unique(c("a", "b", "c", "c", "c", "b", "d"))
```

**It is better to use EnsemblIDs for this reason**, however for now lets just remove the genes which are duplicated

Using ! inverses a T/F vector - allowing us to keep only the genes which ARE NOT duplicated
``` r
temp2 <- RNAseq.data$SOX8_1[!duplicated(RNAseq.data$SOX8_1$gene_name),]
```

After removing these genes we can see there are no longer any duplicated gene names
``` r
any(duplicated(temp2$gene_name))
```

We now need to remove duplicated gene names in ALL 7 samples!!! HOW DO WE DO THIS?

We can use a **for loop!**

***

##### For loops - The basics

This for loop will print out the variable i for every element in 1:5

Let's think about this - 1:5 returns the following array: [1] 1 2 3 4 5

Therefore in the following for loop, i first becomes 1, then 2, then 3... and is subsequently printed

``` r
for(i in 1:5){
  print(i)
}
```

Just as we can provide numbers to for loops, we can also provide character vectors
``` r
names = c("Alex", "Scarlet", "Vida")
for(i in names){
  print(i)
}
```
***

##### Now let's apply this for loop to in order to remove duplicated genes from all of our samples in RNAseq.data

The gene names can be accessed like this
``` r
RNAseq.data[[1]][,"gene_name"]
```

Because we want to output our results to a new list, we need to initialise an empty list which I have called mylist, which we then populate in the loop

``` r
# create empty list
mylist <- list()

# for loop
for(i in names(RNAseq.data)){
  mylist[[i]] <- RNAseq.data[[i]][!duplicated(RNAseq.data[[i]]$gene_name),]
}
```

We can now see that there are no duplicated gene names in the dataframes
``` r
all(duplicated(mylist[[1]]$gene_name))
```


<br/>
<br/>

***

#### Week 3

[Homework](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week3_homework.R)
[Answers](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week3_homework_answers.R)

***
##### Today we will work on how to use for loops to combine multiple RNAseq samples together
***

###### Code to generate test data 1
``` r
temp <- c()
for(i in c("A", "B", "C", "D", "E")){
  temp <- append(temp, paste0(i, 1:5))
}

# Generate random gene counts
test.data1 <- list()
for(i in c("s1", "s2", "s3", "s4", "s5")){
  test.data1[[i]] <- data.frame(sampleID = temp, counts = sample(1:50, length(temp)), stringsAsFactors = F)
}

# save copy as test.data2 for later use
test.data2 <- test.data1
```
***

##### Changing column names

You can change column names directly using strings
``` r
s1 <- data.frame(names = c("a", "b", "c", "d", "e"),
                 counts = 1:5)

s2 <- data.frame(names = c("a", "b", "c", "d", "e"),
                 counts = 6:10)

colnames(s2) <- c("newname", "newcounts")
```

If you want to change just one of the colnames, you can specify this using square brackets [ ]
``` r
colnames(s1)[2] <- "newcounts"
```

##### Now lets do this on our test.data1 list!

First lets change the column names of each dataframe so that the counts column is labelled with the sampleID
``` r
for(i in names(test.data1)){
  colnames(test.data1[[i]])[2] <- paste0("counts.", i)
}
```

You can change all column names at once if you provide a VECTOR of the same length as the column names

``` r
for(i in names(test.data1)){
  colnames(test.data1[[i]]) <- c("alex", paste0("counts.", i))
  print(test.data1)
}
```

Now we make a loop to merge our 5 dataframes (test.data1) into a single dataframe called 'output'
``` r
output <- test.data1[[1]]
for(i in test.data1[2:5]){
  output <- merge(x = output, y = i, by = "sampleID")
}
```

***

Generating test data 2

``` r
# Randomly delete 5 genes from each dataframe in list
for(i in 1:length(test.data2)){
  test.data2[[i]] <- test.data2[[i]][-sample(1:nrow(test.data2[[i]]), 5),]
}
```

Task1: change the names of the 'counts' column in order to reflect the sample, then merge the dataframes into one

First lets change the column names of each dataframe so that the counts column is labelled with the sampleID
``` r
merged.data <- list()
for(i in names(test.data2)){
  colnames(merged.data[[i]])[2] <- paste0("counts.", i)
}
```

Now we make a loop to merge our 5 dataframes (test.data1) into a single dataframe called 'output'
``` r
merged.output <- merged.data[[1]]
for(i in merged.data[2:5]){
  merged.output <- merge(x = merged.output, y = i, by = "sampleID")
}
```


Task2: do same as above, but this time instead of using merge: 1) manually subset shared genes, 2) order dataframes into same order, 3) cbind columns of the dataframe

First get list of shared genes
``` r
shared.genes <- test.data2[[1]]$sampleID[
  test.data2[[1]]$sampleID %in% test.data2[[2]]$sampleID &
  test.data2[[1]]$sampleID %in% test.data2[[3]]$sampleID &
  test.data2[[1]]$sampleID %in% test.data2[[4]]$sampleID &
  test.data2[[1]]$sampleID %in% test.data2[[5]]$sampleID
]
```

Make one loop which: dynamically changes names, subsets shared genes and reorders dataframes
``` r
newdat <- list()
for(i in names(test.data2)){
  colnames(test.data2[[i]])[2] <- paste0("counts.", i)
  newdat[[i]] <- test.data2[[i]][test.data2[[i]][,"sampleID"] %in% shared.genes,]
  newdat[[i]] <- newdat[[i]][order(newdat[[i]]$sampleID),]
}
```

Rows are in the now in the same order, so we can use cbind to combine the columns
``` r
newdat <- cbind(newdat[[1]], newdat[[2]], newdat[[3]], newdat[[4]], newdat[[5]])
```

We now have duplicated columns (the sample IDs), so we need to keep only those which ARE NOT duplicated
``` r
newdat[,!duplicated(colnames(newdat))]
```

<br/>
<br/>

***

#### Week 4

[Homework](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week4_homework.R)
[Answers](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week4_homework_answers.R)

***

Introduction to custom functions

The examples functions have been made to replicate the base R sum() function and are therefore for illustrative purposes only.

R has many base functions and thousands of packages which can handle most of the tasks you will need to carry out. However, there are instances that you will need to make your own functions.


Basic function syntax
``` r
function(arg1, arg2){
  someprocess
  return(output)
}
```

Custom function to add two elements together
``` r
add <- function(x,y){
  return(x+y)
}
```

Arguments are assigned in order, unless they are exlicitly stated
``` r
samplefunc <- function(x,y){
  print(x)
  print(y)
}

samplefunc(1,2)

samplefunc(y = 1, x = 2)
```

Only the arguments specified in a function can be provided
``` r
add <- function(x,y){
  return(x+y)
}

add(1,2,3)
```

If an argument doesnt have a default, it must be provided
``` r
add <- function(x,y,z){
  return(x+y+z)
}

add(x = 1, y = 2)
```

When default arguments are assigned, arguments do not have to be provided
``` r
add <- function(x = 0, y = 0, z = 0){
  return(x+y+z)
}

add()
add(x = 1, y = 3, z = 5)
```

There are many situations where you want to carry out an operation on a vector of variable lengths
For example we might want to add the vector elements together without knowing how long our vector is

Here is our vector - how can we add these together using a for loop?
``` r
c(1,2,3,4,5)
```
We need to initiate temp outside of the loop as either: 0, or the first value of our vector. This provides us with an initial value to add to
``` r
sum.loop <- function(x){
  temp = x[1]
  for(i in x[2:length(x)]){
    temp = temp + i
  }
  return(temp)
}

sum.loop <- function(x){
  temp = 0
  for(i in x){
    temp = temp + i
  }
  return(temp)
}
```


We can use an ellipsis in order to supply non-hard coded arguments
``` r
ellip <- function(...){
  args <- c(...)
  temp = 0
  for(i in args){
    temp = temp + i
  }
  return(temp)
}

ellip(x = 1, y = 4, h = 8, v = 7)
```


The syntax to if else statements is as follows
The statement between the () should return TRUE or FALSE, which dicates whether the operation in {} is carried out

``` r
if(TRUE){
  some_operation
} else if(TRUE){
  some_other_operation
} else{
  final_operation
}
```

We can use if else statements to choose which operation we want to carry out in our custom function
``` r
sum.sub <- function(operation, ...){
  if(operation == "subtract"){
    return(sum(-c(...)))
  } else if (operation == "add"){
    return(sum(c(...)))
  } else if (operation == "multiply"){
    temp = 1
    for(i in c(...)){
      temp = temp*i
    }
    return(temp)
  } else {stop("can only add, subtract or multiply")}
    
}

sum.sub(operation = "add", 1,2,3,4)
sum.sub(operation = "subtract", 1,2,3,4)
sum.sub(operation = "multiply", 1,2,3,4)
sum.sub(operation = "sum", 1,2,3,4)
```

***

#### Week 5

[Homework](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week5_homework.R)
[Answers](https://github.com/Streit-lab/R_for_bulk_RNAseq/blob/master/week5_homework_answers.R)

Week 5 entails putting the tools you have learned together in order to generate a single counts matrix from multiple samples. Work through the homework above and cross-check with the linked answers.
***


