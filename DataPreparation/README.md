Documentation 

 Group 1: Retail_Sales_Data.csv 

Dataset Descriptions: 
● Retail Sales: Retail transactions from multiple store branches with sales, 
payment, and satisfaction details. 
- 1199 Transactions
Transaction_ID,Date,Branch,Product_Category,Product_Name,Quantity,Unit_Price,Total_Sales(cedis),Payment_Method,Customer_Satisfaction
sample of 3:
T1000,07/01/2024,Kumasi,Electronics,Smartwatch,7,,,mobile money,4
T1001,28/06/2024,Kumasi,Groceries,Rice,3,173.42,520.26,Cash,6
T1002,04-07-24,Takoradi,Electronics,Charger,7,,,Mobile Money,6

Data Cleaning Steps
step - purpose
different date formats
empty transaction-id
missing values- empty 
check unique categories
inconsistent text case
mixed integers/string variables for satisfaction column
trim

9 blanks in QUANTITY column
1-10 --> no outlier ->> use average value to fill (5.6 -> 6)
Transform: replace null with 6

unit_price
12 N/A -> replaced with null
change type to decimal
- right-skewed (mean < median)
22 empty => transform - impute with median(307.30), because of outliers and high standard deviation.

total_sales
convert to decimals
 find median(1446.15) < find mean(1609.02),
skewed -> impute median for robustness to outliers

payment_method
Capitalize Each Word

customer_satisfaction
good: 23 rows
blank: 35 rows
count: 300
guess is: 1(worst)-6(best) : worst,worse,bad,good,better,best
so good should be 4, so impute
convert to whole number

WILL DO WHENVER WE ALL OUR ON CALL 
{
replace null with average rating for same item ( we can look for better way later)
- find avearge rating for each product_name

Per each unique product_name:
select product_name, customer_satisfaction 
	filter by product_name 	
		remove records with null in customer_satisfaction 
			return averageValue in customer_satisfaction 
gpr, merge left outer, addCustomColumn
if [Customer_Satisfaction] = null then [Grped.AverageRating] else [Customer_Satisfaction]
RESULT: customer_satisfaction_filled column

used graphs for item against customer rating
}

transaction_id
Split by Non-Digit to Digit
Replace empty_string with T
Fill Down on Digit column
Merge back to transaction_id_filled
Select transaction_id_filled, date. Remove Other columns
Primary table: Partially Cleaned One
SecondaryTable: transaction_id_filled, date.
Merge 

LEFT WITH DATE COLUMN
- oops :transaction_id_filled merge failed. we need better stable key other than Date


transaction_id (new used approach)
was to create new index column
pad it
use as transaction_id_new

transaction_id (final used approach)
- traced back to initial step that preserved the order and fixed the column as follows:
Split by Non-Digit to Digit
Replace empty_string with T
Fill Down on Digit column
Merge Columns
Rename


date to date_filled
fixed 85 roes with helper column
duplicate column to Date - Copy
in Date - Copy, change type to Date. replace Error to 1/1/1000
Add custom column with formula
	 ```= if [#"Date - Copy"] = #date(1000, 1, 1) then Date.FromText(Text.Middle([Date], 3, 2) & "/" & 	Text.Start([Date], 2) & "/" & Text.End([Date], 4)) else [#"Date - Copy"]```
name Custom column date_filled . save. 
delete unessarcy columns now
rename to Date

Question On transaction_id: using new indexes can possibly disrupt the data.

Challenges
Debating New Ids as bad or not


after work is done
Go after merge procedure for customer_satisfaction