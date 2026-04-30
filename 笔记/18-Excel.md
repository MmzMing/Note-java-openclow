- --
uid: 2013042913
title: Excel处理
tags:
  - Excel
  - EasyExcel
category: 中间件
type: 笔记
expired: 2099-01-01
publish: true
- --

# EasyExcel

## 概念
阿里巴巴开源的 Excel 处理框架。

## 依赖
<dependency>
    <groupId>com.aliy</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.3.2</version>
</dependency>

## 示例
EasyExcel.read(file, DemoData.class, listener).sheet().doRead()
