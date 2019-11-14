---
layout: article
title: Redis - 文章投票系统
tags: Redis
 
lang: zh-Hans
key: Redis_Article_System
pageview: true
toc: true
show_subscribe: false
---

将文章信息存储在 Redis 中，每个文章又一个 ID，使用散列（Hash）存储文章，为了产生一个能够随着时间流逝而不断减少的评分，程序需要根据文章的发布时间和当前时间来计算文章的评分，具体计算方法为：将文章得到的支持票数量乘以一个常量，然后加上文章的发布时间，得出的结果就是文章的评分。

使用有序集合（Zset）记录文章发布的时间和文章的得分，为了避免相同用户对同一篇文章投票，需要使用集合（Set）记录每篇文章投票的用户 ID。

## 常量

```python
ONE_WEEK_IN_SECONDS = 7 * 86400
VOTE_SCORE = 432
ARTICLES_PER_PAGE = 25
```

## 发布文章

```python
def post_article(conn, user, title, link):

    # 生成新文章ID
    article_id = str(conn.incr('article:'))

    # 将用户添加到文章已投票 set 中，并设置过期时间
    voted = 'voted:' + article_id
    conn.sadd(voted, user)
    conn.expire(voted, ONE_WEEK_IN_SECONDS)

    now = time.time()
    article = 'article:' + article_id
    conn.hmget(article, {
        'title': title,
        'link': link,
        'poster': user,
        'time': now,
        'votes': 1,
    })

    conn.zadd('socre:', article, now + VOTE_SCORE)
    conn.zadd('time:', article, now)

    return article_id
```

## 获取文章

```python
def get_articles(conn, page, order='socre:'):

    # 记录文章的起始索引
    start = (page - 1) * ARTICLES_PER_PAGE
    end = start + ARTICLES_PER_PAGE - 1

    # 获取多个文章 ID
    ids = conn.zrevrange(order, start, end)
    articles = []
    for _id in ids:
        article_data = conn.hgetall(_id)
        articles.append(article_data)

    return articles
```

## 文章投票

```python
def article_vote(conn, user, article):

    # 文章投票截止时间（一周后不能投票）
    cutoff = time.time() - ONE_WEEK_IN_SECONDS
    if conn.zscore('time:', article) < cutoff:
        return

    # 增加文章的投票数量和评分
    article_id = article.partition(':')[-1]
    if conn.sadd('voted:' + article_id, user):
        conn.zincrby('score:', article, VOTE_SCORE)
        conn.hincrby(article, 'votes', 1)
```

## 对文章进行分组

```python
def add_remove_groups(conn, article_id, to_add=[], to_remove=[]):
    article = 'article:' + article_id
    for group in to_add:
        conn.sadd('group:' + group, article)
    for group in to_remove:
        conn.srem('group:' + group, article)
```

## 获取分组文章

```python
def get_group_articles(conn, group, page, order='score:'):

    # 检查是否已有缓存的排序结果
    key = order + group
    if not conn.exists(key):
        conn.zinterstore(key, ['group:' + group, order], aggregate='max')
        conn.expire(key, 60)

    return get_articles(conn, page, key)
```