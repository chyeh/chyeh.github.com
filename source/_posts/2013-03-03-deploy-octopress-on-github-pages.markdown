---
layout: post
title: "Deploy Octopress on Github Pages"
date: 2013-03-03 23:50
comments: true
categories: 
---

## 前言

此篇紀錄我如何將[Octopress](http://octopress.org/)架設在[Git](https://github.com/)所提供的網頁空間[Github Pages](http://pages.github.com/)上。以下是我的環境：

*	作業系統：Linux Mint 13
*	Ruby版本：ruby 1.9.3p385 (2013-02-06 revision 39114) [x86_64-linux]

若要將Octopress做為自己的網誌架構架，並要架設在像 **http://你的帳號.github.com** 這樣的網址，所要作的步驟大致上如下：

1.	將Octopress專案下載下來
2.	在Git上建立給個人網頁使用的repository
3.	設定ssh key
4.	將網誌專案push到Git上


- - -

## 1.	將Octopress專案下載下來

第一步，用git指令下載：
	
    $ git clone git://github.com/imathis/octopress.git octopress && cd octopress

接下來需要安裝一些dependencies：

	$ sudo gem install bundler
	$ bundle exec rake install

- - -

## 2.	在Git上建立個人網頁使用的repository

先架好Github Pages預設的網頁專案，之後我們會再將Octopress的專案Push上此專案的repository。

### Create the repository

首先在Git上建立一個新的repository。可以在git網頁右上角點[repo](https://github.com/new)那個按鈕，進入新增repository的頁面。在Repository name那個欄位填上"**xxx.github.com**"(**xxx**指的是你的**git帳號**)。填玩之後，按下最下面的create repository鍵。

### Github Pages

再來，進入此repository的管理頁面。按下setting選項進入設定頁面，點下Automatic Page Generator按鍵。直接到最下面按下Continue to Layouts按鍵。按下PUBLISH那個綠勾勾。此時 **http://你的帳號.github.com** 應該已經會有網頁了！這個步驟可能是非必要的，我不確定是否按下PUBLISH鍵才會啟動repository的web server。反正我是做了XD。

- - -

## 3.	設定ssh key

接著回到repository的setting管理頁面，點選左邊的Deploy Keys並按下Add deploy key。

### 檢查是否已有ssh key

首先，先檢查一下是否已經generate過ssh key了。如果已經有了ssh key，就可以直接跳至**利用工具複製ssh key**的部份。打以下指令看是否能將key fingerprint印出：
	
	$ ssh-add -l
	2048 ba:ca:35:bb:a6:86:c9:f3:9c:5b:9e:f7:4e:f4:db:11  xxx@xxxxx.com (RSA)

照理來講，如果有顯示下面那一行key fingerprint，就表示已經有key了。如果並沒有顯示key fingerprint，而是顯示像以下訊息：

	$ ssh-add -l
	Could not open a connection to your authentication agent.

則改用以下指令：

	$ ssh-keygen -lf ~/.ssh/id_rsa.pub
	2048 ba:ca:35:bb:a6:86:c9:f3:9c:5b:9e:f7:4e:f4:db:11  xxx@xxxxx.com (RSA)

如果兩種情況都無法印出key fingerprint，檢查在。可能是本來就沒有generate過ssh key。檢查一下`~/.ssh`目錄底下是否有`id_rsa`和`id_rsa.pub`兩個檔案。如果沒有的話，表示沒有generate過ssh key。

### 產生ssh key

打入指令：

	$ ssh-keygen -t rsa -C "your_email@example.com"
	$ ssh-add ~/.ssh/id_rsa

產生key的時候會問一些問題，不想理他的話就一直按enter吧。到這一步驟應該已經有ssh key了，接下來就把她填到git上吧！

### 利用工具複製ssh key

為了防止不小心多複製或少複製一兩個字元的這種錯誤，所以最好使用xclip這個小工具。首先安裝xclip：

	$ sudo apt-get install xclip

安裝好之後，下面這行指令將會把你的key一字元無差地複製進你的剪貼簿：

	$ xclip -sel clip < ~/.ssh/id_rsa.pub

### deploy key

回到剛剛Add deploy key的那個頁面。下面有一個很大的key的欄位。直接貼上剛剛複製進剪貼簿的key，並按下下面的Add key按鈕。然後網頁會跳到confirm的頁面，輸入完git的密碼之後就完成了Deploy Keys的動作了。

### git ssh 測試

首先要對git設定使用者與信箱：

    $ git config --global user.name "xxx"
    $ git config --global user.email xxx@xxxx.com

以上的步驟應該完成ssh key的設置了！可以打以下指令測試，正常的話應該能產生歡迎訊息：

	$ ssh -T git@github.com
    Hi xxx/xxx.github.com! You've successfully authenticated, but GitHub does not provide shell access.

- - -

## 4.	將專案push到Git上

首先我們必須先設定repository的ssh url。點進repository管理頁面，便能找到ssh的url。然後照著以下輸入指令與url：

	$ bundle exec rake setup_github_pages
    Enter the read/write url for your repository
    (For example, 'git@github.com:your_username/your_username.github.com)
    Repository url: git@github.com:xxx/xxx.github.com.git

再來，產生blog並複製產生的檔案到_deploy/底下：

	$ bundle exec rake generate
	$ bundle exec rake deploy

最後，如果想要寫一篇網誌的話，先用以下指令產生編寫的.markdown檔：

	$ bundle exec rake new_post[title]

編輯產生出來的`source/_posts/2013-03-03-title.markdown`這一個.markdown檔案。完成文章編寫之後，打以下指令之後便可在 http://localhost:4000/ 預覽：

	$ bundle exec rake preview

若確認無誤，只要打以下指令便能將網址push上git，更新你的網頁了！

	$ bundle exec rake generate
	$ git add .
	$ git push origin source
	$ bundle exec rake deploy

- - -

## References

1.	[http://zerodie.github.com/blog/2012/01/19/octopress-github-pages/](http://zerodie.github.com/blog/2012/01/19/octopress-github-pages/)
2.	[http://www.ypwang.info/blog/blog/2012/04/15/octopress-an-zhuang/](http://www.ypwang.info/blog/blog/2012/04/15/octopress-an-zhuang/)
3.	[https://help.github.com/categories/56/articles](https://help.github.com/categories/56/articles)
4.	[http://stackoverflow.com/questions/9560892/permissions-error-github-ssh-key-not-recognized](http://stackoverflow.com/questions/9560892/permissions-error-github-ssh-key-not-recognized)


