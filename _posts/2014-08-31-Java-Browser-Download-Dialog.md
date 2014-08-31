---
layout: post
title: "Java: Download Dialog for the Browser"
description: "Showing the Download Dialog for Downloading File using Java"
---

If ever you've encountered this requirement where you need to make the download dialog from the browser show up when a user is downloading a file from your Java-backed website, the solution is this:

    response.setContentType("text/csv");
    response.setHeader("Content-Disposition", String.format("attachment; filename=\"sample_file.csv\""));
    response.getWriter().write("This is a test file.");
    response.flushBuffer();

You simply need to set the _Header_ and _Content Type_. I was handling a bug last week, wherein the download dialog was working intermittently. After looking at the code, it was because the _Content Type_ and _Header_ were set **AFTER** the response was written. Shifting the write response part after setting those two attributes fixed the issue.