# 百度搜索自动化

下面就通过例子：**百度搜索自动化**，来说明不同平台的自动化具体是什么样子：

## PC端

### selenium

代码：

* 文件：[seleniumDemoBaiduSearch.py](https://book.crifan.com/books/free_hand_test_automation/website/assets/file/baiduSearchAutomation/selenium/seleniumDemoBaiduSearch.py)
* 贴出来是
  ```python
  # Function: demo selenium do baidu search and extract result
  # Author: Crifan Li
  # Update: 20210327

  from selenium import webdriver
  from selenium.webdriver.common.keys import Keys
  from selenium.webdriver.common.by import By
  from selenium.webdriver.support.ui import WebDriverWait
  from selenium.webdriver.support import expected_conditions as EC

  from bs4 import BeautifulSoup
  import re

  chromeDriver = webdriver.Chrome()

  ################################################################################
  # Open url
  ################################################################################
  baiduUrl = "https://www.baidu.com"
  chromeDriver.get(baiduUrl)
  print("title=%s" % chromeDriver.title)

  assert chromeDriver.title == "百度一下，你就知道"
  # assert '百度' in chromeDriver.title

  ################################################################################
  # Find/Locate search button
  ################################################################################
  SearchInputId = "kw"
  searchInputElem = chromeDriver.find_element_by_id(SearchInputId)
  print("searchInputElem=%s" % searchInputElem)
  # searchInputElem=<selenium.webdriver.remote.webelement.WebElement (session="e0d8b72f2fc31e27220f66fcbdb22bfc", element="21aa0e9d-04fa-4385-b16b-d82998068887")>

  ################################################################################
  # Input text
  ################################################################################
  searchInputElem.clear()
  print("Clear existed content")

  searchStr = "crifan"
  searchInputElem.send_keys(searchStr)
  print("Entered %s to search box" % searchStr)

  ################################################################################
  # Click button
  ################################################################################

  # Method 1: emulate press Enter key
  # searchButtonElem.send_keys(Keys.RETURN)
  # print("Pressed Enter/Return key")

  # Method 2: find button and click
  BaiduSearchId = "su"
  baiduSearchButtonElem = chromeDriver.find_element_by_id(BaiduSearchId)
  print("baiduSearchButtonElem=%s" % baiduSearchButtonElem)
  baiduSearchButtonElem.click()
  print("Clicked button %s" % baiduSearchButtonElem)

  ################################################################################
  # Wait page change/loading completed
  #   -> following element makesure show = visible
  #   -> otherwise possibly can NOT find elements
  ################################################################################
  MaxWaitSeconds = 10
  numTextElem = WebDriverWait(chromeDriver, MaxWaitSeconds).until(
      EC.presence_of_element_located((By.XPATH, "//span[@class='nums_text']"))
  )
  print("Search complete, showing: %s" % numTextElem)

  ################################################################################
  # Extract result
  ################################################################################

  # Method 1: use Selenium to extract title list
  searchResultAList = chromeDriver.find_elements_by_xpath("//h3[contains(@class, 't')]/a")
  print("searchResultAList=%s" % searchResultAList)
  searchResultANum = len(searchResultAList)
  print("searchResultANum=%s" % searchResultANum)
  for curIdx, curSearchResultAElem in enumerate(searchResultAList):
    curNum = curIdx + 1
    print("%s [%d] %s" % ("-"*20, curNum, "-"*20))
    baiduLinkUrl = curSearchResultAElem.get_attribute("href")
    print("baiduLinkUrl=%s" % baiduLinkUrl)
    title = curSearchResultAElem.text
    print("title=%s" % title)

  # # Method 2: use BeautifulSoup to extract title list
  # curHtml = chromeDriver.page_source
  # curSoup = BeautifulSoup(curHtml, 'html.parser')
  # beginTP = re.compile("^t.*")
  # searchResultH3List = curSoup.find_all("h3", {"class": beginTP})
  # print("searchResultH3List=%s" % searchResultH3List)
  # searchResultH3Num = len(searchResultH3List)
  # print("searchResultH3Num=%s" % searchResultH3Num)
  # for curIdx, searchResultH3Item in enumerate(searchResultH3List):
  #   curNum = curIdx + 1
  #   print("%s [%d] %s" % ("-"*20, curNum, "-"*20))
  #   aElem = searchResultH3Item.find("a")
  #   # print("aElem=%s" % aElem)
  #   baiduLinkUrl = aElem.attrs["href"]
  #   print("baiduLinkUrl=%s" % baiduLinkUrl)
  #   title = aElem.text
  #   print("title=%s" % title)

  ################################################################################
  # End close
  ################################################################################
  chromeDriver.close()
  ```

效果：

<video id="seleniumDemoBaiduSearch" controls="controls" preload="none" width="800" height="600">
  <source src="../../../assets/file/baiduSearchAutomation/selenium/seleniumDemoBaiduSearch.mp4" type="video/mp4">
</video>

### puppeteer

代码：

* 文件：[puppeteerDemoBaiduSearch.py](https://book.crifan.com/books/free_hand_test_automation/website/assets/file/baiduSearchAutomation/puppeteer/puppeteerDemoBaiduSearch.py)
* 贴出来是
  ```python
  # Function: pyppeteer (python version puppeteer) do baidu search
  # Author: Crifan Li
  # Update: 20210330

  import asyncio
  from pyppeteer import launch

  async def main():
      browser = await launch(headless=False)
      page = await browser.newPage()

      await page.setJavaScriptEnabled(enabled=True)

      baiduUrl = "https://www.baidu.com"
      await page.goto(baiduUrl)
      # await page.screenshot({'path': 'baidu.png'})

      ################################################################################
      # Input text
      ################################################################################
      searchStr = "crifan"

      # SearchInputSelector = "input[id=kw]"
      SearchInputSelector = "input[id='kw']"

      # SearchInputXpath = "//input[@id='kw']"
      # searchInputElem = page.xpath(SearchInputXpath)

      # # Input method 1: selector + click + keyboard type
      # searchInputElem = await page.querySelector(SearchInputSelector)
      # print("searchInputElem=%s" % searchInputElem)
      # await searchInputElem.click()
      # await page.keyboard.type(searchStr)

      # Input method 2: focus then type
      # await page.focus(SearchInputSelector)
      # await page.keyboard.type(searchStr)

      # Input method 3: selector and input once using type
      await page.type(SearchInputSelector, searchStr, delay=20)

      ################################################################################
      # Trigger search
      ################################################################################

      # Method 1: press ENTER key
      await page.keyboard.press('Enter')

      # # Method 2: locator search button then click
      # SearchButtonSelector = "input[id='su']"
      # searchButtonElem = await page.querySelector(SearchButtonSelector)
      # print("searchButtonElem=%s" % searchButtonElem)
      # await searchButtonElem.click()
      # # await searchButtonElem.press("Enter")

      ################################################################################
      # Wait page reload complete
      ################################################################################
      SearchFoundWordsSelector = 'span.nums_text'
      SearchFoundWordsXpath = "//span[@class='nums_text']"

      # await page.waitForSelector(SearchFoundWordsSelector)
      # await page.waitFor(SearchFoundWordsSelector)
      # await page.waitForXPath(SearchFoundWordsXpath)
      # Note: all above exception: 发生异常: ElementHandleError Evaluation failed: TypeError: MutationObserver is not a constructor
      #   so change to following

      # # Method 1: just wait
      # await page.waitFor(2000) # millisecond

      # Method 2: wait element showing
      SingleWaitSeconds = 1
      while not await page.querySelector(SearchFoundWordsSelector):
        print("Still not found %s, wait %s seconds" % (SearchFoundWordsSelector, SingleWaitSeconds))
        await asyncio.sleep(SingleWaitSeconds)
        # pass

      ################################################################################
      # Extract result
      ################################################################################

      resultASelector = "h3[class^='t'] a"
      searchResultAList = await page.querySelectorAll(resultASelector)
      # print("searchResultAList=%s" % searchResultAList)
      searchResultANum = len(searchResultAList)
      print("Found %s search result:" % searchResultANum)
      for curIdx, aElem in enumerate(searchResultAList):
        curNum = curIdx + 1
        print("%s [%d] %s" % ("-"*20, curNum, "-"*20))
        aTextJSHandle = await aElem.getProperty('textContent')
        # print("type(aTextJSHandle)=%s" % type(aTextJSHandle))
        # type(aTextJSHandle)=<class 'pyppeteer.execution_context.JSHandle'>
        # print("aTextJSHandle=%s" % aTextJSHandle)
        # aTextJSHandle=<pyppeteer.execution_context.JSHandle object at 0x10309c9b0>
        title = await aTextJSHandle.jsonValue()
        # print("type(title)=%s" % type(title))
        # type(title)=<class 'str'>
        print("title=%s" % title)

        baiduLinkUrl = await (await aElem.getProperty("href")).jsonValue()
        print("baiduLinkUrl=%s" % baiduLinkUrl)

      await browser.close()

  asyncio.get_event_loop().run_until_complete(main())
  ```

效果：

<video id="puppeteerDemoBaiduSearch" controls="controls" preload="none" width="800" height="600">
  <source src="../../../assets/file/baiduSearchAutomation/puppeteer/puppeteerDemoBaiduSearch.mp4" type="video/mp4">
</video>

### playwright

代码：

* 文件：[playwrithDemoBaiduSearch.py](https://book.crifan.com/books/free_hand_test_automation/website/assets/file/baiduSearchAutomation/playwright/playwrithDemoBaiduSearch.py)
* 贴出来是
  ```python
  # Function: Playwright demo baidu search
  # Author: Crifan Li
  # Update: 20210331

  from playwright.sync_api import sync_playwright

  # here use sync mode
  with sync_playwright() as p:
      chromiumBrowserType = p.chromium
      print("chromiumBrowserType=%s" % chromiumBrowserType)
      browser = chromiumBrowserType.launch(headless=False)
      # chromiumBrowserType=<BrowserType name=chromium executable_path=/Users/limao/Library/Caches/ms-playwright/chromium-857950/chrome-mac/Chromium.app/Contents/MacOS/Chromium>
      print("browser=%s" % browser)
      # browser=<Browser type=<BrowserType name=chromium executable_path=/Users/limao/Library/Caches/ms-playwright/chromium-857950/chrome-mac/Chromium.app/Contents/MacOS/Chromium> version=90.0.4430.0>
      page = browser.new_page()
      print("page=%s" % page)
      # page=<Page url='about:blank'>

      ################################################################################
      # Open url
      ################################################################################
      page.goto('http://www.baidu.com')
      print("page=%s" % page)
      # page=<Page url='https://www.baidu.com/'>

      ################################################################################
      # Input text
      ################################################################################
      searchStr = "crifan"
      SearchInputSelector = "input#kw.s_ipt"

      # page.click(SearchInputSelector)
      page.fill(SearchInputSelector, searchStr)

      ################################################################################
      # Trigger search
      ################################################################################
      EnterKey = "Enter"

      # Method 1: press Enter key
      # page.keyboard.press(EnterKey)

      # Method 2: locate element then click
      SearchButtonSelector = "input#su"
      page.press(SearchButtonSelector, EnterKey)

      # wait -> makesure element visible
      SearchFoundWordsSelector = 'span.nums_text'
      # SearchFoundWordsXpath = "//span[@class='nums_text']"
      page.wait_for_selector(SearchFoundWordsSelector, state="visible")

      ################################################################################
      # Extract content
      ################################################################################
      resultASelector = "h3[class^='t'] a"
      searchResultAList = page.query_selector_all(resultASelector)
      print("searchResultAList=%s" % searchResultAList)
      # searchResultAList=[<JSHandle preview=JSHandle@<a target="_blank" href="http://www.baidu.com/link?…>在路上on the way - 走别人没走过的路,让别人有路可走</a>>, <JSHandle preview=JSHandle@node>, 。。。, <JSHandle preview=JSHandle@node>]
      searchResultANum = len(searchResultAList)
      print("Found %s search result:" % searchResultANum)
      for curIdx, aElem in enumerate(searchResultAList):
          curNum = curIdx + 1
          print("%s [%d] %s" % ("-"*20, curNum, "-"*20))
          title = aElem.text_content()
          print("title=%s" % title)
          # title=在路上on the way - 走别人没走过的路,让别人有路可走
          baiduLinkUrl = aElem.get_attribute("href")
          print("baiduLinkUrl=%s" % baiduLinkUrl)
          # baiduLinkUrl=http://www.baidu.com/link?url=fB3F0xZmwig9r2M_1pK4BJG00xFPLjJ85X39GheP_fzEA_zJIjX-IleEH_ZL8pfo

      # do sceeenshot
      screenshotFilename = 'baidu_search_%s_result.png' % searchStr
      page.screenshot(path=screenshotFilename)

      browser.close()
  ```

效果：

<video id="playwrithDemoBaiduSearch" controls="controls" preload="none" width="800" height="600">
  <source src="../../../assets/file/baiduSearchAutomation/playwright/playwrithDemoBaiduSearch.mp4" type="video/mp4">
</video>

## 移动端

### Android端

#### uiautomator2

代码：

* 文件：[uiautomator2DemoBaiduSearch.py](https://book.crifan.com/books/free_hand_test_automation/website/assets/file/baiduSearchAutomation/uiautomator2/uiautomator2DemoBaiduSearch.py)
* 贴出来是

```python
# Function: uiautomator2 demo baidu search
# Author: Crifan Li
# Update: 20210417

# import time
import uiautomator2 as u2

d = u2.connect() # connect to device
print("d.info=%s" % d.info)
# d.info={'currentPackageName': 'com.android.browser', 'displayHeight': 2201, 'displayRotation': 0, 'displaySizeDpX': 393, 'displaySizeDpY': 873, 'displayWidth': 1080, 'productName': 'atom', 'screenOn': True, 'sdkInt': 29, 'naturalOrientation': True}

# for debug: get current app info
# curApp = d.app_current()
# print("curApp=%s" % curApp)

# for debug: get running app list
# activeAppList = d.app_list_running()
# print("activeAppList=%s" % activeAppList)

################################################################################
# Launch browser
################################################################################
Browser_XiaomiBuiltin = "com.android.browser"
browserPackage = Browser_XiaomiBuiltin
# d.app_start(browserPackage)
d.app_start(browserPackage, stop=True)

# wait util browser launch complete -> appear 我的 tab
# MustShowTabName = "主页"
MustShowTabName = "我的"
# d(text=MustShowTabName).exists(timeout=10)
d(text=MustShowTabName, packageName=browserPackage).exists(timeout=10)
print("Browser homepage loaded")

################################################################################
# Open baidu homepage
################################################################################
SearchInputId = "com.android.browser:id/b4w"

# # open new window
# windowUiObj = d(resourceId="com.android.browser:id/dm")
# windowUiObj.click()

# # click add to new window
# addNewWindowUiObj = d(resourceId="com.android.browser:id/akr")
# addNewWindowUiObj.click()


# for debug
# curPageXml = d.dump_hierarchy(compressed=False, pretty=False)
# print("curPageXml=%s" % curPageXml)

# find input box inside address bar

# # Method 1: use driver pass in parameter
# inputUiObj = d(resourceId=SearchInputId, className="android.widget.TextView")
# # inputUiObj = d(resourceId=SearchInputId)
# print("type(inputUiObj)=%s" % type(inputUiObj)) # type(inputUiObj)=<class 'uiautomator2.session.UiObject'>
# print("inputUiObj=%s" % inputUiObj) # inputUiObj=<uiautomator2.session.UiObject object at 0x10a0bea00>
# inputUiObjectInfo = inputUiObj.info
# print("type(inputUiObjectInfo)=%s" % type(inputUiObjectInfo)) # type(inputUiObjectInfo)=<class 'dict'>
# print("inputUiObjectInfo=%s" % inputUiObjectInfo) # inputUiObjectInfo={'bounds': {'bottom': 172, 'left': 160, 'right': 797, 'top': 107}, 'childCount': 0, 'className': 'android.widget.TextView', 'contentDescription': '搜索框', 'packageName': 'com.android.browser', 'resourceName': 'com.android.browser:id/b4h', 'text': '', 'visibleBounds': {'bottom': 172, 'left': 160, 'right': 797, 'top': 107}, 'checkable': False, 'checked': False, 'clickable': True, 'enabled': True, 'focusable': False, 'focused': False, 'longClickable': False, 'scrollable': False, 'selected': False}
# isFoundInput = inputUiObj.exists # True

# # Method 2: use xpath
# inputXpathSelector = d.xpath("//android.widget.TextView[@resource-id=SearchInputId]")
# # inputXpathSelector = d.xpath("//*[@resource-id=SearchInputId]")
# print("type(inputXpathSelector)=%s" % type(inputXpathSelector)) # type(inputXpathSelector)=<class 'uiautomator2.xpath.XPathSelector'>
# inputXpathElem = inputXpathSelector.get()
# print("type(inputXpathElem)=%s" % type(inputXpathElem)) # type(inputXpathElem)=<class 'uiautomator2.xpath.XMLElement'>
# print("inputXpathElem=%s" % inputXpathElem) # inputXpathElem=<uiautomator2.xpath.XMLElement object at 0x108585d30>
# print("type(inputXpathElem.attrib)=%s" % type(inputXpathElem.attrib)) # type(inputXpathElem.attrib)=<class 'lxml.etree._Attrib'>
# print("inputXpathElem.attrib=%s" % inputXpathElem.attrib) # inputXpathElem.attrib={'index': '1', 'text': '', 'resource-id': 'com.android.browser:id/b4h', 'package': 'com.android.browser', 'content-desc': '搜索框', 'checkable': 'false', 'checked': 'false', 'clickable': 'true', 'enabled': 'true', 'focusable': 'false', 'focused': 'false', 'scrollable': 'false', 'long-clickable': 'false', 'password': 'false', 'selected': 'false', 'visible-to-user': 'true', 'bounds': '[160,107][797,172]'}
# isFoundInput = inputXpathSelector.exists # True


# trigger into input page

# Method 1
inputUiObj = d(resourceId=SearchInputId, className="android.widget.TextView")
inputUiObj.click()
print("Clicked search box")

# # Method 2
# inputXpathSelector = d.xpath("//android.widget.TextView[@resource-id=%s]" % SearchInputId)
# inputXpathSelector.click()

# input baidu homr url
BaiduHomeUrl = "https://www.baidu.com/"
AddressInputId = "com.android.browser:id/bqi"
searchUiObj = d(resourceId=AddressInputId, className="android.widget.EditText")
searchUiObj.set_text(BaiduHomeUrl)
print("Inputed baidu homepage url: %s" % BaiduHomeUrl)

# trigger jump to baidu home
EnterKey = "enter"
d.press(EnterKey)
print("Emulated press key %s" % EnterKey)

# wait util baidu home loaded
# d(text="百度一下", resourceId="com.android.browser:id/bq3").exists(timeout=10)
d(text="百度一下,你就知道", className="android.view.View").exists(timeout=10)
print("Baidu home loaded")

################################################################################
# Input text
################################################################################
searchStr = "crifan"

baiduSearchKeywordUiObj = d(resourceId="index-kw", className="android.widget.EditText")
baiduSearchKeywordUiObj.set_text(searchStr)
print("Inputed baidu search text %s" % searchStr)

################################################################################
# Trigger baidu search
################################################################################

# # Method 1: press key
# TriggerSearchKey = "enter" # work
# # TriggerSearchKey = "search" # not work
# # TriggerSearchKey = "go" # not work
# # TriggerSearchKey = "done" # not work
# d.press(TriggerSearchKey)
# print("Emulated press key %s" % TriggerSearchKey)

# Method 2: find 百度一下 button then click
baiduSearchButtonUiObj = d(resourceId="index-bn", className="android.widget.Button")
baiduSearchButtonUiObj.click()
print("Clicked baidu search button")

################################################################################
# Extract search result content
################################################################################

# Special: for fixbug of get page xml is not latest, so using following code to refresh to get latest page source xml
d.service("uiautomator").stop()
d.service("uiautomator").start()
# time.sleep(1)

# for debug
# get page source xml
# curPageXml = d.dump_hierarchy(compressed=False, pretty=False)
# print("curPageXml=%s" % curPageXml)
# with open("baidu_search_%s_result_pageSource_reloaded.xml" % searchStr, "w") as fp:
#     fp.write(curPageXml)

d(resourceId="results").exists(timeout=10)

# Note: following syntax can NOT find elements
# resultsSelector = d.xpath("//*[@resource-id='results']")
# titleButtonSelectorList = resultsSelector.xpath("//android.widget.Button[@clickable='true']").all()
# titleButtonSelectorList = resultsSelector.xpath(".//android.widget.Button[@clickable='true']").all()

# Xpath chain search can find elements
titleButtonElementList = d.xpath("//*[@resource-id='results']//android.widget.Button[@clickable='true']").all()
titleButtonNum = len(titleButtonElementList)
print("Found %s search result title" % titleButtonNum)

# descriptionElementList = d.xpath("//*[@resource-id='results']/android.view.View[1]/android.view.View[1]/android.view.View[2]/android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]").all()
descriptionElementList = d.xpath("//*[@resource-id='results']/android.view.View/android.view.View[1]/android.view.View[2]/android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]").all()
descriptionNum = len(descriptionElementList)
print("Found %s description" % descriptionNum)

# # sourceWebsiteElementList = d.xpath('//*[@resource-id="results"]/android.view.View/android.view.View[1]/android.view.View[2]/android.view.View[1]').all()
# sourceWebsiteElementList = d.xpath('//*[@resource-id="results"]/android.view.View/android.view.View[1]/android.view.View[2]/android.view.View[2]').all()
# sourceWebsiteNum = len(sourceWebsiteElementList)
# print("Found %s source website" % sourceWebsiteNum)

for curIdx, eachTitleButtonElement in enumerate(titleButtonElementList):
    curNum = curIdx + 1
    print("%s  [%d/%d] %s" % ("-"*20, curNum, titleButtonNum, "-"*20))
    # eachTitleButtonElemAttrib = eachTitleButtonElement.attrib
    # print("title attrib: %s" % eachTitleButtonElemAttrib)
    # curTitle = eachTitleButtonElemAttrib["text"]
    curTitle = eachTitleButtonElement.text
    print("title=%s" % curTitle)

    curDescriptionElem = descriptionElementList[curIdx]
    curDescription = curDescriptionElem.text
    print("description=%s" % curDescription)

    # curSourceWebsiteElem = sourceWebsiteElementList[curIdx]
    # curSourceWebsite = curSourceWebsiteElem.text
    # print("curSourceWebsite=%s" % curSourceWebsite)

print("Demo baidu search complete")
```

效果：

<video id="uiautomator2DemoBaiduSearch" controls="controls" preload="none" width="800" height="600">
  <source src="../../../assets/file/baiduSearchAutomation/uiautomator2/uiautomator2DemoBaiduSearch.mp4" type="video/mp4">
</video>

### iOS端

#### facebook-wda

代码：

* 文件：[facebookWdaDemoBaiduSearch.py](https://book.crifan.com/books/free_hand_test_automation/website/assets/file/baiduSearchAutomation/facebook-wda/facebookWdaDemoBaiduSearch.py)
* 贴出来是
  ```python
  # Function: facebook-wda demo baidu search
  # Author: Crifan Li
  # Update: 20210410

  import wda

  # for debug
  # Enable debug will see http Request and Response
  # wda.DEBUG = True

  c = wda.Client('http://localhost:8100')

  curStatus = c.status()
  print("curStatus=%s" % curStatus)
  ```

注：由于苹果开发者过期，导致：未完待续

详见：

【未解决】Mac中用facebook-wda自动操作安卓手机浏览器实现百度搜索
