---
title: "Swiping on a touch screen"
date: 2019-06-12T18:00:00+01:00
publishdate: 2019-06-12T18:00:00+01:00
tags: ["testing"]
comments: false
---
We had already a few months a problem on the machines touchscreen that our interface behaved differently with a finger swipe as with a mouse drag and drop. The problem was that swiping from left to right was navigating to the previous page. And swiping the other way around was taking us to the next page.

Because a customer does not need to know that our interface is in fact a html page, this is unwanted behavior from user testings point of view. In the options of Chrome itself I could not find any options that could explain this behavior.

But there is a solution. Just start chrome or chromium with a command line switch.

{{< highlight bash >}}
  --overscroll-history-navigation
{{< /highlight >}}

Did you know that chrome has a lot of command line parameters? Look at [Peter Beverloo’s page](http://peter.sh/experiments/chromium-command-line-switches/) for an overview. 

You see, as a tester you also need to think how we can solve bugs, because not all bugs are code related.
