```java
import java.util.*;

public class Main {

    private static List<Entity1> data;


    public static void init(int amount) {
        data = new ArrayList<>();
        for (int i=0; i<amount; i++) {
            Entity1 e = new Entity1();
            e.setId(String.valueOf(i+1234));
            e.setData("data "+ (i+72439));
            data.add(e);
        }
    }


    public static void test1() {
        List<String> idList = new ArrayList<>();
        for(Entity1 e: data) {
            idList.add(e.getId());
        }

        List<Entity2> entity2List = getEntity2(idList);

        for(Entity2 e2 : entity2List) {
            for(Entity1 e1 : data) {
                if(e1.getId().equals(e2.getId())) {
                    //do
                    e2.setData(e1.getData());
                }
            }
        }


    }

    public static void test2() {
        Map<String, Entity1> map = new HashMap<>();
        for(Entity1 e: data) {
            map.put(e.getId(), e);
        }

        List<Entity2> entity2List = getEntity2(map.keySet());
        for (Entity2 e2 : entity2List) {
            Entity1 e1 = map.get(e2.getId());
            e2.setData(e1.getData());
        }

    }


    public static List<Entity2> getEntity2(Collection<String> idList) {
        //模拟使用ids从数据库查询数据
        List<Entity2> list = new ArrayList<>();
        for(String id : idList) {
            Entity2 e2 = new Entity2();
            e2.setId(id);
            e2.setData("data" + id);
            list.add(e2);
        }
        return list;
    }

    public static void main(String[] args) {
        init(100000);
        long test1, test2;
        long time = 0;
        System.out.println("----------------------------------test1");
        time = System.currentTimeMillis();
        test1();
        test1 = System.currentTimeMillis()-time;

        System.out.println("----------------------------------test2");
        time = System.currentTimeMillis();
        test2();
        test2 = System.currentTimeMillis()-time;

        System.out.println( test1 + "    :    " + test2);



    }
}


class Entity1 {
    private String id;
    private String data;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }
}

class Entity2  {

    private String id;
    private String data;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }
}

```

| 优化time | 未优化time | 数据量 |
| :------: | :--------: | :----: |
|    1     |     1      |   1    |
|    1     |     4      |   10   |
|    1     |     8      |  100   |
|    2     |     34     |  1000  |
|    14    |    1217    | 10000  |
|    69    |   147226   | 100000 |
|          |            |        |
|          |            |        |
|          |            |        |
|          |            |        |

