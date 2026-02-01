# Top50Sql
Leetcode top 50 sql questions

SQL is case insensitive.

While comparing with null use 'is' instead of '='

To rename column in the output use 'as' e.x: Select author_id as id from table_name ..

<img width="1445" height="734" alt="image" src="https://github.com/user-attachments/assets/0c0d429a-0210-4454-adc2-5caae55a8302" />

To sort the result, use order by(sorts in ascending order by default)

To find length of string data : Length
e.x:Where Length(content) > 15 #here content is column with type varchar

LEFT JOIN must include an ON clause.
	•	The join condition should not be in WHERE, otherwise it turns into an implicit INNER JOIN.
	•	Table aliases (e, eu) keep things neat and readable.

1581. Customer Who Visited but Did Not Make Any Transactions:


For Comparing Dates-> use DateDiff function
