# intro - 1m 41s
Hi everyone, my name is Justin Ting, and my supervisor is Simon O'Callaghan from NICTA. The topic I'll be talking about today is the approximation of sparse Gaussian Processes in benthic habitat classification. So, first consider that less than 10% of the Earth's oceans have been mapped, even though they take up over 70% of the Earth's surface. There's compelling reasons we want to have a better understanding of our oceans and how they're changing, as we need to be able to better monitor them and how they cope with changing environmental conditions, and take necessary steps to preserve their pristine state.

To do this, we use something called benthic habitat mapping. Benthic refers to the region at the lowest level of a body of water, essentially the seafloor. Habitat mapping is the creation of maps by using high volume low resolution data and low volume high resolution data - and so by modeling a relationship between the high and low resolution data in areas where they overlap, we can extrapolate by assuming this relationship in areas where we only have low resolution data, thus creating a habitat map.

High resolution data generally come in the form of still images or videos that are later processed into images for clustering and classification, while low resolution data is generally acoustic backscatter data that provides properties of the seafloor such as depth, density, roughness, slope etc., which is obtained by measuring the strength and frequency of the radio waves fired at the ocean floor.

# problem statement - 1m 40s
The main difficulty with any habitat mapping is that the collection of high resolution data is prohibitively expensive when considering the full possible space for mapping. This is especially the case for benthic habitat mapping as deep sea exploration is far more costly than terrestrial exploration - the cost of capturing high resolution images of every inch of the ocean floor would be astronomical, and yet it is still knowledge we need to be able to prevent the oceans from degrading to the point of affecting human life irreversibly. 

Most studies use deterministic machine learning methods that in essence hard code the relationship between variables, such as depth and sediment type, i.e. sand or gravel, etc. collected from the data, implicitly making assumptions that have long been debated in marine mapping studies. Deterministic methods by definition also state their results with full confidence, meaning that a prediction that would only otherwise have 30% confidence has the same weighting as one that is 90% confident. 

We aim to create maps that state their certainty for any predicted area to allow governments or organisations to make better informed decisions regarding the management and preservation of marine bodies. To do this, we need an approach that inherently states its confidence levels in the predictions made, while doing so without explicitly defining the relationships between variables, especially as these can vary from habitat to habitat.

# solution - 3m
To attempt to solve this problem, we use something called Gaussian Processes for the classification of habitats, which returns us a distribution of possible habitats in a particular area, allowing us to state our certianty with any one prediction. However, there is a inherent limitation with using GPs naively with full datasets, as there is an O(n^3) operation. This means that any dataset with more than several thousand points is no longer computationally tractable, something that we will attempt to solve so that we can utilise all the data.

For this study, our high resolution data was collected using USYD's AFRC's AUV, Sirius, at Scott Reef, a lagoon off the coast of Western Australia, and our acoustic backscatter data was obtained using Eric Schmidt's Falkor ship.

Some of the concepts I am about to very briefly go over each warrant their own lecutres, if not multiple, so if you wish to see some of the details, feel free to refer my mini paper on the INFO5993 site. 

To give a brief overview of Gaussian Processes - when a variable has a Gaussian distribution, it means that it is normally distributed, better recognisable as the bell curve. Gaussian Processes are simply an infinite collection of these random variables, such that any linear combination of them are also Gaussian distributed. 

Gaussian Processes are defined by their mean functions and covariance functions - and the cubic computational complexity I mentioned earlier is a result of needing to invert the covariance matrix when performing predictions. As this is not possible to do outright on even our small training dataset of 16,000 points, we must use methods of approximation to utilise all the data.

The three methods we will use are Subset of Regressors, Projected Processes, and Fully Independent Training Conditionals, using Subset of Data as a benchmark. The gist of the first three methods are that they attempt to represent the full dataset of *n* points by communiciating through only *m* latent variables, where m is consideriably less than n. Subset of Data, however, simply as the name suggests, takes a subset of the data to represent the full dataset, which we predict to underperform compared to the more complex methods.

# results - 1m 20s
As a baseline for our Gaussian Process classifiers to see how they perform to more commonly used deterministic machine learning algorithms, here are the results of k nearest neighbours, logistic regression, random forest, and SVM. The aggregated classes are referring to a summarisation of the more granular and specific 24, done in collaboration with an expert. For example, 3 visually similar or even identical habitats defined as separate by the unsupervised clustering algorithm would have been combined into one. As you can see, the accuracy for these is around 68% for the granular classes, and 81% for the aggregated classes. 

So now let's look at the results using different methods of approximating sparse Gaussian processes. As expected, our subset of data baseline was the lowest performing of the group. Of the remaining three, Subset of Regressors was the least performant, while Fully Independent Training Conditional was the highest performing. Most of this difference is likely attributable to the fact that the mathematical relationship between the unknown function and latent variables is fixed when using the subset of regressors method, whereas this is not the case with Fully Independent training conditionals, as they are, as the name might suggest, fully independent here. 

# discussion & analysis - 

