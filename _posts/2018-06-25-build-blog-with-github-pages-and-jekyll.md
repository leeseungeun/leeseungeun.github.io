---
title:  "Github Pages와 Jekyll로 블로그 시작하기"
date:   2018-06-25 18:56:05 +0900
tags: ["github pages", "jekyll"]
---


블로그에 공부한 바를 정리하기 위해서, Github Pages와 Jekyll을 사용하게 되었습니다.


## Github Pages [^1]
Github 리포지토리를 통해 정적 사이트를 호스팅하는 서비스로,


내부 엔진으로 Jekyll을 사용합니다.


## Jekyll [^2]
루비 기반 정적 사이트 생성기입니다.


텍스트 파일로써 컨텐츠를 작성합니다.

- - -

Github Pages와 Jekyll은 아래의 과정을 거쳐 설정했습니다.


1. 도커로써 우분투 (Ubuntu) 개발 환경 설정

    윈도우 운영체제 위에서 루비 사용은 권장되지 않습니다.[^3][^4]  
    * 유닉스 계열 운영체제에서와 비교했을 때, 윈도우 운영체제에서 루비는 느립니다.
    * 일부 젬(Gem)과 라이브러리는 윈도우 운영체제에서 동작하지 않습니다.


    Jekyll은 루비를 기반으로 하기 때문에, 도커를 이용해 우분투 개발 환경을 설정했습니다.


    자세한 방법은 [해당 포스팅](../run-linux-on-windows-03-docker)을 참고해주세요.


2. Jekyll 설치

    Jekyll 설치에는 준비 프로그램이 필요합니다.[^5]


    준비 프로그램의 목록은 아래와 같습니다.
    * Ruby
    * RubyGems
    * NodeJS
    * Python

    우선 아래의 명령어로 설치된 패키지를 모두 업데이트합니다.

    ```apt-get update```
    
    
    그리고 아래의 명령어로 준비 프로그램을 설치합니다.
    ```
    apt-get install -y ruby
    apt-get install -y ruby-dev
    apt-get install -y nodejs
    apt-get install -y python3
    apt-get install -y bundler
    ```

    사전 프로그램이 모두 설치되면 아래의 명령어로 Jekyll을 설치합니다.
    
    ``` gem install jekyll```


3. Jekyll로 블로그 시작
    
    Jekyll이 설치 완료되면 아래의 명령어로 블로그를 시작합니다.

    ```jekyll new .```

    위 명령어에서 ```.```은 현재 디렉토리에서 블로그를 시작함을 의미합니다.
    
    
    명령이 수행되면 아래의 파일이 생성됩니다.
    * _posts/yyyy-MM-dd-welcome-to-jekyll.markdown
    * _config.yml
    * .gitignore
    * 404.html
    * Gemfile
    * Gemfile.lock
    * index.html


4. 테마 적용

    여러 기능을 제공하고 커스터마이즈가 용이한 Minimal Mistakes Jekyll Theme을 블로그에 적용했습니다.[^6]

    테마 적용을 위해서는 ```jekyll new .``` 명령어로 생성한 ```Gemfile```의 수정이 필요합니다.


    수정할 내용은 다음과 같습니다.

    ```
    # 아래의 코드는 주석 처리합니다.
    # gem "jekyll", "~> 3.8.3"

    # 아래의 코드 역시 주석 처리합니다. Jekyll 기본 테마 관련 코드입니다. 
    # gem "minima", "~> 2.0"

    # 아래 코드는 주석을 해제합니다. 
    gem "github-pages", group: :jekyll_plugins
    ```

    Gemfile 편집이 완료되면 ```bundle``` 명령어를 실행합니다.


    이때 ```Bundler could not find compatible versions for gem ```라는 메시지가 나타나면


    메시지대로 ```bundle update```를 실행합니다.


    저는 ```bundle update``` 실행 중 아래와 같은 메시지가 나타났습니다.
    ```
    An error occurred while installing nokogiri (1.4.7), and Bundler cannot
    continue. Make sure that `gem install nokogiri -v '1.4.7'` succeeds before bundling.
    ```
    해당 문제는 

    ```
    apt-get install zlib1g-dev
    apt-get install libxml2-dev
    ```
    명령어를 실행해 zlib1g-dev와 libxml2-dev를 설치함으로써 해결했습니다.[^7]


    ```bundle update```로써 젬을 업데이트했다면 ```_config.yml```에 ```remote_theme: "mmistakes/minimal-mistakes"``` 코드를 추가하고,


    ```theme: ``` 또는 ```remote_theme: ``` 엔트리를 삭제합니다.


    ```remote_theme:```과 ```"mmistakes/minimal-mistakes"``` 사이에 **공백이 없을 경우** 페이지 빌드에 실패하므로 유의하시는 편이 좋습니다!


    마지막으로 
    * ```index.md``` 를 ```index.html```로 변경합니다.
    * ```_posts/yyyy-MM-dd-welcome-to-jekyll.markdown```의 ```layout: post```를 ```layout: single```로 바꿉니다. 
    * ```about.md``` 삭제합니다. 

5. 커스터마이즈 (customize)

    블로그에 테마를 적용했다면 적용한 테마를 커스터마이즈해줍니다.


    저의 경우 
    * 태그 메뉴 추가
    * 댓글 기능 추가
    * 프로필 등 편집

    을 진행했습니다.

    * 태그 메뉴 추가


        ```_data/navigation.yml```과 ```_pages/tag-archive.html```을 추가합니다.[^8]
        * ```_data/navigation.yml```
        
        ```
        # main links
        main:
            - title: "Tags"
              url: /tags
        ```
        * ```_pages/tag-archive.html```

        ```
        ---
        title: "Posts by Tag"
        permalink: /tags/
        layout: tags
        author_profile: true
        ---
        {% for tag in group_names %}
        {% assign posts = group_items[forloop.index0] %}
        <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
        {% for post in posts %}
        {% include archive-single.html %}
        {% endfor %}
        {% endfor %}
        ```        

    * 댓글 기능 추가


        댓글을 직접 구현하지 않고 위젯의 형태로 서비스에 삽입할 수 있는 댓글 전문 서비스인 Disqus를 이용했습니다.[^9]


        Minimal-Mistakes-Jekyll-Theme에서는 ```_config.yml```의 편집으로 간단하게 댓글 기능을 추가할 수 있습니다.


        Disqus에 회원가입해 ```Website Name, Website URL, Category, Language``` 기입하면 Web Site가 생성됩니다. 
        
        
        Web Site가 생성되면 ```https://Your-Website-Name.disqus.com/admin/settings/general```에서 Shortname을 확인하고,

        
        ```_config.yml```을 아래와 같이 수정합니다.

        ```
        comments:
            provider               : "disqus"
            disqus:
                shortname            : "your-short-name"
        ```

        _post 디렉토리 아래 모든 파일에 동일한 설정을 적용하기 위해서 ```_config.yml```에 아래의 내용도 수정해줍니다.

        ```
        defaults:
        # _posts
        - scope:
            path: "_posts" # 경로 설정
            type: "posts" 
            values:
                layout: single # 레이아웃
                author_profile: true # 저자 프로필 표시 여부
                read_time: false # 읽은 시간 표시 여부
                comments: true # 댓글 허용 여부
                share: true # 공유 허용 여부
                related: true # 관련 포스트 표시 여부
        ```

        위와 같이 ```_config.yml``` 기본 설정을 지정해주면 개별 포스트마다 관련 설정을 할 필요가 없습니다.


        다만, ```config.yml```과 다르게 설정해야 할 포스트가 존재한다면 해당 포스트를 다음과 같이 작성해 설정을 변경할 수 있습니다.[^10]

        ```
        ---
        layout:  "some-other-layout"
        title: "title"
        date:   "yyyy-MM-dd hh:mm:ii +timezone"
        tags: [tag1, tag2]
        author_profile: toggled_config_value
        comments: toggle_config_value
        ---
        ```

6. 호스팅

    마지막으로 Jekyll을 이용해 만든 블로그를 Github Pages에 호스팅해줍니다.

    ```user_name.github.io```를 이름으로 리포지토리를 생성합니다.

    해당 리포지토리에 작업한 내용을 커밋 및 푸시하면 ```https://user_name.github.io```에 블로그 내용이 표시됩니다!


[^1]: https://help.github.com/articles/what-is-github-pages
[^2]: https://jekyllrb-ko.github.io/docs/home/
[^3]: https://stackoverflow.com/questions/164896/limitations-in-running-ruby-rails-on-windows
[^4]: https://www.reddit.com/r/ruby/comments/65n186/is_rubyror_on_windows_really_that_bad
[^5]: http://blog.napier.kr/blog/2016/09/01/jekyll-ubuntu%EC%97%90-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0
[^6]: https://github.com/mmistakes/minimal-mistakes
[^7]: https://stackoverflow.com/questions/30780988/error-installing-nokogiri-in-ubuntu-14-0-4-ruby-1-8-7
[^8]: https://junhobaik.github.io/jekyll-apply-theme
[^9]: https://opentutorials.org/course/2473/13865
[^10]: https://mmistakes.github.io/minimal-mistakes/docs/configuration