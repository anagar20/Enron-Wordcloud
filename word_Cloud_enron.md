

```python
# Download the enron dataset May 7 2015 version at http://www.cs.cmu.edu/~./enron/

docs = []
from os import listdir, chdir
import re
import numpy as np
import string
import requests
import re
from string import punctuation
from nltk.corpus import stopwords
import pandas as pd
from os import path
from PIL import Image
from wordcloud import WordCloud, STOPWORDS
import matplotlib.pyplot as plt

email_pat = re.compile(".+@.+")
to_pat = re.compile("To:.+\n")
cc_pat = re.compile("cc:.+\n")
subject_pat = re.compile("Subject:.+\n")
from_pat = re.compile("From:.+\n")
sent_pat = re.compile("Sent:.+\n")
received_pat = re.compile("Received:.+\n")
ctype_pat = re.compile("Content-Type:.+\n")
reply_pat = re.compile("Reply- Organization:.+\n")
date_pat = re.compile("Date:.+\n")
xmail_pat = re.compile("X-Mailer:.+\n")
mimver_pat = re.compile("MIME-Version:.+\n")
contentinfo_pat = re.compile("----------------------------------------.+----------------------------------------")
forwardedby_pat = re.compile("----------------------.+----------------------")
caution_pat = re.compile('''\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*.+\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*''')
privacy_pat = re.compile(" _______________________________________________________________.+ _______________________________________________________________")
```


```python
chdir("/.../Downloads/maildir")
names = [d for d in listdir(".") if "." not in d]
names[1:12]
```




    ['arnold-j',
     'arora-h',
     'badeer-r',
     'bailey-s',
     'bass-e',
     'baughman-d',
     'beck-s',
     'benson-r',
     'blair-l',
     'brawner-s',
     'buy-r']




```python
for name in names:
    chdir("/.../Downloads/maildir/%s" % name)
    subfolders = listdir('.')
    sent_dirs = [n for n, sf in enumerate(subfolders) if "sent" in sf]
    sent_dirs_words = [subfolders[i] for i in sent_dirs]
    for d in sent_dirs_words:
        chdir('/.../Downloads/maildir/%s/%s' % (name,d))
        file_list = listdir('.')
        docs.append([" ".join(open(f, 'r').readlines()) for f in file_list if "." in f])
        
```


```python
docs_final = []
for subfolder in docs:
    for email in subfolder:
        if ".nsf" in email:
            etype = ".nsf"
        elif ".pst" in email:
            etype = ".pst"
        email_new = email[email.find(etype)+4:]
        email_new = to_pat.sub('', email_new)
        email_new = cc_pat.sub('', email_new)
        email_new = subject_pat.sub('', email_new)
        email_new = from_pat.sub('', email_new)
        email_new = sent_pat.sub('', email_new)
        email_new = email_pat.sub('', email_new)
        if "-----Original Message-----" in email_new:
            email_new = email_new.replace("-----Original Message-----","")
        email_new = ctype_pat.sub('', email_new)
        email_new = reply_pat.sub('', email_new)
        email_new = date_pat.sub('', email_new)
        email_new = xmail_pat.sub('', email_new)
        email_new = mimver_pat.sub('', email_new)
        email_new = contentinfo_pat.sub('', email_new)
        email_new = forwardedby_pat.sub('', email_new)
        email_new = caution_pat.sub('', email_new)
        email_new = privacy_pat.sub('', email_new)
        docs_final.append(email_new) 
```


```python
type(docs_final)
```




    list




```python
len(docs_final)
```




    126059




```python
data = " ".join(docs_final)
```


```python
pattern = re.compile(r'[^\w\s]', re.U)
```


```python
data = str(re.sub(r'_', '', re.sub(pattern, '', data)))
```


```python
replace_punctuation = string.maketrans(string.punctuation, ' '*len(string.punctuation))
s = data.translate(replace_punctuation)   
```


```python
s = ''.join([i for i in s if not i.isdigit()])
s = s.lower()
```


```python
def strip_punctuation(s):
    s=''.join(_ for _ in s if _ not in punctuation)
    return s
```


```python
s = strip_punctuation(s)
```


```python
#word cloud 1
wordcloud = WordCloud().generate(s)

import matplotlib.pyplot as plt
plt.imshow(wordcloud)
plt.axis("off")

#word cloud 2
wordcloud = WordCloud(max_font_size=40).generate(s)
plt.figure()
plt.imshow(wordcloud)
plt.axis("off")
plt.show()

```


```python
#download the image from http://www.stencilry.org/stencils/movies/alice%20in%20wonderland/255fk.jpg, rename it as img1
#and store it in 
#the data folder. In my case it is stored in /maildir/data/img1.jpg

#word cloud 3
d = path.dirname("/Users/abhi/Downloads/maildir/data/")
alice_mask = np.array(Image.open(path.join(d, "img1.jpg")))
stopwords = set(STOPWORDS)
wc = WordCloud(background_color="white", mask=alice_mask,
               stopwords=stopwords)
wc.generate(s)
wc.to_file(path.join(d, "alice.png"))

plt.imshow(wc)
plt.axis("off")
plt.figure()
plt.imshow(alice_mask, cmap=plt.cm.gray)
plt.axis("off")
plt.show()
```


```python

```
