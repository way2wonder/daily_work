1.  上报审核做成公共的，如何映射？ 仅仅是type不一样而已
2. 第二章start a  project 没仔细看


###  domain models && metadata
en:  
   - Caveat  警告
   - Emptor  买者
   - auction  拍卖
   - layered  分层的


jpa2  可以将validator 和 pojo 绑定在一起

validator：确保持久信息和业务信息的完整和正确性




A layered architecture 分层架构
   - Persistence
   - presentation
   - workflow
   - business logic
Typical cross-cutting（横切的层） concerns include 
     - logging, 
     - authorization
     - transaction demarcation

分层结构的特性
   - Layers communicate from top to bottom. A layer is dependent only on the
interface of the layer directly below it.
   - Each layer is unaware of any other layers except for the layer just below it





1. JPA doesn’t require that persistent classes implement java.io.Serializable
when instances are stored in an HttpSession or
passed by value using RMI, serialization is necessary. 

2. The persistence-capable class and any of its methods can’t be final (a requirement of the JPA specification).  可序列话的类和其中任意方法都不能是final， JAP spec

3. hibernate && JPA 都需要一个空的构造器（constructor）
4.  wonderful  name = firstname+lastname
```java
public class User {
protected String firstname;
protected String lastname;
public String getName() {
return firstname + ' ' + lastname;
}
public void setName(String name) {
StringTokenizer t = new StringTokenizer(name);
firstname = t.nextToken();
lastname = t.nextToken();
}
}
```

5. There is one important exception to this: collections are compared by identity!    这种情况hibernate 无论如何都会update。 如果hibernate是直接访问这个那么就不会出现这种问题  
```java
protected String[] names = new String[0];
public void setNames(List<String> names) {
this.names = names.toArray(new String[names.size()]);
}
public List<String> getNames() {
return Arrays.asList(names);
}
```

####3.2.4 Implementing POJO associations  pojo间的关联关系
1. relationships
      - one-to-many
      - many-to-one
      - bidirectional  （双向的）



Next, we’ll increase the level of 
1. abstraction,
2. adding metadata to the domain model implementation and
3. declaring aspects such as validation and persistence rules.


### 3.3 Domain model metadata
元数据
1. JPA 有两种方式 annotation   or   xml
2. Bean Validation (JSR 303)                                     *这里需要自己实践一下*
3. Externalizing metadata with XML files
    + XML METADATA WITH JPA
    + HIBERNATE XML MAPPING FILES
4. Accessing metadata at runtime  运行时获取
    -  java的反射
    -  java 6的annotation processor.

The code in the next listing shows how to read metadata with Java
Persistence interfaces.   获取metadata
```java
Metamodel mm = entityManagerFactory.getMetamodel();
Set<ManagedType<?>> managedTypes = mm.getManagedTypes();
assertEquals(managedTypes.size(), 1);
ManagedType itemType = managedTypes.iterator().next();
assertEquals(
itemType.getPersistenceType(),
Type.PersistenceType.ENTITY
);
```

5. java commandl-line tool :apt( the annotation processing tool)



## Mapping strategies (映射策略)
###Target: 
1. Understanding entities and value type concepts
2. Mapping entity classes with identity
3. Controlling entity-level mapping options. 掌握类级别的映射选项



###Understanding entities and value types
1. Fine-grained domain models  细粒度
2. Defining application concepts 
    - You can retrieve an instance of entity type using its persistent identity
    - An instance of value type has no persistent identifier property; it belongs to an entity instance
But value types in JPA  are called basic property types or embeddable classes.(值类型在jpa中称为基本属性类型或者内置类型)
3. Distinguishing entities and value types。  区别实体和值类型



####3 things：
1. Shared references—Avoid shared references to value type instances when you
write your POJO classes. （避免共享值类型的引用）
2. Life cycle dependencies—If a User is deleted, its Address dependency has to be deleted as well.  (生命周期依赖。）
3. Identity—Entity classes need an identifier property in almost all cases. （主键）


###Mapping entities with identity
it’s vital（至关重要的） to understand the difference between Java object identity and object equality before we discuss terms like database identity and the way JPA manages identity


1. Understanding Java identity and equality
2. A first entity class and mapping。   @Id
3. Selecting a primary key
4. Configuring key generators
   - GenerationType.AUTO
   - GenerationType.SEQUENCE （通过序列生成）
   - GenerationType.IDENTITY（generates a numeric value on INSERT, ）
   - GenerationType.TABLE
5. Identifier generator strategies  *这里需要后面研究下，很多策略*

hibernate 不允许修改主键 a  candidate key
> You should declare candidate keys not chosen as the primary key as unique
keys in the database if their value is indeed unique (but maybe not immutable). 必须既是唯一的，也是不可变的。  immutable


>A good primary key must be unique, immutable, and never null. 

```java
    @Id
    @GenericGenerator(name = "hibernate-uuid", strategy = "uuid")
    @GeneratedValue(generator = "hibernate-uuid")
```


###Entity-mapping options

1. Naming defaults and strategies
2. Dynamic SQL generation
3. Entity mutability

  
#### QUOTING SQL IDENTIFIERS(SQL 保留字)
You can enable this automatic quoting with hibernate.auto_quote
_keyword=true in your persistence unit configuration.
`@Table(name = "`USER`")`
`@Table(name = "\"USER\"")` JPA like

可以加前缀自动在前面加上‘CE_’

```java
public class CENamingStrategy extends
org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl {
@Override
public Identifier toPhysicalTableName(Identifier name,
JdbcEnvironment context) {
return new Identifier("CE_" + name.getText(), name.isQuoted());
}
}
```
You have to enable the naming-strategy implementation in persistence.xml:

```xml
<persistence-unit name="CaveatEmptorPU">
<properties>
<property name="hibernate.physical_naming_strategy"
value="org.jpwh.shared.CENamingStrategy"/>
</properties>
</persistence-unit>
```






























###### webservice 中的数据绑定

