---
layout: post
title: "[SnapEvent]穩定又快速的請求網路xml資料"
description: "介紹穩定請求Server端資料的Volley框架和快速解析XML的套件SimpleXML，並將兩者結合使用在Snapevent。"
modified: 2015-08-21
tags: [android, snapevent, volley, GET, SimpleXML]
image:
  feature: volleyxml.JPG
  credit: hsuan-ju in Newfoundland, Canada
---

## 大綱

本篇文章會先介紹為何Android中，用到網路請求時建議使用Volley框架，和Volley中如何使用GET請求網路資料。接下來會介紹在JAVA中，解析複雜的XML資料時，超級好用的SimpleXML以及如何使用。
最後再解說，我如何利用Volley框架結合SimpleXML解析網站上的RSS xml Data，本文會花多一點篇幅在這部分。

## 為何使用Volley框架請求網路數據

一談到Android的網路請求方式，相信大家第一個想到的就是HttpURLConnection和HttpClient，我以前也是這樣寫過來的。但若請求的東西單純還好，如果要POST、要傳複雜的參數或請求網路圖片等...
就會感到前所未有的煩躁，重複創建AsyncTask，一直重複copy/past的動作，還要擔心自己的寫法穩定性是否足夠。

幸運的是我亂逛網站時，偶然得知了Volley框架。Volley是Google在2013 Google I/O大會上發表的網路通訊框架，它將Http的通訊細節都經過封裝，讓使用者能輕鬆的請求網路數據。
GitHub中，比較複雜的APP也常看到Volley的蹤跡。但Volley有優點也有缺點，是否使用它要視情況而定。下面是Volley優缺點列表：

### 優點

1. 高效能的GET/POST數據請求交互
2. 幫忙處理複雜的網路圖片加載與緩存
3. 性能穩定
4. Google官方出的，保證功能全面

### 缺點

1. 不適合大數據的上傳/下載

Volley設計的目標就是請求**數據量不大但頻繁的網路資料**，所以如果有大數據量的網路下載(ex 下載檔案文件)，Volley表現就會不太好。

## Volley的GET請求

因為這次我只會使用到Volley的GET，而Volley裡文字數據的請求有三個，StringRequest、JsonObjectRequest和JsonArrayRequest。JsonObjectRequest和JsonArrayRequest可以讓請求JSON數據時更快速，只可惜這次要請求的是XML數據，所以以下說明將使用**StringRequest的GET方法做請求範例**，JsonObjectRequest和JsonArrayRequest的用法其實和StringRequest相似，詳細會在以後用到時再做介紹，還有Volley最方便的網路圖片請求也會在以後做說明！

 1 ) 引用volley包

感恩好心網友幫gradle和Maven建立Volley庫，讓我們能輕鬆使用框架。原repo請至<a href="https://github.com/mcxiaoke/android-volley">這裡</a>。

{% highlight html %}
dependencies {
   compile 'com.mcxiaoke.volley:library:1.0.18'
}
{% endhighlight %}

 2 ) 宣告請求對列RequestQueue

RequestQueue用來緩存所有HTTP請求。RequestQueue內會按照一定算法發出網路請求。而RequestQueue本身就適合高開發，所以重複宣告多個很浪費資源，故RequestQueue物件大都設為全局Application的變數。

{% highlight java%}
public class MyApp extends Application{
/*......*/
RequestQueue mQueue = Volley.newRequestQueue(context);
/*......*/
}
{% endhighlight %}

> MyApp別忘記在AndroidManifest.xml加上**android:name="MyApp"**，也別忘記加上網路權限**<uses-permission android:name="android.permission.INTERNET" />**。

3 ) 創建StringRequest物件

{% highlight java%}
/*
使用POST時用Request.Method.POST；使用GET時用Request.Method.GET
String url 為請求對像server的網址，因為是GET所以參數能直接夾帶在網址後
Response.Listener response successful時調用
Response.ErrorListener response error時調用
*/
StringRequest stringRequest = new StringRequest(Request.Method.GET, url,  
                        new Response.Listener<String>() {  
                            @Override  
                            public void onResponse(String response) {  
                                //response成功時做的事
                            }  
                        }, new Response.ErrorListener() {  
                            @Override  
                            public void onErrorResponse(VolleyError error) {  
                                Log.e("TAG", error.getMessage(), error);  //response失敗時做的事
                            }  
                        });  
{% endhighlight %}

4 ) 把建立好的request丟到RequestQueue執行，大功告成！

{% highlight java%}
mQueue.add(stringRequest); 
{% endhighlight %}

> 如果想知道更詳細的Volley使用方法，能參考<a href="http://blog.csdn.net/guolin_blog/article/details/17482095">這裡</a>。

## Java中，解析XML的方便套件：SimpleXML

台灣有很多活動網站，但不是沒提供完整的API，要不然就是API資料還死守XML不放......尋了好久，最後還是決定，用請求KKTIX的RSS xml資料，來獲得每日活動詳細。
RSS的xml 資料層次多，一層包一層很複雜，如果想快速取出想要的資訊，並跳過不需要的資料，好的XML解析套件就非常重要。
在這個JSON輕量級資料傳輸格式幾乎取代XML的時代，找出好的XML解析套件花了我不少時間，最後終於找到超直觀好用又穩定的套件：SimpleXML了！！

SimpleXML的使用思路是，先跟據要解析的XML文件創建一個java class(用SimpleXML規定的方式撰寫)，再通過這個java class去取得XML文件內的具體值。
以下會做些簡易說明：

 1 ) 以下為要解析的文件

{% highlight html %}
<?xml version="1.0" encoding="UTF-8"?>  
 <list>  
    <entry id="1">  
        <name>John</name>  
        <gender>Boy</gender>  
    </entry>
 </list>  
{% endhighlight %}

 2 ) 到官網<a href="http://simple.sourceforge.net/download.php">下載SimpleXML Jar</a>並加到Android Studio或Eclipse的library，<a href="http://simple.sourceforge.net/download/stream/doc/examples/examples.php">官網的example</a>也有教學能讓初學者更快上手。

3 ) 撰寫和要解析的XML文件相對應的java class

{% highlight java %}
@Root(name="list", strict=true) //Root為根，如果<list>內不是每個元素都想去，strict要用false
public class Mylist {  
  @Attribute(required = true)  
    protected String id;  // entry: id="1"

  @Element(name = "name", required = true)  
    protected String Name;  

    @Element(name = "gender", required = true)  
    protected String Gender;  
    
  
    public String getID( ) {  
        return id;  
    }  
      
    public String getName( ) {  
        return Name;  
    }  
  
    public String getGender( ) {  
        return Gender;  
    }  
    
}
{% endhighlight %}

怎麼樣！上面的java class對應看起來超直觀的吧！(灑花)

 4 ) 通過這個java class去取得XML文件內的具體值

{% highlight java %}
final Serializer serializer;  
serializer = new Persister();  
Mylist list = serializer.read(Mylist.class, String fileData);  //得到Mylist
{% endhighlight %}

> 其中還有能將重複Item都整理成list的@ElementList，歡迎參考<a href="http://www.javacodegeeks.com/2011/02/android-xml-binding-simple-tutorial.html">這裡</a>。

## 利用Volley框架結合SimpleXML解析網站上的RSS xml Data

本次實作架構的UML如下：

<figure>
	<img src="/images/snapevent3/UML.PNG" alt="UML">
	<figcaption></figcaption>
</figure>

1. app資料夾：放置App全局資源
   1. AppController.java ：繼承了Application，放置全局變量和method的地方
   2. AppRemoteConfig.java：放置資料請求目標端的URL
2. bean資料夾
   1. Author.java、xmlBeanList.java、xmlEventBean.java：根據KKTIX RSS的xml data撰寫的映射物件。
3. volleyResponse資料夾：放置Volley相關改寫
   1. SimpleXmlRequest.java：繼承自Volley的Request<T>，給Request做了改寫。

### 思路始末

我原來的思路，是希望使用Volley的StringRequest把所有xml請求下來，到Response.Listener再用SimpleXML進行XML的解析。但是當我使用StringRequest請求完資料，並在Response.Listener用Log打印出來時，竟然只打印出一半的XML資料，後半資料都消失得莫名其妙，但StringRequest內確實是接收到全部的XML資料，這樣的結果讓我想到Response.Listener內可能不適合放置耗時邏輯，像是我本來將要做的，大量映射XML物件的邏輯...
經過翻找，很幸運地看到itsalif大大的<a href="https://gist.github.com/itsalif/6149365">SimpleXmlRequest.java</a>貢獻，這位大大繼承了Volley的Request<T>，並在parseNetworkResponse method中，一接收完資料就用SimpleXML解析XML資料，將映射出來的所有物件再丟給Response.Listener處理，這樣就完美的一個資料都不會漏了！
我就趕快來試一試，真的非常好用！如果各位有想從網路上請求大量的XML資料並解析，非常推薦這個方法，<a href="https://gist.github.com/itsalif/6149365">SimpleXmlRequest頁面</a>下也有許多網友做了進階的改寫。再觀看的途中，可能會對<T> void method( )這種寫法感到疑惑，可以參考<a href="http://stackoverflow.com/questions/14260775/what-t-signifies-in-t-void-say">這裡</a>。
### AppController.java內的GetKKTIXRequestToEventBean(Context context)實作

{% highlight java %}
public void GetKKTIXRequestToEventBean(Context context){

eventRequestQueue.cancelAll(VolleyTAG.KKTIX_ALL.getTAG()); //取消還在運行的同一個request

        if(ifInternetOpen(context)) { //如果網路有開啟
           /*
            *AppRemoteConfig使用single pattern，確保不浪費空間資源
            *xmlBeanList.class為給SimpleXML解析資料時的映射物件
            *Response.Listener<xmlBeanList>為Volley資料回傳成功時調用，要實作onResponse method，xmlBeanList為回傳資料型態。
            *Response.ErrorListener為錯誤時調用，要實作 onErrorResponse。
            */
            SimpleXmlRequest<xmlBeanList> simpleRequest = new SimpleXmlRequest<xmlBeanList>          (Request.Method.GET,  
                    AppRemoteConfig.getInstance().getKKTIX_ALL_url(), 
                    xmlBeanList.class,
                    new Response.Listener<xmlBeanList>() {
                        @Override
                        public void onResponse(xmlBeanList response) {
                            List<xmlEventBean> datas = response.getMatches();
                            for (xmlEventBean bean : datas) {
                                Log.i("success", "title: " + bean.getTitle() + " and " + bean.getAuthor().getName());
                            }

                        }
                    },
                    new Response.ErrorListener() {
                        @Override
                        public void onErrorResponse(VolleyError error) {
                            Log.i("error", error.getMessage());
                        }
                    }
            );
            AppController.getInstance().addToRequestQueue(simpleRequest, VolleyTAG.KKTIX_ALL.getTAG()); //加入RequestQueue執行請求
        }else{
            //如果網路未開啟
            Log.i("Internet Error","user phone didn't open internet.");
        }
{% endhighlight %}

關於網路的判斷可以對照下面。

{% highlight java %}
public boolean ifInternetOpen(Context context){

        final ConnectivityManager connMgr = (ConnectivityManager)
                this.getSystemService(Context.CONNECTIVITY_SERVICE); //呼叫App內建Service

        final android.net.NetworkInfo wifi =
                connMgr.getNetworkInfo(ConnectivityManager.TYPE_WIFI); //Wifi狀態

        final android.net.NetworkInfo mobile =
                connMgr.getNetworkInfo(ConnectivityManager.TYPE_MOBILE); //3G、4G網路狀態

        if( wifi.isAvailable() || mobile.isAvailable()){
            return true;
        }
        else{
            Toast.makeText(context, "請開起網路連線" , Toast.LENGTH_LONG).show();
            return false;
        }
    }
{% endhighlight %}

之後在MainActivity就能很輕鬆的使用**AppController.getInstance().GetKKTIXRequestToEventBean(MainActivity.this);**載入網路上的KKTIX RSS的xml data。

### 撰寫解析KKTIX RSS的xml資料的映射物件

首先，觀察KKTIX RSS的xml data。網址<a href="https://kktix.com/events.atom?end_at=2015/08/31&locale=zh-TW&start_at=2015/08/31">如這</a>，下面截圖已經取了片段說明。

<figure>
	<img src="/images/snapevent3/01.PNG" alt="XML">
	<figcaption></figcaption>
</figure>

由上面說明撰寫物件。

### xmlBeanList.java

{% highlight java %}
public class xmlBeanList {

    @Root(name="feed",strict = false) //strict = false:非嚴格的feed tag中每個element和Attribute都要取，root為feed

        @ElementList(entry = "entry", inline = true) //<entry></entry>的list
        private List<xmlEventBean> beans; //集合，內容物對應到下面的xmlEventBean.java

        public xmlBeanList() {
        }

        public List<xmlEventBean> getMatches() { //必須要有GET method才能拿到data
            return beans;
        }
}
{% endhighlight %}

### xmlEventBean.java

{% highlight java %}
public class xmlEventBean {

    @Root(name="entry",strict = false) //root為entry

    @Element(name = "title", required = true) //<title>...</title>，required設true:沒符合的話會丟error
    private String title;

    @Element(name="link",required = true) //<link rel="..." ..... href="..."/>
    public class Link{
        @Attribute(name="href",required = true)
        private String url;
    }
    @Element(name = "summary", required = false) //<summary>...</summary>
    private String summary;

    @Element(name = "content", required = true) //<content>...</content>
    private String timeANDplace;

    @Element(name = "author", required = false)//<author>...</author>，對應到Author class
    private Author author;

 //必須要有GET method才能拿到data
    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getSummary() {
        return summary;
    }

    public void setSummary(String summary) {
        this.summary = summary;
    }

    public String getTimeANDplace() {
        return timeANDplace;
    }

    public void setTimeANDplace(String timeANDplace) {
        this.timeANDplace = timeANDplace;
    }

    public Author getAuthor() {
        return author;
    }
}
{% endhighlight %}

### Author.java

{% highlight java %}
public class Author{

    @Element(name="name",required = true) 
    private String name;
    @Element(name="url",required = false)
    private String url;

    public String getName() {
        return name;
    }

    public String getUrl() {
        return url;
    }
}
{% endhighlight %}

最後將 xmlBeanList.class給SimpleXmlRequest class當參數，讓SimpleXmlRequest內的parseNetworkResponse中，serializer.read(clazz, data,**false**)去將XML data都解析成物件。其中的false代表XML格式不嚴格，要加才不易抱錯。

### 限制選項Enum: VolleyTAG

最後，為了配合不同的請求目標URL，就要有相應不同的TAG管理，Enum就是個很好限制選項的寫法。使用自訂Enum並 限制住呼叫建構子的方法，讓使用者不會搞錯TAG，詳細教學<a href="http://cloudchen.logdown.com/posts/179666/about-the-java-enum-i-know">見這</a>。

{% highlight java %}
public enum VolleyTAG {

   //限制住本class只允許2種建構子： VolleyTAG("KKTIX_ALL") / VolleyTAG("GOV_EVENT") 
    KKTIX_ALL("KKTIX_ALL"),
    GOV_EVENT("GOV_EVENT"); 

    private String TAG=null;

    VolleyTAG(String TAG) { 
        this.TAG=TAG;
    }

    public String getTAG(){
        return TAG;
    }
}
{% endhighlight %}

### 結果：成功映射出XML data物件

<figure>
	<img src="/images/snapevent3/02.PNG" alt="XML">
	<figcaption></figcaption>
</figure>

### 程式碼

git clone下面專案到自己的電腦後，使用 **git checkout a5ab .**。

<div markdown="0"><a href="https://github.com/mis101bird/SnapEvent/tree/practice" class="btn">SnapEvent</a></div>

