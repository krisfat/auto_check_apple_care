# auto_check_apple_care
自动在苹果官网查询剩余保修的日期

```
import base64

# 识别图片验证码，免费的算法不太灵，我用个是超级鹰的付费算法
from chaojiying import Chaojiying_Client
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from lxml import etree


url = "https://checkcoverage.apple.com/cn/zh/"
browser = webdriver.Chrome()
wait = WebDriverWait(browser, 10)
html = etree.HTML(browser.page_source)


def get_html():
   browser.get(url)
   # wait.until(EC.presence_of_all_elements_located((By.CSS_SELECTOR, '*')))
   html = etree.HTML(browser.page_source)
   nums = wait.until(EC.presence_of_element_located((By.ID, 'serial-number')))
   result = wait.until(EC.presence_of_element_located((By.ID, 'captcha-input')))
   search = wait.until(EC.element_to_be_clickable((By.CLASS_NAME, 'button-label')))
   nums.send_keys('DMPT7A2YHP9X')
   img_src = html.xpath('//img[@class="captcha-image"][1]/@src')[0][23:]
   password = Chaojiying_Client('超级鹰账户', '超级鹰密码', '软件的soft_id')
   imgdata = base64.b64decode(img_src)
   file = open('1.jpg', 'wb')
   file.write(imgdata)
   file.close()
   im = open('1.jpg', 'rb').read()
   # 1902为验证码的类型在超级鹰官网查询
   text = password.PostPic(im, 1902)
   print(text['pic_str'])
   result.send_keys(text['pic_str'])
   search.click()


def main():
   get_html()


if __name__ == '__main__':
   main()
```