---
layout: post
title: Google Extension for Converting Odds 
---


The same betting odds are often displayed in disparate ways, depending on the context. In America, normally in terms of a Moneyline, where +200 is shorthand for "if you bet $100, you can win an additional $200". In other contexts, we generally see the tradition "X/Y" fractional odds. Both of these have clear mathematical meanings, but for inexperienced bettors, they tend to be poor conveyors of intuitive meaning. We can quickly estimate what "+350" implies, but for most people, it takes a mental calculation to do so. The most useful intuitive information about betting odds comes from the "Implied Probability", which is the probability of the event necessary for you to "break even" on the bet. Betting sites don't particularly like Implied Probability because it makes the cut they take more obvious (when mutually exclusive and complete events have probabilities that sum up to greater than one), and more intuitively accurate information for bettors can make them realize how daunting it is to actually keep up a positive expected value.

As someone without the perfect intuition needed to convert these odds in my head, I got tired of glancing at a table of odds and trying to work out their precise value one by one. I thought it would be nice to have a very simple Google Chrome extension that could do it for me. The initial functionality is simple: click on the icon, and it converts any odds it can find on the page into Implied Probability. Click on it again, and it converts back. The challenge is that I have zero knowledge of JavaScript. Luckily, this task was simple enough that it could be cobbled together in the style of Frankenstein's monster, with some slight modifications.

## Click Icon, Replace Text

The first feature necessary is simple an extension that runs a script when the icon is clicked. We find a [Stack Overflow question](https://stackoverflow.com/questions/7168362/run-script-each-time-chrome-extension-icon-clicked) (which will be the source of the code for most of the extension) with this very task. Every Chrome Extension begins with a manifest.json file, which gives an overview of the permissions and structure of the extension. I grab their initial suggested manifest.json, and make a number of modifications which will become necessary later. The "contextmenus" under permissions is only necessary to activate context menus within Chrome, which seems like a useful feature but is not yet crucial to the Extension's functionality. I also made a set of three simple icons in GIMP, so that it looks natural in the browser. The key parts here are the two scripts under "background". "rightclick.js" provides instructions for how to create a context menu upon right click, which we ignore for now (I played with it, but it's still a work in progress). However, "background.js" is the script that tells our  

~~~~

{
  "manifest_version": 2,

  "name": "Convert Odds into Implied Probability",
  "description": "Click this icon to convert moneyline odds into implied probability, and vice versa.",
  "version": "1.0",
  "background" : {
    "scripts" : ["background.js", "rightclick.js"]
  },
  "icons": { "16": "icon16.png",
             "48": "icon48.png",
             "128": "icon128.png" },

  "browser_action": {
    "default_icon": "icon48.png",
    "default_title": "Convert Odds"
  },
  "permissions": [
    "activeTab",
    "contextMenus"
  ]
}
~~~~

The key parts here are the two scripts under "background": "rightclick.js" provides instructions for how to create a context menu upon right click, which we ignore for now (I played with it, but it's still a work in progress), and "background.js" is a script that calls our text replace script when the Extension icon is clicked. 

~~~~
chrome.browserAction.onClicked.addListener(function(tab) {
   chrome.tabs.executeScript(null, {file: "replaceScript.js"});
});
~~~~

"replaceScript.js" is the script that  runs a text replacement that converts anything that "looks like" odds into implied probabilities (and you can view the final script [here](https://github.com/dylanpotteroconnell/oddsextension/blob/master/replaceScript.js)). Being totally unfamiliar with JavaScript, I grab code from an Extension that performs a [simple text replace for each webpage](https://9to5google.com/2015/06/14/how-to-make-a-chrome-extensions/). The base structure is as follows. 

~~~~
var elements = document.getElementsByTagName('*');
for (var i = 0; i < elements.length; i++) {
    var element = elements[i];
    for (var j = 0; j < element.childNodes.length; j++) {
        var node = element.childNodes[j];
        if (node.nodeType === 3) {
            var pattern = /abc/gi;
            var text = node.nodeValue;
            var replacedText = text.replace(pattern, 'def');
            if (replacedText !== text) {
                element.replaceChild(document.createTextNode(replacedText), node);
            }
        }
    }
}
~~~~

## Detect Patterns, Calculate Conversions

Next, we can use Regular Expressions to only replace certain structures of characters, rather than just one static string. To start, we want to try and identify sequences of exactly a plus or minus sign, followed by at least one digit (e.g. "-220"). The parentheses "capture" the characters inside, as we need to not just find these patterns, but extract the numbers. We want to find the end of the digits, but we want to leave the following character in the page's HTML, so we "grab" the first nondigit character (the [^0-9), but only if it exists (the "?") using "([^0-9]?)", so that we can place that character after our converted expression. 

~~~
var pattern = /([+|-])([0-9]+)([^0-9]?)/gi;
~~~~


Now, the challenge is that we want to perform a small calculation using the number provided. That means we need to be able to feed captured characters (preferably, the numbers and the +/- sign) as inputs into a function that determines the output of the replace function of text. I don't know of the optimal way to do this in JavaScript, but with a Google search we find a way to do just that [here](https://www.bennadel.com/blog/55-using-methods-in-javascript-replace-method.htm).

We can replace the previous string used for our pattern with a lambda function. Note that the link provided is inaccurate in its description of the inputs to the lambda function, and this is corrected to the comments. The inputs to the dummy function here are always "the full match, $1, $2, $3, ..." (where $X refers to the xth capture element), while in the linked tutorial they use "$1$" in the function input to refer to the full text. We can adapt their function to convert an American moneyline into its implied odds as follows.

~~~~

var replacedText = text.replace(pattern,
                                function(fm,$1,$2,$3)
				    {
				      if ($1=="+")
				      {
					return("|"+String(Math.round(100.0/(100.0+parseInt($2))*1000)/10.0)+"\%"+"|"+$3)
				      } else
				      {
					return("|"+String(Math.round(parseInt($2)/(100.0+parseInt($2))*1000)/10.0)
						 +"\%"+"|"+$3)
				      }
				    });
~~~~

It's worth breaking this into parts. The inputs to our function are the full text ('fm', which we do not use here, but I was not aware of how to suppress this input), and the three strings 'captured' from our regex matching (where $1 refers to the +/- sign, $2 is the number of the line, and $3 is the trailing character). We compute the simple formula to go from a moneyline bet to its implied odds. For a moneyline of +$X, we earn $X+$100 on a win. Thus, the odds (O) needed to break even are $100=0*($X+$100), so O=$100/($X+$100). Similarly, for a moneyline of -$X, we bet $X to win $100, and the implied odds are O=$X/($X+$100). The final return statement is simply cobbled together from some JavaScript documentation. ParseInt turns the '$2' input into an integer, we then multiply by 1000, and divide by 10 to get the result in the form of a percentage with a single decimal place.  

We repeat this process with two other conversions: 2. Fractional Odds (i.e. "3/1") to Implied Probability, and 3. "Undoing" our first conversion, converting implied probabilities back into moneyline odds. We do not include a separate "undo" conversion for Fractional Odds, because I wanted to keep this extension very minimal, and detecting the most accurate Fractional Odds to a rounded decimal percentage is a less straightforward conversion.

## Next...

This results in a simple but effective Chrome Extension that can save quite a bit of time. When on a page with odds in HTML text, you tap the button and can quickly see them replaced by their Implied Probabilities. The major downside is that more complex websites don't always show their odds in such simple HTML text, and learning to detect them for the major bookmakers would be a more substantial challenge. However, most people encounter odds referenced in articles or text discussions, where this Extension works quite cleanly.


