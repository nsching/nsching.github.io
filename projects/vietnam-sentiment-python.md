---
layout: project
type: project
image: img/code.png
title: "r/Vietnam Sentiment Analysis in Python"
date: July 2024
published: false
labels:
  - Sentiment Analysis
  - Python
  - Code
summary: "My code written in Python"
---

```python
import praw
import pandas as pd
import re
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer
import matplotlib.pyplot as plt
from langdetect import detect, LangDetectException
from textblob import TextBlob
from textblob.exceptions import NotTranslated

# Download necessary NLTK resources
import nltk
nltk.download('stopwords')
nltk.download('wordnet')

# Initialize PRAW
reddit = praw.Reddit(
    client_id="Ci2PFvmlrfRvEHMo3_idyQ",
    client_secret="DmIml05C3tyQc3NOmhiS1AstApesdQ",
    user_agent="Comment Extraction (by u/Positive_Lie3685)",
)

# Initialize lemmatizer
lemmatizer = WordNetLemmatizer()

# Define keywords for sentiment analysis
usa_keywords = ['usa', 'america', 'americas', 'mỹ', 'hoa kỳ', 'united states']
china_keywords = ['china', 'chinese', 'trung quốc', 'tàu']

# Function to clean text
def clean_text(text):
    # Remove URLs, special characters, and numbers
    text = re.sub(r'https?://\S+|www\.\S+', '', text)  # Remove URLs
    text = re.sub(r'[^a-zA-Z\s]', '', text, re.I | re.A)  # Remove non-letters
    text = text.lower()  # Lowercase text
    # Tokenization and removing stopwords
    tokens = text.split()
    tokens = [lemmatizer.lemmatize(word) for word in tokens if word not in stopwords.words('english')]
    return ' '.join(tokens)

# Function to analyze sentiment
def analyze_sentiment(text):
    if not text.strip():  # Check if text is empty or contains only whitespace
        return 0  # Neutral sentiment for empty text

    try:
        # Detect the language of the text
        language = detect(text)
        
        # Use English sentiment analysis
        if language == 'en':
            blob = TextBlob(text)
            return blob.sentiment.polarity
        
        # Placeholder for Vietnamese sentiment analysis
        # You would replace this with actual Vietnamese sentiment analysis if available
        return 0  # Neutral sentiment if no analysis is available
        
    except (LangDetectException, NotTranslated) as e:
        print(f"Error analyzing sentiment: {e}")
        return 0  # Neutral sentiment if an error occurs

# Function to check if text contains any of the keywords
def contains_keyword(text, keywords):
    text = text.lower()  # Convert text to lowercase for matching
    return any(keyword in text for keyword in keywords)

# Creating subreddit instance
subreddit_name = "VietNam"
subreddit = reddit.subreddit(subreddit_name)

# Initialize lists to store post data, comments, comment upvotes, and sentiment scores
titles = []
scores = []
ids = []
all_comments = []
comment_upvotes = []
comment_sentiments = []

# Fetch posts from the subreddit
for submission in subreddit.new(limit=500):
    titles.append(submission.title)
    scores.append(submission.score)
    ids.append(submission.id)
    
    # Fetch comments from the current post
    submission.comments.replace_more(limit=None)  # This line ensures you get all comments by removing the "MoreComments" instances
    comments = []
    upvotes = []
    sentiments = []
    for comment in submission.comments.list():
        cleaned_comment = clean_text(comment.body)
        comments.append(cleaned_comment)
        upvotes.append(comment.score)  # Track upvotes for each comment
        sentiments.append(analyze_sentiment(cleaned_comment))  # Analyze sentiment
    all_comments.append(comments)
    comment_upvotes.append(upvotes)
    comment_sentiments.append(sentiments)

# Create DataFrame
df = pd.DataFrame({
    'Title': titles,
    'Id': ids,
    'Upvotes': scores,
    'Comments': all_comments,
    'Comment_Upvotes': comment_upvotes,
    'Comment_Sentiments': comment_sentiments
})

# Save DataFrame to CSV
df.to_csv('reddit_vietnam_sentiment_data.csv', index=False)

# Perform sentiment analysis for specific keywords
def calculate_average_sentiment(df, keywords):
    sentiments = []
    total_upvotes = 0
    weighted_sentiments = 0
    
    for comments, upvotes in zip(df['Comments'], df['Comment_Upvotes']):
        for comment, upvote in zip(comments, upvotes):
            if contains_keyword(comment, keywords):
                sentiment = analyze_sentiment(comment)
                sentiments.append(sentiment * upvote)  # Multiply sentiment by upvotes for importance
                weighted_sentiments += sentiment * upvote
                total_upvotes += upvote
    
    if total_upvotes > 0:
        weighted_average_sentiment = weighted_sentiments / total_upvotes
        return weighted_average_sentiment
    return 0

# Calculate average sentiment for USA and China
usa_average_sentiment = calculate_average_sentiment(df, usa_keywords)
china_average_sentiment = calculate_average_sentiment(df, china_keywords)

print(f"Average Sentiment for USA: {usa_average_sentiment}")
print(f"Average Sentiment for China: {china_average_sentiment}")

import matplotlib.pyplot as plt
import numpy as np

# Data
usa_average_sentiment = 0.1189396737919763  # Example value, replace with actual data
china_average_sentiment = 0.04110618062366072  # Example value, replace with actual data
categories = ['USA', 'China']
average_sentiments = [usa_average_sentiment, china_average_sentiment]

# Create figure and axis
fig, ax = plt.subplots(figsize=(10, 6))

# Bar plot
bars = ax.bar(categories, average_sentiments, color=['blue', 'red'])

# Set labels and title
ax.set_xlabel('Country', fontsize=12)
ax.set_ylabel('Average Sentiment', fontsize=12)
ax.set_title('Average Sentiment Analysis for USA and China in r/VietNam', fontsize=14, pad=20)

# Set y-axis limits and ticks
ax.set_ylim(-1, 1)
ax.set_yticks(np.arange(-1, 1.1, 0.5))

# Add gridlines
ax.grid(axis='y', linestyle='--', alpha=0.7)

# Add background shading for sentiment ranges
ax.axhspan(0.5, 1, facecolor='lightgreen', alpha=0.3)
ax.axhspan(0, 0.5, facecolor='palegreen', alpha=0.3)
ax.axhspan(-0.5, 0, facecolor='lightcoral', alpha=0.3)
ax.axhspan(-1, -0.5, facecolor='indianred', alpha=0.3)

# Add sentiment labels
ax.text(1.1, 0.75, '0.5 to 1: Strong positive sentiment', fontsize=10, verticalalignment='center', bbox=dict(facecolor='white', alpha=0.7))
ax.text(1.1, 0.25, '0 to 0.5: Mild positive sentiment', fontsize=10, verticalalignment='center', bbox=dict(facecolor='white', alpha=0.7))
ax.text(1.1, -0.25, '-0.5 to 0: Mild negative sentiment', fontsize=10, verticalalignment='center', bbox=dict(facecolor='white', alpha=0.7))
ax.text(1.1, -0.75, '-1 to -0.5: Strong negative sentiment', fontsize=10, verticalalignment='center', bbox=dict(facecolor='white', alpha=0.7))

# Annotate bars with values
for bar in bars:
    height = bar.get_height()
    ax.annotate(f'{height:.2f}', 
                xy=(bar.get_x() + bar.get_width() / 2, height),
                xytext=(0, 3),  # 3 points vertical offset
                textcoords="offset points",
                ha='center', va='bottom', fontsize=10)

# Show plot
plt.tight_layout()
plt.show()

# Calculate the number of comments for each post
df['Num_Comments'] = df['Comments'].apply(len)

# Calculate the total number of comments
total_comments = df['Num_Comments'].sum()

print(f"Total number of comments: {total_comments}")
