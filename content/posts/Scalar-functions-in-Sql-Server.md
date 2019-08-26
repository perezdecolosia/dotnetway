---
title: "Scalar functions in Sql Server"
date: 2019-02-11T11:53:42+02:00
draft: false
comments: true
tags: ["Sql server", "T-sql"]
---


Today I wanted to talk about the scalar functions in Sql Server and a very interesting utility that I discovered recently.

A scalar function has to return a single value if it does not give an error so we have to make sure that the query with which we define it only returns a value. As parameters we can pass them the ones we want.

They are used differently than the Table functions, they are removed FROM between the SELECT and the name of the function.

An example of use would be:

{{< highlight sql >}}

SELECT GetUserName(10); 

{{< /highlight >}}

In this case we would return the full name of a user that we can have concatenated in the function with all the fields that we have type Name, Surname1, Surname 2. In the function parameter I have set an example of a user ID that is would happen, so it would give me back the full name of the user with Id 10.

Now comes the interesting. If we have quite complex queries with many joins and to obtain other data we have to cross many tables again and we can make mistakes we can use the scalar functions within the select of our query to return a single value per processed line, as parameter of the function we would pass the field of the current query that we need to pass and Sql Server will process the function for each line passing the value of the field passed by parameter and will return a field with the data we need to which we must put an Alias ​​to distinguish it in the query because by default, that field is not assigned a name.

For example we could put the previous function within the select to return the full user name.

{{< highlight sql >}}

SELECT GetUserName(U.Id) AS FullName, D.Departament 
FROM Users U 
  JOIN Departments D ON U.IdDepartment = D.Id

{{< /highlight >}}

Lo mejor de todo es que también podemos usar la función en el where.

{{< highlight sql >}}
SELECT GetUserName(U.Id) AS FullName, D.Departament 
FROM Users 
  JOIN Sales S ON U.Id = S.IdUser 
WHERE GetUserName(U.Id) = 'Javier Pérez Pérez' 
{{< /highlight >}}

At first like this for simple queries it may not make much sense other than the usefulness of not having to concatenate the name, which is not a small thing either. But in others much more complex with many Joins saves your life.

Now the problems come. Put the scalar functions in the Select and the Where overloads the bbdd because the function is executed for each line that returns the query so in queries that return many results is not advisable to use them because they take a long time to execute and have very bad performance.

We can observe this by opening the profiler to trace the query enabling the events SP: Completed and SQL: BatchCompleted events. There we can with SP: Completed that the function is executed as many times as lines return the query, so in the end we shoot the use of CPU and the worst the use of readings, with respect to the query concatenated directly the user name and that in this case only one time is executed and the SQL event: BatchCompleted events, which summarizes the cost of the query, we see that the CPU is used much less and, above all, the readings are drastically reduced.

Now the good news. This can be avoided with a table function instead of scaling and a CROSS APPLY or with a normal JOIN against a view that has the query that returns the full name. But that will be the subject of another post.

