---
layout: post
title: Chrome Extension HTML Page 
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

This is pretty rote, with small changes from the linked work (and final code [here](https://github.com/dylanpotteroconnell/oddsextension/blob/master/replaceScript.js)). Initially, I hoped to perform some calculations with the selected text, and then display the result in the context menu itself. After some trial and error, it seems that this is quite a bit more complicated than it would seem. While you can reference the text in the selection using "%s", the issue is that this is passed through our function as the literal string "%s", and not the selection itself, until it is displayed (at which point it is replaced by tthe selection). This means we can't easily perform a calculation with it. The underlying challenge is that Chrome wants to display the text on the context menu immediately, so we would need to manually program in a structure that lets it wait for us to convert the text. For now, we instead focusing on simply opening the useful HTML page "oddsconverter.html".

## Odds Converter HTML Page

We want an HTML page that neatly converts between each sort of odds. For a rough idea of the base template, we borrow [this](https://www.daniweb.com/programming/web-development/threads/468068/auto-calculate-two-textfields) guide. While their goal is quite unrelated, this form has the neat feature where it calculates its result after *each* keystroke. I want this extension to be light and nimble, so I like the idea of it constantly updating based on new input.

```html
<!doctype html>
<html>
<!-- https://www.daniweb.com/programming/web-development/threads/468068/auto-calculate-two-textfields -->
<head>
<script src="converter.js"></script>

</head>
<form name="convert" action="" id="convert" >
    <span id="update">Odds Converter</span>
    <p><input type="text" id="ml" name="ml" onkeyup="convML(this)"/> Money Line </p>
    <p><input type="text" id="frac" name="frac" onkeyup="convFrac(this)"/> Fractional Odds </p>
    <p><input type="text" id="ip" name="ip" onkeyup="convIP(this)"/> Implied Probability </p>
    <input type="hidden" id="total" name="total" value="0" />
</form>
</body>
</html>
``` 
The heavy lifting is of course done by the functions stored in the javascript file "converter.js".

```javascript
function convML(obj) {
  if(/^[\+|-] ?[0-9]+$/.test(obj.value)){
    var pattern = /([+|-]) ?([0-9]+)/;
    var newIP = obj.value.replace(pattern,
      function(fm,$1,$2)
      {
        if ($1=="+")
        {
          return(Math.round(100.0/(100.0+parseInt($2))*1000)/10.0)
        } else
        {
          return(Math.round(parseInt($2)/(100.0+parseInt($2))*1000)/10.0)
        }
      });
     var newFrac = String(Math.round(100/(newIP/100.0)-100)/100)+"/"+"1";
     document.getElementById('ip').value = newIP;
     document.getElementById('frac').value = newFrac;
  }
}

function convFrac(obj) {
  document.getElementById('ip').value = obj.value;
// Add decimals

  if(/^[0-9]+\.?[0-9]*\/[0-9]+\.?[0-9]*$/.test(obj.value)){
    var pattern = /([0-9]+\.?[0-9]*)\/([0-9]+\.?[0-9]*)/;
    var newIP  = obj.value.replace(pattern,
                                          function(fm,$1,$2)
                                          {
                                             return(Math.round(1000*parseFloat($2)/(parseFloat($2)+parseFloat($1)))/10.0)
                                          }
                                    );
     document.getElementById('ip').value = newIP;
     document.getElementById('ml').value = IPtoML(parseFloat(newIP)/100.0);
  }
}
  if(/^[0-9]+\.?[0-9]*$/.test(obj.value)){
    document.getElementById('ml').value = IPtoML(parseFloat(obj.value)/100.0);
  }
}

function IPtoML(IP) {
  if (IP > .5) {
    return("-" + String(Math.round(100.0*IP/(1-IP))))
  } else if (IP <= .5) {
    return("+"+String(Math.round(100.0/IP - 100)))
  }
}
```

Each of the conversion functions follows the same format: after each keystroke in the text box, check if the given input is of the correct form, and if it is, upate the other two text boxes with the converted odds. The functionality is very limited, but it fulfills our purposes for now. Converting moneyline odds and implied probability to fractional odds is a tougher challenge, so for now we simiply display them as decimals with a denominator of one (while there is no straightforward one to one mapping to fractional odds, it's possible to write an algorithm to make an educated guess as to what whole number odds are closest, but for now we keep it simple).

When we open up this HTML page, it works as desired, but when we load it into our extension, it opens up and... does nothing. None of the fields update themselves, no matter howm much you try. A quick Google search determines that we are barking right up the wrong tree. A few years ago, [Chrome banned all inline Javascript](https://developer.chrome.com/extensions/contentSecurityPolicy#JSExecution) from its extension pages. I have no doubt that they had good security related reasons to do so, but it means that our page as written is quite useless. However, it should be possible to rewrite each of these functions in Chrome's preferred format and arrive at the same functionality.

