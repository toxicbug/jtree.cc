+++
title = "hexo博客hexo主题添加备案号"
date = "2018-03-04"
tags = [ "Hexo", "blog" ]
categories = ["Linux-blog", "Linux-software", "Linux-server"]
+++


已经用hexo搭建好了博客，主题是next，也已经有备案号了，但是应该如何添加备案号到博客最下面呢？
　　修改footer.swig文件（有的扩展名是.ejs），路径存放博客文件夹/themes/next/layout/_partial，在`{% endif %}`之前加入备案代码，最终效果如下：

![](http://p.jtree.cc/jtree.cc/Ashampoo_Snap_2018.03.24_12h18m43s_004_.png)　　

<!-- more -->



```
<div class="theme-info">
  {{ __('footer.theme') }} -
  <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
    NexT.{{ theme.scheme }}
  </a>
</div>

<div class=BbeiAn-info">
  {{ __('渝ICP备') }} -
  <a href="http://www.miitbeian.gov.cn/">15013373号</a>
  </a>
</div>
{% endif %}

```

备案号请自行修改:
![](http://p.jtree.cc/jtree.cc/Ashampoo_Snap_2018.03.24_11h57m51s_003_.png)