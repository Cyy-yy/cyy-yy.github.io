---
title: Flink Table API 踩坑【Java】
date: 2024-01-27
categories: [大数据, Flink]
tags: [Flink]

author: <author_id>

math: true
mermaid: true
pin: true
---



- 引入依赖

  ```java
  <!--负责Table API和下层DataStream API的连接支持-->
  <!--此依赖已经包含flink-tabe-api-java-->
  <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-table-api-java-bridge</artifactId>
      <version>${flink.version}</version>
  </dependency>
  
   <!--在本地的集成开发环境里运行Table API和SQL的支持-->
  <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-table-planner-loader</artifactId>
      <version>${flink.version}</version>
  </dependency>
  
  <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-table-runtime</artifactId>
      <version>${flink.version}</version>
  </dependency>
  ```

  
