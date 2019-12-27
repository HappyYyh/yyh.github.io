GitHub pages搭建

1、创建gitHub仓库

在github上面创建一个名称为`username.github.io`的项目这个username就是github账号的用户名

2、域名cname

3、安装Ruby

​     1、https://rubyinstaller.org/downloads/选择Ruby+Devkit 2.6.5-1 (x64) （网速可能很慢，多试几次）

​	 2、（界面上全选）最好不要该安装路径

 	   3、安装完后不要勾选ridk install的选项，因为从默认的原去下载几百兆会非常缓慢；

​     4、查找Ruby安装目录下的msys64\etc\pacman.d，编辑更新源：

​	Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/i686 加入mirrorlist.mingw32首位

​	Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/x86_64 加入mirrorlist.mingw64首位

​	Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/msys/$arch 加入mirrorlist.msys首位

然后再msys目录下mingw64.exe里面执行pacman -Syu

​		5、执行在命令行执行ridk install（如果安装时选择了不加入系统环境变量的，去Ruby安装目录的bin之下执行），一路回车至结束；

6、更新gem源：

​		gem sources --add https://mirrors.tuna.tsinghua.edu.cn/rubygems/ --remove https://rubygems.org/

4、gem install jekyll

这里面遇到的问题大多数是下载ruby不全，没有选择devkit或者网速问题造成的，重新安装

5、bundle install（在项目仓库里执行）

遇到问题：

ffi requires Ruby version >= 2.0, < 2.6. The current ruby version is 2.6.5.114.

解决办法：https://github.com/ffi/ffi/issues/598

执行gem install ffi -f

然后bundle update

6、bundle exec jekyll serve运行

7、本地访问乱码

在Ruby26-x64\lib\ruby\2.6.0\webrick\httpservlet里面打开filehandler.rb

258行

~~~xml
path = req.path_info.dup.force_encoding(Encoding.find("filesystem"))
				path.force_encoding("UTF-8")
        if trailing_pathsep?(req.path_info)
~~~

333行

~~~xml
break if base == "/"
		  base.force_encoding("UTF-8")  #这里是添加的一行
          break unless File.directory?(File.expand_path(res.filename + base))
~~~







~~~cmd
gem install jekyll
bundle install
bundle exec jekyll serve
~~~

