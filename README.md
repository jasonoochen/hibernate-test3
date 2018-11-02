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
