In this example, we'll train a logistic regression to classify tweets
using only the natural language text found in these tweets. We'll only
need about 800 tweets per user account.

Setup
-----

To run this example, you will need the following packages.

    install.packages("dplyr", "ROCR")

Step 1: Embedding all tweets
----------------------------

We'll use a collection of about 800 tweets from Bill Gates and Kanye
West and train a logistic regression to predict (given a tweet) which
account the tweet belongs to. In order to do that, we'll first load the
tweets from the basilica package.

    library(jsonlite)
    bill <- fromJSON(system.file("extdata/twitter/billgates.json", package="basilica"))
    kanye <- fromJSON(system.file("extdata/twitter/kanyewest.json", package="basilica"))

Now that we've loaded the JSON files, we can embedded the text of these
tweets using Basilica.

    library(basilica)
    conn <- connect("05e19f1c-39de-ed9c-ae42-feab42f5f84d")

    embeddings <- rbind(embed_sentences(bill[, 7], conn=conn), embed_sentences(kanye[, 7], conn=conn)) # 7 is the index of the text

Step 2: Running PCA + Cleaning Data
-----------------------------------

Now that we have these embeddings, we'll want to run PCA and get the 100
features that explain the most variance. We'll also add a column to the
matrix with the corresponding category each tweet belongs to.

    pca <- prcomp(t(embeddings), center = TRUE,scale = TRUE)
    features <- pca$rotation[,1:100]

    type <- c(integer(dim(bill)[1]) + 1, integer(dim(kanye)[1]))
    features <- cbind(type, features)
    features <- data.frame(features[sample.int(nrow(features)),])

Step 3: Training the model
--------------------------

Finally, we can now train our model. In order to do that we'll separate
out the data into training and test data.

    library(dplyr)
    train_data <- sample_frac(features, 0.8)
    train_index <- as.numeric(rownames(train_data))
    test_data <- features[-train_index, ]

    model <- glm(type ~ ., data = train_data, family = "binomial")

Step 4: Verifying Results
-------------------------

After training the model, we can verify who well it's trained by taking
a look at the confusion matrix.

    predict <- predict(model, newdata=test_data, type = 'response')
    table(train_data$type, predict > 0.5)

    library(ROCR)
    ROCRpred <- prediction(predict, test_data$type)
    ROCRperf <- performance(ROCRpred, 'tpr','fpr')
    plot(ROCRperf, colorize = TRUE, text.adj = c(-0.2,1.7))

You have now trained a logistic regression with only the natural
language text of the tweets and 800 data points per category and getting
an R squared of about `0.80`.
