---
layout: post
title: Chrome Extension, HTML Page 
---

## Opening a New Page

Our simple extension from [last time](https://dylanpotteroconnell.github.io/bettingextension/) works quite well to replace text on a page, but whenever text isn't displayed in a nice format (which is true for many mdoern websites), it won't do much good. The hope is to add functionality to it so that the user can easily and manually input the odds that they need. My vision was to let the user right click, and open up a page that lets them impute the odds to convert in a more detailed format.

As before, I don't know enough about Javascript to start from scratch, so I borrow and adapt from some others online. [This StackOverflow answer](https://stackoverflow.com/questions/4376167/text-selection-and-display-in-context-menu-chrome-extension) provides an example of creating a context menu upon a user right click that is the right structure. Importantly, this also provides a framework for storing any selected text that the user right clicks. The end goal is to let them auto populate our odds converter page, so we leave in that functionality.

```javascript
\\ rightClick.js
var selection_callbacks = [];
function getSelection(callback) {
 selection_callbacks.push(callback);
    chrome.tabs.executeScript(null, { file:"selection.js" });
  };
  chrome.extension.onRequest.addListener(function (request) {
    var callback = selection_callbacks.shift();
    callback(request);
  });

function openConverter(selectedText) {
  var serviceCall = 'converter.html'
  chrome.tabs.create({ url: serviceCall });
}
var tx = getSelection();
var title = "Convert: '" + tx + "' title";
var id = chrome.contextMenus.create({
    title: "Convert Odds?",
    contexts:["selection"],
    onclick: function(info, tab) {
        openConverter(info.selectionText);
    }
});
console.log("selection item:" + id);

```

```javascript
\\ selection.js
chrome.extension.sendResponse(convert(window.getSelection().toString()));
```


This is pretty rote. 
