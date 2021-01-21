#Coleta de tweets pelo pacote GOT

import GetOldTweets3 as got
import codecs

caminho_saida = r"C:\Users\milen\Desktop\2015m1.csv"
limite_tweets = 300000

# %% Get tweets by query search:

tweetCriteria = got.manager.TweetCriteria().setQuerySearch('petrobras lang:pt -filter:retweets -filter:replies') \
    .setSince("2015-05-01") \
    .setUntil("2015-06-03") \
    .setEmoji("unicode")

tweets = got.manager.TweetManager.getTweets(tweetCriteria)
if tweets:
    tweet = tweets[min([len(tweets), 10]) - 1]
    print(tweet.text)

    # %% salvar arquivo

    with codecs.open(caminho_saida, "w+", "utf-8-sig") as outputFile:
        outputFile.write('username;date;retweets;favorites;text;geo;mentions;hashtags;id;permalink')

        for t in tweets:
            outputFile.write(('\n%s;%s;%d;%d;"%s";%s;%s;%s;"%s";%s' % (
            t.username, t.date.strftime("%Y-%m-%d %H:%M"), t.retweets, t.favorites, t.text, t.geo, t.mentions,
            t.hashtags, t.id, t.permalink)))
            outputFile.flush()

        print('%d more saved on file...\n' % len(tweets))
