##时间格式
Solr中的`TrieDateField`属性（不建议使用`DateField`属性）代表了可以精确到毫秒的时间点。
这个属性的格式严格的遵照了 [XML模式文档](http://www.w3.org/TR/xmlschema-2/#dateTime) 中的描述：

YYYY-MM-DDThh:mm:ssZ

* YYYY&nbsp;代表4位数字的年份
* MM&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表2位数字的月份
* DD&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表2位数字的月份中的哪一天
* hh&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表24小时制的小时
* mm&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表分钟
* ss&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表秒
* Z&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代表了“Z”这个字符是使用UTC时间

注意：不可以指定时区；使用String将会表示成 协调世界时 （UTC）。如下：

1972-05-20T17:33:18Z

虽然你可以随意的在秒中设置小数，但是任何精度超过毫秒的设置都会被忽略。
下面是一些带有小数的时间设置：

* 1972-05-20T17:33:18.772Z
* 1972-05-20T17:33:18.77Z 
* 1972-05-20T17:33:18.7Z  
