---
layout:     post
title:      "Sap Data Service Implementation #1"
subtitle:   "ETL Tool"
date:       2017-04-26
author:     "Ding"
header-img: "img/aaron-burden-36113.jpg"
catalog: true
tags:
    - SAP
---

# Data Service 
再びETLツールのPJTに配属しましたので、色々製品のことを書こうと思っています。

![](/img/in-post/post-sap-data-service-1/19.3-4.png)
> [Introduction to SAP Data Services](http://saphanatutorial.com/ntroduction-to-sap-data-services/)
**Designer**: GUI編集ツール
> This is the front end GUI (Graphical User Interface) tool where developers can login and build the jobs in SAP Data Services to move the data from one system to other or with-in the system and define the logic for transformations. 
> To Open Data Service Designer go to Start Menu -\> All Programs -\> SAP Data Services (4.2 here) -\> Data Service Designer.

**Repository**:  DS自身や作成したジョブ等の情報を保存するDB
> Repository is a database that stores designer predefine objects and user defined objects (source and target metadata, transformation rules).Repository are of two types 
> > - Local Repository (Used by Designer and Job Server).
> > - Central Repository ( Used for object sharing and version control)


**Access Server**:  即実行用サーバー
> This server is used to execute the real-time jobs created by developers in the repositories.
**Job Server**: ジョブサーバー
> This is one of the main server component in data services and is used to execute all the batch jobs created by developers in the system. Repositories should be attached to at least to one job server to execute the jobs in the repository, otherwise developer cannot execute the jobs.
**注意：Real-Time JobとBatch Jobが存在する**
**Management Console**:ジョブスケジューラ
> It is a web based console for managing SAP Data Services like scheduling the jobs, looking at system statistics on memory usage, runtime of jobs, CPU utilization etc.


# Data Serviceの歴史
1. **Acta Technology Inc**がData Integration (DI)とData Quality (DQ)を開発
2. **BusinessObjects**がActa Technology Incを買収する（2002年）。製品名がBusinessObjects Data Integration (BODI) tool と BusinessObjects Data Quality (BODQ) toolになった。
3. **SAP**がBusinessObjectsを買収し、両製品をSAP BusinessObjects Data Services (BODS)に統一した。（2008）BODSが4.2バージョンになった際にSAP Data Service（SDS）に変更した。
# Load Data to SAP HANA
> [How to load data to SAP HANA using SAP Data Services](http://saphanatutorial.com/how-to-load-data-to-sap-hana-using-sap-data-services/)
1. Create Data Store between Source and BODS
2. Import the metadata (Structures) to BODS.
3. Configure Import Server
4. Import the metadata to HANA system.
5. Create Data Store between BODS to HANA.
6. Create Project.
7. Define Job
8. Define Work Flow
9. Define Data Flow
10. Add Object in Dataflow
11. Execute the job
12. Validate/Check the Data in HANA
