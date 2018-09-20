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
newList = []
totalList = []
url = 'https://www.reddit.com/r/learnprogramming/comments/5w50g5/eli5_what_is_recursion/'


def get_text_negative_proba(text):
   return sid.polarity_scores(text)['neg']


def get_text_neutral_proba(text):
   return sid.polarity_scores(text)['neu']


def get_text_positive_proba(text):
   return sid.polarity_scores(text)['pos']


def get_submission_comments(url):
    submission = reddit.submission(url=url)
    submission.comments.replace_more(limit=0)

    return submission.comments

def get_submission_comments_sort(url):
    submission = reddit.submission(url=url)
    submission.comment_sort = 'old' #to sort the list from oldest to new
    submission.comments.replace_more(limit=0)

    return submission.comments




def traverse_comments(comments):
    for i in range (len(comments)):
        neg = get_text_negative_proba(comments[i].body)
        pos = get_text_positive_proba(comments[i].body)
        neu = get_text_neutral_proba(comments[i].body)

        #checking to see which prob value is larger in order to append to the correct list
        if neg > max(pos,neu):
            negativeList.append(comments[i].body) #adding to the appropriate list
        if pos > max(neg,neu):
            positiveList.append(comments[i].body)
        if neu > max(pos, neg):
            neutralList.append(comments[i].body)

        try:
            traverse_comments(comments[i].replies) #recursion part
        except:
            print('No replies')

def oldest_positive_comments(comments):
    try:
        return comments.pop()
    except:
        print("List is empty")

def oldest_negative_comments(comments):
    try:
        return comments.pop()
    except:
        print("List is empty")

def oldest_comments(comments):

    try:
        return comments.pop()
    except:
        print("List is empty")


def print_mainComments(comments):

    from praw.models import MoreComments
    for top_level_comment in comments:
        if isinstance(top_level_comment, MoreComments):
            continue
        print(top_level_comment.body)


def to_oldest_sort():
    comments = get_submission_comments_sort(url)

    return comments


def main():
    comments = get_submission_comments(url)

    print("===============================================")
    # print_mainComments(comments)

    comments_sorted = to_oldest_sort()


    totalList.sort(reverse=True)

    print('OLDEST COMMENT EXTRA CREDIT \n=========================================== \n', oldest_comments(totalList))

    traverse_comments(comments_sorted)




main()
print("\n")

print('POSITIVE COMMENTS:')
print("==============================================================")
print(*positiveList, sep="\n")

print("\n")

print('NEGATIVE COMMENTS:')
print("==============================================================")
print(*negativeList, sep="\n")

print("\n")

print("NEUTRAL COMMENTS:")
print("==============================================================")
print(*neutralList, sep="\n")

print("\n")

positiveList.sort(reverse=True)
negativeList.sort(reverse=True)


print('OLDEST POSITIVE COMMENTS EXTRA CREDIT: \n=========================================== \n', oldest_positive_comments(positiveList))

print("\n")

print('OLDEST NEGATIVE COMMENT EXTRA CREDIT: \n=========================================== \n', oldest_negative_comments(negativeList))


