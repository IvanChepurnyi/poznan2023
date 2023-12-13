---
theme: dracula
canvasWidth: 700
layout: cover
text-align: center
title: NewRelic Magic 🪄
---

# 🪄 NewRelic Magic 

---

# About me

<v-clicks>

🫶 Passionate about open source projects

🚀 Expert in optimizing web appplications

👴🏻 One of grand-parents of Magento

🧑‍🚒 Experienced in putting out fires on production systems

</v-clicks>

---

# Benefits of New Relic


<v-clicks>

⏱ Measurement of your production performance

💡 Out of the box support for Magento application

🔥 Amazing set of tools for find potential bottlenecks

</v-clicks>

---

# Drawbacks of New Relic


<v-clicks>

🚧 Very limited and in-practical transaction tracing

💰 Expensive for larger projects

🧨 Easy to configure Browser agent in the wrong way

</v-clicks>

---
layout: fact
---

## 😱 Live Demo of APM usage

---
layout: fact
---

## 😎 Custom Dashboards


---

# NRQL (NewRelic Query Language)


<v-clicks>

🥪 Overlaying your different metrics together

✨ Monitor key performance metrics over time

😎 Show how cool you are to your merchant

</v-clicks>

---
layout: fact
---

## Let's build custom dashboard

---

# Available Applications

```sql
SELECT uniques(appName) FROM Transaction
```

---

# Facet Filter By Page Types

```sql
SELECT count(*) 
FROM Transaction
FACET CASES (
	WHERE request.uri like '/' AS 'Home',
	WHERE request.uri  LIKE '%/checkout'
	    OR request.uri LIKE '%/checkout/' AS 'Checkout',
	WHERE request.uri  LIKE '/rest/%cart%' AS 'Checkout API',
	WHERE request.uri  LIKE '/checkout/cart%' AS 'Cart',
	WHERE request.uri  LIKE '/catalogsearch%' AS 'Search',
	WHERE request.uri  LIKE '/customer/account/login%' AS 'Login',
	WHERE request.uri  LIKE '/customer/account_' AS 'Account',
	WHERE request.uri  LIKE '%/success%' AS 'Success page',
    WHERE name LIKE '%catalog/category/view' as 'PLP',
	WHERE name LIKE '%catalog/product/view' as 'PDP'
)
WHERE appName = {{ app }}
```

---

# Facet by Response Code

```sql
SELECT count(*) 
FROM Transaction 
FACET httpResponseCode
WHERE appName = {{ app }} 
```

---

# Timeseries per Page Type

```sql
SELECT count(*) FROM Transaction
FACET CASES (
	WHERE request.uri like '/' AS 'Home',
	WHERE request.uri  LIKE '%/checkout'
	    OR request.uri LIKE '%/checkout/' AS 'Checkout',
	WHERE request.uri  LIKE '/rest/%cart%' AS 'Checkout API',
	WHERE request.uri  LIKE '/checkout/cart%' AS 'Cart',
	WHERE request.uri  LIKE '/catalogsearch%' AS 'Search',
	WHERE request.uri  LIKE '/customer/account/login%' AS 'Login',
	WHERE request.uri  LIKE '/customer/account_' AS 'Account',
	WHERE request.uri  LIKE '%/success%' AS 'Success page',
    WHERE name LIKE '%catalog/category/view' as 'PLP',
	WHERE name LIKE '%catalog/product/view' as 'PDP'
)
WHERE appName = {{ app }} TIMESERIES EXTRAPOLATE
```

---

# Timeseries of TTFB distribution

```sql
SELECT 
    percentage(count(*), WHERE duration < 0.8) AS 'good'
    percentage(count(*), WHERE duration >= 0.8 and duration < 1.5) AS 'needs improvement', 
    percentage(count(*), WHERE duration >= 1.5 and duration < 2.5) AS 'poor', 
    percentage(count(*), WHERE duration >= 2.5 and duration < 4) AS 'very poor', 
    percentage(count(*), WHERE duration >= 2.5 and duration >= 4) AS 'terrible'
FROM Transaction
WHERE appName = {{ app }} AND transactionSubType = 'Action' AND transactionType = 'Web' 
TIMESERIES EXTRAPOLATE
```

---

# Timeseries of DB queries distribution

```sql
SELECT 
    percentage(count(*), WHERE databaseCallCount < 120) AS 'good', 
    percentage(count(*), WHERE databaseCallCount >= 120 and databaseCallCount < 250) AS 'needs improvement', 
    percentage(count(*), WHERE databaseCallCount >= 250) AS 'poor'
FROM Transaction 
WHERE appName = {{ app }} AND transactionSubType = 'Action' AND transactionType = 'Web' 
TIMESERIES EXTRAPOLATE
```

---

# Timeseries of TTFB percentiles

```sql
SELECT
    median(duration),
    percentile(duration, 75), 
    percentile(duration, 90),
    percentile(duration, 95), 
    percentile(duration, 99)
FROM Transaction
WHERE appName = {{ app }} AND transactionSubType = 'Action' and transactionType = 'Web' 
TIMESERIES EXTRAPOLATE
```

---

# Timeseries of DB query percentiles

```sql
SELECT 
    median(databaseCallCount), 
    percentile(databaseCallCount, 75),
    percentile(databaseCallCount, 90), 
    percentile(databaseCallCount, 95), 
    percentile(databaseCallCount, 99) 
FROM Transaction 
WHERE appName = {{ app }} and transactionSubType = 'Action' and transactionType = 'Web' 
TIMESERIES EXTRAPOLATE
```


---

# Top 10 URLs by slow TTFB

```sql
SELECT
    average(TTFB)
FROM (
    SELECT percentile(duration, 75) as TTFB,
              count(*) as 'hits' FROM Transaction
       WHERE appName = {{ app }}
       WHERE transactionSubType = 'Action'
         FACET request.uri, name, httpResponseCode LIMIT MAX
)
FACET request.uri, name, httpResponseCode
WHERE hits > 5 
ORDER BY TTFB DESC LIMIT 10 

```
--- 

# Top 10 URLs by DB queries

```sql
SELECT 
    average(DatabaseCalls) 
FROM (
    SELECT percentile(databaseCallCount, 75) as DatabaseCalls, 
           count(*) as 'hits'
    FROM Transaction 
    WHERE appName = {{ app }} 
    WHERE transactionSubType = 'Action' 
    FACET request.uri, name, httpResponseCode 
    LIMIT MAX
)
FACET request.uri, name, httpResponseCode
WHERE hits > 5 
ORDER BY DatabaseCalls DESC 
EXTRAPOLATE  
LIMIT 10
```


---
layout: two-cols
---


# Thank You!

## Support Charities

- Ukrainian Foundation "Come Back Alive"

  https://savelife.in.ua/en/


- Dutch Foundation "Sails of Freedom"

  https://zeilenvanvrijheid.nl/

::right::

# &nbsp;

## Help Ukraine 🇺🇦

<img src="/qrcode-come-back-alive.png" class="w-20 ml-10 mt-8  shadow"  /> 

<img src="/qrcode-zeilen.png"  class="w-20 ml-10 mt-8 shadow"  />
