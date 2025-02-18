# MO-Tester 简介

从 0.5.0 版本开始，MatrixOne 引入了一个自动测试框架 [MO-Tester](https://github.com/matrixorigin/mo-tester)。

MO-Tester 测试框架，也可以称作为测试器，是通过 SQL 测试 MatrixOne 或其他数据库功能的。

MO-Tester 是基于 Java 语言进行开发，用于 MatrixOne 的测试套件。MO-Tester 构建了一整套完整的工具链来进行 SQL 自动测试。它包含测试用例和运行结果。MO-Tester 启动后，MO-Tester 将使用 MatrixOne 运行所有 SQL 测试用例，并将所有输出 SQL 测试结果与预期结果进行比较。所有案例的结果无论成功或者失败，都将记录在报告中。

MO-Tester 相关用例、结果和报告的放在 [MatrixOne](https://github.com/matrixorigin/matrixone) 仓库内，链接如下：

* *Cases*: <https://github.com/matrixorigin/matrixone/tree/main/test/distributed/cases>

* *Result*: 生成在 **/cases** 的具体测试用例下，例如 [/cases/auto_increment](https://github.com/matrixorigin/matrixone/tree/main/test/distributed/cases/auto_increment) 目录的具体测试用例同级目录下生成对应的*. result* 文件。

* *Report*: 运行结束后，本地目录自动生成 `mo-tester/report`。

测试用例和测试结果一一对应。如需添加新的测试用例和测试结果请进入右侧所示 MatrixOne 仓库路径中进行添加：<https://github.com/matrixorigin/matrixone/tree/main/test>

## 使用 MO-Tester

### 1. 准备测试环境

* 请先确认已安装 jdk8。

* 启动 MatrixOne 或其他数据库用例。参见更多信息 >>[安装单机版 MatrixOne](../../Get-Started/install-standalone-matrixone.md).

* 克隆 *mo-tester* 仓库。

   ```
   git clone https://github.com/matrixorigin/mo-tester.git
   ```

* 克隆 *matrixone* 仓库。

   ```
   git clone https://github.com/matrixorigin/matrixone.git
   ```

### 2. 配置 MO-Tester

MO-tester 基于 Java 语言进行开发，因此 Mo-tester 所依赖的 Java 数据库连接（JDBC，Java Database Connectivity）驱动程序需要配置 *mo.yml* 文件里的参数信息：进入到 *mo-tester* 本地仓库，打开 *mo.yml* 文件，配置服务器地址、默认的数据库名称、用户名和密码等。

以下是本地独立版本 MatrixOne 的默认示例。

  ```
  #jdbc
  jdbc:
    driver: "com.mysql.cj.jdbc.Driver"
    server:
    - addr: "127.0.0.1:6001"
    database:
      default: "test"
    paremeter:
      characterSetResults: "utf8"
      continueBatchOnError: "false"
      useServerPrepStmts: "true"
      alwaysSendSetIsolation: "false"
      useLocalSessionState: "true"
      zeroDateTimeBehavior: "CONVERT_TO_NULL"
      failoverReadOnly: "false"
      serverTimezone: "Asia/Shanghai"
      socketTimeout: 30000
  #users
  user:
    name: "dump"
    passwrod: "111"
  ```

### 3. 运行 MO-Tester

运行以下所示命令行，SQL 所有测试用例将自动运行，并将报告和错误消息生成至 *report/report.txt* 和 *report/error.txt* 文件中。

```
> ./run.sh -p {path_name}/matrixone/test/cases
```

如果你想调整测试范围，你可以修改 `run.yml` 文件中的 `path` 参数。或者，在执行 `./run.sh` 命令时，你也可以指定一些参数，参数解释如下：

|参数 | 参数释义|
|---|---|
|-p|设置由 MO-tester 执行的测试用例的路径。默认值可以参见 *run.yml* 文件中 `path` 的配置参数|
|-m|设置 MO-tester 测试的方法，即直接运行或者生成新的测试结果。默认值可以参见 *run.yaml* 文件中 `method` 的配置参数|
|-t| 设置 MO-tester 执行 SQL 命令的格式类型。默认值可以参见 *run.yml* 文件中 `type` 的配置参数。|
|-r| 设置测试用例应该达到的成功率。默认值可以参见 *run.yml* 文件中 `rate` 的配置参数。|
|-i|设置包含列表，只有路径中名称包含其中一个列表的脚本文件将被执行，如果有多个，如果没有指定，用'，'分隔，指的是包含的所有情况 set the including list, and only script files in the path whose name contains one of the lists will be executed, if more than one, separated by `,`, if not specified, refers to all cases included|
|-e|设置排除列表，如果路径下的脚本文件的名称包含一个排除列表，则不会被执行，如果有多个，用','分隔，如果没有指定，表示不排除任何情况 set the excluding list, and script files in the path whose name contains one of the lists will not be executed, if more than one, separated by `,`, if not specified, refers to none of the cases excluded|
|-g|表示带有[-- @bvt:issue#{issueNO.}]标志的 SQL 命令将不会被执行，该标志以 [-- @bvt:issue#{issueNO.}]开始，以 [-- @bvt:issue]结束。例如，<br>-- @bvt:issue#3236<br/><br>select date_add("1997-12-31 23:59:59",INTERVAL "-10000:1" HOUR_MINUTE);<br/><br>select date_add("1997-12-31 23:59:59",INTERVAL "-100 1" YEAR_MONTH);<br/><br>-- @bvt:issue<br/><br>这两个 SQL 命令与问题 #3236 相关联，它们将不会在 MO-tester 测试中执行，直到问题 #3236 修复后标签移除才可以在测试中执行。<br/>|
|-n|表示在比较结果时将忽略结果集的元数据|
|-c|只需要检查 *case* 文件与 *result* 文件是否匹配|

**示例**：

```
./run.sh -p {path_name}/matrixone/test/cases -m run -t script -r 100 -i select,subquery -e substring -g
```

如果你想测试新的 SQL 用例并自动生成 SQL 结果，运行命令中可以将 `-m run` 更改为 `-m genrs`，或者将 *run.yml* 文件里的 `method` 参数修改为 `genrs`，且*. result* 文件将生成在与这个新的 SQL 用例同级目录内，相关示例参见<p><a href="#new_test_scenario">示例 4</a></p>

!!! note
    每次运行 `./run.sh` 都会覆盖 *mo-tester* 仓库内 *report/* 路径下 *error.txt*、*report.txt* 和 *success.txt* 报告文件。

### 4. 查看测试报告

测试完成后，*mo-tester* 仓库内将生成 *error.txt*、*report.txt* 和 *success.txt* 报告文件。

* *report.txt* 示例如下：

```
[SUMMARY] COST : 98s, TOTAL :12702, SUCCESS : 11851, FAILED :13, IGNORED :838, ABNORAML :0, SUCCESS RATE : 99%
[{path_name}/matrixone/test/cases/auto_increment/auto_increment_columns.sql] COST : 2.159s, TOTAL :185, SUCCESS :163, FAILED :0, IGNORED :22, ABNORAML :0, SUCCESS RATE : 100%
[{path_name}/matrixone/test/cases/benchmark/tpch/01_DDL/01_create_table.sql] COST : 0.226s, TOTAL :11, SUCCESS :11, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
[{path_name}/matrixone/test/cases/benchmark/tpch/02_LOAD/02_insert_customer.sql] COST : 0.357s, TOTAL :16, SUCCESS :16, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
```

|报告关键词 | 解释|
|---|---|
|TOTAL|执行的测试用例（SQL 语句）总数|
|SUCCESS|执行成功的测试用例（SQL 语句）总数|
|FAILED|执行失败的测试用例（SQL 语句）总数|
|IGNORED|忽略执行的测试用例（SQL 语句）总数，特指具有 `--bvt:issue` 标签的测试用例（SQL 语句）|
|ABNORAML|执行异常的测试用例（SQL 语句）总数，比如执行过程中被系统异常导致 MO 无法判断实际结果，或者 *.result* 文件解析失败等|
|SUCCESS RATE|成功率，即 SUCCESS/(TOTAL - IGNORED)|

* *error.txt* 示例如下：

```
[ERROR]
[SCRIPT   FILE]: cases/transaction/atomicity.sql
[ROW    NUMBER]: 14
[SQL STATEMENT]: select * from test_11 ;
[EXPECT RESULT]:
c	d
1	1
2 2
[ACTUAL RESULT]:
c	d
1	1
```

### 5. 测试示例

#### 示例 1

**示例描述**：运行 *matrixone* 仓库内的 *test/cases* 路径下的所有测试用例。

**步骤**：

1. 拉取最新的 *matrixone* 远端仓库。

   ```
   cd matrixone
   git pull https://github.com/matrixorigin/matrixone.git
   ```

2. 先切换进入到 *mo-tester* 仓库，运行如下命令，即可运行 *matrixone* 仓库内所有的测试用例：

   ```
   cd mo-tester
   ./run.sh -p {path_name}/matrixone/test/cases
   ```

3. 在 *MO-Tester* 仓库内 *report/* 路径下的 *error.txt*、*report.txt* 和 *success.txt* 报告文件中查看运行结果。

#### 示例 2

**示例描述**：运行 *matrixone* 仓库内 */cases/transaction/* 路径下的测试用例。

**步骤**：

1. 拉取最新的 *matrixone* 远端仓库。

   ```
   cd matrixone
   git pull https://github.com/matrixorigin/matrixone.git
   ```

2. 先切换进入到 *mo-tester* 仓库，运行如下命令，即可运行 *matrixone* 仓库内 *cases/transaction/* 路径的所有测试用例：

   ```
   cd mo-tester
   ./run.sh -p {path_name}/matrixone/test/cases/transaction/
   ```

3. 在 *MO-Tester* 仓库内 *report/* 路径下的 *error.txt*、*report.txt* 和 *success.txt* 报告文件中查看运行结果。例如，预期 *report.txt* 报告如下所示：

   ```
   [SUMMARY] COST : 5s, TOTAL :1362, SUCCESS : 1354, FAILED :0, IGNORED :8, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/atomicity.sql] COST : 0.575s, TOTAL :66, SUCCESS :66, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/autocommit.test] COST : 0.175s, TOTAL :50, SUCCESS :50, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/autocommit_1.sql] COST : 1.141s, TOTAL :296, SUCCESS :288, FAILED :0, IGNORED :8, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/autocommit_atomicity.sql] COST : 0.52s, TOTAL :75, SUCCESS :75, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/autocommit_isolation.sql] COST : 1.607s, TOTAL :215, SUCCESS :215, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/autocommit_isolation_1.sql] COST : 1.438s, TOTAL :241, SUCCESS :241, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/isolation.sql] COST : 1.632s, TOTAL :202, SUCCESS :202, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/isolation_1.sql] COST : 1.512s, TOTAL :217, SUCCESS :217, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   ```

#### 示例 3

**示例描述**：运行 *matrixone* 仓库内 *cases/transaction/atomicity.sql* 单个测试用例。

**步骤**：

1. 拉取最新的 *matrixone* 远端仓库。

   ```
   cd matrixone
   git pull https://github.com/matrixorigin/matrixone.git
   ```

2. 先切换进入到 *mo-tester* 仓库，运行如下命令，即可运行 *mo-tester* 仓库内 *cases/transaction/atomicity.sql* 测试用例：

   ```
   cd mo-tester
   ./run.sh -p {path_name}/matrixone/test/cases/transaction/atomicity.sql
   ```

3. 在 *MO-Tester* 仓库内 *report/* 路径下的 *error.txt*、*report.txt* 和 *success.txt* 报告文件中查看运行结果。例如，预期 *report.txt* 报告如下所示：

   ```
   [SUMMARY] COST : 0s, TOTAL :66, SUCCESS : 66, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/atomicity.sql] COST : 0.56s, TOTAL :66, SUCCESS :66, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   ```

#### <a name="new_test_scenario">示例 4</a>

**示例描述**：

- 新建一个命名为 *local_test* 的文件夹，放在 *{path_name}/matrixone/test/cases* 目录下
- 本地新增一个命名为测试文件 *new_test.sql*，放在 *{path_name}/matrixone/testcases/local_test/* 路径下
- 仅想要运行 *new_test.sql** 测试用例

**步骤**

1. 拉取最新的 *matrixone* 远端仓库。

   ```
   cd matrixone
   git pull https://github.com/matrixorigin/matrixone.git
   ```

2. 生成测试结果：

    - 方式 1：先切换进入到 *mo-tester* 仓库，运行如下命令，生成测试结果。

       ```
       cd mo-tester
       ./run.sh -p {path_name}/matrixone/test/cases/local_test/new_test.sql -m genrs -g
       ```

    - 方式 2：打开 *mo-tester* 仓库中 *run.yml* 文件，先将 *method* 参数由默认的 `run` 修改为 `genrs`，然后运行如下命令，生成测试结果。

       ```
       cd mo-tester
       ./run.sh -p {path_name}/matrixone/test/cases/local_test/new_test.sql
       ```

3. 在 *matrixone* 仓库内 *test/cases/local_test/* 路径下查看 *new_test.result* 结果。

4. 在 *mo-tester* 仓库内 *report/* 路径下的 *error.txt*、*report.txt* 和 *success.txt* 报告文件中查看运行结果。例如，*report.txt* 中结果如下所示：

   ```
   [SUMMARY] COST : 0s, TOTAL :66, SUCCESS : 66, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   [{path_name}/matrixone/test/cases/transaction/atomicity.sql] COST : 0.56s, TOTAL :66, SUCCESS :66, FAILED :0, IGNORED :0, ABNORAML :0, SUCCESS RATE : 100%
   ```

## 参考文档

更多关于 MO-Tester 测试工具的注解以及测试用例编写规范，参见 [MO-Tester 规范要求](mo-tester-reference.md)。
