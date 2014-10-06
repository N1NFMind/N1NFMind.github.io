---
layout: project
title: SQL CLR - Numbers
categories: [sql_clr, project]
tags: projects, SQL CLR, Tally, Numbers
project_brief: > 
  The numbers (or tally) table is an invaluable tool in creating better performing string manipulation within TSQL. In this project, I have created two simple CLR Table Valued Functions to generate a set of numbers: GetNumbers and GetNumberRange
projectcategory: SQL_CLR
project: SQL_CLR_Numbers
---

# Project: SQL CLR - Numbers

## About this project

This is my implementation of a "Tally" or "Numbers" table. I use CLR to easily create a table of as many numbers as I need. While adding CLR simply to build a numbers table seems like overkill (you are often better off keeping a physical numbers table for re-use), I feel that this is both a great intro into table valued functions with CLR, and I personally have found value in storing functions like these in a central utility database (which without materializing data is very portable). 

For those unfamiliar with the concept of a tally table, or unfamiliare with CLR, I am going to give a brief background. For those who want to get right into the code, please feel free to skip down to "[The Code](#theCode)".

### What is a "Tally" or "Numbers" Table?

The tally table (I prefer the term numbers table myself) is a very powerfull tool to SQL Server development. I use it quite frequently for string manipulation (particularly when I am still playing with the design of new functionality... string manipulation works best in CLR!). You can scower the web for some good examples... this site (http://dwaincsql.com/2014/03/27/tally-tables-in-t-sql/) seems to be fairly comprehensive.

### Why CLR?

SQL CLR provides a means of leveraging pre-compiled within a SQL query. This allows for more efficient string manipulation and other methods. It's important to note, as with any tool, you need to understand when and how to use it. If missused, it can hurt the performance, security, and even the stability of SQL Server! Because of this, I recommend two things. First, only run SQL CLR in Safe mode ([CLR Integration Code Access Security](http://msdn.microsoft.com/en-us/library/ms345101.aspx)). Second, learn the tool, play, research. Only use this "tool" in production once you are very comfortable with it!

### How to use GetNumbers / GetNumbersRange

PENDING EXAMPLES

## <a name="theCode"></a>**The Code**

I have created two simple CLR table value functions to generate a set of numbers to meet the users need. 
- **GetNumbers** - This function takes a single parameter, the number of numbers to return. It returns a table of integers, starting from one.
- **GetNumberRange** - This function takes two parameters, a start and an end number, and returns a table of all integers from and including the first to and including the second parameter. 

Because CLR runs in memory both of these function are FAST. 

## GetNumbers

```
SELECT * FROM GetNumbers(@MaxValue) Nmbrs
```

The GetNumbers method returns a list of integers in a column `N`, from 1 to the number specified by the integer parameter `@MaxValue`.

The CLR code is a very simple use of a table valued function in CLR. To create this, within Microsoft Visual Studio 2013, once I had created my Database Project, I added a new item, a SQL CLR C# User Defined Function. (under "SQL Server >> SQL CLR C#" from the "Add New Item" form)

And here is what I got to start with:

```
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;

public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction]
    public static SqlString SqlFunction1()
    {
        // Put your code here
        return new SqlString (string.Empty);
    }
}
```

The first change was to simply add the System.Collections (necessary for creating a table valued user defined function).

```
using System.Collections;
```

Next, I need to tell the function that it returns a table, so I changed the simple `[Microsoft.SqlServer.Server.SqlFunction]` block into something quite a bit more complex.

```
    [Microsoft.SqlServer.Server.SqlFunction(DataAccess = DataAccessKind.None,
        IsDeterministic = true, IsPrecise = true,
        SystemDataAccess = SystemDataAccessKind.None,
        FillRowMethodName = "FillValues",
        TableDefinition = "N INT")]
```

The function always will return the same result provided the same input, therefore [`IsDeterministic`](http://msdn.microsoft.com/en-us/library/microsoft.sqlserver.server.sqlfunctionattribute.isdeterministic(v=vs.110).aspx)` = true`. The results can be indexed, so [`IsPrecise`](http://msdn.microsoft.com/en-us/library/microsoft.sqlserver.server.sqlfunctionattribute.isdeterministic(v=vs.110).aspx)` = true`. The [`FillRowMethodName`](http://msdn.microsoft.com/en-us/library/microsoft.sqlserver.server.sqlfunctionattribute.fillrowmethodname(v=vs.110).aspx) is a procedure that will be called for each object in the collection (an `IEnumerable`) returned from the method corresponding to the SQL Server function (more on that method in a moment). The [`TableDefinition`](http://msdn.microsoft.com/en-us/library/microsoft.sqlserver.server.sqlfunctionattribute.tabledefinition(v=vs.110).aspx) is just that, the definition of the table to be returned. 

Our fill row method will get passed an enumerable object, and our row consists of a single column, an integer. So our method declaration should look something like this:

```
    private static void FillValues(object obj, out SqlInt32 TheValue)
    {
        ...
    }
```

The fill row method is fairly easy; we will send it an generic object (that is an integer), and it will map this value to a `SqlInt`.

```
    private static void FillValues(object obj, out SqlInt32 TheValue)
    { TheValue = (int)obj; }
```

The core method, the entry point to the CLR function, is `GetNumbers`. Because the function returns a table, the method needs to return an [`IEnumerable`](http://msdn.microsoft.com/en-us/library/system.collections.ienumerable(v=vs.110).aspx) object. Also, we know the parameter we want to return is an integer, which maps to a `SqlInt32` data type. So,

```

    [Microsoft.SqlServer.Server.SqlFunction]
    public static SqlString SqlFunction1()
    {
        // Put your code here
        return new SqlString (string.Empty);
    }
```

becomes

```
    public static IEnumerable GetNumbers(SqlInt32 MaxValue)
    {
        ...
    }
```

Within `GetNumbers`, we do two things. First, if we send an invalid value, we want to bail. 

```
        if (MaxValue.IsNull)
        { yield break; }
```

Next, if we haven't yet bailed, we are going to return values from 1 to our number. 

```
        for (int index = 1; index <= MaxValue.Value; index++)
        { yield return index; }
```

The `yield return` allows us to stream data as we calculate it, making more advanced queries and joins against our table valued function work better.

Pulling it all together, the code should look like:

```
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using System.Collections;
using Microsoft.SqlServer.Server;

public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction(DataAccess = DataAccessKind.None,
        IsDeterministic = true, IsPrecise = true,
        SystemDataAccess = SystemDataAccessKind.None,
        FillRowMethodName = "FillValues",
        TableDefinition = "N INT")]
    public static IEnumerable GetNumbers(SqlInt32 MaxValue)
    {
        if (MaxValue.IsNull)
        { yield break; }

        for (int index = 1; index <= MaxValue.Value; index++)
        { yield return index; }
    }
    
    private static void FillValues(object obj, out SqlInt32 TheValue)
    { TheValue = (int)obj; }
}

```

That's it! Pretty simple for such a powerful tool...

## GetNumbersRange

Now that we have our `GetNumbers` up and working, we can make enhancements to it. As an example, let's say instead of number from 0 to x we want a range of numbers from x to y. By changing just a couple of lines we can create a `GetNumbersRange` table valued function.

```
SELECT * FROM GetNumbersRange(@FromValue, @ToValue) Nmbrs
```

Obviously we'll need to change the name... and add an additional parameter, so 

```
    public static IEnumerable GetNumbers(SqlInt32 MaxValue)
```

becomes

```
    public static IEnumerable GetNumberRange(SqlInt32 FromValue, SqlInt32 ToValue)
```

Next, we'll want to break if either parameter is null, so

```
        if (MaxValue.IsNull)
```

becomes

```
        if (FromValue.IsNull || ToValue.IsNull)
```

Finally, we don't want to go from 1 to our number, but from one number to the other. So

```
        for (int index = 1; index <= MaxValue.Value; index++)
```

becomes

```
        for (int index = FromValue.Value; index <= ToValue.Value; index++)
```

Pulling these simple changes together gives us our new CLR table valued function.

```
public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction(DataAccess = DataAccessKind.None,
        IsDeterministic = true, IsPrecise = true,
        SystemDataAccess = SystemDataAccessKind.None,
        FillRowMethodName = "FillRangeValues",
        TableDefinition = "N INT")]
    public static IEnumerable GetNumberRange(SqlInt32 FromValue, SqlInt32 ToValue)
    {
        if (FromValue.IsNull || ToValue.IsNull)
        { yield break; }

        for (int index = FromValue.Value; index <= ToValue.Value; index++)
        { yield return index; }
    }
    private static void FillRangeValues(object obj, out SqlInt32 TheValue)
    { TheValue = (int)obj; }
}
```

### Downloading
This project is available from https://github.com/N1NFMind/sqlclr-numbers

### Installing, Terms of Use, Making Changes, etc.
Please refer to [SQL CLR]({{ site.baseurl }}/projects/SQL_CLR/) for information on all this house-keeping stuff.
