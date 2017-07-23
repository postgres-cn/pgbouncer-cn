
## 简介

本项目用于[pgbouncer](https://github.com/pgbouncer/pgbouncer)文档的中文翻译，当前对应版本为pgbouncer 1.7.2 。

翻译内容为以下rst文件

- README.rst
- doc/config.rst
- doc/usage.rst


## 翻译注意事项

1. rst文件中的很多符号都有特殊意义，不要随意变更这些符号，比如把`::`改成`:`。
2. rst中的标签符号和中文汉字(不包括全角标点)相邻时，rst2html将无法正确识别出标签，因此需要补一个空格。
	比如:
	
		任何目标应用程序都可以连接到**pgbouncer**，
	
	要改成
	
		任何目标应用程序都可以连接到 **pgbouncer**，

3. rst的语法介绍可参考[Quick reStructuredText](http://docutils.sourceforge.net/docs/user/rst/quickref.html)


## 生成html

### 安装需要的软件

- Ruby 2.1+
- pandoc

### 从rst生成md

    git clone https://github.com/postgres-cn/pgbouncer-cn.git
    git clone https://github.com/postgres-cn/pgbouncer-doc.git
    cd pgbouncer-doc
    make

生成的md文件提交到pgbouncer-doc仓库后github pages会自动更新web站点

    git add --all
    git commit -m "rst到md更新"
    git push

### 本地编译html

    yum install nodejs
    
    cd pgbouncer-doc
    gem install bundler
    bundle install
    
    jekyll build
    
