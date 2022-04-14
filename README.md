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

