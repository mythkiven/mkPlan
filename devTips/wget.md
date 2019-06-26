


The official documentation for wget at https://www.gnu.org/software/wget/manual/wget.html


### Q&A
- Question:
I'm trying to crawl a website which needs to be logged in with wget but it stops everytime it finds a logout url (https://example.com/logout/). I've tried excluding the directories but without success.

- Answer:

I've tried with -R ,--no-clobber , -X but that didn't work, and later solved it in this way:`--reject-regex logout`. This is my final code:

```
wget  --mirror   --convert-links=on    --span-hosts   --domains=xxxx.cn  --adjust-extension   --page-requisites  --html-extension=on   --no-if-modified-since   --output-file log     --wait=0.01 --execute robots=off   --user-agent="Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.3) Gecko/2008092416 Firefox/3.0.3"    --keep-session-cookies    --header  "Connection: keep-alive "  --load-cookies _cookies.txt  --reject-regex logout   https://xxxx.cn
```
