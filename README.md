# 51job词云分析
## 51job词云爬取步骤：首先将需要的数据爬取到各自的txt文件夹中，再用jieba和wordcloud进行词云分析
### 1. 导入程序所需要的库
        import requests
        import re
        import csv
        import threading
        import jieba
        import matplotlib.pyplot as plt
        from wordcloud import WordCloud
### 2. 使用多线程调用信息函数，使爬取速度更快
        def main():
          # 进行简单的多线程操作，进行6次多线程，爬取速度更快
          t1 = threading.Thread(target=BeiJing)
          t2 = threading.Thread(target=ShangHai)
          t3 = threading.Thread(target=GuangZhou)
          t4 = threading.Thread(target=ShenZhen)
          t5 = threading.Thread(target=WuHan)
          t6 = threading.Thread(target=NanJing)

          t1.start()
          t2.start()
          t3.start()
          t4.start()
          t5.start()
          t6.start()
        # 北京
        def BeiJing():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1,100):
                for type in types:
                    urls = ["https://search.51job.com/list/010000,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 北京职位
                    for url in urls:
                        position_url(url)

        # 上海
        def ShangHai():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1,100):
                for type in types:
                    urls = ["https://search.51job.com/list/020000,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 上海职位
                    for url in urls:
                        position_url(url)

        # 广州
        def GuangZhou():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1, 100):
                for type in types:
                    urls = ["https://search.51job.com/list/030200,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 广州职位
                    for url in urls:
                        position_url(url)

        # 深圳
        def ShenZhen():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1, 100):
                for type in types:
                    urls = ["https://search.51job.com/list/040000,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 深圳职位
                    for url in urls:
                        position_url(url)

        # 武汉
        def WuHan():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1, 100):
                for type in types:
                    urls = ["https://search.51job.com/list/180200,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 武汉职位
                    for url in urls:
                        position_url(url)

        # 南京
        def NanJing():
            types = ["Python", "JAVA", "golang", "C%252B%252B", "web", "前端", "node.js", "C%2523"]
            for i in range(1, 100):
                for type in types:
                    urls = ["https://search.51job.com/list/070200,000000,0000,00,9,99,%s,2,%s.html" % (type, i)]  # 南京职位
                    for url in urls:
                        position_url(url)
### 3. 使用正则爬取职位信息，包括职位城市，职位类型，公司，地址，薪资等信息
        def position_url(url):
          # 增加代理
          s = requests.session()
          s.proxies = {"http":"121.237.149.44:3000","http":"117.88.4.132:3000"}
          s.get(url)
          headers = {
              "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.25 Safari/537.36 Core/1.70.3754.400 QQBrowser/10.5.4020.400",
              "Connection": "close"
          }
          # get请求
          response = requests.get(url, headers=headers, timeout=5).content.decode("gbk")
          # 抓取所在城市
          positions_city = re.findall(r'<input autocomplete="off".*?value="(.*?)".*?>.*?', response, re.DOTALL)
          # 对城市名进行优化
          new_position_city = ''.join(positions_city)
          # 爬取职位类型
          position_type = re.findall(r'<p class="ipt">.*?<input.*?value="(.*?)".*?>.*?</p>', response, re.DOTALL)
          # 对职位类型进行优化
          new_position_type = ''.join(position_type)
          # 爬取职位名称
          positions_name = list(re.findall(r'<span>.*?<a target=".*?" title="(.*?)".*?>.*?</a></span>', response, re.DOTALL))
          # 爬取公司名称
          positions_company = list(re.findall(r'<span class="t2"><a target=".*?".*?>(.*?)</a></span>', response, re.DOTALL))
          # 爬取公司地址
          positions_location = list(re.findall(r'<div class="el">.*?<span class="t3">(.*?)</span>.*?</div>', response, re.DOTALL))
          # 爬取职位薪资
          positions_money = list(re.findall(r'<div class="el">.*?<span class="t4">(.*?)</span>.*?</div>', response, re.DOTALL))
          # 爬取职位信息链接
          positions_links = list(re.findall(r'<span>.*?<a target=".*?" title=".*?" href="(.*?)".*?>.*?</a></span>', response, re.DOTALL))
### 4. 将所有信息放到列表中，方便存放到txt文件夹中
        # 将所有信息放入列表中
        positons = list()
        for i in range(50):    # 将职位的所有信息控制在50以内
            values = {
                      "职位名称": positions_name[i],
                      "公司名称": positions_company[i],
                      "公司地址": positions_location[i],
                      "职位薪资": positions_money[i],
                      "职位信息链接": positions_links[i],
                      "职位信息": positions_informations[i]
            }
            positons.append(values)
        print(positons)
        #调用txt进行存放
        positions_excel(positons, new_position_city, new_position_type)
 ### 5. 通过职位链接爬取更加精确的信息
        def xinxi(position_link, headers):
          responses = requests.get(position_link, headers).content.decode("gbk","ignore")
          # 爬取职位信息
          position_information = re.findall(r'<div class="bmsg job_msg inbox">(.*?)</div>', responses, re.DOTALL)
          # 利用sub函数去除多余字符
          for new_position_information in position_information:
              position_information_new = re.sub(r'<.*?>', '', new_position_information)
              # 利用replace二次去除多余字符
              position_information_final = position_information_new.replace('&nbsp;', '')
              # 返回职位信息
              return position_information_final
 ### 6. 将数据存放到txt文件夹中
        def positions_excel(position, position_city, position_type):
          # 对文件名进行修改
          H = "2020年" + position_type + "职位信息表.txt"
          # 存放表头
          headers =["职位名称", "公司名称", "公司地址", "职位薪资", "职位信息链接", "职位信息"]
          # 将所有信息存放到各自文件夹中
          with open(H, "a+", encoding="utf-8-sig", newline="")as f:
              writer = csv.DictWriter(f,headers)
              writer.writeheader()
              writer.writerows(position)
### 7. 调用词云
        types = ["Python", "JAVA", "golang", "C#", "web", "前端", "node.js", "C++"]
        for type in types:
           ciyun(type)
### 8. 词云分析
        def ciyun(position_type):
          # 打开txt文件
          text_from_file = open('2020年' + type + '职位信息表.txt', 'r', encoding='utf-8').read()
          # 生成分词
          word_jieba = jieba.cut(text_from_file)
          word_space = ' '.join(word_jieba)
          # 设置不需要的词语
          stopwords = {'关键字', '类别', '职能', 'jobs', 'job', '要求', '任职'}
          # 生成词云
          my_wordcloud = WordCloud(
              background_color='while',    # 设置背景颜色
              max_words=200,               # 设置最大显示的词数
              stopwords=stopwords,         # 设置停用词
              font_path='arial.ttf',       # 设置字体
              max_font_size=100,           # 设置字体最大值
              random_state=50              # 设置配色方案
          ).generate(word_space)
          # 显示词云
          plt.figure('Python')
          plt.imshow(my_wordcloud)
          plt.axis('off')
          plt.show()
