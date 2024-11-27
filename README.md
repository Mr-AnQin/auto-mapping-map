# 将实体类转为 Map 

### auto-mapping-map 使用方法

1.在项目中的 `.pom` 文件中添加：

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>cc.anqin</groupId>
                            <artifactId>auto-mapping-map</artifactId>
                            <version>${auto-mapping-map.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>
```
2.在需要转换的 Map 的实体类上添加注解：`@cc.anqin.processor.annotation.AutoToMap`.注意：``实体类字段必须要有 Get 方法`` 如添加 `@Data` 注解：
```java
@Data
@AutoToMap
public class User {
    
    private String name;
    
    private String age;
    
    private String gender;
}
```
4.重新编译项目工程，效果如下：
```java
@Generated(
    value = "cc.anqin.processor.MapConverterProcessor",
    date = "2024-11-27T15:34:07.645",
    comments = "Generated by MapConverterProcessor\n"
            + "Author: Mr.An\n"
            + "Introduction: https://anqin.cc/\n"
            + "Blog: https://blog.anqin.cc/"
)
public class User_MapConverter implements MappingConvert<User> {
  @Override
  public Map<String, Object> toMap(User entity) {
    Map<String, Object> map = new HashMap<>();
    map.put("name", entity.getName());
    map.put("age", entity.getAge());
    map.put("gender", entity.getGender());
    return map;
  }
}
```
5.通过`@AutoKeyMapping`进行映射和忽略，如将name 的 key 转为 username,忽略gender字段：
```java
@Data
@AutoToMap
public class User {
    
    @AutoKeyMapping(target = "username")
    private String name;

    private String age;

    @AutoKeyMapping(ignore = true)
    private String gender;
}

// 重新编译效果:
@Generated(
        value = "cc.anqin.processor.MapConverterProcessor",
        date = "2024-11-27T15:38:14.578",
        comments = "Generated by MapConverterProcessor\n"
                + "Author: Mr.An\n"
                + "Introduction: https://anqin.cc/\n"
                + "Blog: https://blog.anqin.cc/"
)
public class User_MapConverter implements MappingConvert<User> {
    @Override
    public Map<String, Object> toMap(User entity) {
        Map<String, Object> map = new HashMap<>();
        map.put("username", entity.getName());
        map.put("age", entity.getAge());
        return map;
    }
}
```
6.调用，直接使用工厂类 `ConvertMap.toMap(Object source, Class<?> clazz)` 调用，如：
```java
@Data
@AutoToMap
public class User {

    private String name;

    private String age;

    private String gender;

    public static void main(String[] args) {
        User user = new User();
        user.setName("Tom");
        user.setAge("18");
        user.setGender("男");

        Map<String, Object> dataMap = ConvertMap.toMap(user, User.class);
        System.out.println(dataMap);
    }
}
// 打印效果：{gender=男, name=Tom, age=18}
```


