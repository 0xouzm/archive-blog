---
title: "django中使用redis缓存页面"
date: 2018-09-10 12:00
categories: python
tags: django
---

# 1.手动redis缓存

### 安装

`sudo pip3 install django-redis`

### settings中进行配置

```python3
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'localhost:6379',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}
```

### 视图中使用：

```python

def write_to_cache(request):
    key = 'hahaha'
    value = json.dumps('xixixixixi')
    cache.set(key, value, settings.NEVER_REDIS_TIMEOUT)
    return render(request, 'test.html')

def read_from_cache(request):
    key = 'hahaha'
    value = cache.get(key)
    if value == None:
        data = None
    else:
        data = json.loads(value)
        return render(request,'test.html',{'show':data})
```



# 2.装饰器进行缓存

使用场景：秒杀

`from django.views.decorators.cache import cache_page`

`@cache_page(timeout)`



# 3.全站缓存

使用场景：新闻类

```python
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',
    ...
    'django.middleware.cache.FetchFromCacheMiddleware',
]
```

