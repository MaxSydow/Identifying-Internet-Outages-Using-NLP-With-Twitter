# Identifying-Internet-Outages-Using-NLP-With-Twitter

# About

This is a summary of my capstone project for my Bachelors in Data Analytics at Western Governors University (2020).  The business need related purpose of it was to use natural language processing to allow an internet service provider to take a more pro-active approach in outage detection.   


##### Table of Contents  
## Project Summary
[#Project Summary](#project-summary)

## Code Overview 
<a name="Code Overview"/>

## Project Methodology 
[#Project Methodology](#project-methodology)

## Vizualization Examples 
<a name="Vizualization Examples"/>



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

Python 3.7 was used in a Jupyter notebook for ETL and NLP.  The code is included in InternetOutagesNLP.ipynb  The following specific modules used include:

- twitterscraper  - used for extracting Tweets
- pandas, numpy, matplotlib  - data anlytics packages
- sklearn, seaborn  - machine learning, and statistics
- nltk, word2vec, CountVectorizer - NLP packages

#### The following outlines what the code does
Functions, packages, and ML models are indicated in **bold**.

Data frames and lists are indicated using _italics_.

1.  Tweets were extracted and saved to csvs from a list of southwestern cities including: Houston, San Antonio, Austin, Dallas, Fort Worth, Oklahoma City, Tulsa, Santa Fe, and Aluquerque.  
2.  The csvs were read into data frames with text, timestamp and location fields.
3.  The **text_clean()** function used a regex to filter only words, and a custom list of stop words was used for further filtering.
4.  The cleaned data frame _tweets_df_ was then updated.
5.  **CountVecotrizer** was instantiated and summaries of top 10 occuring words and phrases were found.
6.  The **nltk.corpus** stopwords package and further use of regex's were applied to _tweets_df_, before the remains were composed into the _final_tweet_list_ list to be tokenized.
7.  The **Word2Vec** model was instantiated as **tweets2vec** and trained on _final_tweet_list_.
8.  A **t-SNE** model from sklearn was applied to the **tweets2vec** model.
9.  The **vectorize_corpus()** function was used to create a corpus of key words related to outages.
10. Lists of words related to outages, and opposites were initialized: _internet_out_ and _not_out_.
11. Cosine similarity scores were computed using the **cos_sim()** function to quantify similarity of words from _final_tweet_list_ to _internet_out_ and _not_out_ lists.
12.  A t-SNE plot was created to show strength of relatedness according to size of circles in 2D plot, with blue relating to occurance of outage, and grey corresponding to not being related to outage.


# Project Methodology

The Scrum project implementation methodology was used to carry this project out.  Scrum breaks a project down into sprints, which involve team members working in a focused manner on a specific objective.  Scrum meetings are scheduled to communicate progress on achieving benchmarks.  Little scope creep or deviation is allowed, which keeps teams focused on their goals defined by the sprints.  The path from extracting Tweets to presenting a functional dashboard can be broken down into manageable tasks.  


A team of analysts, developers, a data scientist, a product owner, and Scrum Master were assembled.  The product owner and Scrum Master are roles specifically designated to manage a Scrum project.  The product owner served as liaison between the project group and stakeholders.  Stakeholders include the relevant workgroups identified above, as well as some senior management.  This role is also responsible for scheduling and managing any backlog of objectives that may not have met the planned timeline.  The Scrum Master serves as a team leader in the project.  They work closely with team members to keep the project on schedule and communicate any needs of the team with the product owner.  The scrum master also conducts sprint meetings and documents progress in achieving objectives.  


The analysts were heavily involved with the earlier objectives of the project including.  These objectives include:
1.	Scraping data from Tweets by username going back 5 yrs.
2.	Cleaning data – remove stop words, etc.
3.	Preparing data frames and conduct NLP
4.	Extracting data from existing outage reporting system going back 5 yrs.

The data scientist was then responsible for performing the machine learning aspects.  This included:
1.	Performing correlation study between frequently appearing words and actual outage occurrence
2.	Grouping high frequency words into training and testing feature sets
3.	Coding and running Word2Vec algorithm

Developers played a crucial role in the latter portions of the project including:
1.	Managing locations of scripts and csv’s, and creating batch files for them
2.	Scheduling execution of scripts, and email alerts
Analysts were also responsible for creating the Tableau dashboard, while developers linked it to the existing outage reporting site.


# Vizualization Examples

### t-SNE Plot

![t-SNE](https://user-images.githubusercontent.com/56166497/80894694-e3a42f00-8ca2-11ea-8f8d-86b02fdd740b.png)


### Example bar chart (made with Tableau) showing key word counts related to outages pertaining to network congestion.

![tweet_count](https://user-images.githubusercontent.com/56166497/80894744-907eac00-8ca3-11ea-87c1-fc09ca2c34aa.png)


