# Activision-Brand-Monitoring

# Background
## Business Understanding
Established in 1979, Activision Publishing, Inc. is one of the world’s largest gaming companies. Over its lifetime of over four decades, Activision has acquired numerous other gaming studios, including, Raven Software, Infinity Ward, and Blizzard Entertainment, and it has published some of the biggest hits in the gaming world, such as Call of Duty (CoD) and World of Warcraft (WoW). And it has recently been acquired by tech giant Microsoft for nearly $ 70 billion. 

Unfortunately, Activision has been going through quite a rough patch lately. Its iconic Call of Duty franchise has been highly criticized by creators and gamers alike for the past few years. Its most recent Call of Duty release, Call of Duty: Vanguard, is the lowest selling game of the franchise in the past 14 years. There have been countless complaints, pleadings, and requests from the fans and players, and many of them do not feel heard. There is a great amount of valuable information available in the form of user-generated content (UGC) waiting to be harnessed by Activision. 

## Business Objective
Therefore, the goal of our project is to develop a text mining pipeline that would extract insights from Twitter, create popular topics, present them clearly and concisely, and allow Activision to monitor customer sentiment. This pipeline can be executed on a daily, weekly, or monthly basis. Activision will be able to stay on top of what the customers think about its brand and its various games, actively make adjustments and changes to the games based on that information, and eventually, regain the confidence and support of its player base. 

## Project Summary

# Data Processing
## Data Collection
Due to the analytical approaches that we decided to adopt, there is a need for two different types of data cleaning. Since we are using BERT for classification and clustering, and BERT is an advanced Natural Language Processing tool, it does not require stopword removal and lemmatization, however, the other processes that we are using such as Latent Dirichlet Allocation, do require them. Therefore, we have developed steps for the text cleaning process.

* Step 1: General Tweet Cleaning

**Encoding Issue:** oftentimes when exporting tweets from Twitter API to csv files, there might be some sort of encoding errors that would result in strings containing strange characters. For example, “I have always continued to write âœðŸ¼ with a pencil”

**Remove links:** many tweets contain links to a photo or a video, and sometimes a website. These strings do not contribute to our analysis. So they should be removed from our data.

**@ and #:** Most tweets contain many @ and # which allowed the tweets to be identified in certain topics. However, when querying tweets on a certain topic, the @ and # will be very repetitive most of the time. In our case, there might be a lot of “@reMarkable”, or “#tablet”, and these words might skew out text analysis later, so we think that should be removed.

We wrote a function for cleaning these things. We used .encode() method to use ACSII encoding, removing the odd character. And then several regex expression to remove the links, @ and #. 

The python code looks like this: 
```ruby
import regex as re
def clean_tweets(sent):
    # put everythin in lower case
    sent = sent.lower()
    # encode to ascii unicode, it removes strange characters
    sent = sent.encode('ascii', 'ignore').decode() 
    # remove https
    sent = re.sub(r'https\S+', '', sent)    
    # remove http
    sent = re.sub(r'http\S+', '', sent)   
    # remove @
    sent = re.sub(r'@\S+', '', sent)     
    # remove #
    sent = re.sub(r'#\S+', '', sent) 
    sent = " ".join(sent.split())
    return sent
```
* Step 2: Stopword Removal and Lemmatization 

Stopwords are words that do not provide too much semantic meaning to the sentence. We imported stopwords from the NLTK library and wrote a function for the stopwords removal. After removing the stopwords, we wrote another function that would lemmatize each tweet. And the reason why there is a need to lemmatize the words is that the same word can be in different variations, and that could skew the text mining processes if they are not formatted to their root word form. 

```ruby
import nltk
from nltk.corpus import stopwords
nltk.download('stopwords')
stop_words = stopwords.words('english')

# function to remove stopwords
def remove_stopwords(tweets):
  all_tweet = tweets.split(' ')
  rem_text = " ".join([i for i in all_tweet if i not in stop_words])
  return rem_text
  
from nltk.tokenize import word_tokenize
from nltk.corpus import wordnet
from nltk.stem import WordNetLemmatizer
import nltk
nltk.download('punkt')
nltk.download('averaged_perceptron_tagger')
nltk.download('wordnet')
nltk.download('stopwords')

wl = WordNetLemmatizer()
 
# this is a helper function to map NTLK position tags
def get_wordnet_pos(tag):
    if tag.startswith('J'):
        return wordnet.ADJ
    elif tag.startswith('V'):
        return wordnet.VERB
    elif tag.startswith('N'):
        return wordnet.NOUN
    elif tag.startswith('R'):
        return wordnet.ADV
    else:
        return wordnet.NOUN

def lemmatizer(string):
    word_pos_tags = nltk.pos_tag(word_tokenize(string)) # Get position tags
    a=[wl.lemmatize(tag[0], get_wordnet_pos(tag[1])) for idx, tag in enumerate(word_pos_tags)] # Map the position tag and lemmatize the word/token
    return " ".join(a)
```

# Data Analysis
## K-Means Clustering & BERT Extractive Summarizer
K-Means Clustering is one of the simplest and most popular unsupervised machine learning algorithms. It finds the optimized k number of centroids of a given dataset. To find out how the tweets about Activision and Call of Duty are clustered, we decided to use the K-Means algorithm to achieve that and then use BERT Extractive Summarizer to find the most representative sentences from each cluster. And the following are the steps of this analysis:

* Step 1: BERT Embedding

> Before we can start the analysis, we need to first vectorize the sentences. There are many different ways to accomplish that, but in our case, we decided to go with BERT to transform the tweets into tensors. BERT has many different versions that are trained with corpus in different domains. However, in our analysis, we decided to use the generic BERT, which usually returns the best results. 

* Step 2: Find the Optimal K

> Although we are free to choose the value of K depending on how many clusters we want to see, there are mathematic methods that can determine the optimal number of clusters. There are numerous methods for this purpose, and we choose the elbow plot and dendrogram for our analysis. Sometimes the two visualizations return different optimal k, which requires more of a judgment call. Fortunately, both plots returned the same, k = 4, for the tweets we collected. 
