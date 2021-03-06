
### 4.argparse模块解析
[简书](https://www.jianshu.com/p/fef2d215b91d)
[知乎](https://zhuanlan.zhihu.com/p/34395749)
>cat `argv_argparse.py`
```
import argparse

parser = argparse.ArgumentParser(description = 'Personal Information ',epilog = 'Information end ')
    #必写属性,第一位
parser.add_argument("name",         type = str,                  help = "Your name")
    #必写属性,第二位
parser.add_argument("birth",        type = str,                  help = "birthday")
    #可选属性,默认为None
parser.add_argument("-r",'--race',  type = str, dest = "race",   help = u"ming zu")
    #可选属性,默认为0,范围必须在0~10
parser.add_argument("-a", "--age",  type = int, dest = "age",    help = "Your age",         default = 0,      choices=range(10))
    #可选属性,默认为male
parser.add_argument('-s',"--sex",   type = str, dest = "sex",    help = 'Your sex',         default = 'male', choices=['male', 'female'])
    
	#添加互斥参数
group = parser.add_mutually_exclusive_group()
group.add_argument("-y","--very_handsome" ,help="handsome yes",action="store_true")
group.add_argument("-n","--common" ,help="handsome no",action="store_true")
	#可选属性,默认为None,-p后可接多个参数
parser.add_argument("-p","--parent",type = str, dest = 'parent', help = "Your parent",      default = "None", nargs = '*')
    #可选属性,默认为None,-o后可接多个参数
parser.add_argument("-o","--other", type = str, dest = 'other',  help = "other Information",required = False,nargs = '*')
args = parser.parse_args()

if args.very_handsome:
	print("yes,very_handsome!\n")
if args.common:
	print("common peopel")

print ("argparse.parser=",args,type(args))
print ('name = %s'%args.name)
print("\n")
d = args.__dict__
for key,value in d.items():
    print ('%s = %s'%(key,value))



```

>运行 $ python `argv_argparse.py` xiaoming 1991.11.11 -p xiaohong xiaohei -a 9 -r han -s female -o 1 2 3 4 5 6 -y
```
yes,very_handsome!

argparse.parser= Namespace(age=9, birth='1991.11.11', common=False, name='xiaoming', other=['1', '2', '3', '4', '5', '6'], parent=['xiaohong', 'xiaohei'], race='han', sex='female', very_handsome=True) <class 'argparse.Namespace'>
name = xiaoming


name = xiaoming
birth = 1991.11.11
race = han
age = 9
sex = female
very_handsome = True
common = False
parent = ['xiaohong', 'xiaohei']
other = ['1', '2', '3', '4', '5', '6']
```







## 3.plotnine 模块画图及其图片保存及其打开
[plotnine](https://plotnine.readthedocs.io/en/stable/generated/plotnine.geoms.geom_tile.html#periodic-table-of-elements)
https://blog.csdn.net/kmd8d5r/article/details/79987785
>ggplot2 

```
from plotnine import *
import plotnine
from PIL import Image
gsea.enrichment_table
df = gsea.enrichment_table.sort_values(by="pvalue", ascending=True)
df = df.iloc[:10, :]
df
#保存
p=ggplot(df,aes(x="pvalue",y="factor(gene_num)"))+geom_point()+theme(figure_size=[4,5])
df.sort_values(by="gene_num", ascending=True)["HPO_term_name"]
p.save("test.png")
#打开
Image.open("test.png")

```
```
ggplot(df,aes(x="pvalue",y="factor(gene_num)"))+geom_point()+theme(figure_size=(6,4),plot_background=element_rect(fill='white'),axis_text_y=element_text(margin={'r': 5}, color='blue', size=9))+theme_bw()+scale_x_continuous(breaks=[0.001,0.002],labels=["1e^-3","2e^-3"])+scale_y_discrete(labels=df.sort_values(by="gene_num", ascending=False)["HPO_term_name"].values)

```







## 2.log模块
```
import sys
import logging

LOGGING_FMT = "%(name)-20s %(levelname)-7s @ %(asctime)s: %(message)s"
LOGGING_DATE_FMT = "%m/%d/%y %H:%M:%S"

def get_logger(name, level=None):
    log = logging.getLogger(name)
    handler = logging.StreamHandler(sys.stderr)
    handler.setFormatter(logging.Formatter(fmt=LOGGING_FMT, datefmt=LOGGING_DATE_FMT))
    log.addHandler(handler)
    if level:
        log.setLevel(level)
    else:
        log.setLevel(logging.DEBUG)
    return log
log=get_logger("yes","INFO")
log.warning("hello")
```



## 1. 下载及其展示图片  
参考[HPO project](https://github.com/Nanguage/BioTMCourse/blob/master/HPO%20enrich/hpoea/utils/download.py)

```
url = "http://wx4.sinaimg.cn/large/d030806aly1fq1vn8j0ajj21ho28bduy.jpg"

rsp = requests.get(url, stream=True)
with open("1.jpg","wb") as f:
    for i in rsp.iter_content(chunk_size=1024):  # 边下载边存硬盘, chunk_size 可以自由调整为可以更好地适合您的用例的数字
        f.write(i)
from PIL import Image
img=Image.open("1.jpg")
img.show()

from IPython.display import Image
Image(url= "1.jpg")

```


## 1. 下载文件
```
import logging
import requests
import os
from tqdm import tqdm


DATA_DIR = os.path.join(os.path.expanduser("~"))
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)



def download_file(url, file_name,file_dir):
    """Download file and save it."""
    logger.info("Download file from: {}".format(url))
    response = requests.get(url, stream=True)
    path=os.path.join(file_dir,file_name)
    with open(path, "wb") as handle:
        for data in tqdm(response.iter_content()):
            handle.write(data)
    logger.info("Data download done, file saved to: {}".format(path))
    return path


##
DATA_DIR = os.path.join(os.path.expanduser("~"),r"Desktop\152\HPO")
print(DATA_DIR)
# such as HPO example
HPO_OBO_URL = "https://raw.githubusercontent.com/obophenotype/human-phenotype-ontology/master/hp.obo"
download_file(HPO_OBO_URL,"hpo.txt",DATA_DIR)
```



