import nltk
from nltk.sentiment.vader import SentimentIntensityAnalyzer
import praw

reddit = praw.Reddit(client_id='Wq-mTiXtcyxM5A',
                     client_secret='UrNUwHqKRS9OR_SUWUzOjK9ZU34',
                     user_agent='kvnrd'
                     )


nltk.download('vader_lexicon')
sid = SentimentIntensityAnalyzer()


positiveList = []
negativeList = []
neutralList = []


def get_text_negative_proba(text):
   return sid.polarity_scores(text)['neg']


def get_text_neutral_proba(text):
   return sid.polarity_scores(text)['neu']


def get_text_positive_proba(text):
   return sid.polarity_scores(text)['pos']


def get_submission_comments(url):
    submission = reddit.submission(url=url)
    submission.comments.replace_more()

    return submission.comments


def display_list(list):
    for i in range(len(list)):
        print (list[i])
    print("\n")


def get_oldest_comment(comments):
    for comment in reddit.subreddit(comments).stream.comments():
        print(comment)



def traverse_comments(comments):
    for i in range (len(comments)):
        neg = get_text_negative_proba(comments[i].body)
        pos = get_text_positive_proba(comments[i].body)
        neu = get_text_neutral_proba(comments[i].body)

        if neg > max(pos,neu):
            negativeList.append(comments[i].body)
        if pos > max(neg,neu):
            positiveList.append(comments[i].body)
        if neu > max(pos, neg):
            neutralList.append(comments[i].body)

        try:
            traverse_comments(comments[i].replies) #recursion part
        except:
            print('No replies')



def main():
    comments = get_submission_comments('https://www.reddit.com/r/learnprogramming/comments/5w50g5/eli5_what_is_recursion/')
    #print(comments[0].body)
    #print(comments[0].replies[0].body)
    #print(comments[0].replies[0].replies[0].body)

    traverse_comments(comments)

    neg = get_text_negative_proba(comments[0].replies[0].body)

    print(neg)

    #get_oldest_comment(comments)

main()
print('Positive Comments:')
print(*positiveList, sep="\n")

print("----------------------------------------------------------- \n")

print('Negative Comments:')
print(*negativeList, sep="\n")

print("----------------------------------------------------------- \n")

print("Neutral Comments:")
print(*neutralList, sep="\n")

print("----------------------------------------------------------- \n")


