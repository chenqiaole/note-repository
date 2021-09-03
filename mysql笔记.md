# MySQL分区

## 分区类型

###  **RANGE** **分区**

> 基于属于一个给定连续区间的列值，把多行分配给分区,按照RANGE分区的表是通过如下一种方式进行分区的，每个分区包含那些分区表达式的值位于一个给定的**连续区间**内的行。这些区间要**连续且不能相互重叠**，使用**VALUES LESS THAN**操作符来进行定义。

#### 实例

在下面的几个例子中，假定你创建了一个如下的一个表，该表保存有20家音像店的职员记录，这20家音像店的编号从1到20。

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)；
```

##### 根据store_id列分区

根据你的需要，这个表可以有多种方式来按照区间进行分区。一种方式是使用store_id 列。例如，你可能决定通过添加一个PARTITION BY RANGE子句把这个表分割成4个区间，如下所示:

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)
PARTITION BY RANGE (store_id) (
    PARTITION p0 VALUES LESS THAN (6),
    PARTITION p1 VALUES LESS THAN (11),
    PARTITION p2 VALUES LESS THAN (16),
    PARTITION p3 VALUES LESS THAN (21)
)；
```

按照这种分区方案，在商店1到5工作的雇员相对应的所有行被保存在分区P0中，商店6到10的雇员保存在P1中，依次类推。注意，每个分区都是按顺序进行定义，从最低到最高。这是PARTITION BY RANGE 语法的要求；在这点上，它类似于C或Java中的“switch ... case”语句。

对于包含数据(72, 'Michael', 'Widenius', '1998-06-25', NULL, 13)的一个新行，可以很容易地确定它将插入到p2分区中，但是如果增加了一个编号为第21的商店，将会发生什么呢？在这种方案下，由于没有规则把store_id大于20的商店包含在内，服务器将不知道把该行保存在何处，将会导致错误

 要避免这种错误，可以通过在CREATE TABLE语句中使用一个“catchall” VALUES LESS THAN子句，该子句提供给所有大于明确指定的最高值的值：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)
PARTITION BY RANGE (store_id) (
    PARTITION p0 VALUES LESS THAN (6),
    PARTITION p1 VALUES LESS THAN (11),
    PARTITION p2 VALUES LESS THAN (16),
    PARTITION p3 VALUES LESS THAN MAXVALUE
)；
```

MAXVALUE 表示最大的可能的整数值。现在，store_id 列值大于或等于16（定义了的最高值）的所有行都将保存在分区p3中。在将来的某个时候，当商店数已经增长到25, 30, 或更多 ，可以使用ALTER TABLE语句为商店21-25, 26-30,等等增加新的分区

##### 根据job_code 列值的连续区间

在几乎一样的结构中，你还可以基于雇员的工作代码来分割表，也就是说，基于job_code 列值的连续区间。例如——假定2位数字的工作代码用来表示普通（店内的）工人，三个数字代码表示办公室和支持人员，四个数字代码表示管理层，你可以使用下面的语句创建该分区表：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)
PARTITION BY RANGE (job_code) (
    PARTITION p0 VALUES LESS THAN (100),
    PARTITION p1 VALUES LESS THAN (1000),
    PARTITION p2 VALUES LESS THAN (10000)
)；
```

在这个例子中, 店内工人相关的所有行将保存在分区p0中，办公室和支持人员相关的所有行保存在分区p1中，管理层相关的所有行保存在分区p2中。

##### 使用表达式分区

在VALUES LESS THAN 子句中使用一个表达式也是可能的。这里最值得注意的限制是MySQL 必须能够计算表达式的返回值作为LESS THAN (<)比较的一部分；因此，表达式的值不能为NULL 。由于这个原因，雇员表的hired, separated, job_code,和store_id列已经被定义为非空（NOT NULL）。

除了可以根据商店编号分割表数据外，你还可以使用一个基于两个DATE （日期）中的一个的表达式来分割表数据。例如，假定你想基于每个雇员离开公司的年份来分割表，也就是说，YEAR(separated)的值。实现这种分区模式的CREATE TABLE 语句的一个例子如下所示：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY RANGE (YEAR(separated)) (
    PARTITION p0 VALUES LESS THAN (1991),
    PARTITION p1 VALUES LESS THAN (1996),
    PARTITION p2 VALUES LESS THAN (2001),
    PARTITION p3 VALUES LESS THAN MAXVALUE
)；
```

在这个方案中，在1991年前雇佣的所有雇员的记录保存在分区p0中，1991年到1995年期间雇佣的所有雇员的记录保存在分区p1中， 1996年到2000年期间雇佣的所有雇员的记录保存在分区p2中，2000年后雇佣的所有工人的信息保存在p3中。

#### 使用场合

RANGE分区在如下场合特别有用：

>  	当需要删除“旧的”数据时。如果你使用上面最近的那个例子给出的分区方案，你只需简单地使用 “ALTER TABLE employees DROP PARTITION p0；”来删除所有在1991年前就已经停止工作的雇员相对应的所有行。(更多信息请参见13.1.2节，“ALTER TABLE语法” 和 18.3节，“分区管理”）。对于有大量行的表，这比运行一个如“DELETE FROM employees WHERE YEAR(separated) <= 1990；”这样的一个DELETE查询要有效得多。
>
> ​	想要使用一个包含有日期或时间值，或包含有从一些其他级数开始增长的值的列。
>
> ​	**经常运行直接依赖于用于分割表的列的查询**。例如，当执行一个如“SELECT COUNT(*) FROM employees WHERE YEAR(separated) = 2000 GROUP BY store_id；”这样的查询时，MySQL可以很迅速地确定只有分区p2需要扫描，这是因为余下的分区不可能包含有符合该WHERE子句的任何记录。**注释**：这种优化还没有在MySQL 5.1源程序中启用，但是，有关工作正在进行中。

### **RANGE COLUMNS分区**

RANGE COLUMNS是RANGE分区的一种特殊类型，它与RANGE分区的区别如下：

1. RANGE COLUMNS不接受表达式，只能是列名。而RANGE分区则要求分区的对象是整数。

2. RANGE COLUMNS允许多个列，在底层实现上，它比较的是元祖（多个列值组成的列表），而RANGE比较的是标量，即数值的大小。

3. RANGE COLUMNS不限于整数对象，date，datetime，string都可作为分区列。

   ```mysql
   CREATE TABLE rcx (
       a INT,
       b INT,
       c CHAR(3),
       d INT
   )
   PARTITION BY RANGE COLUMNS(a,d,c) (
       PARTITION p0 VALUES LESS THAN (5,10,'ggg'),
       PARTITION p1 VALUES LESS THAN (10,20,'mmmm'),
       PARTITION p2 VALUES LESS THAN (15,30,'sss'),
       PARTITION p3 VALUES LESS THAN (MAXVALUE,MAXVALUE,MAXVALUE)
   );
   ```

   同RANGE分区类似，它的区间范围必须是递增的，有时候，列涉及的太多，不好判断区间的大小，可采用下面的方式进行判断：

   ```mysql
   mysql> SELECT (5,10) < (5,12), (5,11) < (5,12), (5,12) < (5,12);
   +-----------------+-----------------+-----------------+
   | (5,10) < (5,12) | (5,11) < (5,12) | (5,12) < (5,12) |
   +-----------------+-----------------+-----------------+
   |               1 |               1 |               0 |
   +-----------------+-----------------+-----------------+
   1 row in set (0.07 sec)
   ```

   

### LIST分区

​	和按照RANGE分区一样，每个分区必须明确定义。它们的主要区别在于，LIST分区中每个分区的定义和选择是基于某列的值从属于**一个值列表集中的一个值**，而RANGE分区是从属于一个**连续区间值**的集合。LIST分区通过使用“PARTITION BY LIST(*expr*)”来实现，其中“*expr”* 是某列值或一个基于某个列值、并返回一个整数值的表达式，然后通过“VALUES IN (*value_list*)”的方式来定义每个分区，其中“*value_list*”是一个**通过逗号分隔的整数列表**。

#### 实例

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)；
```

##### 根据地区区分商店

![image-20200720105838966](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720105839.png)



要按照属于**同一个地区商店的行**保存**在同一个分区**中的方式来分割表，可以使用下面的“CREATE TABLE”语句

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY LIST(store_id)
    PARTITION pNorth VALUES IN (3,5,6,9,17),
    PARTITION pEast VALUES IN (1,2,10,11,19,20),
    PARTITION pWest VALUES IN (4,12,13,14,18),
    PARTITION pCentral VALUES IN (7,8,15,16)
)；
```

这使得在表中增加或删除指定地区的雇员记录变得容易起来

> 例如，假定西区的所有音像店都卖给了其他公司。那么与在**西区音像店工作雇员相关的所有记录**（行）可以使用查询“ALTER TABLE employees DROP PARTITION pWest；”来进行删除，它与具有同样作用的DELETE （删除）查询“DELETE query DELETE FROM employees WHERE store_id IN (4,12,13,14,18)；”比起来，要有效得多。

**要点**：如果试图插入列值（或分区表达式的返回值）不在分区值列表中的一行时，那么“INSERT”查询将失败并报错。例如，假定LIST分区的采用上面的方案，下面的查询将失败：

```mysql
INSERT INTO employees VALUES 
    (224, 'Linus', 'Torvalds', '2002-05-01', '2004-10-12', 42, 21);
```

这是因为“store_id”列值21不能在用于定义分区pNorth, pEast, pWest,或pCentral的值列表中找到。要重点注意的是，LIST分区没有类似如“VALUES LESS THAN MAXVALUE”这样的包含其他值在内的定义。**将要匹配的任何值都必须在值列表中找到**。

LIST分区除了能和RANGE分区结合起来生成一个**复合的子分区**，与HASH和KEY分区结合起来生成复合的子分区也是可能的。

##### 分区查询

![image-20200722113738752](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722113738.png)



### HASH分区

#### 定义

HASH分区主要用来确保数据在**预先确定数目的分区中平均分布**。在RANGE和LIST分区中，必须明确指定一个给定的列值或列值集合应该保存在哪个分区中；而在HASH分区中，MySQL 自动完成这些工作，你所要做的只是**基于将要被哈希的列值指定一个列值或表达式**，以及指定被分区的表将要被**分割成的分区数量**。

#### 步骤

要使用HASH分区来分割一个表，要在CREATE TABLE 语句上添加一个“PARTITION BY HASH (*expr*)”子句，其中**“*expr*”是一个返回一个整数的表达式**。它可以仅仅是字段类型为MySQL 整型的一列的名字。此外，你很可能需要在后面**再添加一个“PARTITIONS *num*”子句**，其中*num* 是一个非负的整数，它表示表将要被分割成分区的数量。

#### 实例

例如，下面的语句创建了一个使用基于“store_id”列进行 哈希处理的表，该表被分成了4个分区：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY HASH(store_id)
PARTITIONS 4；
```

如果没有包括一个PARTITIONS子句，那么分区的数量将默认为1。

**例外：** 对于NDB Cluster（簇）表，默认的分区数量将与簇数据节点的数量相同，这种修正可能是考虑任何MAX_ROWS 设置，以便确保所有的行都能合适地插入到分区中。

如果在关键字“PARTITIONS”后面没有加上分区的数量，将会出现语法错误。

“*expr*”还可以是一个返回一个整数的SQL表达式。

例如，也许你想基于雇用雇员的年份来进行分区。这可以通过下面的语句来实现：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY HASH(YEAR(hired))
PARTITIONS 4；
```

“*expr*”还可以是MySQL 中有效的任何函数或其他表达式，只要它们返回一个既非常数、也非随机数的整数。（换句话说，**它既是变化的但又是确定的**）。但是应当记住，每当**插入或更新**（或者可能删除）一行，这个**表达式都要计算一次**；这意味着**非常复杂的表达**式可能会引起性能问题，尤其是在执行同时**影响大量行的运算**（例如批量插入）的时候。

最有效率的哈希函数是只对单个表列进行计算，并且它的值随列值进行一致地增大或减小，因为这考虑了在分区范围上的“修剪”。也就是说，表达式值和它所基于的列的值变化越接近，MySQL就可以越有效地使用该表达式来进行HASH分区。

例如，“date_col” 是一个DATE（日期）类型的列，那么表达式TO_DAYS(date_col)就可以说是随列“date_col”值的变化而发生直接的变化，因为列“date_col”值的每个变化，**表达式的值也将发生与之一致的变化**。而表达式YEAR(date_col)的变化就没有表达式TO_DAYS(date_col)那么直接，因为不是列“date_col”每次可能的改变都能使表达式YEAR(date_col)发生同等的改变。即便如此，表达式YEAR(date_col)也还是一个用于 哈希函数的、好的候选表达式，**因为它随列date_col的一部分发生直接变化**，并且列date_col的变化不可能引起表达式YEAR(date_col)不成比例的变化

作为对照，假定有一个类型为整型（INT）的、列名为“int_col”的列。现在考虑表达式“POW(5-int_col,3) + 6”。这对于哈希函数就是一个不好的选择，因为“int_col”值的变化并不能保证表达式产生成比例的变化。列 “int_col”的值发生一个给定数目的变化，可能会引起表达式的值产生一个很大不同的变化。例如，把列“int_col”的值从5变为6，表达式的值将产生“－1”的改变，但是把列“int_col”的值从6变为7时，表达式的值将产生“－7”的变化。

换句话说，如果列值与表达式值之比的曲线图越接近由等式“y=*n*x（其中n为非零的常数）描绘出的直线，则**该表达式越适合于 哈希**。这是因为，表达式的**非线性越严重**，分区中数据产生**非均衡分布**的趋势也将越严重。



理论上讲，对于涉及到多列的表达式，“修剪（pruning）”也是可能的，但是要确定哪些适于 哈希是非常困难和耗时的。基于这个原因，实际上不推荐使用涉及到多列的哈希表达式。

当使用了“PARTITION BY HASH”时，MySQL将基于用户函数结果的模数来确定使用哪个编号的分区。换句话，对于一个表达式“*expr*”，将要保存记录的分区编号为N ，其中“*N* = MOD(*expr*, *num*)”。例如，假定表t1 定义如下，它有4个分区：

```mysql
CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATE)
    PARTITION BY HASH( YEAR(col3) )
    PARTITIONS 4；
```

如果插入一个col3列值为'2005-09-15'的记录到表t1中，那么保存该条记录的分区确定如下：

```mysql
MOD(YEAR('2005-09-01'),4)
=  MOD(2005,4)
=  1
```

每当插入或更新一条记录，用户函数都要计算一次。当删除记录时，用户函数也可能要进行计算，这取决于所处的环境。

**注释**：如果将要分区的表有一个唯一的键，那么用来作为HASH用户函数的自变数或者主键的*column_list*的自变数的任意列都必须是那个键的一部分。

#### LINEAR HASH分区

MySQL还支持线性哈希功能，它与常规哈希的区别在于，线性哈希功能使用的一个线性的2的幂（powers-of-two）运算法则，而常规 哈希使用的是求哈希函数值的模数。

线性哈希分区和常规哈希分区在语法上的唯一区别在于，在“PARTITION BY” 子句中添加“LINEAR”关键字，如下面所示：

```mysql
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY LINEAR HASH(YEAR(hired))
PARTITIONS 4；
```

##### 分区算法：

假设一个表达式*expr*, 当使用线性哈希功能时，记录将要保存到的分区是*num* 个分区中的分区N，其中N是根据下面的算法得到：

> 1. 找到下一个大于*num*.的、2的幂，我们把这个值称为*V* ，它可以通过下面的公式得到：
> 2. V = POWER(2, CEILING(LOG(2, num)))
>
> （例如，假定num是13。那么LOG(2,13)就是3.7004397181411。 CEILING(3.7004397181411)就是4，则*V* = POWER(2,4), 即等于16）。
>
> 3. 设置 *N* = *F*(*column_list*) & (*V* - 1).
>
> 4. 当 *N* >= *num*:
>
>    设置 *V* = CEIL(*V* / 2)
>
>    设置 *N* = *N* & (*V* - 1)

例如，假设表t1，使用线性哈希分区且有4个分区，是通过下面的语句创建的：

```mysql
CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATE)
    PARTITION BY LINEAR HASH( YEAR(col3) )
    PARTITIONS 6;
```

![image-20200720112808239](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720112808.png)

##### 优缺点

按照线性哈希分区的优点在于增加、删除、合并和拆分分区将变得更加快捷，有利于处理含有极其大量（1000吉）数据的表。它的缺点在于，与使用常规HASH分区得到的数据分布相比，各个分区间数据的分布不大可能均衡。

### KEY分区

按照KEY进行分区**类似于按照HASH分区**，除了HASH分区使用的用户定义的表达式，而KEY分区的 哈希函数是由MySQL 服务器提供。MySQL 簇（Cluster）使用函数MD5()来实现KEY分区；对于使用其他存储引擎的表，**服务器使用其自己内部的 哈希函数**，这些函数是基于与PASSWORD()一样的运算法则。

“CREATE TABLE ... PARTITION BY KEY”的语法规则类似于创建一个通过HASH分区的表的规则。它们唯一的区别在于使用的**关键字是KEY而不是HASH**，并且KEY分区只采用一个或多个列名的一个列表。

通过线性KEY分割一个表也是可能的。下面是一个简单的例子：

```
CREATE TABLE tk (
    col1 INT NOT NULL,
    col2 CHAR(5),
    col3 DATE
) 
PARTITION BY LINEAR KEY (col1)
PARTITIONS 3;
```

在KEY分区中**使用关键字LINEAR**和在HASH分区中使用具有同样的作用，**分区的编号是通过2的幂（powers-of-two）算法得到**，而不是通过模数算法。

### 子分区

子分区是分区表中**每个分区的再次分割**。

例如，考虑下面的CREATE TABLE 语句：

```mysql
CREATE TABLE ts (id INT, purchased DATE)
    PARTITION BY RANGE(YEAR(purchased))
    SUBPARTITION BY HASH(TO_DAYS(purchased))
    SUBPARTITIONS 2
    (
        PARTITION p0 VALUES LESS THAN (1990),
        PARTITION p1 VALUES LESS THAN (2000),
        PARTITION p2 VALUES LESS THAN MAXVALUE
    )；
```

表ts 有3个RANGE分区。这3个分区中的每一个分区——p0, p1, 和 p2 ——又被进一步分成了2个子分区。实际上，**整个表被分成了3 * 2 = 6个分区**。但是，由于PARTITION BY RANGE子句的作用，这些分区的头2个**只保存“purchased”列中值小于1990的那些记录**。

在MySQL 5.1中，对于已经通过RANGE或LIST分区了的表再进行子分区是可能的。子分区既可以使用HASH希分区，也可以使用KEY分区。这也被称为**复合分区**（*composite partitioning*）。

为了对个别的子分区指定选项，使用SUBPARTITION 子句来明确定义子分区也是可能的。例如，创建在前面例子中给出的同一个表的、一个更加详细的方式如下：

```mysql
CREATE TABLE ts (id INT, purchased DATE)
    PARTITION BY RANGE(YEAR(purchased))
    SUBPARTITION BY HASH(TO_DAYS(purchased))
    (
        PARTITION p0 VALUES LESS THAN (1990)
        (
            SUBPARTITION s0,
            SUBPARTITION s1
        ),
        PARTITION p1 VALUES LESS THAN (2000)
        (
            SUBPARTITION s2,
            SUBPARTITION s3
        ),
        PARTITION p2 VALUES LESS THAN MAXVALUE
        (
            SUBPARTITION s4,
            SUBPARTITION s5
        )
    );
```

##### 注意的语法：

1、每个分区必须有**相同数量**的子分区。

2、如果在**一个分区表**上的任何分区上使用SUBPARTITION 来明确**定义任何子分区**，那么就必须**定义所有的子分区**。换句话说，下面的语句将执行失败：

```mysql
CREATE TABLE ts (id INT, purchased DATE)
·                    PARTITION BY RANGE(YEAR(purchased))
·                    SUBPARTITION BY HASH(TO_DAYS(purchased))
·                    (
·                        PARTITION p0 VALUES LESS THAN (1990)
·                        (
·                            SUBPARTITION s0,
·                            SUBPARTITION s1
·                        ),
·                        PARTITION p1 VALUES LESS THAN (2000),
·                        PARTITION p2 VALUES LESS THAN MAXVALUE
·                        (
·                            SUBPARTITION s2,
·                            SUBPARTITION s3
·                        )
·                    )；
```

即便这个语句包含了一个SUBPARTITIONS 2子句，但是它仍然会执行失败。

3、每个SUBPARTITION 子句**必须包括 (至少)子分区的一个名字**。否则，你可能要对该子分区设置任何你所需要的选项，或者允许该子分区对那些选项采用其默认的设置。

在每个分区内，子分区的名字必须是唯一的，但是在整个表中，没有必要保持唯一。例如，下面的CREATE TABLE 语句是有效的：

![image-20200720162551746](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720162552.png)

子分区可以用于特别大的表，在多个磁盘间分配数据和索引。假设有6个磁盘，分别为/disk0， /disk1， /disk2等。现在考虑下面的例子：

```mysql
CREATE TABLE ts (id INT, purchased DATE)
    PARTITION BY RANGE(YEAR(purchased))
    SUBPARTITION BY HASH(TO_DAYS(purchased))
    (
        PARTITION p0 VALUES LESS THAN (1990)
        (
            SUBPARTITION s0 
                DATA DIRECTORY = '/disk0/data' 
                INDEX DIRECTORY = '/disk0/idx',
            SUBPARTITION s1 
                DATA DIRECTORY = '/disk1/data' 
                INDEX DIRECTORY = '/disk1/idx'
        ),
        PARTITION p1 VALUES LESS THAN (2000)
        (
            SUBPARTITION s0 
                DATA DIRECTORY = '/disk2/data' 
                INDEX DIRECTORY = '/disk2/idx',
            SUBPARTITION s1 
                DATA DIRECTORY = '/disk3/data' 
                INDEX DIRECTORY = '/disk3/idx'
        ),
        PARTITION p2 VALUES LESS THAN MAXVALUE
        (
            SUBPARTITION s0 
                DATA DIRECTORY = '/disk4/data' 
                INDEX DIRECTORY = '/disk4/idx',
            SUBPARTITION s1 
                DATA DIRECTORY = '/disk5/data' 
                INDEX DIRECTORY = '/disk5/idx'
        )
    )；
```

在这个例子中，每个RANGE分区的数据和索引都使用一个单独的磁盘。还可能有许多其他的变化；下面是另外一个可能的例子：

```mysql
CREATE TABLE ts (id INT, purchased DATE)
    PARTITION BY RANGE(YEAR(purchased))
    SUBPARTITION BY HASH(TO_DAYS(purchased))
    (
        PARTITION p0 VALUES LESS THAN (1990)
        (
            SUBPARTITION s0a 
                DATA DIRECTORY = '/disk0' 
                INDEX DIRECTORY = '/disk1',
            SUBPARTITION s0b 
                DATA DIRECTORY = '/disk2' 
                INDEX DIRECTORY = '/disk3'
        ),
        PARTITION p1 VALUES LESS THAN (2000)
        (
            SUBPARTITION s1a 
                DATA DIRECTORY = '/disk4/data' 
                INDEX DIRECTORY = '/disk4/idx',
            SUBPARTITION s1b 
                DATA DIRECTORY = '/disk5/data' 
                INDEX DIRECTORY = '/disk5/idx'
        ),
        PARTITION p2 VALUES LESS THAN MAXVALUE
        (
            SUBPARTITION s2a,
            SUBPARTITION s2b
        )
    )；
```

在这个例子中，存储的分配如下：

·     购买日期在**1990年前**的记录**占了大量的存储空间**，所以把它**分为了四个部分进行存储**，组成p0分区的两个子分区（s0a 和s0b）的数据和索引都分别用一个**单独的磁盘进行存储**。换句话说：

o    子分区s0a 的数据保存在磁盘/disk0中。

o    子分区s0a 的索引保存在磁盘/disk1中。

o    子分区s0b 的数据保存在磁盘/disk2中。

o    子分区s0b 的索引保存在磁盘/disk3中。

·     保存购买日期从1990年到1999年间的记录（分区p1）不需要保存购买日期在1990年之前的记录那么大的存储空间。这些记录分在2个磁盘（/disk4和/disk5）上保存，而不是4个磁盘：

o    属于分区p1的第一个子分区（s1a）的数据和索引保存在磁盘/disk4上 — 其中数据保存在路径/disk4/data下，索引保存在/disk4/idx下。

o    属于分区p1的第二个子分区（s1b）的数据和索引保存在磁盘/disk5上 — 其中数据保存在路径/disk5/data下，索引保存在/disk5/idx下。

·     保存购买日期从2000年到现在的记录（分区p2）不需要前面两个RANGE分区那么大的空间。当前，在默认的位置能够足够保存所有这些记录。

将来，如果从2000年开始后十年购买的数量已经达到了默认的位置不能够提供足够的保存空间时，相应的记录（行）可以通过使用“ALTER TABLE ... REORGANIZE PARTITION”语句移动到其他的位置



## MySQL分区处理NULL值的方式

MySQL 中的分区在禁止空值（NULL）上没有进行处理，无论它是一个列值还是一个用户定义表达式的值。一般而言，在这种情况下MySQL 把NULL视为0。如果你希望回避这种做法，你应该在设计表时不允许空值；最可能的方法是，通过声明列“NOT NULL”来实现这一点。

当决定一个行应该保存到哪个分区时，MySQL 是如何处理NULL值的。

如果插入一行到按照RANGE或LIST分区的表，该行用来确定分区的列值为NULL，分区将把该NULL值视为0。例如，考虑下面的两个表，表的创建和插入记录如下：

```mysql
CREATE TABLE tnlist(
 id INT,
 NAME VARCHAR(5)
)
PARTITION BY LIST(id)(
	PARTITION p1 VALUES IN(0),
	PARTITION p2 VALUES IN(1)
)

CREATE TABLE tnrange(
  id INT,
	`name` VARCHAR(5)
)
PARTITION BY RANGE(id)(
	PARTITION p1 VALUES LESS THAN(1),
	PARTITION p2 VALUES LESS THAN MAXVALUE
)

INSERT INTO tnlist VALUES (NULL, 'bob');

INSERT INTO tnrange VALUES (NULL, 'jim');
```

![image-20200720194625057](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720194626.png)

![image-20200720194644649](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720194644.png)

在按HASH和KEY分区的情况下，任何产生NULL值的表达式都视同好像它的返回值为0。我们可以通过先创建一个按HASH分区的表，然后插入一个包含有适当值的记录，再检查对文件系统的作用，来验证这一点。假定有使用下面的语句在测试数据库中创建了一个表tnhash：

```mysql
CREATE TABLE tnhash (
    id INT,
    name VARCHAR(5)
)
PARTITION BY HASH(id)
PARTITIONS 2
```

假定有下面的一个表：

```mysql
CREATE TABLE tndate (
    id INT,
    dt DATE
)
PARTITION BY RANGE( YEAR(dt) ) (
    PARTITION p0 VALUES LESS THAN (1990),
    PARTITION p1 VALUES LESS THAN (2000),
    PARTITION p2 VALUES LESS THAN MAXVALUE
)；
```

像其他的MySQL函数一样，**YEAR(NULL)返回NULL值**。一个dt列值为NULL的行，**其分区表达式的计算结果被视为0**，该行被插入到分区p0中。

## 分区管理

MySQL 5.1 提供了许多修改分区表的方式。添加、删除、重新定义、合并或拆分已经存在的分区是可能的。所有这些操作都可以通过使用ALTER TABLE 命令的分区扩展来实现

也有获得分区表和分区信息的方式

一个分区表的所有分区都必须有子分区同样的名字，并且一旦表已经创建，再改变子分区是不可能的。

从5.1系列起建立的MySQL 服务器就把“ALTER TABLE ... PARTITION BY ...”作为有效的语法

要**改变一个表的分区模式**，只需要使用带有一个“*partition_options*”子句的ALTER TABLE 的命令。这个子句和与创建一个分区表的CREATE TABLE命令一同使用的子句有相同的语法，并且总是以关键字PARTITION BY 开头

例如，假设有一个使用下面CREATE TABLE语句建立的按照RANGE分区的表：

```mysql
CREATE TABLE trb3 (id INT, name VARCHAR(50), purchased DATE)
    PARTITION BY RANGE(YEAR(purchased))
    (
        PARTITION p0 VALUES LESS THAN (1990),
        PARTITION p1 VALUES LESS THAN (1995),
        PARTITION p2 VALUES LESS THAN (2000),
        PARTITION p3 VALUES LESS THAN (2005)
    )；
```

现在，要把这个表按照使用id列值作为键的基础，通过KEY分区把它重新分成两个分区，可以使用下面的语句：

```mysql
ALTER TABLE trb3 PARTITION BY KEY(id) PARTITIONS 2
```

这和先删除这个表、然后使用“CREATE TABLE trb3 PARTITION BY KEY(id) PARTITIONS 2；”重新创建这个表具有同样的效果。



### RANGE和LIST分区的管理

从一个按照RANGE或LIST分区的表中删除一个分区，可以使用带一个DROP PARTITION子句的ALTER TABLE命令来实现。这里有一个非常基本的例子，假设已经使用下面的CREATE TABLE和INSERT语句创建了一个按照RANGE分区的表，并且已经插入了10条记录：

```mysql
CREATE TABLE tr ( id INT, `name` VARCHAR ( 50 ), purchased DATE ) PARTITION BY RANGE (
	YEAR ( purchased )) (
	PARTITION P0
	VALUES
		LESS THAN ( 1900 ),
		PARTITION p1
	VALUES
		LESS THAN ( 1995 ),
		PARTITION p2
	VALUES
		LESS THAN ( 2000 ),
		PARTITION P3
	VALUES
		LESS THAN ( 2005 ) 
	)
	
INSERT INTO tr
VALUES
	( 1, 'desk organiser', '2003-10-15' ),
	( 2, 'CD player', '1993-11-05' ),
	( 3, 'TV set', '1996-03-10' ),
	( 4, 'bookcase', '1982-01-10' ),
	( 5, 'exercise bike', '2004-05-09' ),
	( 6, 'sofa', '1987-06-05' ),
	( 7, 'popcorn maker', '2001-11-22' ),
	( 8, 'aquarium', '1992-08-04' ),
	( 9, 'study desk', '1984-09-16' ),
	( 10, 'lava lamp', '1998-12-25' );
```

##### 删除

要删除名字为p2的分区，执行下面的命令：

```
mysql> ALTER TABLE tr DROP PARTITION p2;
Query OK, 0 rows affected (0.03 sec)
```

记住下面一点非常重要：*当删除了一个分区，也同时删除了该分区中所有的数据。*可以通过重新运行前面的SELECT查询来验证这一点：

```mysql
mysql> SELECT * FROM tr WHERE purchased 
    -> BETWEEN '1995-01-01' AND '1999-12-31';
Empty set (0.00 sec)
```

##### 删除数据保留分区

如果希望从所有分区删除所有的数据，但是又保留表的定义和表的分区模式，使用TRUNCATE TABLE命令。

##### 改变分区不丢失数据

如果希望改变表的分区而又不丢失数据，使用“ALTER TABLE ... REORGANIZE PARTITION”语句。

如果现在执行一个SHOW CREATE TABLE命令，可以观察到表的分区结构是如何被改变的：

```mysql
mysql> SHOW CREATE TABLE tr\G
*************************** 1. row ***************************
       Table: tr
Create Table: CREATE TABLE `tr` (
  `id` int(11) default NULL,
  `name` varchar(50) default NULL,
  `purchased` date default NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1 
PARTITION BY RANGE (YEAR(purchased)) (
  PARTITION p0 VALUES LESS THAN (1990) ENGINE = MyISAM, 
  PARTITION p1 VALUES LESS THAN (1995) ENGINE = MyISAM, 
  PARTITION p3 VALUES LESS THAN (2005) ENGINE = MyISAM
)
1 row in set (0.01 sec)
```

如果**插入**购买日期列的值在'1995-01-01'和 '2004-12-31'之间（含）的**新行**到**已经修改后的表中**时，这些行将被保存在分区p3中。可以通过下面的方式来验证这一点：

```mysql
mysql> INSERT INTO tr VALUES (11, 'pencil holder', '1995-07-12');
Query OK, 1 row affected (0.00 sec)
 
mysql> SELECT * FROM tr WHERE purchased 
    -> BETWEEN '1995-01-01' AND '2004-12-31';
+------+----------------+------------+
| id   | name           | purchased  |
+------+----------------+------------+
|   11 | pencil holder  | 1995-07-12 |
|    1 | desk organiser | 2003-10-15 |
|    5 | exercise bike  | 2004-05-09 |
|    7 | popcorn maker  | 2001-11-22 |
+------+----------------+------------+
4 rows in set (0.00 sec)
 
mysql> ALTER TABLE tr DROP PARTITION p3;
Query OK, 0 rows affected (0.03 sec)
 
mysql> SELECT * FROM tr WHERE purchased 
    -> BETWEEN '1995-01-01' AND '2004-12-31';
Empty set (0.00 sec)
```

注意：由“ALTER TABLE ... DROP PARTITION”语句引起的、从表中删除的行数并没有被服务器报告出来，就好像通过同等的DELETE查询操作一样。

删除LIST分区使用和删除RANGE分区完全相同的“ALTER TABLE ... DROP PARTITION”语法。但是，在对其后使用这个表的影响方面，还是有重大的区别：在这个表中，再也不能插入这么一些行，这些行的列值包含在定义已经删除了的分区的值列表中

##### RANGE增加分区

要增加一个新的RANGE或LIST分区到一个前面已经分区了的表，使用“ALTER TABLE ... ADD PARTITION”语句。对于使用RANGE分区的表，可以用这个语句添加新的区间到已有分区的序列的前面或后面。例如，假设有一个包含你所在组织的全体成员数据的分区表，该表的定义如下：

```mysql
CREATE TABLE members (
    id INT, 
    fname VARCHAR(25),
    lname VARCHAR(25), 
    dob DATE
)
PARTITION BY RANGE(YEAR(dob)) (
    PARTITION p0 VALUES LESS THAN (1970),
    PARTITION p1 VALUES LESS THAN (1980),
    PARTITION p2 VALUES LESS THAN (1990)
);
```

进一步假设成员的最小年纪是16岁。随着日历接近2005年年底，你会认识到不久将要接纳1990年（以及以后年份）出生的成员。可以按照下面的方式，修改成员表来**容纳出生在1990－1999**年之间的成员：

```mysql
ALTER TABLE ADD PARTITION (PARTITION p3 VALUES LESS THAN (2000));
```

**要点：对于通过RANGE分区的表，只可以使用**ADD PARTITION添加新的分区到分区列表的高端。设法通过这种方式在现有分区的前面或之间增加一个新的分区，将会导致下面的一个错误：

```mysql
mysql> ALTER TABLE members ADD PARTITION (PARTITION p3 VALUES LESS THAN (1960));
错误1463 (HY000): 对每个分区，VALUES LESS THAN 值必须严格增长
```

##### LIST增加分区

采用一个类似的方式，可以增加新的分区到已经通过LIST分区的表。例如，假定有如下定义的一个表：

```mysql
CREATE TABLE tt (
    id INT, 
    data INT
)
PARTITION BY LIST(data) (
    PARTITION p0 VALUES IN (5, 10, 15),
    PARTITION p1 VALUES IN (6, 12, 18)
)；
```

可以通过下面的方法添加一个新的分区，用来保存拥有数据列值7，14和21的行：

```mysql
ALTER TABLE tt ADD PARTITION (PARTITION p2 VALUES IN (7, 14, 21))；
```

注意：不能添加这样一个新的LIST分区，该分区包含有已经包含在现有分区值列表中的任意值。如果试图这样做，将会导致错误：

```mysql
mysql> ALTER TABLE tt ADD PARTITION (PARTITION np VALUES IN (4, 8, 12));
错误1465 (HY000): 在LIST分区中，同一个常数的多次定义
```

因为**带有数据列值12**的任何行都**已经分配**给了分区p1，所以不能在表tt上再创建一个其值列表包括12的新分区。

为了实现这一点，可以**先删除分区p1，添加分区np**，然后使用修正后的定义添加一个新的分区p1。但是，正如我们前面讨论过的，这将导致**保存在分区p1中的所有数据丢失**——而这往往并不是你所真正想要做的。另外一种解决方法可能是，建立一个**带有新分区的表的副本**，然后使用“CREATE TABLE ... SELECT ...”把数据拷贝到该新表中，然后删除旧表，重新命名新表，但是，当需要处理大量的数据时，这可能是**非常耗时**的。在需要高可用性的场合，这也可能是不可行的。

幸运地是，MySQL 的分区实现提供了在**不丢失数据**的条件下**重新定义分区**的方式。让我们首先看两个涉及到RANGE分区的简单例子。回想一下现在定义如下的成员表

```mysql
mysql> SHOW CREATE TABLE members\G
*************************** 1. row ***************************
       Table: members
Create Table: CREATE TABLE `members` (
  `id` int(11) default NULL,
  `fname` varchar(25) default NULL,
  `lname` varchar(25) default NULL,
  `dob` date default NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1 
PARTITION BY RANGE (YEAR(dob)) (
  PARTITION p0 VALUES LESS THAN (1970) ENGINE = MyISAM, 
  PARTITION p1 VALUES LESS THAN (1980) ENGINE = MyISAM, 
  PARTITION p2 VALUES LESS THAN (1990) ENGINE = MyISAM.
  PARTITION p3 VALUES LESS THAN (2000) ENGINE = MyISAM
)
```

##### 拆分区：REORGANIZE PARTITION

假定想要把表示出生在1960年前成员的所有行移入到一个分开的分区中。正如我们前面看到的，不能通过使用“ALTER TABLE ... ADD PARTITION”来实现这一点。但是，要实现这一点，可以使用ALTER TABLE上的另外一个与分区有关的扩展，具体实现如下：

```mysql
ALTER TABLE members REORGANIZE PARTITION p0 INTO (
    PARTITION s0 VALUES LESS THAN (1960),
    PARTITION s1 VALUES LESS THAN (1970)
)；
```

实际上，这个命令把**分区p0分成了两个新的分区s0和s1**。同时，它还根据包含在两个“PARTITION ... VALUES ...”子句中的规则，把**保存在分区p0中的数据移入到两个新的分区**中，所以分区s0中只包含YEAR(dob)小于1960的那些行，s1中包含那些YEAR(dob)大于或等于1960但是小于1970的行。

##### 合并分区：REORGANIZE PARTITION

一个REORGANIZE PARTITION语句也可以用来合并相邻的分区。可以使用如下的语句恢复成员表到它以前的分区：

```mysql
ALTER TABLE members REORGANIZE PARTITION s0,s1 INTO (
    PARTITION p0 VALUES LESS THAN (1970)
)；
```

使用“REORGANIZE PARTITION”拆分或合并分区，没有数据丢失。在执行上面的语句中，MySQL 把保存在分区s0和s1中的所有数据都移到分区p0中。

“REORGANIZE PARTITION”的基本语法是：

```mysql
ALTER TABLE tbl_name REORGANIZE PARTITION partition_list INTO (partition_definitions)；
```

其中，*tbl_name* 是分区表的名称，*partition_list* 是通过逗号分开的、一个或多个将要被改变的现有分区的列表。*partition_definitions* 是一个是通过逗号分开的、新分区定义的列表，它遵循与用在“CREATE TABLE”中的*partition_definitions* 相同的规则 (请参见13.1.5节，“CREATE TABLE语法”)。应当注意到，在把多少个分区合并到一个分区或把一个分区拆分成多少个分区方面，没有限制。例如，可以重新组织成员表的四个分区成两个分区，具体实现如下：

```mysql
ALTER TABLE members REORGANIZE PARTITION p0,p1,p2,p3 INTO (
    PARTITION m0 VALUES LESS THAN (1980),
    PARTITION m1 VALUES LESS THAN (2000)
)；
```

##### LIST分区重组

同样，对于按LIST分区的表，也可以使用REORGANIZE PARTITION。让我们回到那个问题，即增加一个新的分区到已经按照LIST分区的表tt中，但是因为该新分区有一个值已经存在于现有分区的值列表中，添加新的分区失败。我们可以通过先添加只包含非冲突值的分区，然后重新组织该新分区和现有的那个分区，以便保存在现有的那个分区中的值现在移到了新的分区中，来处理这个问题：

```mysql
ALTER TABLE tt ADD PARTITION (PARTITION np VALUES IN (4, 8));
ALTER TABLE tt REORGANIZE PARTITION p1,np INTO (
    PARTITION p1 VALUES IN (6, 18),
    PARTITION np VALUES in (4, 8, 12)
)
```

\#对列表分区来说，重新组织的分区必须是相邻的分区

```
mysql> ALTER TABLE tt ADD PARTITION (PARTITION np VALUES IN (4, 8));
```

 

```
mysql> ALTER TABLE tt REORGANIZE PARTITION p1,np INTO ( PARTITION p1 VALUES IN (6, 18), PARTITION np VALUES ``in` `(4, 8, 12) );
ERROR 1519 (HY000): When reorganizing a ``set` `of partitions they must be ``in` `consecutive order
```

 

~~~mysql
mysql> show create table tt\G
*************************** 1. row ***************************
    ``Table: tt
Create Table: CREATE TABLE `tt` (
 `````id``` int(11) DEFAULT NULL,
 ```data` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
/*!50100 PARTITION BY LIST (data)
(PARTITION p0 VALUES IN (5,10,15) ENGINE = InnoDB,
 ``PARTITION p1 VALUES IN (6,12,18) ENGINE = InnoDB,
 ``PARTITION p2 VALUES IN (7,14,21) ENGINE = InnoDB,
 ``PARTITION np VALUES IN (4,8) ENGINE = InnoDB) */
~~~

```mysql
mysql> insert into tt values(1,10),(2,5);
```

 改变列表相邻两个区的值

```mysql
mysql> ALTER TABLE tt REORGANIZE PARTITION p0,p1 INTO ( PARTITION p0 VALUES IN (6, 18), PARTITION p1 VALUES ``in` `(5,15));
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
```

 

~~~mysql
mysql> show create table tt\G
*************************** 1. row ***************************
    ``Table: tt
Create Table: CREATE TABLE `tt` (
 `````id``` int(11) DEFAULT NULL,
 ```data` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
/*!50100 PARTITION BY LIST (data)
(PARTITION p0 VALUES IN (6,18) ENGINE = InnoDB,
 ``PARTITION p1 VALUES IN (5,15) ENGINE = InnoDB,
 ``PARTITION p2 VALUES IN (7,14,21) ENGINE = InnoDB,
 ``PARTITION np VALUES IN (4,8) ENGINE = InnoDB) */
~~~

```mysql
#如果表里已有的数据在新重组的分区中没有指定的值，则数据会丢失
mysql> ``select` `* from tt; ``#(1,10)数据丢失了，新的分区没有包含data=10的值
+------+------+
| ``id`  `| data |
+------+------+
|  2 |  5 |
+------+------+
```

修改后的分区

![image-20200720211845715](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720211845.png)

###### 注意点

当使用“ALTER TABLE ... REORGANIZE PARTITION”来对已经按照RANGE和LIST分区表进行重新分区时，下面是一些要记住的关键点：

1、用来确定新分区模式的PARTITION子句使用与用在CREATE TABLE中确定分区模式的PARTITION子句相同的规则。

2、最重要的是，应该记住：新分区模式不能有任何重叠的区间（适用于按照RANGE分区的表）或值集合（适用于重新组织按照LIST分区的表）。

3、 *partition_definitions* 列表中分区的合集应该与在*partition_list* 中命名分区的合集占有相同的区间或值集合。

例如，在本节中用作例子的成员表中，分区p1和p2总共覆盖了1980到1999的这些年。因此，对这两个分区的**重新组织都应该覆盖相同范围的年份**。

4、 对于按照RANGE分区的表，只能重新组织相邻的分区；不能跳过RANGE分区。

例如，不能使用以“ALTER TABLE members REORGANIZE PARTITION p0,p2 INTO ...”开头的语句，来重新组织本节中用作例子的成员表。因为，p0覆盖了1970年以前的年份，而p2覆盖了从1990到1999（包括1990和1999）之间的年份，因而这两个分区不是相邻的分区。

**不能使用REORGANIZE PARTITION**来改变表的**分区类型**；也就是说，例如，不能把RANGE分区变为HASH分区，反之亦然。也不能使用该命令来**改变分区表达式或列**。如果想在不删除和重建表的条件下实现这两个任务，可以使用“**ALTER TABLE ... PARTITION BY** ....”，例如：

```mysql
                ALTER TABLE members 
                    PARTITION BY HASH(YEAR(dob))
                    PARTITIONS 8；
```



### HASH和KEY分区的管理

**不能使用**与从按照RANGE或LIST分区的表中删除分区相同的方式，来从HASH或KEY分区的表中删除分区。

但是，可以使用“**ALTER TABLE ... COALESCE PARTITION**”命令来合并HASH或KEY分区。例如，假定有一个包含顾客信息数据的表，它被分成了12个分区。该顾客表的定义如下：

```mysql
CREATE TABLE clients (
    id INT,
    fname VARCHAR(30),
    lname VARCHAR(30),
    signed DATE
)
PARTITION BY HASH( MONTH(signed) )
PARTITIONS 12；
```

##### 减少分区

要减少分区的数量从12到6，执行下面的ALTER TABLE命令：

```mysql
mysql> ALTER TABLE clients COALESCE PARTITION 6；
Query OK, 0 rows affected (0.02 sec)
```

对于按照HASH，KEY，LINEAR HASH，或LINEAR KEY分区的表， COALESCE能起到同样的作用。下面是一个类似于前面例子的另外一个例子，它们的区别只是在于表是按照LINEAR KEY 进行分区：

```mysql
mysql> CREATE TABLE clients_lk (
    ->     id INT,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     signed DATE
    -> )
    -> PARTITION BY LINEAR KEY(signed)
    -> PARTITIONS 12；
Query OK, 0 rows affected (0.03 sec)
 
mysql> ALTER TABLE clients_lk COALESCE PARTITION 6；
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

COALESCE不能用来增加分区的数量，如果你尝试这么做，结果会出现类似于下面的错误：

```mysql
mysql> ALTER TABLE clients COALESCE PARTITION 18;
错误1478 (HY000): 不能移动所有分区，使用DROP TABLE代替
```

##### 增加分区

要增加顾客表的分区数量从12到18，使用“ALTER TABLE ... ADD PARTITION”,具体如下：

```mysql
ALTER TABLE clients ADD PARTITION PARTITIONS 18；
```

**注释**：“ALTER TABLE ... REORGANIZE PARTITION”不能用于按照HASH或HASH分区的表。

### 分区维护

MySQL 5.1中可以执行许多分区维护的任务。对于分区表，MySQL不支持命令CHECK TABLE，OPTIMIZE TABLE，ANALYZE TABLE，或REPAIR TABLE。作为替代，可以使用ALTER TABLE 的许多扩展来在一个或多个分区上直接地执行这些操作，如下面列出的那样：

##### **重建分区**

这和先删除保存在分区中的所有记录，然后重新插入它们，具有同样的效果。它可用于整理分区碎片。

```mysql
ALTER TABLE t1 REBUILD PARTITION (p0, p1)
```

#####  **优化分区**

如果从分区中**删除了大量的行**，或者对一个**带有可变长度的行**（也就是说，有VARCHAR，BLOB，或TEXT类型的列）作了许多修改，可以使用“ALTER TABLE ... OPTIMIZE PARTITION”来**收回没有使用的空间**，并**整理分区数据文件的碎片**。

```mysql
ALTER TABLE t1 OPTIMIZE PARTITION (p0, p1)；
```

在一个给定的分区表上使用“**OPTIMIZE PARTITION**”等同于在那个分区上运行CHECK PARTITION，ANALYZE PARTITION，和REPAIR PARTITION。

##### **分析分区**

读取并保存分区的键分布。

```mysql
ALTER TABLE t1 ANALYZE PARTITION (p3)；
```

##### 修补分区

 修补被破坏的分区。

```mysql
ALTER TABLE t1 REPAIR PARTITION (p0,p1);
```

#####  **检查分区**

可以使用几乎与对非分区表使用CHECK TABLE 相同的方式检查分区。

```mysql
ALTER TABLE trb3 CHECK PARTITION (p1)；
```

这个命令可以告诉你表t1的分区p1中的数据或索引是否已经被破坏。如果发生了这种情况，使用“ALTER TABLE ... REPAIR PARTITION”来修补该分区。

还可以使用**mysqlcheck**或**myisamchk** 应用程序，在对表进行分区时所产生的、单独的MYI文件上进行操作，来完成这些任务。



### 获取关于分区的信息

##### 1）通过show create table命令

如在本章中别处讨论的一样，在**SHOW CREATE TABLE**的输出中包含了用于创建分区表的PARTITION BY子句

```mysql
mysql> SHOW CREATE TABLE trb3\G
*************************** 1. row ***************************
       Table: trb3
Create Table: CREATE TABLE `trb3` (
  `id` int(11) default NULL,
  `name` varchar(50) default NULL,
  `purchased` date default NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1 
PARTITION BY RANGE (YEAR(purchased)) (
  PARTITION p0 VALUES LESS THAN (1990) ENGINE = MyISAM, 
  PARTITION p1 VALUES LESS THAN (1995) ENGINE = MyISAM, 
  PARTITION p2 VALUES LESS THAN (2000) ENGINE = MyISAM, 
  PARTITION p3 VALUES LESS THAN (2005) ENGINE = MyISAM
)
1 row in set (0.00 sec)
```

**注释：**当前，对于按HASH或KEY分区的表，PARTITIONS子句并不显示。 ([Bug #14327](http://bugs.mysql.com/14327))

##### 2）show table status命令

```mysql
mysql> show table status like ``'e'``;
+------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
| Name | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time     | Update_time     | Check_time | Collation    | Checksum | Create_options | Comment |
+------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
| e  | InnoDB |   10 | Dynamic  |  3 |     21845 |    65536 |        0 |      0 |     0 |      NULL | 2019-10-10 14:37:16 | 2019-10-10 14:37:45 | NULL    | utf8_general_ci |   NULL | partitioned  |     |
+------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
```

##### 3）通过information_schema.partitions系统表来查看分区表的具体信息

## 表分区修剪

##### 概念

表分区修剪是MySQL优化的一种，其核心就是**只扫描需要的分区**

##### 建表

```mysql
CREATE TABLE t1 ( 
fname VARCHAR(50) NOT NULL, 
lname VARCHAR(50) NOT NULL, 
region_code TINYINT UNSIGNED NOT NULL, 
dob DATE NOT NULL ) 
PARTITION BY RANGE( region_code ) ( 
PARTITION p0 VALUES LESS THAN (64), 
PARTITION p1 VALUES LESS THAN (128), 
PARTITION p2 VALUES LESS THAN (192), 
PARTITION p3 VALUES LESS THAN MAXVALUE );
```

##### 查看语句执行计划

```
##查看语句执行计划
mysql> explain SELECT fname, lname, region_code, dob FROM t1 WHERE region_code > 125 AND region_code < 130;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| ``id` `| select_type | table | partitions | ``type` `| possible_keys | key | key_len | ref | rows | filtered | Extra    |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| 1 | SIMPLE   | t1  | p1,p2   | ALL | NULL     | NULL | NULL  | NULL |  1 |  100.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

 #此查询只需要扫描P1和P2两个分区就能得到结果，从而获得额外的性能提升
#不光是select语句可以被使用表分区修剪，update和delete语句也可以使用

#表分区选择和表分区修剪类似，只不过修剪是自动实现的，而表分区选择是现实的指定分区范围
#表分区选择不仅支持select语句，也支持update，insert，delete等语句

##### select/update/delete时指定分区

![image-20200720220005363](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200720220005.png)

##### 无法分区裁剪的情况

1、分区裁剪需要使用分区表上面的查询条件，所以根据优化器的优化规则，如果查询条件不能下推到分区表，则相应的查询语句无法执行分区裁剪。

![image-20200722110947525](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722110947.png)

![image-20200722111017553](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722111017.png)

2、由于分区裁剪的规则优化是在查询计划的生成阶段，对于执行阶段才能获取到过滤条件的场景，无法利用分区裁剪的优化。

![image-20200722111159025](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722111159.png)

3、由于当前实现中的一处限制，对于查询条件无法下推到 TiKV 的表达式，不支持分区裁剪。

> 对于一个函数表达式 `fn(col)`，如果 TiKV 支持这个函数 `fn`，则在查询优化做谓词下推的时候，`fn(col)` 会被推到叶子节点（也就是分区），因而能够执行分区裁剪。
>
> 如果 TiKV 不支持 `fn`，则优化阶段不会把 `fn(col)` 推到叶子节点，而是在叶子上面连接一个 Selection 节点，分区裁剪的实现没有处理这种父节点的 Selection 中的条件，因此对不能下推到 TiKV 的表达式不支持分区裁剪。

4、对于 Hash 分区类型，只有等值比较的查询条件能够支持分区裁剪。

5、对于 Range 分区类型，分区表达式必须是 `col` 或者 `fn(col)` 的简单形式，查询条件是 > < = >= <= 时才能支持分区裁剪。如果分区表达式是 `fn(col)` 形式，还要求 `fn` 必须是单调函数，才有可能分区裁剪。

这里单调函数是指某个函数 `fn` 满足条件：对于任意 `x` `y`，如果 `x > y`，则 `fn(x) > fn(y)`。

这种是严格递增的单调函数，非严格递增的单调函数也可以符合分区裁剪要求，只要函数 `fn` 满足：对于任意 `x` `y`，如果 `x > y`，则 `fn(x) >= fn(y)`。

理论上所有满足单调条件（严格或者非严格）的函数都是可以支持分区裁剪。实际上，目前 TiDB 已经支持的单调函数只有：

```fallback
unix_timestamp
to_days
```

例如，分区表达式是简单列的情况：

![image-20200722112151553](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722112151.png)

## 关于函数的分区限制

![image-20200722163021990](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722163022.png)

![image-20200722163048483](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722163048.png)

![image-20200722163106412](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722163106.png)

```fallback
ABS()
CEILING()
DATEDIFF()
DAY()
DAYOFMONTH()
DAYOFWEEK()
DAYOFYEAR()
EXTRACT() (see EXTRACT() function with WEEK specifier)
FLOOR()
HOUR()
MICROSECOND()
MINUTE()
MOD()
MONTH()
QUARTER()
SECOND()
TIME_TO_SEC()
TO_DAYS()
TO_SECONDS()
UNIX_TIMESTAMP() (with TIMESTAMP columns)
WEEKDAY()
YEAR()
YEARWEEK()
```

## 数据交换exchange命令

对分区表可以通过ALTER TABLE pt EXCHANGE PARTITION p WITH TABLE nt命令将一个分区或者是子分区的数据与普通的表的数据相互交换，其本身的表结构不会变化。交换的分区表和目标表必须结构完全相同，包括字段，类型，索引，存储引擎必须完全一样

```
CREATE TABLE e ( 
id` `INT NOT NULL, 
fname VARCHAR(30), 
lname VARCHAR(30) ) 
PARTITION BY RANGE (``id``) 
( PARTITION p0 VALUES LESS THAN (50), 
PARTITION p1 VALUES LESS THAN (100), 
PARTITION p2 VALUES LESS THAN (150), 
PARTITION p3 VALUES LESS THAN (MAXVALUE) );
```

 

```
mysql> CREATE TABLE e2 LIKE e; ``#创建结构相同的表
mysql> ALTER TABLE e2 REMOVE PARTITIONING; ``#删除分区
```

 

```
mysql> insert into e(``id``,fname,lname) values(10,``'a'``,``'a'``),(20,``'b'``,``'b'``),(170,``'c'``,``'c'``),(180,``'d'``,``'d'``),(190,``'e'``,``'e'``);
```

 

```
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2; ``#数据交换，原本p0上的数据不在了
```

 

```
mysql> SELECT PARTITION_NAME, TABLE_ROWS FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_NAME = ``'e'``;
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0       |     0 |
| p1       |     0 |
| p2       |     0 |
| p3       |     3 |
+----------------+------------+
```

 

```
mysql> ``select` `* from e2;
+----+-------+-------+
| ``id` `| fname | lname |
+----+-------+-------+
| 10 | a   | a   |
| 20 | b   | b   |
+----+-------+-------+
```

 

```
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2; ``##再次执行后数据交换回来
mysql> ``select` `* from e2;
Empty ``set` `(0.00 sec)
mysql> ``select` `* from e;
+-----+-------+-------+
| ``id` `| fname | lname |
+-----+-------+-------+
| 10 | a   | a   |
| 20 | b   | b   |
| 170 | c   | c   |
| 180 | d   | d   |
| 190 | e   | e   |
+-----+-------+-------+
```

 

```
----------------------------------------------------------------------------
#报错：表结构不一致报错lname2 varchar(30)
mysql> create table e3(``id` `int, fname varchar(30),lname2 varchar(30));
mysql> alter table e exchange partition p3 with table e3;
ERROR 1736 (HY000): Tables have different definitions
```

 

```
#报错：字段不一致lname varchar(32)
mysql> create table e3(``id` `int not null, fname varchar(30),lname varchar(32));
Query OK, 0 rows affected (0.04 sec)
mysql> alter table e exchange partition p3 with table e3;
ERROR 1736 (HY000): Tables have different definitions
--------------------------------------------------------------------------------------------
```

 

```
#执行exchange命令时，目标表里不一定是空数据，如果有数据需要保证里面的数据符合表分区的条件，否则只能用WITHOUT VALIDATION来跳过验证环节
mysql> INSERT INTO e2 VALUES (51, ``"Ellen"``, ``"McDonald"``);
Query OK, 1 row affected (0.08 sec)
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2;
ERROR 1707 (HY000): Found row that does not match the partition
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2 WITHOUT VALIDATION;
Query OK, 0 rows affected (0.02 sec)
```

 

```
#对子表分区也可以执行exchange命令：
CREATE TABLE es ( ``id` `INT NOT NULL, fname VARCHAR(30), lname VARCHAR(30) )
PARTITION BY RANGE (``id``)
SUBPARTITION BY KEY (lname)
SUBPARTITIONS 2 ( 
PARTITION p0 VALUES LESS THAN (50), 
PARTITION p1 VALUES LESS THAN (100), 
PARTITION p2 VALUES LESS THAN (150), 
PARTITION p3 VALUES LESS THAN (MAXVALUE) );
```

 

```
mysql> CREATE TABLE es2 LIKE es;
mysql> ALTER TABLE es2 REMOVE PARTITIONING;
```

 

```
mysql> ALTER TABLE es EXCHANGE PARTITION p3sp0 WITH TABLE es2;
Query OK, 0 rows affected (0.29 sec)
```

 

```mysql
#当表是有子表分区时，只能exchange一个子表分区，而不能交换整个分区
mysql> ALTER TABLE es EXCHANGE PARTITION p3 WITH TABLE es2;
ERROR 1704 (HY000): Subpartitioned table, use subpartition instead of partition
```

## 分区操作

#### 创建表的时候，直接创建分区。注意主键

```mysql
CREATE TABLE employees (
id INT NOT NULL,
first_name VARCHAR(30),
last_name VARCHAR(30),
store_id INT NOT NULL,
create_time DATE NOT NULL DEFAULT '9999-12-31',
)
partition BY 
range(to_days(create_time))
            (PARTITION p201701 VALUES LESS THAN (TO_DAYS('2017-02-01')) ENGINE = InnoDB,
             PARTITION p201702 VALUES LESS THAN (TO_DAYS('2017-03-01')) ENGINE = InnoDB,
              PARTITION p201703 VALUES LESS THAN (TO_DAYS('2017-04-01')) ENGINE = InnoDB);
```

#### 修改表的时候

```mysql
alter table employees  partition by range(to_days(create_time))
            (PARTITION p201701 VALUES LESS THAN (TO_DAYS('2017-02-01')) ENGINE = InnoDB,
             PARTITION p201702 VALUES LESS THAN (TO_DAYS('2017-03-01')) ENGINE = InnoDB,
             PARTITION p201703 VALUES LESS THAN (TO_DAYS('2017-04-01')) ENGINE = InnoDB);
```

#### 增加分区

```mysql
alter table employees add partition(PARTITION p2018 VALUES LESS THAN MAXVALUE);
```

> 只能从range分区列表最大端增加分区 
> 增加list分区，不能添加一个包含现有分区值列表中的任意值分区，也就是说对一个固定的分区键值，必须指定并且只能指定一个 唯一的分区

#### 删除分区

```mysql
alter table employees drop partition p201702;
```

#### 移除表的分区

```mysql
alter table employees remove partitioning
```

> 使用remove移除分区是仅仅移除分区的定义，并不会删除数据和drop PARTITION不一样，后者会连同数据一起删除

#### 重新定义分区

```mysql
ALTER TABLE employees REORGANIZE PARTITION p201701,p201702,p201703,p2018 INTO (PARTITION p0 VALUES LESS THAN MAXVALUE); 
```

将p201701,p201702,p201703,p2018这几个分区数据移到p0分区

> 注意 hash 和 key 分区不能使用 REORGANIZE

#### 日期函数分区方法

##### 使用YEAR() 函数进行分区

```mysql
alter table employees  partition by range(YEAR(create_time))
            (PARTITION p201701 VALUES LESS THAN ('2015-02-01') ENGINE = InnoDB,
             PARTITION p201702 VALUES LESS THAN ('2016-02-01') ENGINE = InnoDB,
             PARTITION p201703 VALUES LESS THAN ('2017-02-01') ENGINE = InnoDB);
```

> 对于日期字段分区，查询条件使用> 、< 、betnwen、=都会利用分区查询,如果条件使用函数转换则不会走分区，比如使用YEAR()。

##### TIMESTAMP类型的列的分区方法

```mysql
alter table employees  partition by range(UNIX_TIMESTAMP(create_time))
            (PARTITION p201701 VALUES LESS THAN (UNIX_TIMESTAMP('2015-02-01')) ENGINE = InnoDB,
             PARTITION p201702 VALUES LESS THAN (UNIX_TIMESTAMP('2016-02-01')) ENGINE = InnoDB,
             PARTITION p201703 VALUES LESS THAN (UNIX_TIMESTAMP('2017-02-01')) ENGINE = InnoDB);
```

##### null值处理

当往分区列中插入null值RANG 分区会将其当作最小值来处理即插入最小的分区中

#### 查看表分区状况

```mysql
-- 查询这个表有多少分区
-- 查询每一个分区对应的数量
SELECT t.`PARTITION_NAME`,t.`SUBPARTITION_NAME`,t.`TABLE_ROWS` FROM information_schema.`PARTITIONS` t WHERE t.`TABLE_NAME` = 't_range_test';
```

#### 添加联合主键

删除主键并增加复合主键

```mysql
ALTER TABLE `table_name`

DROP PRIMARY KEY,

ADD PRIMARY KEY (`id`, `createTime`);
```

## 分区的约束和限制

### 分区键，主键和唯一键

本节讨论分区键，主键和唯一键之间的关系。一句话总结它们之间的关系要满足的规则：**分区表的每个唯一键，必须包含分区表达式中用到的所有列**。

这里所指的唯一也包含了主键，因为根据主键的定义，主键必须是唯一的。例如，下面这些建表语句就是无效的：

![image-20200722112838134](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722112838.png)

![image-20200722112847849](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722112847.png)

![image-20200722112917528](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722112917.png)

下面这个表就没法做分区了，因为无论如何都不可能找到满足条件的分区键：

![image-20200722113016844](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722113016.png)

根据定义，主键也是唯一键，下面两个建表语句是无效的：

![image-20200722113039937](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722113040.png)

### 添加唯一索引

DDL 变更时，添加唯一索引也需要考虑到这个限制。比如创建了这样一个表：

![image-20200722113112098](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200722113112.png)

## 总结

1. MySQL分区中如果存在主键或唯一键，则分区列必须包含在其中。
2. 对于原生的RANGE分区，LIST分区，HASH分区，分区对象返回的只能是整数值。
3. 分区字段不能为NULL，要不然怎么确定分区范围呢，所以尽量NOT NULL

# MySQL存储过程

### 1.创建存储过程语法（格式）

```mysql
DELIMITER $
CREATE PROCEDURE 存储过程名A（IN 传入参数名a INT，IN 传入参数名b VARCHAR(20)，OUT 返回参数名c INT）
BEGIN
　　内容..........
END $
```

> **解析：**
>
> 　　　　**IN** 代表传入的参数，定义**传入**参数名，并且后面**跟上传入参数类型**（INT,VARCHAR,DOUBLE,........）
>
> 　　　　**OUT** 代表存储过程执行**完返回的数**据，定义参数名，并且**后面跟上参数类**型（INT,VARCHAR,DOUBLE,........）
>
> 　　　　**INOUT** 代表既可以传入也可以返回 ，定义参数名，并且后**面跟上参数类型**（INT,VARCHAR,DOUBLE,........）

![img](https://images2015.cnblogs.com/blog/991470/201702/991470-20170205222758183-2127571206.png)

> **说明：**
> 　　**DELIMITER $$ DELIMITER; 用来定义分隔符**，因为MySQL默认以";"为分隔符，**如果我们没有声明分割符，那么编译器会把存储过程当成SQL语句进行处理，则存储过程的编译过程会报错**，所以要事先用DELIMITER关键字申明当前段分隔符，这样MySQL才会将";"当做存储过程中的代码，不会执行这些代码，用完了之后要把分隔符还原。



### **2.存储过程内具体语法与逻辑**

#### A.定义变量语法：

```mysql
DECLARE 变量名a 参数类型（INT,VARCHAR(20),BOOLEAN,........） [DEFAULT NULL]；
```

> 注：可以在参数类型后面加 DEFAULT NULL； 来设置初始值。

##### 局部变量

局部变量一般用在sql语句块中，比如存储过程的begin/end。其作用域仅限于该语句块，在该语句块执行完毕后，局部变量就消失了。

局部变量一般用**declare来声明**，可以使用**default**来说明默认值。

![image-20200721143730572](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721143730.png)

![image-20200721143814453](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721143814.png)



##### 用户变量

用户变量的作用域要比局部变量要广。用户变量可以作用于当前整个连接，但是当当前连接断开后，其所定义的用户变量都会消失。

用户变量使用如下(这里我们**无须使用declare**关键字进行定义，可以直接这样使用):

**select @变量名**

对**用户变量赋值**有两种方式，一种是**直接用"="号**，另一种**是用":="号**。其区别在于使用**set命令**对用户变量进行赋值时，两种方式都可以使用；当**使用select语句**对用户变量进行赋值时，只能使用":="方式，因为在select语句中，"="号被看作是比较操作符。

![image-20200721144054102](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721144054.png)

执行结果

![image-20200721144519662](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721144519.png)

##### 会话变量

服务器为每个连接的客户端维护一系列会话变量。

##### 全局变量

全局变量影响服务器整体操作。当服务器启动时，它将所有全局变量初始化为默认值

#### B.变量赋值：

![image-20200721144647500](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721144647.png)

##### 方式1（直接对变量进行赋值）：

```mysql
　　　　　　　　　　　　SET 定义的变量a = NEW();
```

##### 方式2（sql查询的结果直接赋值给变量）：

```mysql
SELECT `student`.age INTO 定义的变量a FROM `student` WHERE...........
```

##### 方式3（sql查询的结果直接赋值给多个变量）：

```mysql
SELECT `student`.name AS 定义的变量a,`student`.age AS 定义的变量b INTO 定义的变量a,定义的变量b FROM `student` ...............
```

##### 变量的作用域：

> 内部的变量是其作用域范围内享有更高的优先权， 当执行到end， 内部变量消失， 此时已经
> 在其作用域外， 变量不再可见了。因为在存储过程外也不能找到这个声明的变量， 但是你可以通过
> out参数或者将其指派给会话变量来保存其值。
> 内部变量在begin -- end 之间

#### C.逻辑判断：

```mysql
#IF判断：　　　　

　　　　　　IF 条件语句(3>5) THEN
        　　　　 条件为TRUE时执行.........;
　　　　　　END IF；
　　#IF ELSE判断：
　　　　　　IF 条件(a>0) THEN
      　　      条件为(a>0)时执行........;
　　　　　　ELSE IF 条件(a<0) THEN
　　　　　　　　　条件为(a<0)时执行.......;
　　　　　　ELSE
　　　　　　　　　其它执行.......;
　　　　　　END IF;
```

![image-20200721144909965](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721144910.png)



#### D.游标，（LOOP）循环：

```mysql
#例.单游标循环：
DELIMIETER $
create procedure my_procedure() -- 创建存储过程
begin -- 开始存储过程
declare my_id varchar(32); -- 自定义变量1
declare my_name varchar(50); -- 自定义变量2
DECLARE done INT DEFAULT FALSE; -- 自定义控制游标循环变量,默认false

DECLARE cur CURSOR FOR ( SELECT id, name FROM t_people ); -- 定义游标并输入结果集
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE; -- 绑定控制变量到游标,游标循环结束自动转true
 
OPEN cur; -- 打开游标
  posLoop: LOOP -- 开始循环体,myLoop为自定义循环名,结束循环时用到
    FETCH cur into my_id, my_name; -- 将游标当前读取行的数据顺序赋予自定义变量12
   　　 IF done THEN -- 判断是否继续循环
     　　　　 LEAVE posLoop; -- 结束循环
  　　  END IF;
    -- 自己要做的事情,在 sql 中直接使用自定义变量即可
    UPDATE t_user SET c_name = my_name WHERE id = my_id and rtrim(ltrim(c_name)) = ''; -- 左右去空格
 
    COMMIT; -- 提交事务
  END LOOP posLoop; -- 结束自定义循环体
  CLOSE cur; -- 关闭游标
END $ -- 结束存储过程　　
```

```mysql
#例.多游标循环：
DELIMITER $
create procedure my_procedure() -- 创建存储过程
BEGIN -- 开始存储过程

　　declare my_id varchar(32); -- 自定义变量1
　　declare my_name varchar(50); -- 自定义变量2
　　DECLARE done INT DEFAULT FALSE; -- 自定义控制游标循环变量,默认false

　　DECLARE cur_1 CURSOR FOR ( SELECT id, name FROM t_people ); -- 定义游标并输入结果集
　　DECLARE cur_2 CURSOR FOR （ SELECT id_2,name_2 FROM t_people_2）; --定义游标并输入结果集

　　DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE; -- 绑定控制变量到游标,游标循环结束自动转true
 
　　OPEN cur_1; -- 打开游标
 　　 posLoop: LOOP -- 开始循环体,myLoop为自定义循环名,结束循环时用到
  　　  FETCH cur_1 into my_id, my_name; -- 将游标当前读取行的数据顺序赋予自定义变量12
 　　  　　 IF done THEN -- 判断是否继续循环
  　　   　　　　 LEAVE posLoop; -- 结束循环
  　　　　  END IF;
  　　  -- 自己要做的事情,在 sql 中直接使用自定义变量即可
  　　  UPDATE ..........;　　--自己要做的具体操作
　　  END LOOP posLoop; -- 结束自定义循环体
　　 CLOSE cur_1; -- 关闭游标

　　　　SET done = FALSE;　　-- 因为当第一个游标遍历完后其值被handler设置为TRUE了，如果不用set把它设置为 FALSE ，那么第二个游标就不会遍历了。（最好是在每个打开游标的操作前都用该语句，以确保游标能真正遍历）
　　
　　OPEN cur_2; -- 打开游标
　　  posLoop_2: LOOP -- 开始循环体,myLoop为自定义循环名,结束循环时用到
　　    FETCH cur_2 into my_id, my_name; -- 将游标当前读取行的数据顺序赋予自定义变量12
 　　  　　 IF done THEN -- 判断是否继续循环
 　　    　　　　 LEAVE posLoop_2; -- 结束循环
  　　　　  END IF;
 　　   -- 自己要做的事情,在 sql 中直接使用自定义变量即可
 　　   INSERT ..........;　　　--自己要做的具体操作
　　  END LOOP posLoop_2; -- 结束自定义循环体
　　  CLOSE cur_2; -- 关闭游标
　　
　
END; -- 结束存储过程

```

#### E.WHILE语句

![image-20200721145033284](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721145033.png)

#### F. case 语句

![image-20200721144954813](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721144954.png)

#### G. repeat 语句

![image-20200721145117037](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721145117.png)

### 3.存储过程的调用

```mysql
#调用没有返回值的存储过程CALL 存储过程名(参数.....);

#调用有返回值的存储过程（获得返回值）

CALL 存储过程名(@aaa);
SELECT @aaa;
```

### 4.删除存储过程

```mysql
DROP PROCEDURE 存储过程名; 
```

![img](https://images2015.cnblogs.com/blog/991470/201702/991470-20170205223136839-273682019.png)

> **说明：**
>
> 　　请注意只给出了存储过程名，并没有书写()。

### 5.注意事项

存储过程中的分号（;）很重要，不能省略。

### 6、参数

> mysql存储过程的参数用于存储过程的定义， 共有三种参数类型， IN， OUT， INOUT，形式如下：
> CREATE PROCEDURE （【in|out|inout】 函数名  函数类型...）
>
> **IN 输入参数**： 表示该参数的值必须在**调用存储过程时指定**， 在**存储过程中修改**该参数的值**不能被返回**， 为**默认值**。
> **OUT 输出参数**： 该值可在存储过程**内部被改变**， 并可返回。
> **INOUT**输入输出参数： 调用时指定， 并且可被改变和返回。

#### IN参数例子：

IN参数的特定在于， 读取外部变量值， 且有效范围仅限于存储过程内部。

> 例子：
> delimiter //
> create procedure pin(IN p_in int)
> begin
> select p_in;
> set p_in=2;
> select p_in;
> end;
> //
> delimiter;
> -----------
> set @p_in=1;
> call pin(@p_in)   无论内部如何修改， 都不会被传递到外部
> select @p_in  还是1， 而不是函数修改后的2

#### OUT参数例子

![image-20200721142939949](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721142940.png)

#### INOUT 参数例子

![image-20200721143027213](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721143027.png)

#### 不加参数例子

![image-20200721143337071](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721143337.png)

### 7、存储过程基本函数

#### **字符串类** 

> CHARSET(str) //返回字串字符集
> CONCAT (string2 [,... ]) //连接字串
> INSTR (string ,substring ) //返回substring首次在string中出现的位置,不存在返回0
> LCASE (string2 ) //转换成小写
> LEFT (string2 ,length ) //从string2中的左边起取length个字符
> LENGTH (string ) //string长度
> LOAD_FILE (file_name ) //从文件读取内容
> LOCATE (substring , string [,start_position ] ) 同INSTR,但可指定开始位置
> LPAD (string2 ,length ,pad ) //重复用pad加在string开头,直到字串长度为length
> LTRIM (string2 ) //去除前端空格
> REPEAT (string2 ,count ) //重复count次
> REPLACE (str ,search_str ,replace_str ) //在str中用replace_str替换search_str
> RPAD (string2 ,length ,pad) //在str后用pad补充,直到长度为length
> RTRIM (string2 ) //去除后端空格
> STRCMP (string1 ,string2 ) //逐字符比较两字串大小,
> SUBSTRING (str , position [,length ]) //从str的position开始,取length个字符,
> 注：mysql中处理字符串时，默认第一个字符下标为1，即参数position必须大于等于1
>
> mysql> select substring('abcd',0,2);
> +-----------------------+
> | substring('abcd',0,2) |
> +-----------------------+
> |            |
> +-----------------------+
> 1 row in set (0.00 sec)
>
> 
>
> 
>
> mysql> select substring('abcd',1,2);
> +-----------------------+
> | substring('abcd',1,2) |
> +-----------------------+
> | ab          |
> +-----------------------+
> 1 row in set (0.02 sec)
>
>
> TRIM([[BOTH|LEADING|TRAILING] [padding] FROM]string2) //去除指定位置的指定字符
> UCASE (string2 ) //转换成大写
> RIGHT(string2,length) //取string2最后length个字符
> SPACE(count) //生成count个空格 

#### **数学类**

> ABS (number2 ) //绝对值
> BIN (decimal_number ) //十进制转二进制
> CEILING (number2 ) //向上取整
> CONV(number2,from_base,to_base) //进制转换
> FLOOR (number2 ) //向下取整
> FORMAT (number,decimal_places ) //保留小数位数
> HEX (DecimalNumber ) //转十六进制
> 注：HEX()中可传入字符串，则返回其ASC-11码，如HEX('DEF')返回4142143
> 也可以传入十进制整数，返回其十六进制编码，如HEX(25)返回19
> LEAST (number , number2 [,..]) //求最小值
> MOD (numerator ,denominator ) //求余
> POWER (number ,power ) //求指数
> RAND([seed]) //随机数
> ROUND (number [,decimals ]) //四舍五入,decimals为小数位数]
>
> 注：返回类型并非均为整数，如：
> (1)默认变为整形值
> mysql> select round(1.23);
> +-------------+
> | round(1.23) |
> +-------------+
> |      1 |
> +-------------+
> 1 row in set (0.00 sec)
>
> mysql> select round(1.56);
> +-------------+
> | round(1.56) |
> +-------------+
> |      2 |
> +-------------+
> 1 row in set (0.00 sec)
>
> (2)可以设定小数位数，返回浮点型数据
> mysql> select round(1.567,2);
> +----------------+
> | round(1.567,2) |
> +----------------+
> |      1.57 |
> +----------------+
> 1 row in set (0.00 sec)
>
>
> SIGN (number2 ) //返回符号,正负或0
> SQRT(number2) //开平方

#### **日期时间类**

> ADDTIME (date2 ,time_interval ) //将time_interval加到date2
> CONVERT_TZ (datetime2 ,fromTZ ,toTZ ) //转换时区
> CURRENT_DATE ( ) //当前日期
> CURRENT_TIME ( ) //当前时间
> CURRENT_TIMESTAMP ( ) //当前时间戳
> DATE (datetime ) //返回datetime的日期部分
> DATE_ADD (date2 , INTERVAL d_value d_type ) //在date2中加上日期或时间
> DATE_FORMAT (datetime ,FormatCodes ) //使用formatcodes格式显示datetime
> DATE_SUB (date2 , INTERVAL d_value d_type ) //在date2上减去一个时间
> DATEDIFF (date1 ,date2 ) //两个日期差
> DAY (date ) //返回日期的天
> DAYNAME (date ) //英文星期
> DAYOFWEEK (date ) //星期(1-7) ,1为星期天
> DAYOFYEAR (date ) //一年中的第几天
> EXTRACT (interval_name FROM date ) //从date中提取日期的指定部分
> MAKEDATE (year ,day ) //给出年及年中的第几天,生成日期串
> MAKETIME (hour ,minute ,second ) //生成时间串
> MONTHNAME (date ) //英文月份名
> NOW ( ) //当前时间
> SEC_TO_TIME (seconds ) //秒数转成时间
> STR_TO_DATE (string ,format ) //字串转成时间,以format格式显示
> TIMEDIFF (datetime1 ,datetime2 ) //两个时间差
> TIME_TO_SEC (time ) //时间转秒数]
> WEEK (date_time [,start_of_week ]) //第几周
> YEAR (datetime ) //年份
> DAYOFMONTH(datetime) //月的第几天
> HOUR(datetime) //小时
> LAST_DAY(date) //date的月的最后日期
> MICROSECOND(datetime) //微秒
> MONTH(datetime) //月
> MINUTE(datetime) //分
>
> 
>  
>
> 附:可用在INTERVAL中的类型
> DAY ,DAY_HOUR ,DAY_MINUTE ,DAY_SECOND ,HOUR ,HOUR_MINUTE ,HOUR_SECOND ,MINUTE ,MINUTE_SECOND,MONTH ,SECOND ,YEAR 



## 定时事件

### **存储过程整合event事件**

### （1）**利用存储过程+event事件的方式，实现彩票的3D开奖。**

  步骤如下：
  **a）规定每3分钟开一次奖，先编写存储过程open_lucky，用于产生3个随机数，并生成一条开奖记录。**
  **b）编写一个时间调度器，每3分钟调用一次这个过程**

#### **开奖的存储过程`open_lucky()`**

```mysql
create procedure open_lucky()
begin
	insert into tsp_lucky(num1, num2, num3, ctime)
	select FLOOR(rand()*9)+1,FLOOR(rand()*9)+1,FLOOR(rand()*9)+1,now();
end;

```

#### **定时事件`lucky_event`**

![image-20200721150200922](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721150201.png)

### （2）**深入解析event事件的创建**

![image-20200721150301373](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721150301.png)

### （3）**执行时间的实例**

![image-20200721150357724](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721150357.png)

## **在存储过程中使用游标**

（1）游标的定义：**游标是用于保存结果的临时区域。**

（2）实例。**结合游标，利用存储过程逐一更新id为偶数的user。**

![image-20200721150747876](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200721150748.png)