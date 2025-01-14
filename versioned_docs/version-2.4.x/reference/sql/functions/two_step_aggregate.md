---
sidebar_position: 6
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# 两步聚合

首先，通过使用聚合函数创建一个中间聚合，而不是在一步中计算最终结果。然后，使用分析函数计算最终结果。



<Tabs groupId="editions">

<TabItem value="Community" label="社区版">

</TabItem>

<TabItem value="Enterprise" label="企业版">

### stats_agg

对二维数据执行线性回归分析，例如计算相关系数和协方差。 并且还可以分别计算每个维度的常见统计数据，将数据聚合成中间统计聚合形式，以便进行进一步计算。

:::tip

只有 `x` 和 `y` 都不为空时才会纳入聚合。

:::

```sql
stats_agg(y, x)
```

| 选项 | 描述                              |
| ---- | --------------------------------- |
| `y`  | 要进行统计聚合的数据集中的`y`值。 |
| `x`  | 要进行统计聚合的数据集中的`x`值。 |

<details>
  <summary>查看 <code>stats_agg</code> 示例</summary>

**示例数据集。**

```sql {1}
SELECT * FROM test_stats;
+-------------------------------+---+---+
| time                          | x | y |
+-------------------------------+---+---+
| 1970-01-01T00:00:00.000000001 | 1 | 1 |
| 1970-01-01T00:00:00.000000002 | 1 | 2 |
| 1970-01-01T00:00:00.000000003 | 1 | 3 |
| 1970-01-01T00:00:00.000000004 | 1 | 4 |
| 1970-01-01T00:00:00.000000005 | 1 | 5 |
| 1970-01-01T00:00:00.000000006 | 2 | 1 |
| 1970-01-01T00:00:00.000000007 | 2 | 2 |
| 1970-01-01T00:00:00.000000008 | 2 | 3 |
| 1970-01-01T00:00:00.000000009 | 2 | 4 |
| 1970-01-01T00:00:00.000000010 | 2 | 5 |
+-------------------------------+---+---+
```

**使用 `stats_agg` 聚合结果。**

```sql {1}
SELECT stats_agg(y, x) FROM test_stats;
+------------------------------------------------------------------------------------------------------------------------------------------------------------+
| stats_agg(test_stats.y,test_stats.x)                                                                                                                       |
+------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {n: 10, sx: 15.0, sx2: 2.5, sx3: -2.7755575615628914e-16, sx4: 0.6249999999999999, sy: 30.0, sy2: 20.0, sy3: -1.7763568394002505e-15, sy4: 68.0, sxy: 0.0} |
+------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

**以上结果返回了一个结果集，每个返回值的解释分别为：**

```sql
{ 
  n:   bigint, -- count 
  sx:  double, -- sum(x)- sum(x)
  sx2: double, -- sum((x-sx/n)^2) (sum of squares)
  sx3: double, -- sum((x-sx/n)^3)
  sx4: double, -- sum((x-sx/n)^4)
  sy:  double, -- sum(y)
  sy2: double, -- sum((y-sy/n)^2) (sum of squares)
  sy3: double, -- sum((y-sy/n)^3)
  sy4: double, -- sum((y-sy/n)^4)
  sxy: double, -- sum((x-sx/n)*(y-sy/n)) (sum of products) 
}
```

</details> 

**`stats_agg` 支持以下二次聚合的函数**

| 函数                                 | 描述                                                    |
| ------------------------------------ | ------------------------------------------------------- |
| `num_vals`                           | 计算二维统计总量中的数值个数。                          |
| `average_y`, `average_x`             | 计算二维统计聚合后指定维度的平均值。                    |
| `sum_y`,` sum_x`                     | 计算二维统计聚合后指定维度的和，方式为 population。     |
| `stddev_samp_y`, `stddev_samp_x`     | 计算二维统计聚合后指定维度的标准差，方式为 sample。     |
| `stddev_pop_y`, `stddev_pop_x`       | 计算二维统计聚合后指定维度的标准差，方式为 population。 |
| `var_samp_y`,` var_samp_x`           | 计算二维统计聚合后指定维度的方差，方式为 sample。       |
| `var_pop_y`,` var_pop_x`             | 计算二维统计聚合后指定维度的方差，方式为 population。   |
| `skewness_samp_y`, `skewness_samp_x` | 计算二维统计聚合后指定维度的偏度值，方式为 sample。     |
| `skewness_pop_y`, `skewness_pop_x`   | 计算二维统计聚合后指定维度的偏度值，方式为 population。 |
| `kurtosis_samp_y`,` kurtosis_samp_x` | 计算二维统计聚合后指定维度的峰度值，方式为 sample。     |
| `kurtosis_pop_y`, `kurtosis_pop_x`   | 计算二维统计聚合后指定维度的峰度值，方式为 population。 |
| `correlation`                        | 计算二维统计聚合后的相关。                              |
| `covariance_samp`, `covariance_pop`  | 计算二维统计聚合后的协方差。                            |
| `determination_coeff`                | 计算二维统计聚合后的决定系数。                          |
| `slope`                              | 根据二维统计聚合，计算线性拟合线的斜率。                |
| `intercept`                          | 计算二维统计聚合后y的截距。                             |
| `x_intercept`                        | 计算二维统计聚合后x的截距。                             |

<details>
  <summary>查看二次聚合的示例</summary>

```sql {1}
SELECT stddev_samp_x(stats_agg(y, x)) FROM test_stats;
+-----------------------------------------------------+
| stddev_samp_x(stats_agg(test_stats.y,test_stats.x)) |
+-----------------------------------------------------+
| 0.5270462766947299                                  |
+-----------------------------------------------------+
```

</details> 

</TabItem>

</Tabs>

### gauge_agg

分析 Gauge数据。与 Counter 不同，Gauge可以减少也可以增加。

```sql
gauge_agg(time, numeric_expression)
```

| 选项                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| `numeric_expression` | 要操作的数字表达式。可以是常量、列或函数，以及算术运算符的任意组合。 |

<details>
  <summary>查看 <code>gauge_agg</code> 示例</summary>

```sql {1}
SELECT gauge_agg(time, pressure) FROM air GROUP BY date_trunc('month', time);
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| gauge_agg(air.time,air.pressure)                                                                                                                                                                                |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {first: {ts: 2023-03-01T00:00:00, val: 54.0}, second: {ts: 2023-03-01T00:00:00, val: 59.0}, penultimate: {ts: 2023-03-14T16:00:00, val: 55.0}, last: {ts: 2023-03-14T16:00:00, val: 80.0}, num_elements: 13122} |
| {first: {ts: 2023-02-01T00:00:00, val: 60.0}, second: {ts: 2023-02-01T00:00:00, val: 54.0}, penultimate: {ts: 2023-02-28T23:57:00, val: 74.0}, last: {ts: 2023-02-28T23:57:00, val: 59.0}, num_elements: 26880} |
| {first: {ts: 2023-01-14T16:00:00, val: 63.0}, second: {ts: 2023-01-14T16:00:00, val: 68.0}, penultimate: {ts: 2023-01-31T23:57:00, val: 54.0}, last: {ts: 2023-01-31T23:57:00, val: 77.0}, num_elements: 16640} |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

</details>

**`gauge_agg` 支持以下二次聚合的函数**

<Tabs groupId="editions">

<TabItem value="Community" label="社区版">

| 函数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `delta`        | 获取一段时间内Gauge的变化。这是简单的增量，通过从第一个值减去最后一个看到的值来计算。 |
| `time_delta`   | 获取持续时间，最后一个 Gauge 的时间减去第一个 Gauge 的时间。 |
| `rate`         | 计算 Gauge 变化和时间变化的比率。                            |
| `idelta_left`  | 计算 Gauge 最早的瞬时变化。这等于第二个值减去第一个值。      |
| `idelta_right` | 计算 Gauge 最晚的瞬时变化。这等于最后一个值值减去倒数第二个值。 |

</TabItem>

<TabItem value="Enterprise" label="企业版">

| 函数           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `delta`        | 获取一段时间内Gauge的变化。这是简单的增量，通过从第一个值减去最后一个看到的值来计算。 |
| `time_delta`   | 获取持续时间，最后一个 Gauge 的时间减去第一个 Gauge 的时间。 |
| `rate`         | 计算 Gauge 变化和时间变化的比率。                            |
| `first_time`   | 取得 Gauge 中最小的时间戳。                                  |
| `last_time`    | 取得 Gauge 中最大的时间戳。                                  |
| `first_val`    | 取得 Gauge 中最小时间戳对应的值。                            |
| `last_val`     | 取得 Gauge 中最大时间戳对应的值。                            |
| `idelta_left`  | 计算 Gauge 最早的瞬时变化。这等于第二个值减去第一个值。      |
| `idelta_right` | 计算 Gauge 最晚的瞬时变化。这等于最后一个值值减去倒数第二个值。 |

</TabItem>

</Tabs>

### compact_state_agg

给定一个在离散状态之间切换的系统或值，汇总每个状态所花费的时间。例如，您可以使用`compact_state_agg`函数来跟踪系统在`error`、`running`或`starting`状态下花费的时间。

```sql
compact_state_agg(time_expression, state)
```
| 选项              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `time_expression` | 要操作的时间表达式。可以是常量、列或函数，以及算术运算符的任意组合。 |

<details>
  <summary>查看 <code>compact_state_agg</code> 示例</summary>

**示例数据集如下：**

```sql {1,2,3}
CREATE TABLE states(state STRING);
INSERT INTO states VALUES ('2020-01-01 10:00:00', 'starting'),('2020-01-01 10:30:00', 'running'),('2020-01-03 16:00:00', 'error'),('2020-01-03 18:30:00', 'starting'),('2020-01-03 19:30:00', 'running'),('2020-01-05 12:00:00', 'stopping');
SELECT * FROM states;
+---------------------+----------+
| time                | state    |
+---------------------+----------+
| 2020-01-01T10:00:00 | starting |
| 2020-01-01T10:30:00 | running  |
| 2020-01-03T16:00:00 | error    |
| 2020-01-03T18:30:00 | starting |
| 2020-01-03T19:30:00 | running  |
| 2020-01-05T12:00:00 | stopping |
+---------------------+----------+
```

**使用 `compact_state_agg` 函数聚合：**

```sql {1}
SELECT compact_state_agg(time, state) FROM states;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| compact_state_agg(states.time,states.state)                                                                                                                                                                                                                                                                                                                                          |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {state_duration: [{state: error, duration: 0 years 0 mons 0 days 2 hours 30 mins 0.000000000 secs}, {state: starting, duration: 0 years 0 mons 0 days 1 hours 30 mins 0.000000000 secs}, {state: stopping, duration: 0 years 0 mons 0 days 0 hours 0 mins 0.000000000 secs}, {state: running, duration: 0 years 0 mons 3 days 22 hours 0 mins 0.000000000 secs}], state_periods: []} |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

</details>

**以上示例将状态数据汇总在一起，以便进一步分析，`compact_state_agg` 支持如下二次聚合函数：**

| 函数                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| [`duration_in`](#duration_in) | 统计某个状态的持续时间，或统计某个状态在某个时间段内的持续时间。 |

#### duration_in

```sql
duration_in(state_agg_data, state [,begin_time, interval_time]) 
```

| 选项             | 描述                                                   |
| ---------------- | ------------------------------------------------------ |
| `state_agg_data` | `state_agg_data` 函数返回的结果集。                    |
| `state`          | any 与 compact_state_agg 的 state 类型相同。           |
| `begin_time`     | 可选，指定时间段内的开始时间。                         |
| `interval_time`  | 可选，指定时间段的持续时间，不指定时，时间段为无穷大。 |

<details>
  <summary>查看 <code>duration_in</code> 示例</summary>

```sql {1}
SELECT duration_in(compact_state_agg(time, state), 'running') FROM states;
+--------------------------------------------------------------------------+
| duration_in(compact_state_agg(states.time,states.state),Utf8("running")) |
+--------------------------------------------------------------------------+
| 0 years 0 mons 3 days 22 hours 0 mins 0.000000000 secs                   |
+--------------------------------------------------------------------------+
```

</details>

### state_agg

给定一个在离散状态之间切换的系统或值，跟踪状态之间的转换。

```sql
state_agg(time_expression, state)
```

统计每个状态所花费的时间。

<details>
  <summary>查看 <code>state_agg</code> 示例</summary>

**示例数据集如下：**

```sql {1,2,3}
CREATE TABLE states(state STRING);
INSERT INTO states VALUES('2020-01-01 10:00:00', 'starting'),('2020-01-01 10:30:00', 'running'),('2020-01-03 16:00:00', 'error'),('2020-01-03 18:30:00', 'starting'),('2020-01-03 19:30:00', 'running'),('2020-01-05 12:00:00', 'stopping');
SELECT * FROM states;
+---------------------+----------+
| time                | state    |
+---------------------+----------+
| 2020-01-01T10:00:00 | starting |
| 2020-01-01T10:30:00 | running  |
| 2020-01-03T16:00:00 | error    |
| 2020-01-03T18:30:00 | starting |
| 2020-01-03T19:30:00 | running  |
| 2020-01-05T12:00:00 | stopping |
+---------------------+----------+
```

**使用 `state_agg` 函数聚合：**

```sql {1}
SELECT state_agg(time, state) FROM states;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| state_agg(states.time,states.state)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {state_duration: [{state: running, duration: 0 years 0 mons 3 days 22 hours 0 mins 0.000000000 secs}, {state: error, duration: 0 years 0 mons 0 days 2 hours 30 mins 0.000000000 secs}, {state: stopping, duration: 0 years 0 mons 0 days 0 hours 0 mins 0.000000000 secs}, {state: starting, duration: 0 years 0 mons 0 days 1 hours 30 mins 0.000000000 secs}], state_periods: [{state: running, periods: [{start_time: 2020-01-01T10:30:00, end_time: 2020-01-03T16:00:00}, {start_time: 2020-01-03T19:30:00, end_time: 2020-01-05T12:00:00}]}, {state: starting, periods: [{start_time: 2020-01-01T10:00:00, end_time: 2020-01-01T10:30:00}, {start_time: 2020-01-03T18:30:00, end_time: 2020-01-03T19:30:00}]}, {state: error, periods: [{start_time: 2020-01-03T16:00:00, end_time: 2020-01-03T18:30:00}]}]} |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

</details>

**以上示例将状态数据汇总在一起，以便进一步分析，`state_agg` 支持如下二次聚合函数：**

| 函数                            | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| [`duration_in`](#duration_in-1) | 统计某个状态的持续时间，或统计某个状态在某个时间段内的持续时间。 |
| [state_at](#state_at)           | 统计一时刻所处的状态。                                       |

#### duration_in

```sql
duration_in(state_agg_data, state [,begin_time, interval_time]) 
```

| 选项             | 描述                                                   |
| ---------------- | ------------------------------------------------------ |
| `state_agg_data` | `state_agg` 函数返回的结果集。                         |
| `state`          | `any` 与 `compact_state_agg` 的 `state` 类型相同。     |
| `begin_time`     | 可选，指定时间段内的开始时间。                         |
| `interval_time`  | 可选，指定时间段的持续时间，不指定时，时间段为无穷大。 |

<details>
  <summary>查看 <code>duration_in</code> 示例</summary>

**统计 'running' 状态的持续时间。

```sql {1}
SELECT duration_in(state_agg(time, state), 'running') FROM states;
+------------------------------------------------------------------+
| duration_in(state_agg(states.time,states.state),Utf8("running")) |
+------------------------------------------------------------------+
| 0 years 0 mons 3 days 22 hours 0 mins 0.000000000 secs           |
+------------------------------------------------------------------+
```

**统计从 '2020-01-01 11:00:00' 开始 'running' 状态的持续时间。**

```sql {1}
SELECT duration_in(state_agg(time, state), 'running', Timestamp '2020-01-01 11:00:00') FROM states;
+----------------------------------------------------------------------------------------------+
| duration_in(state_agg(states.time,states.state),Utf8("running"),Utf8("2020-01-01 11:00:00")) |
+----------------------------------------------------------------------------------------------+
| 0 years 0 mons 3 days 21 hours 30 mins 0.000000000 secs                                      |
+----------------------------------------------------------------------------------------------+
```

**统计 从2020-01-01 11:00:00 开始的四天内 'running' 状态的持续时间。**

```sql {1}
SELECT duration_in(state_agg(time, state), 'running', Timestamp '2020-01-01 11:00:00', interval '4 day') FROM states;
+-------------------------------------------------------------------------------------------------------------------------------------------+
| duration_in(state_agg(states.time,states.state),Utf8("running"),Utf8("2020-01-01 11:00:00"),IntervalMonthDayNano("73786976294838206464")) |
+-------------------------------------------------------------------------------------------------------------------------------------------+
| 0 years 0 mons 3 days 20 hours 30 mins 0.000000000 secs                                                                                   |
+-------------------------------------------------------------------------------------------------------------------------------------------+
```

</details>

#### state_at

```
state_at(state_agg_data, time_expression)
```

| 选项              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `state_agg_data`  | `state_agg` 函数返回的结果集。                               |
| `time_expression` | 要操作的时间表达式。可以是常量、列或函数，以及算术运算符的任意组合。 |

<details>
  <summary>查看 <code>state_at</code> 示例</summary>

```sql {1}
SELECT state_at(state_agg(time, state), Timestamp '2020-01-01 10:30:00') FROM states;
+---------------------------------------------------------------------------+
| state_at(state_agg(states.time,states.state),Utf8("2020-01-01 10:30:00")) |
+---------------------------------------------------------------------------+
| running                                                                   |
+---------------------------------------------------------------------------+
```

</details>



<Tabs groupId="editions">

<TabItem value="Community" label="社区版">

</TabItem>

<TabItem value="Enterprise" label="企业版">

### candlestick_agg

进行金融资产数据分析，能得到股票的开盘价和收盘价，以及最低和最高价。

```sql
candlestick_agg(time, price, volume)
```

<details>
  <summary>查看 <code>candlestick_agg</code> 示例</summary>

**示例数据集如下：**

```sql {1-3}
CREATE TABLE IF NOT EXISTS tick(price bigint ,volume bigint);
INSERT tick(time, price, volume) VALUES('1999-12-31 00:00:00.000', 111, 444),('1999-12-31 00:00:00.005', 222, 444),('1999-12-31 00:00:00.010', 333, 222),('1999-12-31 00:00:10.015', 444, 111),('1999-12-31 00:00:10.020', 222, 555),('1999-12-31 00:10:00.025', 333, 555),('1999-12-31 00:10:00.030', 444, 333),('1999-12-31 01:00:00.035', 555, 222);
SELECT * FROM tick;
+-------------------------+-------+--------+
| time                    | price | volume |
+-------------------------+-------+--------+
| 1999-12-31T00:00:00     | 111   | 444    |
| 1999-12-31T00:00:00.005 | 222   | 444    |
| 1999-12-31T00:00:00.010 | 333   | 222    |
| 1999-12-31T00:00:10.015 | 444   | 111    |
| 1999-12-31T00:00:10.020 | 222   | 555    |
| 1999-12-31T00:10:00.025 | 333   | 555    |
| 1999-12-31T00:10:00.030 | 444   | 333    |
| 1999-12-31T01:00:00.035 | 555   | 222    |
+-------------------------+-------+--------+
```

**使用 `candlestick_agg` 进行聚合。**

```sql {1}
SELECT candlestick_agg(time, price, volume) FROM tick;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| candlestick_agg(tick.time,tick.price,tick.volume)                                                                                                                                                                                   |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {open: {ts: 1999-12-31T00:00:00, val: 111.0}, close: {ts: 1999-12-31T01:00:00.035, val: 555.0}, low: {ts: 1999-12-31T00:00:00, val: 111.0}, high: {ts: 1999-12-31T01:00:00.035, val: 555.0}, volume: {vol: 2886.0, vwap: 850149.0}} |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

</details>

**可以在上述示例中分别提取开盘价、收盘价等。支持的函数有：**

| 函数         | 描述                 |
| ------------ | -------------------- |
| `close`      | 收盘价。             |
| `close_time` | 收盘时间。           |
| `high`       | 最高价。             |
| `high_time`  | 最高价时间。         |
| `low`        | 最低价。             |
| `low_time`   | 最低价时间。         |
| `open`       | 开盘价。             |
| `open_time`  | 开盘时间。           |
| `volume`     | 总成加量。           |
| `vwap`       | 成交量加权平均价格。 |

<details>
  <summary>查看示例</summary>

```sql {1}
SELECT close(candlestick_agg(time,price,volume)) AS close_price FROM tick;
+-------------+
| close_price |
+-------------+
| 555.0       |
+-------------+
```

</details>

</TabItem>

</Tabs>

