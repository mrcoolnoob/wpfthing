# WPF Application

In WPF, We changed the navigation property for MANY-TO-MANY to **ObservableCollection**. The reason being in WPF as our objects are in Memory so we need to make navigation property **observableCollection**  so that when we add new objects to them the WPF application can get notified and the changes can be reflected on the user interface.

![image-20230910133236916](https://i.imgur.com/7m03nxr.png)

# Structure Of Applicaton

![image-20230910132529308](https://i.imgur.com/53ele4m.png)

## Top Level Folders

1. Core
2. Migrations
3. Persistence

## CORE

1. Domain
2. Repostories
3. IUnitOfWOrk

## Migrations

Managed by EntityFramework

## Persistence

![image-20230910132757736](https://i.imgur.com/zKA2EKT.png)



# Repository Pattern

## What is Repository Pattern?

 ![image-20230910125535102](https://i.imgur.com/HD20edr.png)

We don't have **update** method in the repository. ***The repository shouldn't have the semantics of the database.***

## Benefits

![image-20230910125424496](https://i.imgur.com/z2F3Q4t.png)

## Unit of Work

Maintains a list of objects affected by a business transaction and coordinates the writing out of changes.

![image-20230910125828404](https://i.imgur.com/7Os0ZKE.png)

## Implementing Repository Pattern

1. We will have an interface **IRepository**.
2. We're going to implement the above interface in a class called Repository.
3. We will have specific repositories which will inherit from the Repository.
4. We have an interface of IUnitWork.
5. We will have a class which will implement **IUnitWork**.

![image-20230910130456808](https://i.imgur.com/z1HlwJn.png)

![image-20230910131310239](https://i.imgur.com/Z3dreUH.png)

## Structure

![image-20230910132344022](https://i.imgur.com/Z5ZCTH1.png)

## IRepository

```C#
using System;
using System.Collections.Generic;
using System.Linq.Expressions;

namespace Queries.Core.Repositories
{
    public interface IRepository<TEntity> where TEntity : class
    {
        TEntity Get(int id);
        IEnumerable<TEntity> GetAll();
        IEnumerable<TEntity> Find(Expression<Func<TEntity, bool>> predicate);

        // This method was not in the videos, but I thought it would be useful to add.
        TEntity SingleOrDefault(Expression<Func<TEntity, bool>> predicate);

        void Add(TEntity entity);
        void AddRange(IEnumerable<TEntity> entities);
        
        void Remove(TEntity entity);
        void RemoveRange(IEnumerable<TEntity> entities);
    }
}
```

## Repository Class

```C#
using Queries.Core.Repositories;
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Linq.Expressions;

namespace Queries.Persistence.Repositories
{
    public class Repository<TEntity> : IRepository<TEntity> where TEntity : class
    {
        protected readonly DbContext Context;

        public Repository(DbContext context)
        {
            Context = context;
        }

        public TEntity Get(int id)
        {
            // Here we are working with a DbContext, not PlutoContext. So we don't have DbSets 
            // such as Courses or Authors, and we need to use the generic Set() method to access them.
            return Context.Set<TEntity>().Find(id);
        }

        public IEnumerable<TEntity> GetAll()
        {
            // Note that here I've repeated Context.Set<TEntity>() in every method and this is causing
            // too much noise. I could get a reference to the DbSet returned from this method in the 
            // constructor and store it in a private field like _entities. This way, the implementation
            // of our methods would be cleaner:
            // 
            // _entities.ToList();
            // _entities.Where();
            // _entities.SingleOrDefault();
            // 
            // I didn't change it because I wanted the code to look like the videos. But feel free to change
            // this on your own.
            return Context.Set<TEntity>().ToList();
        }

        public IEnumerable<TEntity> Find(Expression<Func<TEntity, bool>> predicate)
        {
            return Context.Set<TEntity>().Where(predicate);
        }

        public TEntity SingleOrDefault(Expression<Func<TEntity, bool>> predicate)
        {
            return Context.Set<TEntity>().SingleOrDefault(predicate);
        }

        public void Add(TEntity entity)
        {
            Context.Set<TEntity>().Add(entity);
        }

        public void AddRange(IEnumerable<TEntity> entities)
        {
            Context.Set<TEntity>().AddRange(entities);
        }

        public void Remove(TEntity entity)
        {
            Context.Set<TEntity>().Remove(entity);
        }

        public void RemoveRange(IEnumerable<TEntity> entities)
        {
            Context.Set<TEntity>().RemoveRange(entities);
        }
    }
}
```

## ICourse Repository

```C#
using Queries.Core.Domain;
using System.Collections.Generic;

namespace Queries.Core.Repositories
{
    public interface ICourseRepository : IRepository<Course>
    {
        IEnumerable<Course> GetTopSellingCourses(int count);
        IEnumerable<Course> GetCoursesWithAuthors(int pageIndex, int pageSize);
    }
}
```

## CourseRepository

```C#
using Queries.Core.Domain;
using Queries.Core.Repositories;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace Queries.Persistence.Repositories
{
    public class CourseRepository : Repository<Course>, ICourseRepository
    {
        public CourseRepository(PlutoContext context) 
            : base(context)
        {
        }

        public IEnumerable<Course> GetTopSellingCourses(int count)
        {
            return PlutoContext.Courses.OrderByDescending(c => c.FullPrice).Take(count).ToList();
        }

        public IEnumerable<Course> GetCoursesWithAuthors(int pageIndex, int pageSize = 10)
        {
            return PlutoContext.Courses
                .Include(c => c.Author)
                .OrderBy(c => c.Name)
                .Skip((pageIndex - 1) * pageSize)
                .Take(pageSize)
                .ToList();
        }

        public PlutoContext PlutoContext
        {
            get { return Context as PlutoContext; }
        }
    }
}
```

## IUnitOfWork

```C#
using Queries.Core.Repositories;
using System;

namespace Queries.Core
{
    public interface IUnitOfWork : IDisposable
    {
        ICourseRepository Courses { get; }
        IAuthorRepository Authors { get; }
        int Complete();
    }
}
```

## UnitOfWork

```C#
using Queries.Core;
using Queries.Core.Repositories;
using Queries.Persistence.Repositories;

namespace Queries.Persistence
{
    public class UnitOfWork : IUnitOfWork
    {
        private readonly PlutoContext _context;

        public UnitOfWork(PlutoContext context)
        {
            _context = context;
            Courses = new CourseRepository(_context);
            Authors = new AuthorRepository(_context);
        }

        public ICourseRepository Courses { get; private set; }
        public IAuthorRepository Authors { get; private set; }

        public int Complete()
        {
            return _context.SaveChanges();
        }

        public void Dispose()
        {
            _context.Dispose();
        }
    }
}
```

## Using Repository

![image-20230910132257220](https://i.imgur.com/hZTHrlV.png)

