Setting Up the Browser
=======================

Protractor works with [Selenium WebDriver](http://docs.seleniumhq.org/docs/03_webdriver.jsp), a browser automation framework. Selenium WebDriver supports several browser implementations or [drivers](http://docs.seleniumhq.org/docs/03_webdriver.jsp#selenium-webdriver-s-drivers) which are discussed below.

Browser Support
---------------
Protractor supports the two latest major versions of Chrome, Firefox, Safari, and IE.

Please see [Browser Support](/docs/browser-support.md) for a full list of
supported browsers and known issues.


Configuring Browsers
--------------------

In your Protractor config file (see [config.ts](/lib/config.ts)), all browser setup is done within the `capabilities` object. This object is passed directly to the WebDriver builder ([builder.js](https://code.google.com/p/selenium/source/browse/javascript/webdriver/builder.js)). 


See [DesiredCapabilities](https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities) for full information on which properties are available.


Using Mobile Browsers
---------------------

Please see the [Mobile Setup](/docs/mobile-setup.md) documentation for information on mobile browsers.


Using Browsers Other Than Chrome
--------------------------------

To use a browser other than Chrome, simply set a different browser name in the capabilities object.

```javascript
capabilities: {
  'browserName': 'firefox'
}
```

You may need to install a separate binary to run another browser, such as IE or Android. For more information, see [SeleniumHQ Downloads](http://docs.seleniumhq.org/download/).


Adding Chrome-Specific Options
------------------------------

Chrome options are nested in the `chromeOptions` object. A full list of options is at the [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/capabilities) site. For example, to show an FPS counter in the upper right, your configuration would look like this:

```javascript
capabilities: {
  'browserName': 'chrome',
  'chromeOptions': {
    'args': ['show-fps-counter=true']
  }
},
```


Testing Against Multiple Browsers
---------------------------------

If you would like to test against multiple browsers, use the `multiCapabilities` configuration option.

```javascript
multiCapabilities: [{
  'browserName': 'firefox'
}, {
  'browserName': 'chrome'
}]
```

Protractor will run tests in parallel against each set of capabilities. Please note that if `multiCapabilities` is defined, the runner will ignore the `capabilities` configuration.


Using Multiple Browsers in the Same Test
----------------------------------------
If you are testing apps where two browsers need to interact with each other (e.g. chat systems), you can do that with protractor by dynamically creating browsers on the go in your test. Protractor exposes a function in the `browser` object to help you achieve this: `browser.forkNewDriverInstance(opt_useSameUrl, opt_copyMockModules)`. 
Calling this will return a new independent browser object. The first parameter in the function denotes whether you want the new browser to start with the same url as the browser you forked from. The second parameter denotes whether you want the new browser to copy the mock modules from the browser you forked from.

```javascript
browser.get('http://www.angularjs.org');
browser.addMockModule('moduleA', "angular.module('moduleA', []).value('version', '3');");

// To create a new browser.
var browser2 = browser.forkNewDriverInstance();

// To create a new browser with url as 'http://www.angularjs.org':
var browser3 = browser.forkNewDriverInstance(true);

// To create a new browser with mock modules injected:
var browser4 = browser.forkNewDriverInstance(false, true);

// To create a new browser with url as 'http://www.angularjs.org' and mock modules injected:
var browser4 = browser.forkNewDriverInstance(true, true);
```

Now you can interact with the browsers. However, note that the globals `element`, `$`, `$$` and `browser` are all associated with the original browser. In order to interact with the new browsers, you must specifically tell protractor to do so like the following:

```javascript
var element2 = browser2.element;
var $2 = browser2.$;
var $$2 = browser2.$$;
element2(by.model(...)).click();
$2('.css').click();
$$2('.css').click();
```

Protractor will ensure that commands will automatically run in sync. For example, in the following code, `element(by.model(...)).click()` will run before `browser2.$('.css').click()`:

```javascript
browser.get('http://www.angularjs.org');
browser2.get('http://localhost:1234');

browser.sleep(5000);
element(by.model(...)).click();
browser2.$('.css').click();
```


Setting up PhantomJS
--------------------
PhantomJS is [no longer officially supported](https://groups.google.com/forum/#!topic/phantomjs/9aI5d-LDuNE). Instead, we recommend either [running Chrome in Xvfb](http://www.tothenew.com/blog/protractor-with-jenkins-and-headless-chrome-xvfb-setup/) or using Chrome's [headless mode](https://developers.google.com/web/updates/2017/04/headless-chrome).


Using headless Chrome
---------------------
To start Chrome in headless mode, start Chrome with the `--headless` flag.

As of Chrome 58 you also need to set `--disable-gpu`, though this may change in future versions. 
Also, changing the window size during a test will not work in headless mode, but you can set it
on the commandline like this `--window-size=800,600`.

```javascript
capabilities: {
  browserName: 'chrome',

  chromeOptions: {
     args: [ "--headless", "--disable-gpu", "--window-size=800x600" ]
   }
}
```
