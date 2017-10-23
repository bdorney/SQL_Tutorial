# General Remarks
Based on this YouTube video found [here](https://www.youtube.com/watch?v=7Vtl2WggqOg)

SQL = structured query language.

Relational Database:
- Data stored in a table.
- Each table has a number of rows where each row represents an item.
- Each row has a number of columns where each column represents a property of the item.
- We can have relations between tables.

Two tables can be related by a third table.  This is a more efficient way to store data since we can use the third table to form a map between the two tables rather than repeating information in one table in the other.  For example consider teh tables `person`, `cars`, and `owners`.  The `person` table looks like this:

| `person_id` | name | location |
| -- | ---- | -------- |
| 1 | John | California |
| 2 | Jane | NYC|

The `cars` table looks like this:

| `car_id` | Make | Model | year |
| -------- | ---- | ----- | ---- |
| 1 | Ford | Mustang | 2010 |
| 2 | VW | Golf | 2005 |
| 3 | Toyota | Prius | 2007 |

We can say that John and Jane each own a car without adding the extra information to the `person` table but just storing the relation (mapping) in a third table `owners` which looks like:

| `person_id` | `car_id` |
| ----------- | -------- |
| 1 | 3 |
| 2 | 1 |

Now the `owners` table tells us that John `person_id = 1` owns a 2007 Toyat Prius `car_id = 3` and Jane `person_id = 2` owns a 2010 Ford Mustang `car_id = 1`.

Syntax of SQL statements:
- All statements end with a semicolon ;
- Can form complex expressions joining keyword calls together
- Keywords and operators are in all capital letters

# Creating a Database
Using `sqlite3` the syntax is just:

```
$ sqlite3 databaseName.sql
```

In the working directory this will create a file called `databaseName.sql`.  Tables created and data entered will automatically be stored after every transaction.  No need to save it when you exit.

# Creating a Table

How do we create a table? Use the `CREATE` keyword:

```
CREATE TABLE <table name> (col1name data1type, col2name data2type, ...);
```

Example: 
```
CREATE TABLE groceries (id INTEGER PRIMARY KEY, name  TEXT, quantity INTEGER, aisle TEXT);
```

It is a good convention to have an `id` column with type `INTEGER PRIMARY KEY` to serve as a unique identifier for every row in the table.

## Storing the Results of a Query/Join into a Table
This can be done with either the `CREATE` or `INSERT` keyword.  If using the `CREATE` keyword the syntax is:

```
CREATE TABLE table_name SELECT <query>
```

If using the `INSERT` keyword the syntax is:

```
INSERT INTO table_name SELECT <query>
```

## Available Data Types
The available data types that I've come across are:

| Keyword | Type | Description |
| ------- | ---- | ----------- |
| `INT` | int | Self-explanatory. |
| `INT PRIMARY KEY` | int | This column becomes a unique identifier in the table. |
| `INT PRIMARY KEY AUTOINCREMENT` | int | As `INT PRIMARY KEY` but this column is automatically incremented as new rows are added. |
| `TEXT` | string | For storing text. |

More types available [here](http://www.cs.toronto.edu/~nn/csc309/guide/pointbase/docs/html/htmlfiles/dev_datatypesandconversionsFIN.html)

# Manipulating Rows in a Table

## Inserting Rows into Table
How do we insert data into our table? Use the `INSERT` keyword:

```
INSERT INTO <table name> VALUES (col1Val, col2Val, ...);
```

Example:

```
INSERT INTO groceries VALUES (1, "Bananas", 4, 7);
INSERT INTO groceries VALUES (2, "Peanut Butter", 1, 2);
INSERT INTO groceries VALUES (3, "Dark chocolate bars", 2, 2);
```

You can also specify the column names after the table name when inserting values.  The advantage is that you only have to specify the values for the column names listed and not for every column.  This syntax is:

```
INSERT INTO <table name>(col1, col2, ... , colN) VALUES (val1, val2, ... , valN)
``` 

For example if we create the `exercise_logs` table:

```
CREATE TABLE exercise_logs (id INTEGER PRIMARY KEY AUTOINCREMENT, type TEXT, minutes INTEGER, calories INTEGER, heart_rate INTEGER);
```

The column names are `id`, `type`, `minutes`, `calories`, and `heart_rate`.  We can choose to insert values for only the `type`, `calories`, and `heart_rate` columns via:

```
INSERT INTO exercise_logs(type, calories, heart_rate) VALUES ("sitting", 20, 70);
```

Viewing this table shows there is no value for the `minutes` column:

```
sqlite> SELECT * FROM exercise_logs;
1|sitting||20|70
```

## Updating Rows in a Table
We can set *all* rows in a table to a given value via:

```
UPDATE table_name SET col_name = value;
```

We can update the content of a column for a specific row using the row's `id` and the `WHERE` keyword:

```
UPDATE table_name SET col_name = value WHERE id = index;
```

If we don't know the `id` we can try to find the row by looking for another unique identifier (e.g. if a table stores personal info `first_name`, `last_name`, and `DOB` might select a unique row).

```
UPDATE table_name SET col_name = value WHERE col1=val1 AND col2=val2 AND col3=val3
```

Remember the `LIKE` keyword could also be used.

## Deleting Rows in a Table
To delete a row from a table you can use the `DELETE` keyword coupled with a filter, e.g.:

```
DELETE FROM table_name WHERE id = index;
```

Note on design: a lot of times you don't want to delete data. Instead add a boolean column name called `deleted` and when a user wants to detele a row you write `TRUE` to that column for that row.  Then whenever performing queries the filter includes the statement `deleted = false`.

# Querying Tables
How do we retrieve all the rows from our table? To form any query we use keyword `SELECT`
For a specific column name `col nam`, execute:

```
SELECT <col name> FROM <table name>;
```
    
Example: 

```
SELECT name FROM groceries;
```

If we want *all* the column names replace `col name` with the wildcard character `*`:

```
SELECT * FROM <table name>;
```

Example: 

```
SELECT * FROM groceries;
```

## Ordering Results
What if we want to order the results by a give `col name`? Use the `ORDER BY` keyword:

```
SELECT <col1 name> FROM <table name> ORDER BY <col2 name>;
```

Example:

```
SELECT * FROM groceries ORDER BY aisle;
```

## Filtering Results
What if we want to filter results? Use the `WHERE` keyword:

```
SELECT <col1 name> FROM <table name> WHERE <logical exp on col name>;
```

Example:

```
SELECT * FROM groceries WHERE aisle > 5;
SELECT * FROM groceries WHERE aisle <= 4;
```

Can also order the filtered output by using the `ORDER BY` keyword afterward, for example:

```
SELECT * FROM groceries WHERE aisle > 5 ORDER BY aisle;
```

### Mathematical Functions

| Function | Description |
| -------- | ----------- |
| `ROUND(...)` | Rounds a number to an integer. |

### Operators
The following tables show the different types of operators I've come across.  The order of evaluating statements when operators are used can be controlled with paranthesis per normal.

#### Arithmetic Operators
Most mathematical operators can be used in SQL.

| Operator | Description |
| -------- | ----------- |
| `+` | Addition |
| `/` | Division |
| `*` | Multiplication |
| `-` | Subtraction |

#### Filtering Operators
To manipulate rows/queries I've seen these operators:

| Operator | Description |
| -------- | ----------- |
| `AS` | Assigns the results of a query/aggregate function call to a column name. |
| `GROUP BY` | Group results by a given column name. |
| `HAVING` | Can be used to restrict `GROUP BY` calls with a logical expression. Different from `WHERE`.  Applies to the grouped values, not the individual. |
| `IN` | Return rows where the column name matches an input list of values. |
| `WHERE` | Returns rows where that meet some logical expression. Applies to indivudal results in the rows. |

#### Logical Operators
I've come across the following logical operators:

| Operator | Description |
| -------- | ----------- |
| `>` | Greater than. |
| `>=` | Greater than or equal to. |
| `<` | Less than. |
| `<=` | Less than than or equal to. |
| `=`,`==` | Equality, e.g. is X equal to Y? |
| `AND` | Logical and of two statements. |
| `LIKE` | For performing inexact matches, i.e. looking for a substring in a string. |
| `NOT` | Logical inverse of a statement. |
| `OR` | Logical or of two statements. |

## Aggregating functions and GROUP BY
Getting properties from a database can be done by using an *aggregate function*.  An aggregate function is useful for getting things like:

- the maximum, 
- the minimum,
- sum,
- average,
- etc...

of values from the database.  They syntax is:

```
SELECT <aggregate function (col name)> FROM <table name>;
```

For example:

```
SELECT SUM(quantity) FROM groceries;
```

List of common aggregate functions:

| Function | Description |
| -------- | ----------- |
| `AVG(col name)` | Averages the entires of `col name` in a table. |
| `COUNT(col name)` | Returns total number of values for `col name` in a table. |
| `COUNT(*)` | Returns the number of rows in a table. |
| `MIN(col name)` | Returns the minimum value of `col name` in a table. |
| `MAX(col name)` | Returns the maximum value of `col name` in a table. |
| `SUM(col name)` | Returns the sum of values of `col name` in a table. |

Aggregate functions can be used with the keyword `GROUP BY` to perform the operation and group the results by a given column name.  Sytnax:

```
SELECT <aggregate function (col1 name)> FROM <table name> GROUP BY <col2 name>;
```

This will just print a list and not show you what the results correspond to.  To see the results by the group they are grouped by you can execute:

```
SELECT <col2 name>, <aggregate function (col1 name)> FROM <table name> GROUP BY <col2 name>;
```

Note that `col2 name` appears first followed by a comma then the rest of the statement.  Full example:

```
sqlite> SELECT aisle, SUM(quantity) FROM groceries GROUP BY aisle;
2|3
3|6
4|1
7|56
12|1
```

Here the aggregate function `SUM(...)` was applied to `quantity` column from the `groceries` table and the `GROUP BY` column was `aisle`.  Notice the results are displayed by `aisle` with the value of the `aisle` appearing first.

How does the above call work behind the scenes?  The SQL engine will:

1. first the grouping of the rows based on `GROUP BY`,
2. then the aggregate function is applied to each of these groups, and
3. then it selected the first `col2 name` it saw in each group.

The user should take care about the call since we could have used `name` instead of `aisle` just after `SELECT` above and the results would have been by `name`.  But since `aisle` is not unique the result of `SUM(...)` would not necessarily reflect things by `name`; e.g. if both `Cherries` and `Chocolate` are found in `aisle=2` then displaying the above query by `name` may be misleading since the SQL engine will just pick the *first* item out of the `GROUP BY` results.

### Using the AS and HAVING Operators
In a query we can store the results of an aggregate function call as it's own column name using the `AS` operator.  This column name's scope seems to only be in the query it's used.  Syntax:

```
<aggregate function>(col name) AS <new col name>
```

Example:

```
SUM(calories) AS total_calories;
```

We can restrict `GROUP BY` calls to meet a logical expression using the `HAVING` operator.  This can be paired with aggregate functions and the `AS` operator. Syntax:

```
<aggregate function>(col1 name) AS <new col name> FROM <table name> 
    GROUP BY <colA name> 
    HAVING (logical expression using <new col name>);
```

Note here we have also illustrated how queries can span multiple lines for better readability.

Full example of using `AS` and `HAVING` operators:

```
sqlite> SELECT type, SUM(calories) AS total_calories FROM exercise_logs GROUP BY type HAVING total_calories > 120;
biking|130
dancing|200
tree climbing|142
```

When we use the `HAVING` operator we're applying conditions to the grouped values, not the individual values in the individual rows.

## Queries with AND & OR Operators
The `AND` and `OR` operators can be used when building the *logical exp on col name* that is used with the `WHERE` keyword. The `AND` operator will return rows that meet the *logical and* of the conditions.  The `OR` operator will return rows that meet any of the conditions.  The `AND` operator has priority over the `OR` operator but you can use paranthesis to change the order of evaluation.

For example:
```
SELECT * FROM exercise_logs WHERE (calories > 50 AND minutes < 30) ORDER BY calories;
SELECT * FROM exercise_logs WHERE heart_rate > 100 OR (calories > 50 AND minutes < 30) ORDER BY calories;
```

Here the *logical exp on col name* that was used is `calories > 50 AND minutes < 30`.

## Queries with IN Operator
Suppose you want to select rows from your table, you can filter it by calling:

```
SELECT * FROM <table name> WHERE (col1=val1 OR col1=val2 OR col1=val3 ...);
```

This will correctly query your table but there is an easier way to write the above query. The `IN` operator will check to see if a particular value is in a list of values.  The above query can be symplified too:

```
SELECT * FROM <table name> WHERE col1 IN (val1,val2,val3,...)
```

This gives a much more readable, and easier to make, query.  Example:

```
SELECT * FROM exercise_logs WHERE type IN ("biking","hiking","tree climbing", "rowing");
1|biking|30|100|110
2|biking|10|30|105
5|tree climbing|30|70|90
6|tree climbing|25|72|80
7|rowing|30|70|90
8|hiking|60|80|85
```

The `IN` operator can be combined with other operators as well.  For example using the `NOT` operator:

```
sqlite> SELECT * FROM exercise_logs WHERE type NOT IN ("biking","hiking","tree climbing", "rowing");
3|dancing|15|200|120
```

## Queries with the LIKE Operator
For performing an inexact match.  Suppose you have a column name that has the `TEXT` type.  You want to search this column for a value but you're not looking for an exact match (i.e. you're searching for a substring in a string).  Then you can use the `LIKE` operator to perform this *inexact match*:

```
SELECT * FROM <table name> WHERE <col name> LIKE "%substring%")
```

Here the `%` character is interpreted as a wildcard.

For example for the `drs_favorites` table with the second column called `reason`:

```
sqlite> SELECT * FROM drs_favorites;
1|biking|Improves endurance and flexibility.
2|hiking|Improves cardiovascular health.
```

We can return any row with the word "cardiovascular" in the `reason` column via:

```
sqlite> SELECT * FROM drs_favorites WHERE reason LIKE "%cardiovascular%";
2|hiking|Improves cardiovascular health.
```

## Querying Using Subqueries
Suppose you have two tables `table1` and `table2` each with column `col1`.  You can query `table1` using the results of a subquery from `table2` with the `IN` Opertor.  Syntax:

```
SELECT * FROM table1 WHERE col1 IN (SELECT col1 FROM table2);
```

In this case the query of `col1` from `table1` will only return rows having `col1` equal to the `col1` value of `table2`.

For example consider the tables `exercise_logs` and `drs_favorites` each having the column `type` as the second column in the table.

```
sqlite> SELECT * FROM exercise_logs;
1|biking|30|100|110
2|biking|10|30|105
3|dancing|15|200|120
4|sitting||20|70
5|tree climbing|30|70|90
6|tree climbing|25|72|80
7|rowing|30|70|90
8|hiking|60|80|85
sqlite> SELECT * FROM drs_favorites;
1|biking|Improves endurance and flexibility.
2|hiking|Improves cardiovascular health.
```

We can do a query to select rows from `exercise_logs` that are also in the `drs_favorites` table using a subquery of the `type` column:

```
sqlite> SELECT * FROM exercise_logs WHERE type IN (SELECT type FROM drs_favorites);
1|biking|30|100|110
2|biking|10|30|105
8|hiking|60|80|85
```

Here the rows returned from `exercise_logs` are those where `type` is only equal to the value in `drs_favorites`.

## The CASE Statement
Suppose you want to perform a query and group the results from a table by some criterion.  Normally the `GROUP BY` statement can be used but you need a coumn name for that, you may not have one though.  Enter the `CASE` statement.

It's like an `if` or `switch` statement from other programming languages.  We can make some categories for the data and ask how things fall into those categories.  Syntax:

```
SELECT col_name,
    CASE
        WHEN condition1 THEN "statement1"
        WHEN condition2 THEN "statement2"
        ...
        ...
        WHEN conditionN then "statementN"
        ELSE "statement everything eles"
    END as new_col_name
FROM table_name
```

Note the `ELSE` portion is optional.

For example:

```
SELECT type, heart_rate, 
    CASE 
        WHEN heart_rate > 220-30 THEN "above max" 
        WHEN heart_rate > ROUND(0.90 * (220-30)) THEN "above target" 
        WHEN heart_rate > ROUND(0.50 * (220-30)) THEN "within target" 
        ELSE "below target" 
    END AS "heart_rate_zone" 
FROM exercise_logs;
```

Gives output:

```
biking|110|within target
biking|105|within target
dancing|120|within target
sitting|70|below target
tree climbing|90|below target
tree climbing|80|below target
rowing|90|below target
hiking|85|below target
```

We could group these results by the categories we defined in the case using:

```
SELECT COUNT(*), 
    CASE 
        WHEN heart_rate > 220-30 THEN "above max" 
        WHEN heart_rate > ROUND(0.90 * (220-30)) THEN "above target" 
        WHEN heart_rate > ROUND(0.50 * (220-30)) THEN "within target" 
        ELSE "below target" 
    END AS "heart_rate_zone" 
FROM exercise_logs GROUP BY heart_rate_zone;
```

Which now has our grouped output showing the number of entries in each category thanks to the `COUNT(*)` function:

```
5|below target
3|within target
```

# Manipulating Tables

## Getting Column Names of a Table
Can return the column names of a table using `sqlite_master`:

```
SELECT sql FROM sqlite_master WHERE tbl_name = '<table name>' AND type = 'table';
```

Example:

```
sqlite> SELECT sql FROM sqlite_master WHERE tbl_name = 'drs_favorites' AND type = 'table';
CREATE TABLE drs_favorites(id INTEGER PRIMARY KEY, type TEXT, reason TEXT)
```

Can return the column names of a table using `PRAGMA`:

```
PRAGMA table_info(<table name>);
```

Example:

```
sqlite> PRAGMA table_info(drs_favorites);
0|id|INTEGER|0||1
1|type|TEXT|0||0
2|reason|TEXT|0||0
```

## Altering Tables After Creation
In a production environment, once we have a table with data we cannot edit the `CREATE` statement to add new columns since this would re-create the table and wipe all the stored data (bad).

We need to use the `ALTER TABLE` statement.

### Adding a new column
Uses the `ALTER TABLE` keyword with the `ADD` keyword, syntax:

```
ALTER TABLE table_name ADD new_col_name DATA_TYPE;
```

Next time we insert data into the table we need to add `new_col_name` to the `INSERT` statement when adding the row.  

Unspecified values are generally filled in as `NULL`.  However when inserting a table we can specify what the default (e.g. unfilled values) are for the pre-existing rows in the table.  Syntax:

```
ALTER TABLE table_name ADD new_col_name DATA_TYPE default <val>;
```

### Deleting a Table
This is generally never done.  This is done via the `DROP TABLE` statement.  Syntax:

```
DROP TABLE table_name;
```

## Joing Tables
Suppose you have two tables `table1` and `table2` where the `id` columns are related.  You want to do queries such that information from one is display in the other.  How to do this?

For these examples we'll be using the `students`, `student_grades`, and `student_projects` tables as shown below:

```
sqlite> PRAGMA table_info(students);
0|id|INTEGER|0||1
1|first_name|TEXT|0||0
2|last_name|TEXT|0||0
3|email|TEXT|0||0
4|phone|TEXT|0||0
5|birthdate|TEXT|0||0

sqlite> SELECT * FROM students;
1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
```

```
sqlite> PRAGMA table_info(student_grades);
0|id|INTEGER|0||1
1|student_id|INTEGER|0||0
2|test|TEXT|0||0
3|grade|INTEGER|0||0

sqlite> SELECT * FROM student_grades;
1|1|Nutrition|95
2|2|Nutrition|92
3|1|Chemistry|85
4|2|Chemistry|95
```

```
sqlite> PRAGMA table_info(student_projects);
0|id|INTEGER|0||1
1|student_id|INTEGER|0||0
2|title|TEXT|0||0

sqlite> SELECT * FROM student_projects;
1|1|Carrotapault
```

As we can see the `id` columns are related.  Also notice that Alice is not in the `student_projects` table.

### Cross Join

Use both table names after the keyword `FROM`:

```
SELECT * FROM table1, table2
```

For example:

```
sqlite> SELECT * FROM student_grades, students;

1|1|Nutrition|95|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
1|1|Nutrition|95|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
2|2|Nutrition|92|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
2|2|Nutrition|92|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
3|1|Chemistry|85|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
3|1|Chemistry|85|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
4|2|Chemistry|95|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
4|2|Chemistry|95|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
```

The new table will have the number of rows equal to `size(table1)*size(table2)`.  For each row in `table1` a new row will be made pairing the row of `table1` with all the rows in `table2`.  So this is like a vector cross product.

This is the simplest but least useful join.

### Inner Join
This will match rows if their `id` values match.  There are several ways to do this.

Performing an *implicit inner join* using the `WHERE` keyword:

```
SELECT * FROM table1, table2 WHERE table1.id = table2.id
```

For example:

```
sqlite> SELECT * FROM student_grades, students WHERE student_grades.student_id = students.id;

1|1|Nutrition|95|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
2|2|Nutrition|92|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
3|1|Chemistry|85|1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24
4|2|Chemistry|95|2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04
```

This is just a cross join where we have restricted the output rows to those where the `id` values match.  This works, but it's not the best way.

Perfoming an *explicit inner join* using the `JOIN` and `ON` keywords:

```
SELECT * FROM table1
    JOIN table2
    ON table1.id = table2.id;
```

For example:

```
sqlite> SELECT * FROM students 
    JOIN student_grades 
    ON student_grades.student_id = students.id;

1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24|1|1|Nutrition|95
2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04|2|2|Nutrition|92
1|Peter|Rabbit|peter@rabbit.com|555-6666|2002-06-24|3|1|Chemistry|85
2|Alice|Wonderland|alice@wonderland.com|555-4444|2002-07-04|4|2|Chemistry|95
```

Once we've joined tables we can still use all our other keywords, e.g. `WHERE`, `GROUP BY`, etc...

### Left Outer Joins
An inner join *only* creates rows for matching rows between the two tables.  So if a given `id` present in `table1` is not present in `table2` there will be no row that corresponds to this `id` in the table created by the inner join.

To perform a left outer join the syntax is:

```
SELECT <* or comma sep list of column names>
    FROM table1
    LEFT OUTER JOIN table2
    ON table1.id = table2.id;
```

For example:

```
SELECT students.first_name, students.last_name, student_projects.title 
    FROM students 
    LEFT OUTER JOIN student_projects 
    ON students.id = student_projects.student_id;

Peter|Rabbit|Carrotapault
Alice|Wonderland|
```

Notice no value is reported in the last column for row two since Alice doesn't have a project.

The `LEFT` tells SQL that it should maintain *every* row from the left table, which is the table after the `FROM` keyword `table1`; and the `OUTER` keyword tells SQL to retain the rows even if there's no match in the right table, which is after the keyword `OUTER` e.g. `table2`.

Note there's a right outer join, but it's simplier to just to interchange `table1` and `table2` in the syntax.

### Full Outer Join
Same syntax as a *left outer join* but null values are reported for both the right and left tables when a match cannot be found.

Replace the `LEFT` keyword with the `FULL` keyword.  It may not be supported in every SQL environment.

### Self Joins
Suppose we have a column in a table that's related to another column in the same table. We would need to join the table with itself to make this relation easily. However when performing the join we will have ambiguous column names (since the table is itself, we can't specify `table_name.col_name` this is still ambiguous).  We need to use an alias `alias_name`, syntax:

```
SELECT table1.col1, table2.col2, alias_name.col3
    FROM table1
    JOIN table2 alias_name
    ON table1.some_col = alias_name.diff_col
```

### Specifying which Columns to Use When Joining
We can choose what columns are returned by providing a comma separated list of column names instead of using the `*` after SELECT.  However to resolve ambiguities and potential column name mismatches we should prefix our column names by the table they belong to.  For example use `table1.col1` instead of `col1`.  This is important if `table1` and `table2 each have a column named `col1` but the values stored have different *meaning*.

### Combining Multiple Joins
If you have a relational table which is mapping `id` values from multiple tables it will not be the most human readable table.  Performing a multiple join can help to resolve this.  For tables `table1`, `table2`, and `table3` we can construct mulitple joins:

```
SELECT alias1.col1, alias2.col1 FROM table1
    JOIN table1 alias1
    ON table1.some_col = alias1.some_col
    JOIN table2 alias2
    ON table2.some_col = alias1.some_col
    JOIN table3
    ON table3.id = alias1.some_col
```

This shows we can construct a join in a complex way using multiple `JOIN` and `ON` statements.
