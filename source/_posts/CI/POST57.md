---
title: Travis CI持续集成Github
tags: CI
category: CI
abbrlink: 60076
date: 2018-01-09 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0083.jpg)

1. Travis CI 介绍
CI即持续集成系统。对个人而言，就是让你的代码在提交到远程(这里是GitHub)，立即自动编译，自动化测试、自动部署等。

<!--more-->

不需要在担心更换电脑时，还要从新部署环境的问题，只要你能向远程推送文章，其他的事情就都可以交给Travis CI处理就ok了。

2. Travis CI 使用
默认前提是已经通过Github进行授权登录Travis网站，并关联了GitHub上的仓库和相关配置。

增加以下文件：
gulpfile.js 


```js
(function() {
    'use strict';
    var gulp = require('gulp'),
        less = require('gulp-less'),
        rename = require('gulp-rename'),
        minifycss = require('gulp-minify-css'),
        autoprefixer = require('gulp-autoprefixer'),
        uglify = require('gulp-uglify'),
        jshint = require('gulp-jshint'),
        stylish = require('jshint-stylish'),
        notify = require('gulp-notify'),
        plumber = require('gulp-plumber'),
        htmlclean = require('gulp-htmlclean'),
        htmlmin = require('gulp-htmlmin'),
        rev = require('gulp-rev-append'),
        sequence = require('gulp-sequence'),
        path = require('path'),
        paths = {
            root: './',
            source: './themes/snippet/source/' //主题下原文件
        }

    /*====================================================
         开发主题
    ====================================================*/

    // CSS预处理
    gulp.task('less-task', function() {
        return gulp.src(paths.source + 'css/less/_style.less')
        .pipe(plumber({
            errorHandler: notify.onError('Error: <%= error.message %>')
        }))
        .pipe(less())
        .pipe(rename({basename: "style"}))
        .pipe(gulp.dest(paths.source + 'css'))
        .pipe(notify({message: 'less compile complete'}));
    });

    // 校验JS语法和风格
    gulp.task('js-task', function() {
        return gulp.src(paths.source + 'js/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter(stylish))
        .pipe(gulp.dest(paths.source + 'js/'))
        .pipe(notify({message: 'js compile complete'}));
    });

    // 监听任务-主题开发模式
    gulp.task('dev', function() {
        gulp.watch(paths.source + 'css/less/*.less', ['less-task']);
        gulp.watch(paths.source + 'js/*.js', ['js-task']);
    });


    /*====================================================
        部署前代码处理
    ====================================================*/

    // 压缩处理 css
    gulp.task('minify-css', function() {
        return gulp.src('./public/**/*.css')
        .pipe(autoprefixer({
            browsers: ['last 10 versions', 'Firefox >= 20', 'Opera >= 36', 'ie >= 9', 'Android >= 4.0', ],
            cascade: true, //是否美化格式
            remove: false //是否删除不必要的前缀
        }))
        .pipe(minifycss())
        .pipe(gulp.dest('./public'))
        .pipe(notify({message: 'css minify complete'}));
    });

    // 压缩处理 js
    gulp.task('minify-js', function() {
        return gulp.src('./public/js/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./public/js'))
        .pipe(notify({message: 'js minify complete'}));
    });

    // 压缩处理 html
    gulp.task('minify-html', function() {
        return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
             removeComments: true, //清除HTML注释
             collapseWhitespace: true, //压缩HTML
             minifyJS: true, //压缩页面JS
             minifyCSS: true, //压缩页面CSS
             minifyURLs: true
        }))
        .pipe(gulp.dest('./public'));
    });
    
    // 添加版本号
    gulp.task('rev', function() {
        return gulp.src('./public/**/*.html')
        .pipe(rev())
        .pipe(gulp.dest('./public'));
    });

    // 同步执行task
    gulp.task('deploy',sequence(['minify-css','minify-js'],'rev','minify-html'));
    
    // 部署前代码处理
    gulp.task('default',['deploy'],function(e){
       console.log("[complete] please execute： hexo d");
    })
})();

```

travis.yml 

```yml
language: node_js
node_js: stable

sudo: false 

#cache
cache:
  directories:
    - "node_modules"

notifications:
  email:
    recipients:
      - snippet@91h5.cc
    on_success: never
    on_failure: always

# S: Build Lifecycle
install:
  - npm install
#  - gem install travis
#  - travis login --pro --github-token ${GH_TOKEN}

before_script:
  - export TZ='Asia/Shanghai'
  - npm install -g gulp 
  - chmod +x _travis.sh

script:
  - hexo clean && hexo g
  - gulp 

after_success:
 # - LAST_BUILD_NUMBER=68
 # - for i in $(seq 1 $LAST_BUILD_NUMBER ); do  travis logs $i --delete --force ; done

after_script:
  - ./_travis.sh

# E: Build LifeCycle

branches:
  only:
    - dev
env:
 global:
   - GH_REF: github.com/shenliyang/shenliyang.github.io.git

```

travis.sh 

```sh
#--------------------------------------------
#!/bin/bash
# author：shenliyang
# website：https://github.com/shenliyang
# slogan：梦想还是要有的，万一实现了呢。
#--------------------------------------------

#定义时间
time=`date +%Y-%m-%d\ %H:%M:%S`

#执行成功
function success(){
   echo "success"
}

#执行失败
function failure(){
   echo "failure"
}

#默认执行
function default(){

  git clone https://${GH_REF} .deploy_git
  cd .deploy_git

  git checkout master
  cd ../

  mv .deploy_git/.git/ ./public/
  cd ./public

cat <<EOF >> README.md 
部署状态 | 集成结果 | 参考值
---|---|---
完成时间 | $time | yyyy-mm-dd hh:mm:ss
部署环境 | $TRAVIS_OS_NAME + $TRAVIS_NODE_VERSION | window \| linux + stable
部署类型 | $TRAVIS_EVENT_TYPE | push \| pull_request \| api \| cron
启用Sudo | $TRAVIS_SUDO | false \| true
仓库地址 | $TRAVIS_REPO_SLUG | owner_name/repo_name
提交分支 | $TRAVIS_COMMIT | hash 16位
提交信息 | $TRAVIS_COMMIT_MESSAGE |
Job ID   | $TRAVIS_JOB_ID | 
Job NUM  | $TRAVIS_JOB_NUMBER | 
EOF

  git init
  git config user.name "shenliyang"
  git config user.email ""
  git add .
  git commit -m "Build by Travis CI"
  git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
} 

case $1 in 
    "success")
	     success
       ;;
    "failure")
	     failure
	     ;;
	         *) 
       default	
esac

```


配置travis.yml 文件


```yml
language: node_js #使用Node语言环境
node_js: stable #安装稳定版Node

sudo: false  

#cache 启用缓存，加快构建速度
cache: 
  directories:
    - "node_modules"

notifications: #启用通知
  email:
    recipients:
      - snippet@91h5.cc #接收构建消息的邮件 不需要可设置为false
    on_success: never #部署成功时，可设置alway never change
    on_failure: always #部署失败时，同上

# S: Build Lifecycle
install:
  - npm install  #安装依赖

before_script:
  - export TZ='Asia/Shanghai' #设置时区
  - npm install -g gulp  #安装Gulp
  - chmod +x _travis.sh  #授权脚本执行权限

script:
  - hexo clean && hexo g #清除缓存并生成静态文件
  - gulp #执行gulp任务

after_success: #实行成功时(以后扩展功能使用)

after_script:
  - ./_travis.sh #执行部署脚本
# E: Build LifeCycle

branches:
  only:
    - dev #需要监听部署的分支
env:
 global:
   - GH_REF: github.com/shenliyang/shenliyang.github.io.git #更改为自己git地址
```

提交代码到Github，实现自动部署
当 .travis.yml 配置文件修改完成后，将其提交到远程仓库的 hexo 分支下，此时如果之前的配置一切ok，我们应该能在 Travis CI 的博客项目主页页面中看到自动构建已经在开始执行了。上面会显示出构建过程中的日志信息及状态等。
