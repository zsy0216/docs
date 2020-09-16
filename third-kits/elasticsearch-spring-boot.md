# Spring 整合 Elasticsearch

## 导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

## Elasticsearch 配置

```yaml
# ElasticsearchProperties
spring:
  data:
    elasticsearch:
      cluster-name: tassel
      cluster-nodes: 127.0.0.1:9300
```

## 解决与 Redis 共同启动的冲突

在主启动 类增加代码

```java
@PostConstruct
public void init() {
    // 解决 Netty 启动冲突问题
    System.setProperty("es.set.netty.runtime.available.pricessors", "false");
}
```

## 使用

1. 在实体类上加注解关联 Elasticsearch

   ```java
   @Data
   @Document(indexName = "discusspost", type = "_doc", shards = 6, replicas = 3)
   public class DiscussPost {
   	@Id
   	private Integer id;
   	@Field(type = FieldType.Integer)
   	private Integer userId;
       @Field(type = FieldType.Text, analyzer = "ik_max_word", searchAnalyzer = "ik_smart")
   	private String title;
       @Field(type = FieldType.Date)
   	private Date createTime;
       @Field(type = FieldType.Double)
   	private Double score;
   ```

2. 实现 ElasticseachRepository 接口

   ```java
   @Repository
   public interface DiscussPostRepository extends ElasticsearchRepository<DiscussPost, Integer> {
   }
   ```

3. 测试

   ```java
   @Resource
   ElasticsearchRepository<DiscussPost, Integer> discussRepository;
   @Resource
   ElasticsearchTemplate elasticTemplate;
   /**
    * 单个插入 discussRepository.save()
    * 批量插入 discussRepository.saveAll()
    * 修改可以使用增加新的内容
    * 删除调用 discussRepository.delete() / deleteById() deleteAll()
    */
   
   @Test
   public void testSearchByRepository() {
       SearchQuery searchQuery = new NativeSearchQueryBuilder()
           // 查询
           .withQuery(QueryBuilders.multiMatchQuery("互联网寒冬", "title", "content"))
           // 排序
           .withSort(SortBuilders.fieldSort("type").order(SortOrder.DESC))
           .withSort(SortBuilders.fieldSort("score").order(SortOrder.DESC))
           .withSort(SortBuilders.fieldSort("createTime").order(SortOrder.DESC))
           // 分页
           .withPageable(PageRequest.of(0, 10))
           // 命中高亮
           .withHighlightFields(
           new HighlightBuilder.Field("title").preTags("<em>").postTags("</em>"),
           new HighlightBuilder.Field("content").preTags("<em>").postTags("</em>")
       ).build();
       Page<DiscussPost> page = discussRepository.search(searchQuery);
   }
   ```
   
   >  用 discussRepository 获取到的高亮显示的值没有返回
   > 可以使用 elasticTemplate.queryForPage(searchQuery, class, SearchResultMapper)
   
   ```java
   @Test
   public void testSearchByTemplate() {
       SearchQuery searchQuery = new NativeSearchQueryBuilder()
           .withQuery(QueryBuilders.multiMatchQuery("互联网寒冬", "title", "content"))
           .withSort(SortBuilders.fieldSort("type").order(SortOrder.DESC))
           .withSort(SortBuilders.fieldSort("score").order(SortOrder.DESC))
           .withSort(SortBuilders.fieldSort("createTime").order(SortOrder.DESC))
           .withPageable(PageRequest.of(0, 10))
           .withHighlightFields(
           new HighlightBuilder.Field("title").preTags("<em>").postTags("</em>"),
           new HighlightBuilder.Field("content").preTags("<em>").postTags("</em>")
       ).build();
   
       Page<DiscussPost> page = elasticTemplate.queryForPage(searchQuery, DiscussPost.class, new SearchResultMapper() {
           @Override
           public <T> AggregatedPage<T> mapResults(SearchResponse response, Class<T> aClass, Pageable pageable) {
               SearchHits hits = response.getHits();
               if (hits.getTotalHits() <= 0) {
                   return null;
               }
   
               List<DiscussPost> list = new ArrayList<>();
               for (SearchHit hit : hits) {
                   DiscussPost post = new DiscussPost();
                   String id = hit.getSourceAsMap().get("id").toString();
                   post.setId(Integer.valueOf(id));
                   String userId = hit.getSourceAsMap().get("userId").toString();
                   post.setId(Integer.valueOf(userId));
                   String title = hit.getSourceAsMap().get("title").toString();
                   post.setTitle(title);
                   String content = hit.getSourceAsMap().get("content").toString();
                   post.setContent(content);
                   String status = hit.getSourceAsMap().get("status").toString();
                   post.setStatus(Integer.valueOf(status));
                   String createTime = hit.getSourceAsMap().get("createTime").toString();
                   post.setCreateTime(new Date(Long.valueOf(createTime)));
                   String commentCount = hit.getSourceAsMap().get("commentCount").toString();
                   post.setCommentCount(Integer.valueOf(commentCount));
   
                   // 处理高亮显示的结果
                   HighlightField titleField = hit.getHighlightFields().get("title");
                   if (titleField != null) {
                       post.setTitle(titleField.getFragments()[0].toString());
                   }
                   HighlightField contentField = hit.getHighlightFields().get("content");
                   if (contentField != null) {
                       post.setTitle(contentField.getFragments()[0].toString());
                   }
   
                   list.add(post);
               }
               return new AggregatedPageImpl(list, pageable, hits.getTotalHits(), response.getAggregations(), response.getScrollId(), hits.getMaxScore());
           }
   
           @Override
           public <T> T mapSearchHit(SearchHit searchHit, Class<T> aClass) {
               return null;
           }
       });
   
       System.out.println(page.getTotalElements());
       System.out.println(page.getTotalPages());
       System.out.println(page.getNumber());
       System.out.println(page.getSize());
       for (DiscussPost post : page) {
           System.out.println(post);
       }
   }
   ```
   
   

