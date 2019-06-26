


The official documentation for wget at https://www.gnu.org/software/wget/manual/wget.html


### Q&A
- Question:
I'm trying to crawl a website which needs to be logged in with wget but it stops everytime it finds a logout url (https://example.com/logout/). I've tried excluding the directories but without success.

- Answer:

I've tried with -R/--reject,--no-clobber , -X but that didn't work, and later solved it in this way:`--reject-regex logout`. 
```
wget --reject-regex logout   https://xxxx.cn
```
