---
layout: post
title: Google Extension for Converting Odds 
---



We want to be able to activate the extension when someone clicks on the icon. 

https://stackoverflow.com/questions/7168362/run-script-each-time-chrome-extension-icon-clicked

We use this manifest.json

~~~~
{
  "manifest_version": 2,

  "name": "Getting started example",
  "description": "This extension allows the user to change the background color of the current page.",
  "version": "1.0",
  "background" : {
    "scripts" : ["background.js"]
  },

  "browser_action": {
    "default_icon": "icon.png",
    "default_title": "Convert Odds"
  },
  "permissions": [
    "activeTab"
  ]
}

~~~~

And this background.js, which calls the testScript.js script
~~~~
chrome.browserAction.onClicked.addListener(function(tab) {
   chrome.tabs.executeScript(null, {file: "testScript.js"});
});
~~~~

Finally, calling testScript.js, only slightly modified from the original.

~~~~
var elements = document.getElementsByTagName('*');
// https://stackoverflow.com/questions/7168362/run-script-each-time-chrome-extension-icon-clicked
for (var i = 0; i < elements.length; i++) {
    var element = elements[i];

    for (var j = 0; j < element.childNodes.length; j++) {
        var node = element.childNodes[j];

        if (node.nodeType === 3) {
            var pattern = /Macron/gi;
            var text = node.nodeValue;
            var replacedText = text.replace(pattern, 'Macaroon');

            if (replacedText !== text) {
                element.replaceChild(document.createTextNode(replacedText), node);
            }
        }
    }
}
~~~~

Now, this is a simple tool for replacing all instances of a word displayed in the HTML with another word. This happens upon us clicking the icon on the toolbar. This is handy because our extension may not be terribly clever, so we only want it trying to convert odds on a page that we know might have some.

Next, we can use Regular Expressions to only replace certain structures of characters, rather than just one static string. To start, we want to try and identify sequences of exactly a plus or minus sign, followed by exactly three digits (e.g. "-220"). This is a useful starting point because it's a very common pattern writing odds. Instead of matching the string "Macron", we instead match the regular expression written below.

~~~~
var pattern = /([+|-])[0-9][0-9][0-9]([^0-9])/gi;
var text = node.nodeValue;
var replacedText = text.replace(pattern, '$1123$2');
~~~~

We can break down what exactly we are grabbing here. In the middle, [0-9] denotes a digit from 0 to 9, and we are matching for three of them in a row. To ensure that we do not match any 4 digit numbers, this is followed by [^0-9], which instead matches any character that is *not* a digit from 0 to 9. Finally, [+|-] matches either a plus or minus. Now, the parentheses are used to "capture" certain characters. For testing purposes, we want to replace the three digit string with "123" instead, but we don't want to get rid of the beginning and ending elements. Thus, we surround them with parentheses in the capture regular expression, and then we can use those captured strings in our resulting replaced text using "$1" and "$2". Thus, "$1123$2" reads "the first captured string (which should be a + or a -), then the digits 123, and then the second captured string". And sure enough, when we press the button, we turn any instance of "+XYZ" into "+123". 

Now, the challenge is that we want to perform a small calculation using the number provided. That means we need to be able to feed captured characters (preferably, the numbers and the +/- sign) as inputs into a function that determines the output of the replace function of text. I don't know of the optimal way to do this in JavaScript, but with a Google search we find a way to do just that.

https://www.bennadel.com/blog/55-using-methods-in-javascript-replace-method.htm

We can replace the previous string used for our pattern with a lambda function. Note that the link provided is inaccurate in its description of the inputs to the lambda function, and this is corrected to the comments. The inputs to the dummy function here are always "the full match, $1, $2, $3, ..." (where $X refers to the xth capture element), while in the linked tutorial they use "$1$" in the function input to refer to the full text. We can adapt their function to convert an American moneyline into its implied odds as follows.
~~~~
var replacedText = text.replace(pattern,
                                function(fm,$1,$2,$3)
                                {
                                  if ($1=="+")
                                  {
                                    return(String(Math.round(100.0/(100.0+parseInt($2))*1000)/10.0)+"\%"+$3)
                                  } else
                                  {
                                    return(String(Math.round(parseInt($2)/(100.0+parseInt($2))*1000)/10.0)+"\%"+$3)
                                  }
                                });
~~~~

It's worth breaking this into parts. The inputs to our function are the full text ('fm', which we do not use here, but I was not aware of how to suppress this input), and the three strings 'captured' from our regex matching (where $1 refers to the +/- sign, $2 is the number of the line, and $3 is the trailing character). We compute the simple formula to go from a moneyline bet to its implied odds. For a moneyline of +$X, we earn $X+$100 on a win. Thus, the odds (O) needed to break even are $100=0*($X+$100), so O=$100/($X+$100). Similarly, for a moneyline of -$X, we bet $X to win $100, and the implied odds are O=$X/($X+$100). The final return statement is simply cobbled together from some JavaScript documentation. ParseInt turns the '$2' input into an integer, we then multiply by 1000, and divide by 10 to get the result in the form of a percentage with a single decimal place.  

This ends up working quite well, and we now have a simple button that we can press to replace any moneyline with its implied odds. However, the format of the program is a strict "replacement", which is somewhat clunky. Most often, we want to be able to check the implied odds, while still keeping the moneyline in mind. Thus, it would be great if we could simply have the implied odds pop up next to the moneyline, rather than totally replace it. The other functionality I want to add is to calculate the vig taken by the sportsbook when we have a complete state space of mutually exclusive events. That is, often we see a series of odds next to each other that are mutually exclusive and represent all possible outcomes (such as win, draw, and loss in a game of international football). The implied odds of these events will not add up to 1, because the sportsbook needs to take their cut (usually, they will add up to about 1.05). Thus, it would be nice to be able to quickly calculate 1. what this cut actually is, and 2. what the resulting odds are once normalized to add up to 1. This is less important when considering whether or not you should place the bet, but it's useful for telling what the market's best guess of the actual probabilities of these events are. 

https://markb.co.uk/building-a-simple-google-chrome-extension.html


