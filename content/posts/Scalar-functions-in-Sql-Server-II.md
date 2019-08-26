---
title: "Scalar functions in Sql Server II. Performance improvements."
date: 2019-02-13T10:53:42+02:00
draft: false
comments: true
tags: ["Sql server", "T-sql"]
---


Today I write to you as I promised to see the solutions to the performance problems that involved the use of scalar functions in more complex queries.

The first is to create the scalar function as a table function, that is, instead of returning a single value a set of values ​​as a normal query.

We create the function:

{{< highlight sql >}}

CREATE FUNCTION GetUserNameTableFunction (@IdUsuario int) RETURNS TABLE AS RETURN 
(SELECT U.LastName + ', ' + U.FirstName [FullName], D.DepartamentName
  FROM Users 
    JOIN Departament D ON U.IdDepartment = D.Id 
  WHERE U.Id = @IdUsuario) GO 

{{< /highlight >}}

The scalar function scalar function we used it like this:

{{< highlight sql >}}
SELECT GetUserName(U.Id) AS FullName, D.Departament 
FROM Users 
  JOIN Sales S ON U.Id = S.IdUser 
WHERE GetUserName(U.Id) = 'Javier Pérez Pérez' 
{{< /highlight >}}

To use the table function we have to use a CROSS JOIN in the following way:

{{< highlight sql >}}
SELECT U.FullName, U.Departament 
FROM Sales S 
  CROSS APPLY GetUserNameTableFunction (S.IdUser) U 
WHERE U.FullName = 'Javier Pérez Pérez'
{{< /highlight >}}

In this way the number of I/O is much more efficient and CPU usage is much lower.

The other alternative is to create a view. For example:

{{< highlight sql >}}
CREATE VIEW UserNameView AS 
SELECT U.Id AS IdUser, U.LastName + ', ' + U.FirstName [FullName], D.Departament 
FROM Users 
  JOIN Departaments D ON U.IdDepartament = D.Id
GO
{{< /highlight >}}

And now we do a JOIN with the view we have created:

{{< highlight sql >}}
SELECT U.FullName, U.Departament 
FROM Sales S 
  JOIN UserNameView U ON U.IdUser = S.IdUser 
WHERE U.FullName = 'Javier Pérez Pérez' 
{{< /highlight >}}

With the view we gain that the use of CPU is a little lower although the I/O the same as with the use of the CROSS JOIN.

If we gather that CPU usage is a little better using a view, this is my preferred form, because it also makes it more understandable to me than using the CROSS JOIN. In addition, the view is always more generic to filter what we want or without any filter, which with the functions we have to pass the filter in advance and we would have to create other functions so that nothing filtered or to pass more filters or different ones.

I don't put any performance test because here I leave an article that explains the yields very well by attaching tests with the profiler. There is no waste time taking a look and it is essential if you are interested in the performance tests of everything I told you. You can find it in [Performance tests](https://www.databasejournal.com/features/mssql/article.php/3845381/T-SQL-Best-Practices-150-Don146t-Use-Scalar-Value-Functions-in-Column-List-or-WHERE-Clauses.htm).
