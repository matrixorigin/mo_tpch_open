# **完成 TPCH 测试**

TPC Benchmark™H（TPC-H）是决策支持基准。它由一套面向业务的即时查询（ad-hoc）和并发数据修改组成。选择查询和填充数据库的数据具有广泛的行业相关性。该基准测试解释说明了决策支持系统，该系统可检查大量数据，执行高度复杂的查询并为关键业务问题提供答案。TPC-H 是 OLAP 数据库广泛使用的基准测试。

通过阅读本教程，您将学习如何使用 MatrixOne Cloud 完成 TPC-H 测试。

## **1. 生成数据**

使用wget命令下载 tpch 工具包生成 TPC-H 标准测试集并解压， `scale factor=100`表示产生100GB 的完整数据集，当使用 ` 1` 时会产生大约 1GB 的数据集，以此类推，默认生成100G数据，-c表示生成lineitem, orders, partsupp表数据的线程数，默认为10线程。

```
wget https://xxx/tpch.zip
unzip tpch
cd tpch

./bin/gen-tpch-data.sh -s 1
```

生成完整数据集可能需要一段时间。完成后，您可以看到结果文件。

```
total 2150000
-rw-r--r-- 1 root root 24346144 Jan 29 15:44 customer.tbl
-rw-r--r-- 1 root root 75452190 Jan 29 15:44 lineitem.tbl.1
-rw-r--r-- 1 root root 76161204 Jan 29 15:44 lineitem.tbl.10
-rw-r--r-- 1 root root 75599008 Jan 29 15:44 lineitem.tbl.2
-rw-r--r-- 1 root root 76097735 Jan 29 15:44 lineitem.tbl.3
-rw-r--r-- 1 root root 76031989 Jan 29 15:44 lineitem.tbl.4
-rw-r--r-- 1 root root 76094827 Jan 29 15:44 lineitem.tbl.5
-rw-r--r-- 1 root root 76249883 Jan 29 15:44 lineitem.tbl.6
-rw-r--r-- 1 root root 75997797 Jan 29 15:44 lineitem.tbl.7
-rw-r--r-- 1 root root 76125864 Jan 29 15:44 lineitem.tbl.8
-rw-r--r-- 1 root root 76052790 Jan 29 15:44 lineitem.tbl.9
-rw-r--r-- 1 root root     2224 Jan 29 15:43 nation.tbl
-rw-r--r-- 1 root root 17046953 Jan 29 15:44 orders.tbl.1
-rw-r--r-- 1 root root 17223199 Jan 29 15:44 orders.tbl.10
-rw-r--r-- 1 root root 17128525 Jan 29 15:44 orders.tbl.2
-rw-r--r-- 1 root root 17231604 Jan 29 15:44 orders.tbl.3
-rw-r--r-- 1 root root 17232254 Jan 29 15:44 orders.tbl.4
-rw-r--r-- 1 root root 17219809 Jan 29 15:44 orders.tbl.5
-rw-r--r-- 1 root root 17217644 Jan 29 15:44 orders.tbl.6
-rw-r--r-- 1 root root 17221252 Jan 29 15:44 orders.tbl.7
-rw-r--r-- 1 root root 17224480 Jan 29 15:44 orders.tbl.8
-rw-r--r-- 1 root root 17206441 Jan 29 15:44 orders.tbl.9
-rw-r--r-- 1 root root 11807905 Jan 29 15:44 partsupp.tbl.1
-rw-r--r-- 1 root root 11925145 Jan 29 15:44 partsupp.tbl.10
-rw-r--r-- 1 root root 11869229 Jan 29 15:44 partsupp.tbl.2
-rw-r--r-- 1 root root 11860397 Jan 29 15:44 partsupp.tbl.3
-rw-r--r-- 1 root root 11860682 Jan 29 15:44 partsupp.tbl.4
-rw-r--r-- 1 root root 11865929 Jan 29 15:44 partsupp.tbl.5
-rw-r--r-- 1 root root 11942621 Jan 29 15:44 partsupp.tbl.6
-rw-r--r-- 1 root root 11947596 Jan 29 15:44 partsupp.tbl.7
-rw-r--r-- 1 root root 11947206 Jan 29 15:44 partsupp.tbl.8
-rw-r--r-- 1 root root 11957906 Jan 29 15:44 partsupp.tbl.9
-rw-r--r-- 1 root root 24135125 Jan 29 15:43 part.tbl
-rw-r--r-- 1 root root      389 Jan 29 15:43 region.tbl
-rw-r--r-- 1 root root  1409184 Jan 29 15:43 supplier.tbl

```

## **2. 在 MatrixOne 中建表**

修改配置文件 `conf/matrxione.conf`，指定MatrixOne Cloud的地址、用户名、密码，配置文件示例如下

```
# MatrixOne host
export HOST='127.0.0.1'
# MatrixOne port
export PORT=6001
# MatrixOne username
export USER='root'
# MatrixOne password
export PASSWORD='111'
# The database where TPC-H tables located
export DB='tpch'
```

然后执行以下脚本进行建表操作。

```
./bin/create-tpch-tables.sh
```

## **3. 导入数据**

执行以下脚本导入TPC-H测试所需数据，-c可以指定执行导入的线程数，默认为5个线程

```
./bin/load-tpch-data.sh
```

加载完成后，可以使用创建的表查询 MatrixOne 中的数据。

## **4. 查询数据**

```sql
./bin/run-tpch-queries.sh
```

## **5. 运行结果示例**

> 查询结果的单位是 ms。

| SQL  | Time(ms) | Time(ms) | Time(ms) | Time(ms) |
| ---- | -------- | ---- | -------- | ---- |
| Q1   | 126      | 604  | 244      | 209  |
| Q2   | 113      | 186  | 159      | 159  |
| Q3   | 136      | 239  | 150      | 150  |
| Q4   | 120      | 185  | 138      | 132  |
| Q5   | 120      | 157  | 135      | 127  |
| Q6   | 60       | 72   | 62       | 62   |
| Q7   | 106      | 158  | 381      | 108  |
| Q8   | 170      | 110  | 95       | 90   |
| Q9   | 850      | 175  | 192      | 179  |
| Q10  | 253      | 184  | 170      | 168  |
| Q11  | 936      | 220  | 185      | 185  |
| Q12  | 90       | 187  | 111      | 111  |
| Q13  | 283      | 596  | 292      | 290  |
| Q14  | 83       | 120  | 74       | 67   |
| Q15  | 80       | 94   | 89       | 86   |
| Q16  | 883      | 270  | 254      | 235  |
| Q17  | 286      | 113  | 88       | 78   |
| Q18  | 226      | 243  | 275      | 234  |
| Q19  | 193      | 140  | 92       | 92   |
| Q20  | 220      | 70   | 73       | 65   |
| Q21  | 316      | 271  | 232      | 231  |
| Q22  | 93       | 91   | 93       | 84   |





