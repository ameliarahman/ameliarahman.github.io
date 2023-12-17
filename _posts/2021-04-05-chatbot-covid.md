---
layout: post
title: "Chat Bot for Daily Cases of Covid19"
description: "A telegram chat bot to provide daily cases of Covid19 information around the world, especially in Indonesia"
date: 2021-04-05 09:39:18
comments: true
keywords: "bot, covid19, chat"
category: [Tech]
tags:
- bot
---

In March 2019, I discovered a public API providing information about daily Covid-19 cases worldwide. The source API is <a href="https://covid19.mathdro.id/api" target="_top">https://covid19.mathdro.id/api</a>. Recognizing the potential usefulness of an application offering daily updates on Covid-19 cases from this API, I decided to create one. The application is a telegram chat bot that allows user to type the name of a country or a province in Indonesia. The bot then provides information on the total confirmed, recovered, and death cases of Covid-19 in response. The application is built using NodeJS and API Webhook from the Telegram app. You can try the chat bot <a href="https://t.me/test_covid19news_bot" target="_top">here</a>. 

The result example:

![](../assets/img/ss-1-chatbot.png)
