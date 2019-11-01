import requests
import re
import json
import os
import random

# 尽量多的头部 防止被封
dangdang_headers = [
    "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:30.0) Gecko/20100101 Firefox/30.0",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.75.14 (KHTML, like Gecko) Version/7.0.3 Safari/537.75.14",
    "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; Win64; x64; Trident/6.0)",
    'Mozilla/5.0 (Windows; U; Windows NT 5.1; it; rv:1.8.1.11) Gecko/20071127 Firefox/2.0.0.11',
    'Opera/9.25 (Windows NT 5.1; U; en)',
    'Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)',
    'Mozilla/5.0 (compatible; Konqueror/3.5; Linux) KHTML/3.5.5 (like Gecko) (Kubuntu)',
    'Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.8.0.12) Gecko/20070731 Ubuntu/dapper-security Firefox/1.5.0.12',
    'Lynx/2.8.5rel.1 libwww-FM/2.14 SSL-MM/1.4.1 GNUTLS/1.2.9',
    "Mozilla/5.0 (X11; Linux i686) AppleWebKit/535.7 (KHTML, like Gecko) Ubuntu/11.04 Chromium/16.0.912.77 Chrome/16.0.912.77 Safari/535.7",
    "Mozilla/5.0 (X11; Ubuntu; Linux i687; rv:10.0) Gecko/20100101 Firefox/10.0",
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36'
]


def main(page):
    url = 'http://bang.dangdang.com/books/fivestars/01.00.00.00.00.00-recent30-0-0-1-' + str(page)
    html = request_dangdang(url)
    items = parse_result(html)  # 解析过滤我们想要的信息
    book_info = []
    for item in items:
        book_info.append(item)
    # 记录图书的信息
    write_item_to_file(book_info)
    # 下载图书的图片
    download_book_img(book_info)


def request_dangdang(url):
    try:
        global dangdang_headers
        response = requests.get(url, headers={'User-Agent': random.choice(dangdang_headers)})
        if response.status_code == 200:
            return response.text
    except requests.RequestException:
        return None


def parse_result(html):
    pattern = re.compile('<li>.*?list_num.*?(\d+).</div>.*?<img src="(.*?)".*?class="name".*?title="(.*?)">.*?class="star">.*?class="tuijian">(.*?)</span>.*?class="publisher_info">.*?target="_blank">(.*?)</a>.*?class="biaosheng">.*?<span>(.*?)</span></div>.*?<p><span\sclass="price_n">&yen;(.*?)</span>.*?</li>',re.S)
    items = re.findall(pattern, html)
    for item in items:
        yield {
            'range': item[0],
            'image': item[1],
            'title': item[2],
            'recommend': item[3],
            'author': item[4],
            'times': item[5],
            'price': item[6]
        }


def write_item_to_file(book_info):
    for item in book_info:
        print('开始写入数据 ====> ' + str(item))
        with open('book.txt', 'a', encoding='UTF-8') as f:
            f.write(json.dumps(item, ensure_ascii=False) + '\n')
            f.close()


def download_book_img(book_info):
    if os.path.exists('bookImg') is False:  # 判断是否存在bookImg目录
        os.mkdir('bookImg')  # 如果没有就创建该目录
    os.chdir('bookImg')  # 如果有就切换到该目录下
    global dangdang_headers  # 声明全局变量 头部
    for item in book_info:
        filename = 'NO.%s-%s.jpg' % (str(item['range']), item['title'])
        print('downloading....NO.%s:%s' % (item['range'], item['title']))
        with open(filename, 'wb') as f:
            img = requests.get(item['image'], headers={'User-Agent': random.choice(dangdang_headers)}).content
            f.write(img)


if __name__ == "__main__":
    for i in range(1, 2):
        main(i)
        
