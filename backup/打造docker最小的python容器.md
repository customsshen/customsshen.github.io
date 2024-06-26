# 打造docker最小的python容器
1. 如何打造最小 Python Docker 容器 https://b23.tv/BV1w54y1n72A
2. 現在已經是容器 (container) 的時代，團隊之間合作肯定要打包統一的 Docker Image 來解決環境不一致的問題，但是容器的大小決定了部署微服務的時間，本篇來介紹如何打造最小的 Python 容器。如果你需要跨部門合作，Docker 絕對是最棒的工具，讓大家不用為了環境煩惱。底下透過 Flask Application 來介紹如何撰寫 Dockerfile。


> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.wu-boy.com](https://blog.wu-boy.com/2021/07/building-minimal-docker-containers-for-python-applications/)

> 現在已經是容器 (container) 的時代，團隊之間合作肯定要打包統一的 Docker Image 來解決環境不一致的問題，但是容器的大小決定了部署微服務的時間，本篇來介紹如何打造最小的 Python 容器。

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)

現在已經是容器 (container) 的時代，團隊之間合作肯定要打包統一的 [Docker](https://www.docker.com/) Image 來解決環境不一致的問題，但是容器的大小決定了部署微服務的時間，本篇來介紹如何打造最小的 [Python](https://www.python.org/) 容器。如果你需要跨部門合作，Docker 絕對是最棒的工具，讓大家不用為了環境煩惱。底下透過 [Flask](https://flask.palletsprojects.com/en/2.0.x/) Application 來介紹如何撰寫 [Dockerfile](https://docs.docker.com/engine/reference/builder/)。

影片視頻
----

之後會把影片視頻放到底下課程內

*   [Go 語言課程](https://blog.wu-boy.com/golang-online-course/)
*   [Docker 容器實戰](https://blog.wu-boy.com/docker-course/)

撰寫 Flask 服務
-----------

直接用 Flask 服務當作範例，並且將檔案存放在 `src/server.py` 位置

```python
from flask import Flask

server = Flask(__name__)

@server.route("/")
def hello():
  return "Hello World!"

if __name__ == "__main__":
  server.run(host='0.0.0.0')
```

將著把相關套件都寫在 `requirements.txt` 內

```
Flask==1.1.1
gunicorn==20.1.0
```

開發者可以透過底下指令將所有相依性套件寫入。

```
pip freeze > requirements.txt
```

撰寫 Dockerfile
-------------

找到官方 [Python Docker Image](https://hub.docker.com/_/python) 頁面，裡面有個範例教你如何撰寫 `Dockerfile`。

```docker
FROM python:3.9

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY src /app

CMD [ "python", "server.py" ]
```

如果沒有很在意 Image 大小，這樣寫並沒有什麼問題。要打造小一點的 Image，可以先從 `FROM` 下手，官方其實有提到，如果是要拿來當 Production 的話，建議使用 `python:<version>-slim`，這裡面把一些不必要的套件全部清除了，只留下最基本要跑的 Python 套件，開發者可以透過這個 Image 再去疊加要用的套件，這樣 Image 會是最小的。來把上面的 `python:3.9` 換成 `python:3.9-slim`。

除了 `slim` 之外，我個人會推薦用 `alpine` 版本 (之前[介紹文章](https://blog.wu-boy.com/2015/12/a-super-small-docker-image-based-on-alpine-linux/))，此版本整個 Base Image 只有 5MB 大小，Alpine 版本為了將系統縮成這麼小，移除了大部分軟體，包含 `git` 或 `bash` 這種大型套件，所以基本上登入系統之後，幾乎都是空的，要什麼套件全都要自己裝，這非常適合用在微服務上，可以達到快速部署。

<table><thead><tr><th>python3.9</th><th>python3.9-slim</th><th>python3.9-alpine</th></tr></thead><tbody><tr><td>842MB</td><td>118MB</td><td><strong>55.8MB</strong></td></tr></tbody></table>

這邊寫法請注意，一定要先處理 requirements.txt 部分，在處理 `COPY` 檔案，這樣才可以享受 Layer Cache 的優勢，否則 Source Code 變動後，整個 build image 時間就沒有省到了。

使用 Multistage Build
-------------------

除了用最小 Size 的 Alpine Image 之外，還可以透過 Multistage Build 將需要的檔案複製到全新的 Image 內進行最後的打包，將寫法改成如下，可以再減少一些空間

```
FROM python:3.9-alpine as base
FROM base as builder
COPY requirements.txt /requirements.txt
RUN pip install --user -r /requirements.txt

FROM base
# copy only the dependencies installation from the 1st stage image
COPY --from=builder /root/.local /root/.local
COPY src /app
WORKDIR /app

# update PATH environment variable
ENV PATH=/home/app/.local/bin:$PATH

CMD ["/bin/sh"]
```

看看底下結果

<table><thead><tr><th>python3.9</th><th>python3.9-slim</th><th>python3.9-alpine</th><th>python3.9-alpine (Multistage)</th></tr></thead><tbody><tr><td>842MB</td><td>118MB</td><td>55.8MB</td><td><strong>48.8MB</strong></td></tr></tbody></table>

這邊透過 `--user` 方式將檔案都放在 root 底下，接著在用 `COPY --from=builder` 複製到另一個 Image 內。

心得
--

各種語言都有不同的優化方式，可以參考之前我寫的一篇『[用 Docker Multi-Stage 編譯出 Go 語言最小 Image](https://blog.wu-boy.com/2017/04/build-minimal-docker-container-using-multi-stage-for-go-app/)』。上面的寫法固然已經是最小的方式，但是還是有些安全性的疑慮，關於安全性的議題，我會在另一篇再詳細介紹，大概方向就是 Application 不要用 root 去執行，避免服務被入侵的時候，被拿到 root 權限，就像不用 docker 時，應該也不會拿 root 去跑任何服務吧。這都是相當不安全的作法，那 Docker 該如何避免呢？我會在另一篇做介紹。

參考文獻
----

*   [Building Minimal Docker Containers for Python Applications](https://blog.realkinetic.com/building-minimal-docker-containers-for-python-applications-37d0272c52f3)
*   [Containerized Python Development – Part 1](https://www.docker.com/blog/containerized-python-development-part-1/)
*   [Minimizing python docker images](https://rodneyosodo.medium.com/minimizing-python-docker-images-cf99f4468d39)

#### See also

*   [用 Supervisor 管理系統程式](https://blog.wu-boy.com/2021/09/control-system-process-using-supervisor-in-golang/)
*   [用 docker-compose 優雅關閉服務](https://blog.wu-boy.com/2021/06/graceful-stop-service-using-docker-compose-in-golang/)