---
title: NGINX 相关
date: 
categories:
- 运维
tags:
- nginx
---

### nginx 如何开启gzip

```
  gzip on;
  gzip_buffers 32 4K;
  gzip_comp_level 6;
  gzip_min_length 100;
  gzip_types application/javascript text/css text/xml;
  gzip_disable "MSIE [1-6]\."; #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
  gzip_vary on;
```

