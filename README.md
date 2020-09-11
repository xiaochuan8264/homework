# 数据分析课后练习作业
----
## 第四周
### 作业二
#### 1.截止2020年6月30，客户的销户率和开户首刷率分别是多少？
```mysql
SELECT 
	# close_dt不等于3000-12-31即客户销户，以该数据除以总账户数即为销户率
    COUNT(CASE
        WHEN NOT close_dt = DATE '3000-12-31' THEN acct_num
    END) / COUNT(DISTINCT acct_num) `客户销户率`,
    # first_posting_dt不等于3000-12-31即已进行首刷，以该数据除以总账户数即为开户首刷率
    COUNT(CASE
        WHEN NOT first_posting_dt = DATE '3000-12-31' THEN acct_num
    END) / COUNT(DISTINCT acct_num) `开户首刷率`
FROM
	# 限定数据为截至到2020-06-30这一天，同时仅选择所需字段，节约内存
    (SELECT 
        acct_num, first_posting_dt, close_dt
    FROM
        cd_acct_info
    WHERE
        statistics_dt = '2020-06-30') t;
```
