# gson 使用小结

## 序列化和反序列化

### 概念

把对象转换为字节序列的过程称为对象的序列化。<br>
把字节序列恢复为对象的过程称为对象的反序列化。

### API

当一个类实现 Serializable 接口，则可以通过 ObjectOutputStream 的 writeObject 方法实现对象的序列化；

```java
public final void writeObject(Object obj) throws IOException;
```

通过 ObjectInputStream 的 readObject 方法实现对象的反序列化。

```java
public final Object readObject()
            throws IOException, ClassNotFoundException;
```

### 关键字 transient

当一个实例被持久化时,其内部的一些域却不需要持久化,则可以用 trainsient 修饰符告诉编译器指定的域不需要被持久保存，在反序列化时，这样的域也不会被恢复。

### 控制序列化和反序列化

> Classes that require special handling during the serialization and deserialization process must implement special methods with these exact signatures:<br>
> 在序列化和反序列化中需要特殊处理的类必须实现有着确切签名的特定方法：

```java
private void writeObject(java.io.ObjectOutputStream out)
    throws IOException;
private void readObject(java.io.ObjectInputStream in)
    throws IOException, ClassNotFoundException;
private void readObjectNoData()
    throws ObjectStreamException;
```

writeObject 和 readObject 必须成对出现，写入的顺序和读出的顺序也必须相同。

### serialVersionUID

> 当实现 java.io.Serializable 接口的实体（类）没有显式地定义一个名为 serialVersionUID ，类型为long 的变量时，Java序列化机制会根据编译的 class (它通过类名，方法名等诸多因素经过计算而得，理论上是一一映射的关系，也就是唯一的)自动生成一个 serialVersionUID 作序列化版本比较用，这种情况下，如果class 文件(类名,方法名等)没有发生变化(增加空格,换行,增加注释,等等),就算再编译多次,serialVersionUID 也不会变化的。<br>
> Java 的序列化机制是通过在运行时判断类的 serialVersionUID 来验证版本一致性的。在进行反序列化时，JVM 会把传来的字节流中的 serialVersionUID 与本地相应实体（类）的 serialVersionUID 进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。

--------------------------------------------------------------------------------

> 显式地定义serialVersionUID有两种用途：<br>
> 1）在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的 serialVersionUID；在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的 serialVersionUID。<br>
> 2）当你序列化了一个类实例后，希望更改一个字段或添加一个字段，不设置 serialVersionUID，所做的任何更改都将导致无法反序化旧有实例，并在反序列化时抛出一个异常。如果你添加了 serialVersionUID，在反序列旧有实例时，新添加或更改的字段值将设为初始化值（对象为 null，基本类型为相应的初始默认值），字段被删除将不设置。

## Gson 的使用

### @Expose

- 在需要导出的字段上添加 @Expose 注解；
- 使用 GsonBuilder 生成 Gson 实例；

  ```java
  Gson gson = new GsonBuilder()
        .excludeFieldsWithoutExposeAnnotation()
        .create();
  gson.toJson(category);
  ```

### @SerializedName

由于命名方式不统一，造成命名规范的不一致。 @SerializedName("name") 可以为成员变量起别名。

```java
import com.google.gson.Gson;
import com.google.gson.annotations.SerializedName;

public class GsonTestModel {
    @SerializedName("age")
    private int mAge ;
    @SerializedName("name")
    private String mName;
    @SerializedName("gender")
    private String mGender;
    @SerializedName("id")
    private String mId;

    public int getAge() {
        return mAge;
    }
    public void setAge(int age) {
        mAge = age;
    }
    public String getName() {
        return mName;
    }
    public void setName(String name) {
        mName = name;
    }
    public String getGender() {
        return mGender;
    }
    public void setGender(String gender) {
        mGender = gender;
    }
    public String getId() {
        return mId;
    }
    public void setId(String id) {
        mId = id;
    }

    public String toJson(){
        Gson gson = new Gson();
        return gson.toJson(this);
    }


    @Override
    public String toString() {
        return "GsonTestModel [mAge=" + mAge + ", mName=" + mName + ", mGender=" + mGender + ", mId=" + mId + "]";
    }
    public static GsonTestModel parseJson(String json) {
        Gson gson = new Gson();
        return gson.fromJson(json, GsonTestModel.class);
    }

    public static void main(String[] args) {
        GsonTestModel model = new GsonTestModel();
        model.setAge(10);
        model.setGender("男");
        model.setId("0901101416");
        model.setName("李小六");
        String json = model.toJson();
        System.out.println(json);

        GsonTestModel gsonTestModel = parseJson(json);
        System.out.println(gsonTestModel.toString());
    }
}
```

## 参考链接

- [Java 对象的序列化和反序列化](http://www.cnblogs.com/xdp-gacl/p/3777987.html)
- [Java Doc - Interface Serializable](https://docs.oracle.com/javase/7/docs/api/java/io/Serializable.html)
- [serialVersionUID 的作用](http://www.cnblogs.com/guanghuiqq/archive/2012/07/18/2597036.html)
- [serialVersionUID 作用](http://blog.csdn.net/dancen/article/details/7236575)
- [gson 字段过滤](http://www.jianshu.com/p/0e40a52c0063)
- [技巧----gson之实体对应的字段取别名](http://blog.csdn.net/bzy601638015/article/details/32916281)
