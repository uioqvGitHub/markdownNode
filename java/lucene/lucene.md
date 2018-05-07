## lucene示例

```
1：新建一个工程
2：导入所需jar包

```

- 入库对象数据实体

  1：新建一个入库的实体对象

  2：入库IndexWrite

  代码如下：

  ```java
  /**
   * 数据对象数据
   * @author likang
   * @date   2018-5-7 上午9:08:24
   */
  public class BookEntity implements Serializable{

      private static final long serialVersionUID = 1L;


      private int id;
      private String bookname;
      private String context;
      public int getId() {
          return id;
      }
      public void setId(int id) {
          this.id = id;
      }
      public String getBookname() {
          return bookname;
      }
      public void setBookname(String bookname) {
          this.bookname = bookname;
      }
      public String getContext() {
          return context;
      }
      public void setContext(String context) {
          this.context = context;
      }
  }
  ```

- 入库

  ```java
  /**
   * 入库
   * @throws Exception 
   */
  @Test
  public void indexWriter() throws Exception{

      /**
       * 初始化对象数据
       */
      BookEntity book = new BookEntity();
      book.setId(1);
      book.setBookname("Lucene是一款搜索引擎技术");
      book.setContext("Lucene是一款很好的搜索引擎技术，产品有百度，谷歌等");

      //存储到磁盘的路径（文件夹）
      Directory directory = FSDirectory.open(new File("./index"));
      //中文分词器
      Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_30);

      //lucene核心入库类
      IndexWriter iw = new IndexWriter(directory, analyzer, MaxFieldLength.LIMITED);


      /**
       * 参数1：磁盘中关键词的编码
       * 参数2：代表编码的对象数据值
       * 参数3：代表是否支持存储，如果是yes--代表存储对象，如果是no---代表不支持存储对象
       * 参数4：
       *      NOT_ANALYZED：对象数据不支持分词，支持存储，支持索引，支持创建对象
       *      ANALYZED:对象数据支持分词，支持存储，支持索引，支持创建对象
       *      NO：不支持分词，不支持存储，不支持索引，不支持创建对象
       *      ANALYZED_NO_NORMS：对象数据支持分词，不支持创建对象，支持索引，支持存储
       *      NOT_ANALYZED_NO_NORMS：对象数据不支持分词，不支持创建对象，支持索引，支持存储
       */


      Field field1 = new Field("id", String.valueOf(book.getId()), Store.YES, Index.NOT_ANALYZED);
      Field field2 = new Field("bookname", book.getBookname(), Store.YES, Index.NO);
      Field field3 = new Field("context", book.getContext(), Store.YES, Index.ANALYZED);

      //将对象数据封装成document对象
      Document doc = new Document();//创建的对象数据
      doc.add(field1);
      doc.add(field2);
      doc.add(field3);
      //增加对象数据到索引库
      iw.addDocument(doc);
      //关闭入库流
      iw.close();
  }
  ```

- 出库

  ```java
   /**
   * 出库
   * @throws Exception 
   */
  @Test
  public void indexSerach() throws Exception{

      //搜索索引库目录
      Directory directory = FSDirectory.open(new File("./index"));
      //中文分词器
      Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_30);

      //出库核心处理类
      IndexSearcher inSearcher = new IndexSearcher(directory);

      //匹配关键词
      //QueryParser queryParser = new QueryParser(Version.LUCENE_30, "context", analyzer);
      //匹配多个关键词
  	String[] strs = {"bookname", "context"};
  	MultiFieldQueryParser queryParser = new MultiFieldQueryParser(Version.LUCENE_30, strs , analyzer);

      Query query = queryParser.parse("Lucene");

      /**
       * 参数1：搜索关键词
       * 参数2：匹配显示个数
       */
      TopDocs topdocs = inSearcher.search(query, 4);

      //出库后的对象数据--数组
      ScoreDoc[] sds = topdocs.scoreDocs;

      List<BookEntity> list = new ArrayList<BookEntity>();

      for (ScoreDoc sd : sds) {

          BookEntity book = new BookEntity();

          int index = sd.doc;//获取对象数据的下标
          Document document = inSearcher.doc(index);//根据对象下标，获取对象数据的详细内容
          book.setId(Integer.valueOf(document.get("id")));
          book.setBookname(document.get("bookname"));
          book.setContext(document.get("context"));
          list.add(book);
      }

      if (list != null && list.size() > 0) {
          for (int i = 0; i < list.size(); i++) {
              System.out.println("ID:" + list.get(i).getId());
              System.out.println("名称：" + list.get(i).getBookname());
              System.out.println("内容为：" + list.get(i).getContext());
          }
      }
  }
  ```