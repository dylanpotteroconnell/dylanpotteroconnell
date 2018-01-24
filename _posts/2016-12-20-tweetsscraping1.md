---
layout: post
title: Scraping all of a User's Tweets
---

Traceback (most recent call last):
  File "compileTweets.py", line 32, in <module>
    thedatawriter.writerow(tweet.text)
UnicodeEncodeError: 'ascii' codec can't encode character u'\u2019' in position 0: ordinal not in range(128)


The next issue was that each letter was being put in as a separate entry, as so

A,s, ,t,o, ,t,h,e, ,U,.,N,.,",", ,t,h,i,n,g,s, ,w,i,l,l, ,b,e, ,d,i,f,f,e,r,e,n,t, ,a,f,t,e,r, ,J,a,n,., ,2,0,t,h,.



['AsDict', 'AsJsonString', 'NewFromJsonDict', '__class__', '__delattr__', '__dict__', '__doc__', '__eq__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_json', 'contributors', 'coordinates', 'created_at', 'created_at_in_seconds', 'current_user_retweet', 'favorite_count', 'favorited', 'full_text', 'geo', 'hashtags', 'id', 'id_str', 'in_reply_to_screen_name', 'in_reply_to_status_id', 'in_reply_to_user_id', 'lang', 'location', 'media', 'param_defaults', 'place', 'possibly_sensitive', 'retweet_count', 'retweeted', 'retweeted_status', 'scopes', 'source', 'text', 'truncated', 'tweet_mode', 'urls', 'user', 'user_mentions', 'withheld_copyright', 'withheld_in_countries', 'withheld_scope']
