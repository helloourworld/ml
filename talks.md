---
layout: page
title: "Talks"
description: "Hey, this is Lijun Yu."
header-img: "images/IMG_0006.JPG"
---

<!-- Language Selector -->
<select onchange= "onLanChange(this.options[this.options.selectedIndex].value)">
    <option value="0" selected> 中文 Chinese </option>
    <option value="1"> 英文 English </option>
</select>

<!-- Chinese Version -->
<div class="zh post-container">

    <h5>Talks</h5>
    <ul>
    <li><a href="https://github.com/helloourworld/machinelearningadvance/blob/gh-pages/attach/speech/%E9%9B%86%E6%88%90%E5%AD%A6%E4%B9%A0%E5%8E%9F%E7%90%86%E4%B8%8E%E5%BA%94%E7%94%A8.pptx">2018.4 The Principle and Application of Ensemble Learning</a></li>
    </ul>
    <ul>
    <li><a href="takls/attach/speech/">2018.3 Basic Concepts in Graph Theory</a></li>
    </ul>
    <ul>
    <li><a href="https://github.com/helloourworld/machinelearningadvance/blob/gh-pages/attach/speech/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98%E6%B5%81%E7%A8%8B%E4%B8%8E%E5%B8%B8%E7%94%A8%E7%AE%97%E6%B3%95%E6%A6%82%E8%A7%88.pptx">2017.6 Data Mining Processes and Common Algorithms Overview</a></li>
    </ul>


    <ul>
    <li><a href="">Program on Hadoop</a></li>
    </ul>


</div>

<!-- English Version -->
<div class="en post-container">

    <h5>Talks</h5>

     <ul>
    <li><a href="attach/speech/集成学习原理与应用.pptx">2018.4 The Principle and Application of Ensemble Learning</a></li>
    </ul>
    <ul>
    <li><a href="attach/speech/">2018.3 Basic Concepts in Graph Theory</a></li>
    </ul>
    <ul>
    <li><a href="attach/speech/数据挖掘流程与常用算法概览.pptx">2017.6 Data Mining Processes and Common Algorithms Overview</a></li>
    </ul>

    <ul>
    <li><a href="">Program on Hadoop</a></li>
    </ul>

</div>

<!-- Handle Language Change -->
<script type="text/javascript">
    // get nodes
    var $zh = document.querySelector(".zh");
    var $en = document.querySelector(".en");
    var $select = document.querySelector("select");

    // bind hashchange event
    window.addEventListener('hashchange', _render);

    // handle render
    function _render(){
        var _hash = window.location.hash;
        // en
        if(_hash == "#en"){
            $select.selectedIndex = 1;
            $en.style.display = "block";
            $zh.style.display = "none";
        // zh by default
        }else{
            // not trigger onChange, otherwise cause a loop call.
            $select.selectedIndex = 0;
            $zh.style.display = "block";
            $en.style.display = "none";
        }
    }

    // handle select change
    function onLanChange(index){
        if(index == 0){
            window.location.hash = "#zh"
        }else{
            window.location.hash = "#en"
        }
    }

    // init
    _render();
</script>



{% if site.duoshuo_username %}
<!-- 多说评论框 start -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"machinelearningadvance"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.unstable.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0]
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
    </script>
<!-- 多说公共JS代码 end -->
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"machinelearningadvance"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.unstable.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0]
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
    </script>
<!-- 多说公共JS代码 end -->
<!-- 多说评论框 end -->

<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
    // dynamic User hacking by Hux
    var _user = '{{site.duoshuo_username}}';

    // duoshuo comment query.
    var duoshuoQuery = {short_name: _user };
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0]
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
</script>
<!-- 多说公共JS代码 end -->
{% endif %}


{% if site.disqus_username %}
<!-- disqus 评论框 start -->
<div class="comment">
    <div id="disqus_thread" class="disqus-thread">

    </div>
</div>
<!-- disqus 评论框 end -->

<!-- disqus 公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES * * */
    var disqus_shortname = "{{site.disqus_username}}";
    var disqus_identifier = "{{site.disqus_username}}/{{page.url}}";
    var disqus_url = "{{site.url}}{{page.url}}";

    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<!-- disqus 公共JS代码 end -->
{% endif %}
