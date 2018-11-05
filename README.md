1. 基于外键映射的1-1关联关系  
2. 基于主键映射的1-1关联关系   
3. 映射多对多关系  
4. 映射继承关系(subclass, joined-subclass, union-subclass)  
a. subclass  
\<!-- 映射子类 Student, 使用 subclass 进行映射 -->    
\<subclass name="Student" discriminator-value="STUDENT">  
  \<property name="school" type="string" column="SCHOOL"></property>  
\</subclass>  
 * 缺点:  
 * 1. 使用了辨别者列.  
 * 2. 子类独有的字段不能添加非空约束.  
 * 3. 若继承层次较深, 则数据表的字段也会较多.   
 * 查询:
 * 1. 查询父类记录, 只需要查询一张数据表
 * 2. 对于子类记录, 也只需要查询一张数据表
 * 插入操作: 
 * 1. 对于子类对象只需把记录插入到一张数据表中.
 * 2. 辨别者列有 Hibernate 自动维护. 
 
 b. joined-subclass  
 \<joined-subclass name="Student" table="STUDENTS">  
  \<key column="STUDENT_id"></key>  
  \<property name="school" type="string" column="SCHOOL"></property>  
\</joined-subclass>  
 * 优点:
 * 1. 不需要使用了辨别者列.
 * 2. 子类独有的字段能添加非空约束.
 * 3. 没有冗余的字段. 
 * 查询:
 * 1. 查询父类记录, 做一个左外连接查询
 * 2. 对于子类记录, 做一个内连接查询. 
 * 插入操作: 
 * 1. 对于子类对象至少需要插入到两张数据表中. 
 
 c.union-subclass  
 \<union-subclass name="Student" table="STUDENTS">  
  \<property name="school" column="SCHOOL" type="string"></property>  
\</union-subclass>  
 * 优点:
 * 1. 无需使用辨别者列.
 * 2. 子类独有的字段能添加非空约束.
 * 
 * 缺点:
 * 1. 存在冗余的字段
 * 2. 若更新父表的字段, 则更新的效率较低
 * 查询:
 * 1. 查询父类记录, 需把父表和子表记录汇总到一起再做查询. 性能稍差. 
 * 2. 对于子类记录, 也只需要查询一张数据表
 * 插入操作: 
 * 1. 对于子类对象只需把记录插入到一张数据表中.

5. 检索策略(lazy、fetch、batch-size等)(strategy)(ppt-116)  
why: 不浪费内存，更高的查询效率  
<class> 元素： 
  lazy;  
<set> 元素：
  one to many:   
  //---------------set 的 lazy 属性------------------  
  //1. 1-n 或 n-n 的集合属性默认使用懒加载检索策略.  
  //2. 可以通过设置 set 的 lazy 属性来修改默认的检索策略. 默认为 true  
  //并不建议设置为  false.   
  //3. lazy 还可以设置为 extra. 增强的延迟检索. 该取值会尽可能的延迟集合初始化的时机!  
    
  //set 元素的 batch-size 属性: 设定一次初始化 set 集合的数量.   
    
  //set 集合的 fetch 属性: 确定初始化 orders 集合的方式.   
  //1. 默认值为 select. 通过正常的方式来初始化 set 元素  
  //2. 可以取值为 subselect. 通过子查询的方式来初始化所有的 set 集合. 子查询  
  //作为 where 子句的 in 的条件出现, 子查询查询所有 1 的一端的 ID. 此时 lazy 有效. 但 batch-size 失效.   
  //3. 若取值为 join. 则  
  //3.1 在加载 1 的一端的对象时, 使用迫切左外连接(使用左外链接进行查询, 且把集合属性进行初始化)的方式检索 n 的一端的集合属性  
  //3.2 忽略 lazy 属性.  
  //3.3 HQL 查询忽略 fetch=join 的取值  
    
  many to one:
  //1. lazy 取值为 proxy 和 false 分别代表对应对应的属性采用延迟检索和立即检索  
  //2. fetch 取值为 join, 表示使用迫切左外连接的方式初始化 n 关联的 1 的一端的属性  
  //忽略 lazy 属性.   
  //3. batch-size, 该属性需要设置在 1 那一端的 class 元素中:   
  //\<class name="Customer" table="CUSTOMERS" lazy="true" batch-size="5">  
  //作用: 一次初始化 1 的这一段代理对象的个数.   
