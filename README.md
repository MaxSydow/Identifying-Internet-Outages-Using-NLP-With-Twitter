# Identifying-Internet-Outages-Using-NLP-With-Twitter

# About

This is a project proposal, and hypothetical summary of implementation used in my capstone project for my B.S. in Data Analytics at Western Governors University.

## Project Summary

The overall goal of this project was to provide alerts for possible internet outages by analyzing content from customers posted on Twitter.  The result was a visual dashboard showing counts of observed word groupings related to possible outage types.  This dashboard was incorporated into the ISPs existing outage reporting system, and was used to trigger email alerts to relevant work groups to aid in diagnosing and resolving a common cause issue.  

Directed Tweets were already being responded to by technical support representatives, and this same set of Tweets was chosen to analyze.  Python’s TwitterScraper package was used to extract and filter Tweet content.  The remains were exported to comma separated files (CSVs).  Spurious content such as stop words, articles, and names were filtered out.  Different forms of the same words were also grouped together in a process known as lemmatization.  For example, “talk”, “talking”, and “talked” are different verb forms of the same word.  Once the content of Tweets was cleaned, counts of the most frequently occurring words and 2-3 word phrases were extracted.  The sets of most frequent words and phrases were then organized into dataframes using Python’s Pandas data analytics package. 

From here word groupings were identified using a form of machine learning called natural language processing (NLP).  Lists of words that may describe internet outages  and those that don’t were identified and used to test for similarity to form the word groupings from NLP.  These lists were agreed upon during a brainstorming session involving all team members.   The Word2Vec NLP model was used to obtain the groupings, and cosine similarity scores were computed to measure relatedness of groupings to the list of outage related terms.  A t-SNE plot to visually represent the degrees of relatedness of words in Tweets to outage descriptors was made.  Each word appears as a point on a 2-dimensional plane, color coded according to grouping.  The larger the point, the stronger the word is related to an outage.  

The ISPs existing outage reporting system was used as well.  Outage categories, durations, and locations were extracted.  The ISP uses an Oracle database to store the information from this system so SQL was used to make these queries.  TwitterScraper was used again on each word grouping for the times that each outage actually occurred.  If any frequency of word groupings coincided with the duration of an outage it was noted along with the number of words and phrases in each group.  Since TwitterScraper does not provide exact locations, only cities that users identified were used to compare with real outage locations.  

For some groupings it was clear that a higher spike in word groupings were related to outages, for others statistical T-tests could be performed.  These T-tests allowed for rejection of a null hypothesis that the mean word frequencies for coincidence is less than or equal to the mean for non-coincidence.  For coinciding groups the mean frequency minus standard deviations were computed to use as a threshold indicator of possible outage.  This threshold was divided by the time of outage duration to obtain a scalable metric.

Now that word groupings and thresholds of occurrences were identified, their figures were summarized graphically in Tableau.  Each outage related word grouping was color coded and the counts for outage durations were displayed in histogram form.  This visual dashboard was then added to the existing outage reporting system, and functionality to adjust time intervals was added.  

Twitter’s API provides geolocations, but can only be used to extract data going back one month.  The same process of comparing with real outages carried out above was performed for the previous month.  Deviations in corresponding word group frequencies were found to be negligible during that interval, so the metrics found from the longer duration analysis were kept. 

Scripts to trigger the code to extract and analyze Tweets using the API were made.  These scripts were set to run every hour.  The output of word group counts were then updated to the dashboard with each iteration.  The threshold of frequencies was indicated as a solid vertical line in each histogram column to visualize if/when the counts indicate a possible outage.   If the height of a bin exceeded that threshold an alert was triggered.  Scripts to monitor that were made, which then triggered sending emails to relevant work groups.  The relevant work groups and content of emails were determined during an early brainstorming Scrum meeting.  

A representative from each work group was chosen to respond to follow up to confirm that alerts and emails were received during the last day of work.  Representatives from each organization that uses the dashboard were also consulted to make sure they were able to access it.  After these meetings it was determined that further training on the use of these new features was needed.  

# Code Overview

Python 3.7 using Jupyter notebook was used for ETL and NLP.  The code is included in InternetOutagesNLP.ipynb  The following specific modules used include:

- twitterscraper  - used for extracting Tweets
- pandas, numpy, matplotlib  - data anlytics packages
- sklearn, seaborn  - machine learning, and statistics
- nltk, word2vec, CountVectorizer - NLP packages

1.  Tweets were extracted and saved to csvs from a list of southwestern cities including: Houston, San Antonio, Austin, Dallas, Fort Worth, Oklahoma City, Tulsa, Santa Fe, and Aluquerque.  
2.  The csvs were read into data frames with text, timestamp and location fields.
3.  The text_clean() function used a regex to filter only words, and a custom list of stop words was used for further filtering.
4.  The cleaned data frame tweets_df was then updated.
5.  CountVecotrizer was instantiated and summaries of top 10 occuring words and phrases were found.
6.  The nltk.corpus stopwords package and further use of regex's were applied to tweets_df, before the remains were composed into the final_tweet_list list to be tokenized.
7.  The Word2Vec model was instantiated as tweets2vec and trained on final_tweet_list.
8.  A t-SNE model from sklearn was applied to the tweets2vec model.
9.  



