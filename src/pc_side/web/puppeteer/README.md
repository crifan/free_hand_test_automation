# puppeteer

## 背景

前端就有了对`headless`浏览器的需求，最多的应用场景有两个

1. `UI自动化测试`：摆脱手工浏览点击页面确认功能模式
2. `爬虫`：解决页面内容异步加载等问题

前端经常使用的莫过于

* `PhantomJS`
  * http://phantomjs.org/
* `selenium + webdriver`
  * http://seleniumhq.github.io/selenium/docs/api/javascript/

但两个库有一个共性：

* 难用
  * 环境安装复杂
  * API 调用不友好

2017 年 Chrome 团队连续放了两个大招

* Headless Chromium
  * https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md
* NodeJS API Puppeteer
  * https://github.com/GoogleChrome/puppeteer

-> 直接让 PhantomJS 和 Selenium IDE for Firefox 作者悬宣布没必要继续维护其产品

我们手工可以在浏览器上做的事情 Puppeteer 都能胜任

1. 生成网页截图或者 PDF
2. 爬取大量异步渲染内容的网页，基本就是人肉爬虫
3. 模拟键盘输入、表单自动提交、UI 自动化测试


## 概述

* `puppeteer`
  * 概述：微软开发的基于`Javascript`的web自动化的库
* 官网
  * github
    * puppeteer/puppeteer: Headless Chrome Node.js API
      * https://github.com/puppeteer/puppeteer
  * google
    * Puppeteer  |  Tools for Web Developers  |  Google Developers
      * https://developers.google.com/web/tools/puppeteer
* 优势
  * 可以用TypeScript编写测试
  * Devs还可以在运行测试时连接Chrome DevTools
