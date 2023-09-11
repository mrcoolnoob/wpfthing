
# LINQ

## Fetching

```C#
var context = new PlutoContext();
var courses = context.Courses.Where(c=> c.Name.Contains("C#")).OrderBy(c => c.Name);
```

### Iterating over the fetched courses

```C#
foreach(var course in courses)
{
  Console.WriteLine(course.Name);
}
```

### Restricting/Filtering

```C#
var context = new PlutoContext();
var courses = context.Courses.Where(c=>c.Level == 1);
```

### Ordering

```C#
var context = new PlutoContext();
var courses = context.Courses.Where(c=>c.Level == 1).OrderBy(c=>c.Name).ThenBy(c=>c.Level);
```

By default, it's in ascending you can do it Descending as follow.

```C#
var courses = context.Courses.Where(c=>c.Level == 1).OrderByDescending(c=>c.Name).ThenByDescending(c=>c.Level);
```

### Projection (Mapping to different object)

```C#
var courses = context.Courses.Where(c=>c.Level == 1).OrderByDescending(c=>c.Name).ThenByDescending(c=>c.Level)
.Select(c=>new (courseName = c.Name, AuthorName = c.Author.Name));
```

When you're selecting the List item then you end up with the list of list.

```C#
var courses = context.Courses.Where(c=>c.Level == 1).OrderByDescending(c=>c.Name).ThenByDescending(c=>c.Level)
.Select(c=>c.Tags);
```

In case if you want to fetch only the list not the list of list you can flatten it using the **SelectMany** as follow.

```C#
var courses = context.Courses.Where(c=>c.Level == 1).OrderByDescending(c=>c.Name).ThenByDescending(c=>c.Level)
.SelectMany(c=>c.Tags);
```

### Set Operator (Making Unique)

```C#
var courses = context.Courses.Where(c=>c.Level == 1).OrderByDescending(c=>c.Name).ThenByDescending(c=>c.Level)
.SelectMany(c=>c.Tags).Distinct();
```

The above query removes the redundant records resulting in unique records only.

### Grouping

Grouping in LINQ is different than grouping in SQL. Each group has a property key which set to the filter property.

```C#
var groups = context.Courses.GroupBy(c=>c.Level);
```

![image-20230910103006138](https://i.imgur.com/XfZFLZj.png)

**Output**

![image-20230910103042600](https://i.imgur.com/YhpK9sb.png)

### JOINS

#### Inner Join

We use inner join when there is no relationship between our objects and we want to relate them. 

![image-20230910103432852](https://i.imgur.com/Rbokvv5.png)

We're joing courses with Author on the AuthorId in courseTable and Id in Author Table.

#### Group Join

We do Group Join in LINQ when we need a Left JOIN in SQL.

![image-20230910103615132](https://i.imgur.com/PO8euEN.png)

Group Join match each item in the table in left side to one or more items in the table on the right side. 

![image-20230910103833116](https://i.imgur.com/zgXX5Eu.png)

![image-20230910103948718](https://i.imgur.com/KlGt3qk.png)

#### CROSS Join

Similiar to CROSS JOIN in SQL. We use SelectMany for CrossJoin.

![image-20230910104111492](https://i.imgur.com/eK5BOTc.png)

### Partitioning (Paging)

Say we have a total of 10 records in each page now we want to fetch 10 records for next page. This is how we can do it.

```C#
var courses = context.Courses.Skip(10).Take(10);
```

### Element Operator

 We used it if we need to fetch only single or first element in the list.

```C#
context.Courses.First();
context.Courses.FirstOrDefault();
```

> With **First** method if the table is empty and we are going to fetch the record then we will get the exception but with the **FirstOrDefault** method it will return **null** if there is no record.

You can also provide the condition inside it as well.

```C#
context.Courses.OrderBy(c => c.Level).FirstOrDefault(c=>c.FullPrice > 100);
```

Similary, we also have **Last** and **LastOrDefault** method as well as **Single** and **SingleOrDefault**.

> **Single** method also throws exception if the criteria you provide gets satisfied by more than one record i.e. in case of multiple records.

### Quantifying

To see if all courses satisfy a criteria.

```C#
var res = context.Courses.All(c => c.Price > 100);
```

It returns the boolean i.e. true if all records satisfies the condition else it returns the false.

To see if atleast on course satisfy a criteria.

```C#
var res = context.Courses.Any(c => c.Price > 100);
```

To get count of records

```C#
var count = context.Courses.Count();
```

To get Max, Min, Avg

```C#
context.Courses.Max(c => c.Price);
context.Courses.Min(c => c.Price);
context.Courses.Average(c => c.Price);
```

## Deferred Execution

LINQ queries are not executed at the time you create them instead they are executed at the time when you

1. Iterating over query variable
2. Calling ToList, ToDictionary, ToArray
3. Calling First, Last, Single, Count, Max, Min, Average

## Lazy Loading

First you have to mark the property as **virtual** it is the key to lazy loading. For lazy loading, you have to make all the navigation properties **virtual**. Loading data on demand.

```C#
public virtual ICollection<Tag> Tags { get; set; }
```

Declaring **virtual** is the key to enable lazy loading on this property.

### When to use Lazy loading

![image-20230910120640468](https://i.imgur.com/f2y9fbS.png)

## N+1 Issues

![image-20230910120905500](https://i.imgur.com/5zHC5Tu.png)

You may say that as we're doing **ToList** the query will be immediatly sent to the database and records will be fetched which is correct. But another problem is the fact that as We have multiple Tags for a single course what will happen is the ToList does fetch all the courses but not the courses cerrosponding tags. They will be fetch one by one when we iterate over the loop this is why it is said as N+1.

We can solve this problem by fetching the courses as well as the tags as a result of single query which is nothing but **eager loading**.

## Eager Loading

![image-20230910121547201](https://i.imgur.com/dFsgvxt.png)

### For multiple Levels

![image-20230910121817895](https://i.imgur.com/lR5HdSQ.png)

## Explicit Loading

![image-20230910121950925](https://i.imgur.com/3VlkRff.png)

![image-20230910122150300](https://i.imgur.com/saMfTl0.png)

This will result in only two queries.

**Load** is the key to Explicit Loading.

![image-20230910122439744](https://i.imgur.com/sdyIz7A.png)

## CURD

### CREATE/ADD

![image-20230910123018032](https://i.imgur.com/kbhdLbO.png)

Even though we're referring to Author with ID 1 this code will create a new Author in the table say with ID 6 because we're initiating a new author.

There are two ways to resolve this.

1.  Get this new author object into DbContext so that  Dbcontext knows that this object exists. (This is recommended for WPF application).
2.  Another way is to use AuthorID foreign key property (This is recomnended for web application).

#### WPF Way

![image-20230910123536069](https://i.imgur.com/0dIqbi3.png)

The theme is that in WPF application we will have the DbContext in the memory as long as the application is open so what happens is that **.ToList** will load the objects in the memory and now even if that **.Single** query is executed it will be runned on that in memory objects loaded due to **ToList** method on the database.

#### Web Way

In web our context is short lived, we dispose our context after a few seconds.

![image-20230910123830572](https://i.imgur.com/VSugonE.png)

![image-20230910123957478](https://i.imgur.com/ONnyg9p.png)

### Updating

![image-20230910124130378](https://i.imgur.com/xOzrIjd.png)

### Remove

By default, CASCADE delete is Enabled. To remove more than one element you can use the **RemoveRange** method.

####  With CASCADE delete

![image-20230910124310165](https://i.imgur.com/gILnVHK.png)

#### Without CASCADE delete

![image-20230910124704795](https://i.imgur.com/DtQaiJS.png)



