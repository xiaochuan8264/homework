
```mysql
 /* 
			 ##选取数据（第三步）##
             1. 选取题目所需目标数据，选取季度排行第一的省份(字段province_quarter_rank)
		 */
SELECT province, year, quarter, total_sales, order_count, avg_payment 
FROM
	(   /* 
			 ##数据聚合（第二步）##
             1. 以province, year, quater进行分组聚合，
				得到每个省份每年每季的销售总额及平均价格
			 2. 使用row_number函数将year, quarter字段下的数据进行排序，
				得到每年每季度省份销售额度排名
		 */
	SELECT
		province,
		year,
		quarter,
		sum( payment_amount ) total_sales,
		count( order_id ) order_count,
		avg( payment_amount ) avg_payment,
		row_number() over ( PARTITION BY year, quarter ORDER BY sum( payment_amount ) DESC ) 
        AS province_quarter_rank 
	FROM
		( /* 
			 ##数据预处理（第一步）##
             1. order_info与customer_info使用inner join进行联结查询，
				为每一个order添加一个province字段
			 2. 使用substring得到年份，判断语句case when进行季度标记
		 */
        SELECT
			a.order_id,
			a.customer_id,
			b.province,
			SUBSTRING( a.create_time, 1, 4 ) YEAR,
		CASE
			WHEN SUBSTRING( a.create_time, 6, 2 ) IN ( '01', '02', '03' ) THEN 1 
			WHEN SUBSTRING( a.create_time, 6, 2 ) IN ( '04', '05', '06' ) THEN 2 
			WHEN SUBSTRING( a.create_time, 6, 2 ) IN ( '07', '08', '09' ) THEN 3 
			WHEN SUBSTRING( a.create_time, 6, 2 ) IN ( '10', '11', '12' ) THEN 4 
			END AS quarter,
			a.payment_amount 
		FROM
			order_info a
			INNER JOIN customer_info b ON a.customer_id = b.customer_id 
		) t1 
	GROUP BY
		province,
		year,
	quarter 
	) t2 
WHERE
	province_quarter_rank = 1;
  ```
