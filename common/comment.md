<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
 <script src="../../source/md5.min.js"></script>
 <script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
 <div id="gitalk-container"></div>
 <script>
   var gitalk = new Gitalk({
     "clientID": "f8eec1cd59e53d8158d2",
     "clientSecret": "83873640a1aa569a87ab8d07b210efe35b6797b8",
     "repo": "IPU-DOCUMENT",
     "owner": "luckywangyj",
     "admin": ["luckywangyj"],
     "id": md5(location.href),
     "distractionFreeMode": false
   });
   gitalk.render("gitalk-container");
 </script>