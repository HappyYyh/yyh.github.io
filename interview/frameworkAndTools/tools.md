---
title: 工具
permalink: /interview/frameworkAndTools/tools
key: frameworkAndTools-tools
---

## Excel

### 【用什么解析的Excel】

1. **Apache POI**

2. **Alibaba EasyExcel**

   地址：https://github.com/alibaba/easyexcel

   ~~~java
       /**
        * 最简单的读
        * <p>1. 创建excel对应的实体对象 参照{@link DemoData}
        * <p>2. 由于默认异步读取excel，所以需要创建excel一行一行的回调监听器，参照{@link DemoDataListener}
        * <p>3. 直接读即可
        */
       @Test
       public void simpleRead() {
           String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
           // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
           EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();
       }
   ~~~

   ~~~java
      /**
        * 最简单的写
        * <p>1. 创建excel对应的实体对象 参照{@link com.alibaba.easyexcel.test.demo.write.DemoData}
        * <p>2. 直接写即可
        */
       @Test
       public void simpleWrite() {
           String fileName = TestFileUtil.getPath() + "write" + System.currentTimeMillis() + ".xlsx";
           // 这里 需要指定写用哪个class去读，然后写到第一个sheet，名字为模板 然后文件流会自动关闭
           // 如果这里想使用03 则 传入excelType参数即可
           EasyExcel.write(fileName, DemoData.class).sheet("模板").doWrite(data());
       }
   ~~~

   

### 【POI解析Excel会存在什么问题】    

1. **数值类型处理**

   通过POI取出的数值默认都是double，即使excel单元格中存的是1，取出来的值也是1.0

2. **日期类型处理**

   很遗憾，POI对单元格日期处理很弱，没有针对的类型，日期类型取出来的也是一个double值，所以同样作为数值类型。

3. **性能问题**