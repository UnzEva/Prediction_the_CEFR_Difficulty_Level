# Prediction the CEFR Level of English Movies

## Project Description
Watching movies in the original language is a popular and effective method to get pumped when learning foreign languages. It is important to choose a film that suits the student's level of difficulty, so that the student understands 50-70% of the dialogues. 

CEFR stands for Common European Framework of Reference. It is the most trusted language ability measurement system in the world. It ranks students as beginner, intermediate and advanced. CEFR is also used to rank the difficulty of teaching materials like reading and listening.

Organisations like Cambridge, Oxford, British Council, etc. rely on CEFR to structure their teaching content.

![An image](https://www.cathoven.com/wp-content/uploads/2021/11/cefr-levels.gif)

## Goals

The idea of this project is to develop an ML solution to automatically determine the difficulty level of English-language movies. We will develop a classification for these films based on their difficulty level.

## Data
### Key Data
- [Full list of movies and scores](https://www.notion.so/Films-according-to-CEFR-a702061c0fe341ec9fcc98c4eea341e3)
- [Subtitle folder](https://disk.yandex.ru/d/CkmiSRnKba76sA)

### Supplementary Data
- Similar work has been done here:
> - [The Best Movies to Learn a Foreign Language According to Data Science](https://towardsdatascience.com/the-best-movies-to-learn-english-according-to-data-science-2dccb4b3ee23)
> - [Attempting to Predict the CEFR Level of English Texts](https://amontgomerie.github.io/2021/03/14/cefr-level-prediction.html)
> - [A New Resource to Measure Text Difficulty](https://aclanthology.org/2020.lrec-1.890.pdf)
- There are services for analyzing text complexity:
> - [Text Inspector](https://languageresearch.cambridge.org/wordlists/text-inspector)
> - [CEFR Checker](https://www.cathoven.com/en/cefr-checker/)
> - [Oxford 3000 and 5000](https://www.oxfordlearnersdictionaries.com/wordlists/oxford3000-5000)
> - [Data Augmentation in NLP](https://neptune.ai/blog/data-augmentation-nlp)

# Loading and Preprocessing Data

CEFR dictionaries with difficulty levels are stored in text files.

level |text
------|--------------------------------------------------
A1    |[about, above, across, action, activity, actor...
A2    |[able, abroad, accept, accident, according, ac...
B1    |[academic, access, accommodation, account, ach...
B2    |[absolute, academic, acceptable, accompany, ac...
C1    |[abortion, absence, absent, absurd, abundance,...

Subtitles for movies are in separate files.
- Files with subtitles have been opened and read.
- The dataset is assembled.

Movie	                           | text
---------------------------------|-------------------------------------------------
10_Cloverfield_lane(2016)	       |font color="#ffff80"<b>Fixed & Synced by boz...
10_things_I_hate_about_you(1999) |Hey! I'll be right with you. So, Cameron. Here...
A_knights_tale(2001)	           |Resync: Xenzai[NEF]\nRETAIL Should we help him...
A_star_is_born(2018)	           |<i><font color="#ffffff"> Synced and correct...
Aladdin(1992)	                   |<i>Oh, I come from a land\nFrom a faraway plac...
  
Subtitles are messy and contain a lot of service information.
- Removed tags, punctuation, capital letters, stop words (prepositions and pronouns). 
- The text is divided into **tokens**, i.e. we received **a list of single words** included in the subtitles for each movie.
  
Movie	                           | text
---------------------------------|-------------------------------------------------
10_Cloverfield_lane(2016)	       |[fixed, synced, bozxphd, enjoy, the, flick, cl...
10_things_I_hate_about_you(1999) |[hey, i'll, right, so, cameron, here, go, nine...
A_knights_tale(2001)	           |[resync, xenzai, nef, retail, should, help, he...
A_star_is_born(2018)	           |[synced, corrected, mrcjnthn, get, black, eyes...
Aladdin(1992)	                   |[oh, i, come, land, from, faraway, place, wher...
  
A table with a target variable. 
  
The CEFR level is set by a linguistic expert. 
  
The criteria for assessing the assignment of the difficulty level are unknown.
  
#| Movie	                    |Level	|Subtitles	|Kinopoisk
-|----------------------------|-------|-----------|-----------
0|10 Cloverfield lane	        |B1	    |Yes	      |NaN        
1|10 things I hate about you	|B1	    |Yes	      |No subs    
2|A knights tale	            |B2	    |Yes	      |Everything 
3|A star is born	            |B2	    |Yes	      |Nope       
4|Aladdin	                    |A2/A2+	|Yes	      |Everything 
  
Merged the dataset with the movies and the target variable into one data frame.
  
## Target variable
  
There is a classification problem with five classes.

The data is unbalanced.

The dataset contains only 86 observations. This can significantly affect the accuracy of the prediction.
  
![image](https://user-images.githubusercontent.com/67246071/182303796-75804614-6f9a-479d-afb4-790b80e52829.png)

# Features Analysis
  
The distribution of words from the CEFR levels is close to normal.

That is, some words are quite rare, and the rest are quite common in all movies.

![image](https://user-images.githubusercontent.com/67246071/182303942-85dc52f7-bb54-4aee-930f-9b7ec08ae26d.png)

A1 level words dominate in all movies.
  
![image](https://user-images.githubusercontent.com/67246071/182304029-6e8337ed-e6a0-40d4-bbc4-9acef48ffd06.png)

The content of the words of each level for the target variable
  
![image](https://user-images.githubusercontent.com/67246071/182304180-7578885c-28f2-475a-9ace-2f40423908bc.png)

There is a good correlation between the levels of difficulty in movies.
  
![image](https://user-images.githubusercontent.com/67246071/182304300-2b5b87ff-2cab-42b5-a8a5-2f109352260e.png)

## Conclusion

 - The graphs show that all subtitles are dominated by A1-level words. Since the texts consist mainly of dialogues, this makes sense. 
 - Since there is a correlation between the number of words of each level, we will train numerical and text data separately.

# Prediction by text features
  
To increase the size of the dataset, we will generate synthetic data.

In this task, the semantic aspect of the generated sentences does not matter to us. Therefore, a method based on the frequency of use of training corps bigrams is taken.

- A dictionary based on text classification has been created. 
- The dictionary consists of grouped bigrams: the keys are individual words (tokens), the value is a list of words that follow them in a text corpus with their own frequency. 
- 5 sentences of 15 words each are generated.
  
#|Level|text
-|-----|------------------------------------------------
0|'B2' |seanathon, meter, jacket and blue i through, k...
1|'B1' |operating, spaghetti, well and ended with, eli...
2|'B1' |figure the i tired of silver tuna tonight, hyd...
3|'B2' |unpresentable appearance, patriot, belts, shoo...
4|'B1' |owned that anywhere i alive you last governmen...
  
**The dataset has 7,500 rows.**
  
## Machine learning
  
We have a multi classification problem, so we will choose:

- **Logistic Regression.** This is a classic classification algorithm. It has average accuracy, learns quickly, predicts linear dependence well, effective on small amounts of data.

- **Random Forest Classifier.** This algorithm is suitable for most machine learning tasks. It has excellent accuracy, average training time and a small number of hyperparameters.

- **XGBoost Classifier.** This algorithm uses weak linear algorithms and decision trees. It takes a long time to learn and has a large number of hyperparameters, but it has excellent accuracy.
  
## Accuracy
  
**Accuracy** is the percentage of documents for which the classifier made the correct prediction.

The **F1** metric is a harmonic mean between precision and recall.

The **precision** of the system within a class is the proportion of documents actually belonging to this class relative to all documents that the system has assigned to this class. 

The **recall** of the system is the proportion of documents found by the classifier belonging to the class relative to all documents of this class in the test sample.

The model with the addition of generated data gave the good result of prediction accuracy and F1 metrics. 

The initial classes are highly unbalanced, this is clearly visible in the precision and recall indicators, for poorly represented classes.
  
name of class| precision    |recall   |f1-score  |support
-------------|--------------|---------|----------|-------
'A2/A2+'     |      0.60    |  0.58   |   0.59   |  382
'A2/A2+,B1'  |      0.66    |  0.63   |   0.64   |  374
'B1'         |      0.65    |  0.66   |   0.65   |  378
'B1,B2'      |      0.61    |  0.65   |   0.63   |  371
'B2'         |      0.62    |  0.62   |   0.62   |  370
-------------|--------------|---------|----------|-------
accuracy     |              |         |   0.63   | 1875
macro avg    |      0.63    |  0.63   |   0.63   | 1875
weighted avg |      0.63    |  0.63   |   0.63   | 1875

The confusion matrix shows the predictions of the model against the true. On the diagonal there are correct predictions. There are prediction errors above and below.
  
![image](https://user-images.githubusercontent.com/67246071/182309646-0e995f7d-9af1-46d0-b55c-215336b3b1a6.png)

## Conclusion
  
Classifying a text with more than two classes is one of the most difficult problem in machine learning. 

Raw data requires serious processing before training the model. 

The size of the dataset and a strong imbalance in the classes have a very noticeable effect on the final accuracy of the prediction.
  
# Testing the Model
  
There are many sites on the Internet for learning English from movies. 

Movies are already marked up by difficulty levels. 

For prediction, the movie "Charlie and the Chocolate Factory (2005)" with difficulty level B1 is taken.

[Movies by Levels]('https://moviesbylevels.wordpress.com/')	
  
A2/A2+  |A2/A2+,B1 |B1   |B1,B2 |B2
--------|----------|-----|------|-----
0       |42        |98   |5067  |3  
  
### Conclusion
  
The close relationship between the difficulty levels **A2/A2+** -> **A2/A2+,B1** -> **B1** -> **B1,B2** -> **B2** in the training data affects the determination of the difficulty level.

Testing showed that for a movie with a difficulty level of B1, the model predicted more values of class **"B1,B2"**.

It accurately determined that this is not the A2 or B2 level, so we can develop this approach further to obtain more accurate forecasts.
  
# Conclusion
  
**Goal:**
The goal of the project is to predict the level of complexity of an English-language movie by subtitles.

**Baseline Information:**
- Small data size
- Strong class imbalance
- Lack of criteria for determining the level of difficulty

**Approaches:**
1. Building models based on numerical features of comparing CEFR dictionaries and movie subtitles. The results did not give a good level of accuracy. The best prediction turned out to be for the A2/A2+ level
2. Building models for text data based on constructing a vector of words of CEFR dictionaries in subtitles.
The accuracy results turned out better. The best prediction turned out to be for level B2.
3. Building models on generated text data using the probability of meeting several words in the text side by side. This approach gave the highest accuracy. The best prediction turned out to be for class A2/A2+

**Recommendations:**
1. Collect more data for training.
2. Use methods and linguistic approaches based on which experts determine the level of complexity of the text.
3. Implement pre-trained models to determine the complexity of subtitles by: semantic proximity of the meanings of texts, to find patterns and sequences of words and parts of speech in the text.

**Thank you for your interest!**
