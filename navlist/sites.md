
- mybatis
  - [mybatis-3](https://mybatis.org/mybatis-3/zh/index.html)
  - [mybatis-spring](https://mybatis.org/spring/zh/index.html)
  - [mybatis-spring-boot](http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/index.html#)
  
- node 
  - [Nodejs英文](https://nodejs.org/en/)
  - [Nodejs中文](http://nodejs.cn/)
  - [vuejs](https://vuejs.org/)


<div id="site">
  <div v-for="item in siteList">
    <h3>{{item.capter}}</h3>
    <div class="effects">
    <a v-for="it in item.data" class="hvr-grow-shadow" :href="it.url" :title="it.title" target="_blank">{{ it.title }}</a>
    </div>
  </div>
</div>

<script>
  const siteList = [
    {
      "capter": "mybatis",
      "data" :[
          {"title":"mybatis-3", "url":"https://mybatis.org/mybatis-3/zh/index.html"},
          {"title":"mybatis-spring", "url":"https://mybatis.org/spring/zh/index.html"},
          {"title":"mybatis-spring-boot", "url":"http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/index.html#"}
      ]
    },
    {
      "capter":"node",
      "data":[
        {"title":"Nodejs英文", "url":"https://nodejs.org/en/"},
        {"title":"Nodejs中文", "url":"http://nodejs.cn/"},
        {"title":"vuejs", "url":"https://vuejs.org/"}
      ]
    }
  ]
  new Vue({
    el: '#site',
    data() {
      return {
        siteList:siteList
      };
    }
  });
</script>
