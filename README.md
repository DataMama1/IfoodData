-- This contains all the query i Used to analsye the Ifood Data in SQL. 

-- To find the Most successfull campaign

  select
  sum(AcceptedCmp1) as CMP1SUCESS, sum(AcceptedCmp2) AS CMP2SUCESS, 
  sum(AcceptedCmp3) AS CMP3SUCESS, 
  sum(AcceptedCmp4) AS CMP4SUCESS,sum(AcceptedCmp5) AS CMP5SUCESS
  from [moviedatabase].[dbo].[iFoodData]

-- We're looking for the the lowest and highest income

 select min(Income), max(Income)
 from [moviedatabase].[dbo].[iFoodData]

 -- Exploring the data by finding the amount spent by each CustomerSegment on each food items.
 select Case when Income > 70000 then 'HighIncome'
 when Income between 30000 and 70000 then 'MiddleIncome' else 'LowIncome'
 end as IncomeSegmentation, 
 sum(MntWines) as Wine, SUM(MntFishProducts) as Fish, SUM(MntFruits) as Fruits,  SUM(MntMeatProducts) as Meat, SUM(MntSweetProducts) as Sweet
 from [moviedatabase].[dbo].[iFoodData]
 group by Case when Income > 70000 then 'HighIncome'
 when Income between 30000 and 70000 then 'MiddleIncome' else 'LowIncome'
 end 

 -- This query shows the customer segmentation by income and where they prefer to get Items
 select sum(NumCatalogPurchases) as Catalog, sum(NumStorePurchases) as StorePurcahses, sum(NumWebPurchases) as WebPurchases,
 Case when Income > 70000 then 'HighEarners'
 when Income between 30000 and 70000 then 'MiddleEarners' else 'LowEarners'
 end as IncomeSegmentation
 from [moviedatabase].[dbo].[iFoodData]
 group by Case when Income > 70000 then 'HighEarners'
 when Income between 30000 and 70000 then 'MiddleEarners' else 'LowEarners'
 end 

 -- Looking at Buying behnaviors of married customers with Kids and Teenagers
 with MarriedCte (Income, MaritalStatus, NoKids, NoTeenagers, MntFish, MntMeat, MntFruits, MntSweets) as (
 select Income, Case when marital_Married = 1 then 'yes' else 'No' end as MaritalStatus,
 Kidhome, Teenhome, MntFishProducts, MntMeatProducts, MntFruits, MntSweetProducts
from
 [moviedatabase].[dbo].[iFoodData]
 where marital_Married= 1 and Kidhome >=1 and Teenhome >=1)
 select sum(MntFish), sum(MntMeat), sum(MntFruits), sum(MntSweets) from MarriedCte

 with UnmarriedWithKids (Income, MaritalStatus, NoKids, NoTeenagers, MntFish, MntMeat, MntFruits, MntSweets) as
 (select Income, Case when marital_Married = 1 then 'yes' else 'No' end as MaritalStatus,
 Kidhome, Teenhome, MntFishProducts, MntMeatProducts, MntFruits, MntSweetProducts
from
 [moviedatabase].[dbo].[iFoodData]
 where marital_Married= 0 and Kidhome >=1 and Teenhome >=1)
 select sum(MntFish), sum(MntMeat), sum(MntFruits), sum(MntSweets) from UnmarriedWithKids

with SingleOnly (Income, MaritalStatus, MntFish, MntMeat, MntFruits, MntSweets, MntGoldProds, MntRegularProds) as 
 (select Income, Case when marital_Single = 1 then 'yes' else 'No' end as Single,
MntFishProducts, MntMeatProducts, MntFruits, MntSweetProducts, MntGoldProds, MntRegularProds
from
[moviedatabase].[dbo].[iFoodData]
where marital_Single= 1)
select * from SingleOnly

select Income, Case when marital_Married = 1 then 'yes' else 'No' end as MaritalStatus,
 Kidhome, Teenhome, MntFishProducts, MntMeatProducts, MntFruits, MntSweetProducts, MntGoldProds, MntRegularProds
from
 [moviedatabase].[dbo].[iFoodData]
 where marital_Married= 1 and Kidhome >=1 and Teenhome >=1
 
 --The buying behaviuors are simliar, every customer spend more on fish and Meat

 -- Looking at customer Behavoiur by AGE 
SELECT case when Age between 0 and 30 then '20 to 30' 
 when Age between 30 and 50 then '30 to 50'
 when Age between 50 and 70 then '50 to 70'
 when Age between 70 and 100 then '>70'
 else null end as AgeGroup, SUM(MntFishProducts) as Fish, SUM(MntGoldProds) as GoldProds, SUM(MntFruits) as Fruits, 
 SUM(MntMeatProducts) as Meat, SUM(MntRegularProds) as RegularProd,
 SUM(MntSweetProducts) as Sweets, SUM(MntWines) as Wines
 FROM [moviedatabase].[dbo].[iFoodData]
group by case when Age between 0 and 30 then '20 to 30' 
 when Age between 30 and 50 then '30 to 50'
 when Age between 50 and 70 then '50 to 70'
 when Age between 70 and 100 then '>70'
 else null end 


 -- Buying channels for Customers By Age. What Channel is best for Advertisement for each age group

 select case when Age between 0 and 30 then '20-30' 
 when Age between 30 and 50 then '30-50'
 when Age between 50 and 70 then '50-70'
 when Age >70 then '>70'
 else 'Nothing'
 end as AgeGroup, sum(NumWebPurchases) AS WEBPURCHASES, sum(NumCatalogPurchases) AS CATALOGPURCHASES, sum(NumStorePurchases) AS STOREPURCHASES
 from [moviedatabase].[dbo].[iFoodData]
 GROUP BY 
 case when Age between 0 and 30 then '20-30' 
 when Age between 30 and 50 then '30-50'
 when Age between 50 and 70 then '50-70'
 when Age >70 then '>70'
 else 'Nothing'
 end

 -- The average customer
 select cast (avg(Income) as Int) as Income, Avg(Kidhome)  as Kids, Avg(Teenhome)  as Teen, cast(avg(Age) as Int) as Age,
 cast (AVG(MntWines) as Int) as Wines, cast (AVG(MntFruits) as Int) as Fruits, 
 cast(Avg(MntMeatProducts) as Int) as Meats, cast (Avg(MntFishProducts) as Int) as Fish, cast (AVG(MntSweetProducts) as int) as Sweets, 
 cast( AVG(MntGoldProds) as Int) as GoldProds,
 cast(Avg(NumDealsPurchases) as Int) as DEalsPurchases, cast(Avg(NumWebPurchases) as Int) as WebPurchases, 
 cast(AVg(NumCatalogPurchases) as Int) as CatalogPurchases, cast(AVG(NumStorePurchases) as Int) as StorePurchases, 
 cast(Avg(NumWebVisitsMonth) as Int) as WebVisits,  avg(marital_Married) as Married, 
 AVG( marital_Single) as Single, AVG(marital_Divorced) as Divorced, 
  avg(marital_Together) as Together, avg(marital_Widow) AS WIDOW, avg(education_Basic) as BasicED , 
 avg(education_Graduation)  as Graduate,
 avg(education_Master) as Masterdegree, avg(education_PhD) as PHD
 from [moviedatabase].[dbo].[iFoodData]


 -- to find the Number of each customer in Status Segment

 select case when marital_Divorced = 1
 then 'Divorced' else 'no' end as 'Status',
 SUM(marital_Divorced) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where marital_Divorced= 1
 group by marital_Divorced
 union all
 select case when marital_Single = 1
 then 'Single' else 'no' end as 'Status',
 sum(marital_Single) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where marital_Single= 1
 group by marital_Single
 union all
 select case when marital_Together = 1
 then 'Together' else 'no' end as 'Status',
 sum(marital_Together) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where marital_Together= 1 
 group by marital_Together
 union all
 select case when marital_Married = 1
 then 'Married' else 'no' end as 'Status',
 sum(marital_Married) as Married
 from [moviedatabase].[dbo].[iFoodData]
 where marital_Married= 1
 group by marital_Married

 -- To find the amount of customers Educated
 select case when education_Basic = 1
 then 'Basic' else 'no' end as 'Education',
 SUM(education_Basic) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where education_Basic= 1
 group by education_Basic
 union all
 select case when education_Graduation = 1
 then 'Grad' else 'no' end as 'Education',
 sum(education_Graduation) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where education_Graduation= 1
 group by education_Graduation
 union all
 select case when education_Master = 1
 then 'Masters' else 'no' end as 'Education',
 sum(education_Master) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where education_Master= 1 
 group by education_Master
 union all
 select case when [education_2n Cycle] = 1
 then '2n_Cycle' else 'no' end as 'Education',
 sum([education_2n Cycle]) as Sum
 from [moviedatabase].[dbo].[iFoodData]
 where [education_2n Cycle]= 1
 group by [education_2n Cycle]

 -- This query is used to check the avg amount of kids for each customer in a segment

with MiddleIncome as (
 select * from
 [moviedatabase].[dbo].[iFoodData]
 where Income between 30000 and 70000) 
 select * from MiddleIncome
 
 with MiddleIncome as (
 select * from
 [moviedatabase].[dbo].[iFoodData]
 where Income between 30000 and 70000) 
 select avg(Teenhome) from MiddleIncome

 with MiddleIncome as (
 select * from
 [moviedatabase].[dbo].[iFoodData]
 where Income between 30000 and 70000) 
 select avg(AcceptedCmpOverall) from MiddleIncome

 with LowIncome as (
 select * from
 [moviedatabase].[dbo].[iFoodData]
 where Income  <30000) 
 select * from LowIncome
