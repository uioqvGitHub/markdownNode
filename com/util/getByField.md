```java
public <T> List<T> getByField(Class<T> cla, T obj) {
    if (obj == null) {
        throw new OptimusParamMissException("缺少参数");
    }
    Map<String, Object> map = ObjectUtils.getObjectNotNullFieldToColumn(obj);

    QueryObj query = null;
    try {
        T stu = cla.newInstance();
        query = new QueryObj(stu);
    } catch (Exception e) {
        log.error("创建实例失败请检查参数类型是否存在无参构造", e);
    }

    for(Map.Entry<String, Object> entry : map.entrySet()) {
        StringBuilder sql = new StringBuilder();
        sql.append(entry.getKey()).append("=#{")
                .append(entry.getKey()).append("}");
        query.addQuery(sql.toString(), entry.getValue());
    }
    List<T> listObject = basicDao.findListByQuery(
            query, cla);
    return listObject;
}
```

