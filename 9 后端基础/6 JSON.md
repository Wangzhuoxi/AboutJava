# 1 什么是JSON

JSON：JavaScript Object Notation【JavaScript 对象表示法】

JSON 是存储和交换文本信息的语法。类似 XML.

JSON采用完全独立于任何程序语言的文本格式，使JSON成为理想的数据交换语言.

# 2 为什么需要JSON

提到JSON，我们就应该和XML来进行对比。XML也是一种存储和交换文本信息的手段。那么JSON好在哪里呢？

JSON 比 XML 更小、更快，更易解析。

- javaScript原生支持JSON，解析速度会很快
- XML解析成DOM对象的时候，浏览器【IE和fireFox】会有差异
- 使用JSON会更简单

![è¿éåå¾çæè¿°](https://segmentfault.com/img/remote/1460000013279259?w=1304&h=720)

更加容易创建JavaScript对象

```javascript
var p = {'city':['北京','上海','广州','深圳']};
for(var i=0;i<p.city.length;i++){
    document.write(p.city[i]+"<br/>");
}
```

# 3 JSON语法

客户端与服务端的交互数据无非就是两种：

- 数组
- 对象

于是乎，JSON所表示的数据要么就是对象，要么就是数组。

JSON语法是javaScript语法的子集，javaScript用[]中括号来表示数组，用{}大括号来表示对象，JSON亦是如此。

## 3.1 JSON数组

```javascript
var employees = [
    { "firstName":"Bill" , "lastName":"Gates" },
    { "firstName":"George" , "lastName":"Bush" },
    { "firstName":"Thomas" , "lastName": "Carter" }
];
```

## 3.2 JSON对象

```javascript
var obj = {
    age: 20,
    str: "zhongfucheng",
    method: function () {
        alert("我爱学习");
    }
};
```

当然啦，数组可以包含对象，在对象中也可以包含数组.

# 4 解析JSON

javaScript原生支持JSON的，我们可以使用eval()函数来解析JSON，把JSON文本数据转换成一个JavaScript对象。

```javascript
function test() {
    // 在写JOSN的时候，记得把带上逗号
    var txt = "{a:123," + "b:'zhongfucheng'}";
    // 使用eval解析JSON字符串，需要增添()
    var aa = eval("(" + txt + ")");
    alert(aa);
}
```
# 5 不用框架时将JavaBean转成JSON

- 使用Strus2的时候，Struts2自带了组件能够让JavaBean对象、集合转成是JSON，不用我们自己拼接...这是非常方便的。
- 使用SpringMVC的时候，SpringMVC也支持将JavaBean转成JSON

但是，我们不一定使用框架来做开发呀。因此，我们还得学习使用第三方库来将JavaBean对象、集合转成JSON.

## 5.1 导入开发包

- commons-io-2.0.1.jar
- commons-lang-2.5.jar
- commons-collections-3.1.jar
- commons-beanutils-1.7.0.jar
- ezmorph-1.0.3.jar
- json-lib-2.1-jdk15.jar

## 5.2 示例代码

```java
import net.sf.json.JSONArray;
import java.util.*;
/**
 * 使用第三方工具，将JavaBean对象/List或Set或Map对象转成JSON 
 * @author AdminTC
 */
public class TestBean2Json {
    private static void javabean2json() {
        City city = new City(1,"广州");
        JSONArray jSONArray = JSONArray.fromObject(city);
        String jsonJAVA = jSONArray.toString();
        System.out.println(jsonJAVA);
        //[{"id":1,"name":"广州"}]
    }
    private static void list2json() {
        List<City> cityList = new ArrayList<City>();
        cityList.add(new City(1,"广州"));
        cityList.add(new City(2,"珠海"));
        JSONArray jSONArray = JSONArray.fromObject(cityList);
        String jsonJAVA = jSONArray.toString();
        System.out.println(jsonJAVA);
        //[{"id":1,"name":"广州"},{"id":2,"name":"珠海"}]
    }
    private static void set2json() {
        Set<City> citySet = new LinkedHashSet<City>();
        citySet.add(new City(1,"广州"));
        citySet.add(new City(2,"珠海"));
        JSONArray jSONArray = JSONArray.fromObject(citySet);
        String jsonJAVA = jSONArray.toString();
        System.out.println(jsonJAVA);
        //[{"id":1,"name":"广州"},{"id":2,"name":"珠海"}]
    }
    private static void javabeanlist2json() {
        List<City> cityList = new ArrayList<City>();
        cityList.add(new City(1,"中山"));
        cityList.add(new City(2,"佛山"));
        Province province = new Province(1,"广东",cityList);

        JSONArray jSONArray = JSONArray.fromObject(province);
        String jsonJAVA = jSONArray.toString();
        System.out.println(jsonJAVA);
        /*
          [
             {
              "id":1,
              "name":"广东"
              "cityList":[{"id":1,"name":"中山"},{"id":2,"name":"佛山"}],
             }
          ]
          */
    }
    private static void map2json() {
        List<City> cityList = new ArrayList<City>();
        cityList.add(new City(1,"中山"));
        cityList.add(new City(2,"佛山"));

        Map<String,Object> map = new LinkedHashMap<String,Object>();
        map.put("total",cityList.size());//表示集合的长度
        map.put("rows",cityList);//rows表示集合

        JSONArray jSONArray = JSONArray.fromObject(map);
        String jsonJAVA = jSONArray.toString();
        System.out.println(jsonJAVA);
        //[{"total":2,"rows":[{"id":1,"name":"中山"},{"id":2,"name":"佛山"}]}]

        jsonJAVA = jsonJAVA.substring(1,jsonJAVA.length()-1);
        System.out.println(jsonJAVA);
    }
}
```

把要解析成JSON 的javaBena对象、集合放进下面这段代码即可！

```java
JSONArray jSONArray = JSONArray.fromObject(map);
```

无论放进去什么，返回的都是数组.

# 6 总结

![è¿éåå¾çæè¿°](https://segmentfault.com/img/remote/1460000013279261)

# 7.JSONObject

JSONObject只是一种数据结构，可以理解为JSON格式的数据结构（[key-value](https://www.baidu.com/s?wd=key-value&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) 结构），可以使用put方法给json对象添加元素。JSONObject可以很方便的转换成字符串，也可以很方便的把其他对象转换成JSONObject对象。

，而后调用其 put() 方法，将数据写入。