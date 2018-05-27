---
title: Apache Solr 学习入门 (一)
date: 2017-06-25 00:07:05
categories: architecture
tags: 
  - Apache Solr
---

>学习环境：  
OS：CentOS 7 mini  
Apache Solr 6.3.0

### Solr的安装  
1. 从[`Solr Release Page`](https://archive.apache.org/dist/lucene/solr/) 可以下载到过去的历史版本。
2. `tar xzvf solr-6.3.0.tgz`解压到指定目录下，就可以直接启动了。如下命令可以启动sample data：  
```
${solr}/bin/solr start -e tecjproducts
```
`Solritas UI` : http://localhost:8983/solr/techproducts/browse  
3. solr的停止和重启：  
```
[solr@min02 solr63]$ bin/solr stop -p 8983  
[solr@min02 solr63]$ bin/solr restart
```

### Solr Core 的准备
4. core (solrbook) 的创建：  
```
[solr@min02 solr63]$ bin/solr create_core -c solrbook -d basic_configs
Copying configuration to new core instance directory:
/mnt/apps/solr63/server/solr/solrbook
Creating new core 'solrbook' using command:
http://localhost:8983/solr/admin/cores?action=CREATE&name=solrbook&instanceDir=solrbook
{
  "responseHeader":{
    "status":0,
    "QTime":4950},
  "core":"solrbook"}
```
5. solr core的重载：  
```
[solr@min02 solr63]$ curl "http://192.168.2.122:8983/solr/admin/cores?action=RELOAD&core=solrbook"
<?xml version="1.0" encoding="UTF-8"?>
<response>
<lst name="responseHeader"><int name="status">400</int><int name="QTime">194</int></lst><lst name="error"><lst name="metadata"><str name="error-class">org.apache.solr.common.SolrException</str><str name="root-error-class">org.apache.solr.common.SolrException</str></lst><str name="msg">Core with core name [solrbook] does not exist.</str><int name="code">400</int></lst>
</response>
[solr@min02 solr63]$ 
```

### Schema的定义
6. 通过`Schema API`新加`schema.xml`定义field：  
```
[solr@min02 solr63]$ curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"id","type":"string","indexed":"true","stored":"true","required":"true","multiValued":"false"}}' http://192.168.2.122:8983/solr/solrbook/schema
{
  "responseHeader":{
    "status":0,
    "QTime":87},
  "errors":[{
      "add-field":{
        "name":"id",
        "type":"string",
        "indexed":"true",
        "stored":"true",
        "required":"true",
        "multiValued":"false"},
      "errorMessages":["Field 'id' already exists.\n"]}]}
[solr@min02 conf]$ curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"title","type":"text_ja","indexed":"true","stored":"true","required":"false","multiValued":"false"},"add-field":{"name":"sumamry","type":"text_ja","indexed":"true","stored":"true","required":"false","multiValued":"false"}}' http://192.168.2.122:8983/solr/solrbook/schema
{
  "responseHeader":{
    "status":0,
    "QTime":633}}
[solr@min02 conf]$ 
[solr@min02 conf]$ vi managed-schema 
[solr@min02 conf]$ curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"author","type":"string","indexed":"false","stored":"true","required":"false","multiValued":"true"},"add-field":{"name":"intended_reader","type":"string","indexed":"false","stored":"true","required":"false","multiValued":"true"},"add-field":{"name":"genre","type":"string","indexed":"false","stored":"true","required":"false","multiValued":"true"},"add-field":{"name":"isbn","type":"string","indexed":"false","stored":"true","required":"false","multiValued":"true"}}' http://192.168.2.122:8983/solr/solrbook/schema
{
  "responseHeader":{
    "status":0,
    "QTime":488}}
[solr@min02 conf]$ curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"pub_date","type":"date","indexed":"true","stored":"true","required":"false","multiValued":"false"}}' http://192.168.2.122:8983/solr/solrbook/schema
{
  "responseHeader":{
    "status":0,
    "QTime":267}}
[solr@min02 conf]$ curl -X POST -H 'Content-type:application/json' --data-binary '{"add-field":{"name":"price","type":"tint","indexed":"true","stored":"true","required":"false","multiValued":"false"},"add-field":{"name":"pages","type":"tint","indexed":"false","stored":"true","required":"false","multiValued":"false"}}' http://192.168.2.122:8983/solr/solrbook/schema
{
  "responseHeader":{
    "status":0,
    "QTime":242}}
```
添加完后在schema.xml（6.x 是manage schema）中就可以看到定义的field了：  
```
<field name="author" type="string" multiValued="true" indexed="false" required="false" stored="true"/>
  <field name="genre" type="string" multiValued="true" indexed="false" required="false" stored="true"/>
  <field name="id" type="string" multiValued="false" indexed="true" required="true" stored="true"/>
  <field name="intended_reader" type="string" multiValued="true" indexed="false" required="false" stored="true"/>
  <field name="isbn" type="string" multiValued="true" indexed="false" required="false" stored="true"/>
  <field name="pages" type="tint" multiValued="false" indexed="false" required="false" stored="true"/>
  <field name="price" type="tint" multiValued="false" indexed="true" required="false" stored="true"/>
  <field name="pub_date" type="date" multiValued="false" indexed="true" required="false" stored="true"/>
  <field name="sumamry" type="text_ja" multiValued="false" indexed="true" required="false" stored="true"/>
  <field name="title" type="text_ja" multiValued="false" indexed="true" required="false" stored="true"/>
```
    也可以导入配置好的solr_schema.json文件使之反应到schema定义文件中：  
```
curl -X POST -H 'Content-type:application/json' --data-binary @solrbook_schema.json http://192.168.2.122:8983/solr/solrbook/schema
```

### 文件的提交更新和删除
6. 添加文件，可以使用post tool和curl两种方法：  
    1. 使用post tool投入json/xml数据
    ```
    [solr@min02 solr63]$ bin/post -c solrbook /mnt/hgfs/shared/examples/ch03/json-sample.json 
    java -classpath /mnt/apps/solr63/dist/solr-core-6.3.0.jar -Dauto=yes -Dc=solrbook -Ddata=files org.apache.solr.util.SimplePostTool /mnt/hgfs/shared/examples/ch03/json-sample.json
    SimplePostTool version 5.0.0
    Posting files to [base] url http://localhost:8983/solr/solrbook/update...
    Entering auto mode. File endings considered are xml,json,jsonl,csv,pdf,doc,docx,ppt,pptx,xls,xlsx,odt,odp,ods,ott,otp,ots,rtf,htm,html,txt,log
    POSTing file json-sample.json (application/json) to [base]/json/docs
    1 files indexed.
    COMMITting Solr index changes to http://localhost:8983/solr/solrbook/update...
    Time spent: 0:00:01.210
    ```
    2. 使用curl投入json/xml数据
    ```
    [solr@min02 ch03]$ curl "http://192.168.2.122:8983/solr/solrbook/update?commit=true" --data-binary @json-sample.json -H 'Content-type:text/json; charset=utf-8'
    {"responseHeader":{"status":0,"QTime":265}}

    ```
    3. 除此登录方法之外还有可使用`stream.file/stream.url` 参数来直接传递本地文件登录。前提是需要将solrconfig.xml中的设置`<requestParsers enableRemoteStreaming="true"`打开。  
7. 文件更新，id，unique key设定为前提，跟新加一样，支持json/xml/csv的post和curl来更新：  
```
curl "http://192.168.2.122:8983/solr/solrbook/update?commit=true" --data-binary @update.json -H 'Content-type:text/json; charset=utf-8'
bin/post -c solrbook ${EXAMPLE}/ch03/update.json
```
8. 局部更新可以使用修饰子`set`，`add(multiValue=true时候)`，`remove`等记述在数据中，如下json文件格式：  
```
[
 {"id": "http://sample.com/book?id=001",
  "title": {"set":"Apache Solr 入門 改訂"},
  "author": {"add":["西潟 一生"]}
 }
]
```
9. 文档的删除有两种方法，一是指定unique key删除，二是使用query删除：  
```
{"delete":{"id":"http://sample.com/book?id=001"}}
{"delete":{"id":["http://sample.com/book?id=001","http://xxx"]}}
{"delete":{"query":"author:xxx"}}
```
- post tool:
    ```
    $ ${SOLR}/bin/post -c solrbook ${EXAMPLE}/ch03/del.json -format solr
    ```
- curl:
    ```
    $ curl "http://192.168.2.122:8983/solr/solrbook/update?commit=true" --data-binary @del.json -H 'Content-type:text/json; charset=utf-8'
    ```
- `commit=true` 和 `rollback=true`
