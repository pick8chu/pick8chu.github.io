---
title: session cookie
last_updated: Dec 11, 2025
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: session-cookie.html

---

Session Cookie only live in a Browser associated with the issued BE. FE's origin doesn't matter at all. Once browser get response from BE with the session cookie, it store in a global cookie jar. And whenever the browser fires the request to that BE, it add the session cookie. 

So for example, 
FE1 -> browser -> BE set session cookie -> browser (save cookie) -> Redirect to FE2 with response

So under application tab, session cookie will be saved under the BE's domain, not FE's domain. But, with localhosts with different ports, as cookie session is saved as domain, it will saved in any localhost.