title: Greasemonkey脚本开发
tags:
  - Greasemonkey
  - javascript
date: 2014-09-26 21:48:00
categories:

---

## 简介

Greasemonkey，简称GM，中文俗称为“油猴子”，是Mozilla Firefox的一个附加组件。它让用户安装一些脚本使大部分HTML为主的网页于用户端直接改变得更方便易用。随着Greasemonkey脚本常驻于浏览器，每次随着目的网页打开而自动做修改，使得运行脚本的用户印象深刻地享受其固定便利性。
Greasemonkey可替网页加入些新功能、修正网页错误、组合来自不同网页的数据、或者数繁不及备载的其他功能。写的好的Greasemonkey脚本甚至可让其输出与被修改的页面集成得天衣无缝，像是原本网页里的一部分。

<!--more-->

## 实例：过滤新浪微博上那些烦人的信息

新浪微博上烦人的广告等信息非常多，甚为讨厌，作为练手，就拿他开刀了。代码很简单，不多说。

```javascript
// ==UserScript==
// @name          No Weibo Advertisements
// @namespace     http://cyy0523xc.github.io/
// @description   删除微博的广告等多余信息。基于Cao Weibo修改
// @include       http://www.weibo.com/*
// @include       http://weibo.com/*
// @copyright     2014, cyy0523xc@gmail.com
// ==/UserScript==

// return script name
function script_name()
{
    return "NoWeiboAd";
}

// return debug flag
function debug_flag()
{
    return true;
}

// function return the result of xpath query
function xpath(query)
{
    return document.evaluate(query, document, null, XPathResult.UNORDERED_NODE_SNAPSHOT_TYPE, null);
}

// common function: remove nodes
function remove_nodes(allNode)
{
    var remove_num = 0;
    // for each forward
    for (var i=0; i<allNode.snapshotLength; ++i)
    {
        var thisNode = allNode.snapshotItem(i);
        if (thisNode)
        {
            thisNode.parentNode.removeChild(thisNode);
            remove_num++;
        }
    }

    return remove_num;
}

function remove_xpath(node_path)
{
    var remove_num = remove_nodes(xpath(node_path));
    console.log('Remove xpath = ' + node_path + '    num = ' + remove_num);
}

// function
function remove_ad()
{
    // get '<div>' with attribute 'feedtype="ad"'
    remove_xpath('//div[@feedtype="ad"]');
    
    remove_xpath('//div[@ad-data]');
}

// 
function remove_other()
{
	var id_list = ["trustPagelet_checkin_lotteryv5", "trustPagelet_indexright_recom", "pl_rightmod_ads36", "trustPagelet_recom_memberv5", "pl_leftnav_app" ];
    for (var i in id_list) {
        remove_xpath('//div[@id="' + id_list[i] + '"]');
    }
}

// function
function remove_feed_spread()
{
    // get '<div>' with attribute 'node-type="feed_spread"'
    remove_xpath('//div[@node-type="feed_spread"]');
}

// main function
function main()
{
    if (debug_flag())
    {
        console.log('Starting ' + script_name());
    }
    // remove advertisements
    remove_ad();
    // remove feed spread
    remove_feed_spread();
    // remove other
    remove_other();

    return 0;
}

/////////////////////////////////////////////////////////////////////////////////

window.setTimeout(main, 1000);
```

代码见：https://github.com/cyy0523xc/code/raw/master/greasemonkey/no_weibo_ad.user.js
