---
layout: post
title: "vcpkg编译libcurl注意事项"
categories: win32
tags: vcpkg libcurl
author: admin
---


- ### vcpkg\ports\curl\portfile.cmake 120行左右添加以下内容
 	- -DCURL_ZLIB = OFF				# 否则依赖zlib库
 	- -DENABLE_INET_PTON = OFF		# 否找INET_PTON功能不支持xp
 	- -DCURL_STATIC_CRT=ON		#这个好像不用



