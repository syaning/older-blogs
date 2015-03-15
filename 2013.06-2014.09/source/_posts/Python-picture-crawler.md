title: Python抓取图片
date: 2014-04-30 15:28:56
categories: Python
tags:
---
抓的是豆瓣妹子的图片。
```python
import threading
import urllib.request
import re, os
 
class dbmeiziCrawler(threading.Thread):
    def __init__(self, name, startpage, endpage, imgdir):
        threading.Thread.__init__(self, name=name)
        self.startpage = startpage
        self.endpage = endpage
        self.imgdir = imgdir
     
    def run(self):
        for page in range(self.startpage, self.endpage + 1):
            pageurl = 'http://dbmeizi.com/?p=' + str(page)
            pagehtml = urllib.request.urlopen(pageurl).read().decode('utf-8')
            reg = 'http://pic.dbmeizi.com/pics/\d+/p\d+\.jpg'
            imgurllist = re.findall(reg, pagehtml)
         
            for imgurl in imgurllist:
                imgname = imgurl[imgurl.rfind('/') + 1:]
                urllib.request.urlretrieve(imgurl, self.imgdir + imgname)
                print(self.name + '\t' + imgurl)
 
if __name__ == '__main__':
    maxpage = 300
    pages_each_thread = 20
    tasknums = maxpage // pages_each_thread + (0 if maxpage % pages_each_thread == 0 else 1)
     
    imgdir = os.getcwd() + '/dbmeizi/'
    if not os.path.exists(imgdir):
        os.mkdir(imgdir)
 
    for i in range(1, tasknums + 1):
        dbmeiziCrawler('task' + str(i), (i - 1) * pages_each_thread, i * pages_each_thread - 1, imgdir).start()
```