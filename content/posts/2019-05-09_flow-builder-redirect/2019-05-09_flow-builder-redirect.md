+++
title = "Flow Builder Redirect"
date = "2019-05-09T12:23:48+02:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["salesforce", "flow"]
keywords = ["salesforce", "flow"]
description = ""
summary="I'm not using Flow Builder (aka Visual Flow) very often, but when I do... I'm baffled, positively ánd (unfortunately) also negatively 🙄 #diamondintherough"
slug="flow-builder-redirect"
+++

<!-- ## Flow Builder Redirect after Interview Completion -->
I'm not using Flow Builder (aka Visual Flow) very often, but when I do... I'm baffled, positively ánd (unfortunately) also negatively 🙄 #diamondintherough

Anyway, to keep it short, I had created a flow in which a Contract was being created. The only requirement I still had, was to redirect to the newly created Contract, immediately after the flow would finish! Simple right? Hmm, yeah, not really.

Apparently, there seems to be no standard way in Flow Builder to do such a redirect. Code Vs Clicks: 1 - 0 !! (if you do know a standard way, please let me know 😉)

## Solution
### Lightning Component

Create a Lightning Component which uses the "navigateToSObject"- and/or "navigateToURL"-event.
I found this little code snippet provided by Salesforce itself:

```javascript
({ 
    invoke : function(component, event, helper) { 
        // Get the record ID attribute 
        var record = component.get("v.recordId"); 

        // Get the Lightning event that opens a record in a new tab 
        var redirect = $A.get("e.force:navigateToSObject"); 

        // Pass the record ID to the event 
        redirect.setParams({ "recordId": record }); 
        // Open the record 
        redirect.fire(); 
    }
})
```

## Failure #1

Although the developer docs mention that the "navigateToSObject" is thrown at the end of the interview, there is no redirect taking place. Result: the interview starts over. Not exactly what I wanted.

Looking closer into this, it seemed the Lightning component was being initialized correctly, except for the Lightning Event to be executed. Therefor, no redirect happens. 
Could it be that it has something to do with?

* https://salesforce.stackexchange.com/questions/193935/navigating-to-record-detail-page-after-edit-does-not-reflect-new-data
* https://success.salesforce.com/issues_view?id=a1p3A0000001C8QQAU

When testing though, it appeared to me that Flows started in Lightning (through a Button) are not being executed in a "real" Lightning context. The layout seems to be the standard classic layout with the "lightningStylesheets="true"" applied to the page.

So in theory, it seems logical that the "navigateToSObject"-event does not fire correctly. But then, why is the Lightning Component instantiated correctly? Maybe because the flow container includes the LightningOut.js by default (https://www.jitendrazaa.com/blog/salesforce/use-lightning-component-in-visualforce-page/) but that doesn't mean the lightning events will work. Might be a cause.

## Workaround #1

Use pure javascript by setting the "window.location" inside the Lightning Component.

Finally, I came up with:
```javascript
({ 
    // Get the "url" attribute
    var url = component.get("v.url");

    if ((typeof sforce != 'undefined') && sforce && (!!sforce.one)) {
        sforce.one.navigateToURL(url);
    } else {
        window.location.assign(url);
    }
})
```

As you can see, I stopped using the "recordId" and replaced it with "url" (also changed the attribute inside the component of course ). This makes it clear to the end user or administrator that anything can be entered as a URL. Eg:

* If you want your flow to be redirected to https://www.salesforce.com, just provide that same value: "https://www.salesforce.com".
* If, after the flow finishes, you want it to be redirected to the created record, you can provide: "/{!contractId}", where {!contractId} is a variable inside your flow

![local-action-navigatetourl](/posts/2019-05-09_flow-builder-redirect/local-action-navigatetourl.png)
