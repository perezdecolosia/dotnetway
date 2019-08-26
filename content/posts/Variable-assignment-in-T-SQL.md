---
title: "Variable Assignment in T SQL"
date: 2019-05-12T13:53:42+02:00
draft: false
comments: true
tags: ["Sql server", "T-sql", "Programming"]
---


Last week I asked a partner how to initialize a variable with the result of a query in T-SQL.

I told him how he had found the JOINT assignment, but also with the way to do it.

The two forms have changed like this:

{{< highlight sql >}}

DECLARE @variableSET Int 
DECLARE @variableSelect1 Int 
DECLARE @variableSelect2 varchar(50)

-- SET Initialization
SET @variableSET = (SELECT IdUsuario FROM USERS WHERE IDUser = 127)

-- SELECT Initialization
SELECT @variableSelect1 = IDUsuario, @variableSelect2 = UserName 
FROM USERS 
WHERE IDUser = 127

-- Show results 
PRINT @variableSET 
PRINT @variableSelect1 
PRINT @variableSelect2

{{< /highlight >}}

Then I put the differences between the two initialization methods

1. SET is ANSI standard while SELECT is not. This means that SET can be used in any BBDD engine while SELECT only in SQL SERVER.

2. SET can only assign one variable at a time while SELECT can do it with several. We see it in the example as with SET we only declare @variableSET while with SELECT we assign the variables @ variableSelect1 and @ variableSelect2.

3. If we assign from a query, as is the case of the example, we must be sure that it will return a scalar value if it returns a result set the SET will fail. The SELECT does not fail which is even more dangerous since it will assign a value of the result ignoring the others, so it will be assigning a result that we did not expect and failing to do so could be very difficult to debug.

4. If the query does not return any value with SET, the variable will be assigned NULL, but with SELECT no assignment will be made, so the variable will maintain its previous value, which can also lead to errors that are difficult to debug.

For all the above we can see that it is better to use SET as a method of assigning variables in T-SQL.
