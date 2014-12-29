---
layout: post
title: "Using Swift to Immediately Update UICollectionView with API Result"
description: "Using Swift to Immediately Update UICollectionView with API Result"
tags: [swift, ios, uicollectionview]
---

I was puzzled a few days ago when the UICollectionView I was looking at didn't automatically get updated even after calling ```reloadData()```. After much googling, I found this post in [stackoverflow](http://stackoverflow.com/questions/14804359/uicollectionview-doesnt-update-immediately-when-calling-reloaddata-but-randoml) which explained what caused it.

It wasn't because my API was slow nor was it because of some Swift issue I wasn't aware of. It was because the task was added in a background queue. To fix this, you just need to make sure that the block you want to get updated immediately was running on the main thread.

This was resolved by wrapping ```reloadData()``` in a dispatch_async with the queue for the main thread passed as the argument. We can retrieve the main thread queue through [dispatch_get_main_queue](https://developer.apple.com/library/mac/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html#//apple_ref/doc/uid/TP40008079-CH2-SW11).

    dispatch_async(dispatch_get_main_queue(), {
        self.collectionView.reloadData()
    })


After adding that, my UICollectionView gets updated immediately after receiving the result from the API.