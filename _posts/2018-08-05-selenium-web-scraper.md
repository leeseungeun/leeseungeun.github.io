---
title:  "셀레니움 (Selenium)으로 웹 스크랩퍼 (Web Scraper) 만들기"
date:   2018-08-05 22:33:05 +0900
tags: 파이썬 셀레니움 스크랩퍼 Python Selenium Scraper
---
아래의 내용은 개인적으로 공부한 내용을 정리한 것입니다. 잘못된 내용이 있다면 댓글 부탁드립니다!

- - -

근래 이전에 진행했던 프로젝트의 리팩토링 (Refactoring)을 하고 있습니다.  

### 리팩토링[1][2] 
프로그래밍 상 똑같이 동작하는데 코드를 효율적으로 바꾸는 활동을 의미합니다.  
이때 기능을 추가해서는 안되며, 코드의 구조에만 신경써야 합니다.  

이전에 진행했던 프로젝트는 소셜네트워크서비스 (Social Network Service, SNS)에서의 맛집 후기가 업체로부터 대가를 제공 받고 작성한 광고성 후기인지, 순수하게 정보 제공을 목적으로 작성한 비광고성 후기인지 구분해주는 검색 필터링 서비스였습니다.  
이 프로젝트를 위해서는 웹 스크래핑 (Web Scraping)이 필요합니다.

### 웹 크롤링 (Web Crawling) vs 웹 스크래핑 (Web Scraping)[3]
웹 크롤링은 크롤러(Crawler)라는 봇 (Bot)을 이용해 정보를 인덱싱 (Indexing)하는 것을 의미합니다. 대표적인 크롤러로 구글, 야후 등 검색 엔진이 있습니다.  
웹 스크래핑은 스크래퍼 (Scraper)라는 봇 (Bot)을 이용해 자동적으로 웹에서 정보와 컨테츠를 추출하는 것을 의미합니다. 이때 정보는 복제되어 다른 사이트에서 이용되거나, 분석에 이용됩니다.

이에 따라 현재 개발 환경 (우분투 도커 컨테이너)에서 스크래퍼를 구현하고자 합니다.  
스크래퍼 구현을 위해서 셀리니움 (Selenium)을 이용했습니다.  
셀레니움은 브라우저를 자동화해주는 웹 드라이버 (Web driver)입니다. 웹 드라이버는 언어에 상관 없이 프로그래밍 또는 스크립트로 웹 브라우저를 조사, 제어하는 인터페이스, 플랫폼을 의미합니다.  

우선 셀레니움 이용을 위해서 설치를 진행합니다. 맥 또는 리눅스라면 터미널에서, 윈도우의 경우 파워셸 또는 커맨드창에서 아래의 명령을 실행합니다. 코드는 파이썬 3.5를 기준으로 합니다!

```
pip install selenium

# if you use linux or mac, type command below
pip3 install selenium 
``` 

웹 드라이브를 사용하기 위해서는 해당 브라우저가 머신에 설치되어 있어야 합니다. 이를 위해서 도커 우분투 컨테이너에 크롬 (Chrome) 브라우저를 설치합니다.[4]  

크롬 설치 전 필요한 패키지를 받습니다.
```
apt-get update
apt-get install libxss1 libappindicator1 libindicator7
```

이제 ```wget``` 명령어를 이용해서 크롬 설치기를 다운로드 받습니다.
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

wget은 World Wide Web Get의 줄임말로, CLI 환경에서 웹 사이트의 정보를 가져오는 역할을 수행합니다.[5] wget 패키지가 없다면 위와 같이 ```apt-get install wget``` 명령어를 수행해주세요!  

그 다음 ```.deb``` 파일을 추출 (extract), 분석 (analyse), 언팩 (unpack), 설치 (install), 제거 (remove)하는 저수준(low-level)의 시스템 도구인 dpkg 명렁어를 사용합니다.[6]
```
dpkg -i google-chrome*.deb
```

저의 경우 이 명령어 실행 후 길게 에러 메시지가 나타났습니다. 에러 메시지의 골자는 의존성이 있는 패키지가 모두 설치되지 않았다는 것입니다. 그런데 비슷한 에러가 자주 나타나는 것으로 보입니다. (필요한 패키지가 너무 많아서 그런 게 아닌 것인가 생각됩니다...) 설치 방법을 기술한 페이지에도 이에 대한 해결 방법을 기술하고 있습니다. 해결방법은 아래와 같습니다.
```
apt-get install -f
``` 

```apt-get install```의 ```-f``` 옵션은 ```--fix-broken```으로, 의존성이 깨진 시스템을 수정하려는 시도를 합니다. 하지만 이 명령어만으로 깨진 시스템이 복구되지 않을 수도 있습니다.[7]  

위 명령어를 통해 필요한 의존성을 모두 설치했다면, ```dpkg -i google-chrome*.deb``` 명령어를 다시 실행해 크롬 설치를 완료합니다.  

이하는 셀레니움의 사용 방법 등을 다루고자 합니다. 이 [링크](https://beomi.github.io/gb-crawling/posts/2017-09-28-HowToMakeWebCrawler-Headless-Chrome.html)를 기반으로 진행하여 자세한 내용은 이를 참고하시면 좋을 것 같습니다.

크롬이 설치가 완료되었다면 크롬 웹 드라이버를 다운로드합니다. 이 [링크](https://sites.google.com/a/chromium.org/chromedriver/downloads)에서 다운 받은 후, 압축 해체하면 끝입니다!  

이제 코드로 브라우저를 제어해보고자 합니다.아래의 코드를 응용하시면 이가 가능해집니다!
```
from selenium import webdriver


# windows requires extension e.g. path/chromedriver.exe
driver = webdriver.Chrome('path/chromedriver')

# open window and move to url
driver.get(url)
# wait for loading
driver.implicit_wait(3)

# some codes for controlling browser

"""
see https://stackoverflow.com/questions/15067107/difference-between-webdriver-dispose-close-and-quit

driver.close() - close browser that driver focuses of
driver.quit() - call dispose()
driver.dispose() - close all browser windows and end session
"""
driver.quit()
```

셀레니움은 브라우저를 코드로써 제어하도록 도와주기 때문에, 별도의 옵션을 추가하지 않으면 실제 브라우저 윈도우가 생성됩니다. 실제 브라우저가 생성되지 않고 조작할 수 있도록 하는 옵션을 ```headless``` 옵션이라고 하는데요. 저는 브라우저를 화면에 표시할 필요가 없어 이 옵션을 아래와 같이 추가했습니다.

```
options = webdriver.ChromeOptions()
options.add_argument('headless')

driver = webdriver.Chrome('path/chromedriver',chrome_options=options)
```

이제 실제로 셀레니움을 실행할 차례입니다.
제 경우 위 명령어를 실행하던 중 아래의 오류를 만났습니다.
```
org.openqa.selenium.WebDriverException: unknown error: DevToolsActivePort file doesn't exist while trying to initiate Chrome Browser
```

이를 위해 위 코드의 상단 부분을 아래와 같이 수정했습니다.
```
...

options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument("--no-sandbox")
options.add_argument("--disable-dev-shm-usage")

...
```

각 옵션이 무엇을 의미하는지 찾아보았습니다.  
그 결과, 샌드박스(Sandbox)는 외부로부터 받은 파일을 바로 실행하지 않고 보호된 영역에서 실행해봄으로써 시켜봄으로써 내부 시스템에 악영향을 주는 것을 미연에 방지하는 기술을 지칭합니다.[8] 위의 옵션은 그런 샌드박스를 해제하는 것으로, 보안 상 권장되지 않는다고 합니다. 하지만 다른 방법을 찾기 전까지는 우선 위의 옵션을 사용하려 합니다.

또한 ```--disable-dev-shm-usage```는 프로그램 간 데이터를 주고 받는 효율적인 방법을 사용하지 못하도록 하는 옵션입니다.[9] 

위의 설정을 추가한 후에는 문제가 발생하지 않았습니다!

이제는 마지막으로 스크래퍼를 테스트하며 알게 된 몇 가지 팁을 정리하고자 합니다.  

첫 번째 팁은 ```frame``` 또는 ```iframe```태그에 관한 내용입니다.  
페이지가 ```frame``` 또는 ```iframe```으로 구성된 경우 찾고자 하는 태그가 선택되지 않거나 ```NoSuchElementException```가 발생합니다.[10] 분명히 존재하는 태그가 선택되지 않거나 ```NoSuchElementException```이 발생한 경우 ```frame``` 또는 ```iframe``` 태그가 페이지에 있는지 확인해봅니다.

### frame vs iframe
```frame``` 태그는 ```frameset``` 태그 안에서 특정 윈도우 (프레임)을 정의합니다. HTML5에서는 지원하지 않으므로 참고 부탁드립니다.[11]   
```iframe``` 태그는 인라인 프레임을 지정합니다.[12]    
```frameset```태그는 화면을 분할해 다른 문서를 각각에 보여줄 수 있고, ```iframe``` 태그는 플로트 (float)할 수 있다는 점이 둘 간의 차이입니다.  
자신의 도메인에서 나오지 않은 콘텐츠를 포함할 수 있다는 점에서 이 두 태그를 사용할 경우 사용하지 않는 경우에 비해 안전하지 않다고 봅니다.[13]  

두 번째 팁은 ```Xpath```입니다.  
```Xpath```란 XML 경로 표현으로, 웹 페이지에서 id, class 등으로 요소 (element)를 찾지 못할 때 사용합니다.[14] 표현은 아래와 같습니다.[15]    

표현|설명
----|----
노드명|해당 노드명의 노들를 모두 선택
/|루트 노드 (root node)부터 선택
//|선택된 노드와 그 후손 노드들 안에서 지정한 노드를 선택 
.|현재 노드 선택
..|현재 노드의 부모 노드 선택
@|속성 (attribute) 선택

가령 ```CSS 선택자```로 ```.se_textarea```는 ```Xpath```로 ```//*[contains(@class,"se_textarea")]```으로 표현이 가능합니다. (더 많은 예시는 [여기](https://stackoverflow.com/questions/16347121/selenium-looking-for-examples-of-converting-xpath-locators-to-css)에서 확인하실 수 있습니다.)  

```Xpath```를 이용하면 아래와 같은 html 문서에서 텍스트만 추출하는 것이 가능합니다.  
```
<p>
    <span> text1 </span>
    <span> text2 </span>
</p>
```
텍스트를 추출하고 싶다면 셀레니움에서 ```element = driver.find_element_by_xpath('//p'))``` (일치하는 모든 요소를 찾는다면 ```elements = driver.find_elements_by_xpath('//p')```)를 이용해 선택한 후, ```element.text``` (또는 ```elements.text```)로써 텍스트를 추출합니다.[16] 

[1]: https://opentutorials.org/course/1189/13813
[2]: https://medium.com/@trilliwon/2-%EC%98%A4%EB%9E%98%EC%82%AC%EB%8A%94-%EC%BD%94%EB%93%9C-8a16c82166d2
[3]: https://www.quora.com/What-are-the-biggest-differences-between-web-crawling-and-web-scraping
[4]: https://askubuntu.com/questions/79280/how-to-install-chrome-browser-properly-via-command-line
[5]: http://korean-daeddo.blogspot.com/2016/01/wget.html
[6]: https://askubuntu.com/questions/173465/what-is-dpkg-for
[7]: https://askubuntu.com/questions/58378/what-exactly-does-sudo-apt-get-f-install-do
[8]: http://www.itworld.co.kr/news/90280
[9]: https://www.cyberciti.biz/tips/what-is-devshm-and-its-practical-usage.html
[10]: https://stackoverflow.com/questions/39190910/nosuchelementexception-unable-to-locate-element
[11]: https://www.w3schools.com/tags/tag_frame.asp
[12]: https://www.w3schools.com/tags/tag_iframe.asp
[13]: https://stackoverflow.com/questions/1079128/whats-the-difference-between-iframe-and-frame
[14]: https://www.guru99.com/xpath-selenium.html
[15]: https://www.w3schools.com/xml/xpath_syntax.asp
[16]: https://stackoverflow.com/questions/45238364/python-and-selenium-get-text-excluding-child-nodes-text