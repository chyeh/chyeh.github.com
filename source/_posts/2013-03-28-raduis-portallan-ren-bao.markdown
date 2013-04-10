---
layout: post
title: "Raduis Portal懶人包"
date: 2013-03-28 15:45
comments: true
categories: 
---

## 1.建立專案

輸入以下指令：

	$ rails new rails3-bootstrap-devise-cancan -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb -T

此指令將會建立一個[rails3-bootstrap-devise-cancan](https://github.com/RailsApps/rails3-bootstrap-devise-cancan)專案。輸入指令之後，terminal會出現以下幾個問題：

    question  Install an example application?
          1)  I want to build my own application
          2)  membership/subscription/saas
          3)  rails-prelaunch-signup
          4)  rails3-bootstrap-devise-cancan
          5)  rails3-devise-rspec-cucumber
          6)  rails3-mongoid-devise
          7)  rails3-mongoid-omniauth
          8)  rails3-subdomains
	railsapps  Enter your selection: 4
    //略...
    question  Web server for development?
          1)  WEBrick (default)
          2)  Thin
          3)  Unicorn
          4)  Puma
       setup  Enter your selection: 1
	//略...
    question  Web server for production?
          1)  Same as development
          2)  Thin
          3)  Unicorn
          4)  Puma
       setup  Enter your selection: 2
	//略...
    question  Template engine?
          1)  ERB
          2)  Haml
          3)  Slim (experimental)
       setup  Enter your selection: 1


其中，在選擇production server的時候選thin，正式上線才不會出現一堆debug用的log。之後還有一些問題要回答，如下：

	extras  Set a robots.txt file to ban spiders? (y/n) y
	//略...
	extras  Create a GitHub repository? (y/n) n
	//略...
    extras  Use or create a project-specific rvm gemset and .rvmrc? (y/n) y
	//略...
    extras  Add 'therubyracer' JavaScript runtime (for Linux users without node.js)? (y/n) y

關於**robots.txt**，git頁面的說明如下：

>Set a robots.txt file to ban spiders if you want to keep your new site out of Google search results.


接著打入以下指令之後，瀏覽[http://localhost:3000/](http://localhost:3000/)應該就會有東西了：

    $ cd rails3-bootstrap-devise-cancan
    $ bundle install
    $ rails s

- - -

## 2.將devise-radius-authenticatable加入專案

編輯`Gemfile`，在最後一行加上：

	gem "devise-radius-authenticatable"

之後打入指令：
	
    $ bundle install
	$ generate devise:install

會出現以下訊息，請選Y：

	    conflict  config/initializers/devise.rb
	Overwrite /home/chyehlt/ror/test/rails3-bootstrap-devise-cancan/config/initializers/devise.rb? (enter "h" for help) [Ynaqdh] Y


編輯`app/models/user.rb`，並在原本的**:database_authenticatable**前面加上**:radius_authenticatable**。接著，打入以下指令：

	$ rails generate devise_radius_authenticatable:install <IP> <SECRET> [options]

接下來請參考了git頁面說明文件，編輯`config/initializers/devise.rb`。請在**warden**的部份加入幾行code，如下：

	config.warden do |warden_config|
	  warden_config.default_strategies(:database_authenticatable,
	                                   :radius_authenticatable,
	                                   {:scope => :admin})
	end

其中，database的method寫在前面，所以server會先檢查資料庫，有的話就算認證成功。如果資料庫這關沒過，就再問radius。至於 **:scope => :admin** 其實我不太知道這行是幹嘛的。接著，將此行：

    config.radius_uid_field = :uid

改為

	config.radius_uid_field = :email

最後，若要測試請打以下指令，應該就算完成了吧！

	$ rails s
