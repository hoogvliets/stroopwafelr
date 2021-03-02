-   [1 Config](#config)
    -   [1.1 Libraries](#libraries)
    -   [1.2 ](#section)
-   [2 Import](#import)
    -   [2.1 Storage connectors](#storage-connectors)
    -   [2.2 Local connectors](#local-connectors)
-   [3 Tidy](#tidy)
    -   [3.1 Bla](#bla)
-   [4 Transform](#transform)
    -   [4.1 Bla](#bla-1)
-   [5 Model](#model)
    -   [5.1 Supervised learning](#supervised-learning)
        -   [5.1.1 ](#section-1)
    -   [5.2 Unsupervised learning](#unsupervised-learning)
        -   [5.2.1 K-means](#k-means)
        -   [5.2.2 PCA](#pca)
    -   [5.3 Times series](#times-series)
        -   [5.3.1 Arima](#arima)
    -   [5.4 Recommendation engines](#recommendation-engines)
        -   [5.4.1 ALS](#als)
-   [6 Visualise](#visualise)
    -   [6.1 Plotting](#plotting)
    -   [6.2 Maps](#maps)
    -   [6.3 Tables](#tables)
-   [7 Report](#report)
    -   [7.1 Rmarkdown](#rmarkdown)
    -   [7.2 Dashboards](#dashboards)

1 Config
========

1.1 Libraries
-------------

    # list of required packages
    l_packages <- list("caret",
                  "lubridate",
                  "magrittr",
                  "partykit",
                  "pROC",
                  "purrr",
                  "RODBC",
                  "skimr",
                  "tidyverse") 

    # install missing packages
    if (length(setdiff(l_packages, rownames(installed.packages()))) > 0) {
      install.packages(setdiff(l_packages, rownames(installed.packages())))  
    }

    # open required packages
    lapply(l_packages, library, character.only = TRUE)

    rm(l_packages)

1.2 
----

2 Import
========

2.1 Storage connectors
----------------------

2.2 Local connectors
--------------------

3 Tidy
======

3.1 Bla
-------

4 Transform
===========

4.1 Bla
-------

5 Model
=======

5.1 Supervised learning
-----------------------

### 5.1.1 

5.2 Unsupervised learning
-------------------------

### 5.2.1 K-means

#### 5.2.1.1 Scree and elbow plots to determine clusters

    scree_selection <- data_cluster[,-1]          

    # scree parameters
    scree_wss <- 0                 # initialize total within sum of squares error
    scree_k   <- 5                 # number of clusters to cycle
    scree_ns <- 10                 # number of random starts to cycle

    # For 1 to 15 cluster centers
    for (i in 1:scree_k) {
      km_out <- kmeans(scree_selection, centers = i, nstart = scree_ns)
      # Save total within sum of squares to wss variable
      scree_wss[i] <- km_out$tot.withinss
    }

    # Quickplot of total within sum of squares vs. number of clusters
    plot(1:scree_k, scree_wss, type = "b", 
         xlab = "Number of Clusters", 
         ylab = "Within groups sum of squares")

    # clean up environment
    rm(i, scree_selection, scree_k, scree_ns, scree_wss, km_out)

#### 5.2.1.2 Run clustering algorithm

    # set random seed for reproducibility
    set.seed(1337)

    # run k-means with 3 centers and 20 starts
    k_output <- kmeans(data_cluster_elite[,-1], 3, nstart = 20)

#### 5.2.1.3 Merge clusters to data

    data_cluster_elite$cluster <- k_output$cluster

#### 5.2.1.4 Calculate cluster means

    # calculate cluster means as seperate columns and bind them together
    clusters <-
      cbind(
    data_cluster_elite[,-1] %>% 
      subset(cluster == 1) %>% 
      colMeans(),
    data_cluster_elite[,-1] %>% 
      subset(cluster == 2) %>% 
      colMeans(),
    data_cluster_elite[,-1] %>% 
      subset(cluster == 3) %>% 
      colMeans()
    )

    # set cluster number as columnnames
    colnames(clusters) = clusters["cluster",]

#### 5.2.1.5 Plot cluster heatmap

    # remove row names cluster and melt data
    plotdata_h <-clusters[-which(rownames(clusters) %in% c("cluster")), ] %>% 
      melt()

    # remove columns to standardised names
    colnames(plotdata_h) <- c("Var1", "Var2", "value")

    # plot heatmap
    ggplot(plotdata_h, aes(Var2, Var1)) + geom_tile(aes(fill = value), colour = "white") + scale_fill_gradient(low = "white", high = "#01B8AA") + theme(legend.position="none", axis.line=element_blank(), axis.ticks=element_blank(), axis.title.x=element_blank(), axis.title.y=element_blank())

### 5.2.2 PCA

    library(FactoMineR)
    library(skimr)
    library(tidyverse)
    data("poison")
    skim(poison)

    snippet_mca <- poison %>% 
      select_if(function(col) is.factor(col) | is.numeric(col)) %>% 
      names()


    data_mcatest <- poison[snippet_mca]


    data_mcatest %>% skim()


    res.mca <- FAMD(data_mcatest,
                                 graph=TRUE)





    eig.val <- res.mca$eig
    barplot(eig.val[, 2], 
            names.arg = 1:nrow(eig.val), 
            main = "Variances Explained by Dimensions (%)",
            xlab = "Principal Dimensions",
            ylab = "Percentage of variances",
            col ="steelblue")
    # Add connected line segments to the plot
    lines(x = 1:nrow(eig.val), eig.val[, 2], 
          type = "b", pch = 19, col = "red")

    plot(res.mca, autoLab = "yes")

    plot(res.mca,
         invisible = c("var", "quali.sup", "quanti.sup"),
         cex = 0.8,                                    
         autoLab = "yes")

5.3 Times series
----------------

### 5.3.1 Arima

5.4 Recommendation engines
--------------------------

### 5.4.1 ALS

6 Visualise
===========

6.1 Plotting
------------

6.2 Maps
--------

6.3 Tables
----------

7 Report
========

7.1 Rmarkdown
-------------

7.2 Dashboards
--------------
