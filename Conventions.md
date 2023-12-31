# Overriding Code First Conventions

There are two way to override code first conventions.

1. Data Annotations.
2. Fluent API.

## Default

1. By default, String is nullable in C# so the respective columns are also made nullable in the database by Entity Framework.
1. By default, the name of the table will be plural form of your class name.
1. Name of the column would be same as your property name in the class.
1. By dafault, if your class has the property **Id** with or **className[Id]** those fields will be created as primary key.
1. If property of class is nullable then the column in database should also be nullable.
1. By default, string fields are set to NVARCHAR(MAX)
1. By default, foreign key is added by seperated the class name and id by _ such as AuthorId will be Author_Id in database. 

## Overriding String NULL Convention

### Using Annotations

1. Make an import

   ```sh
   using System.ComponentModel.DataAnnotations
   ```

2. You will add the [Required] to the field you want to be non-nullable.

   ```sh
   [Required]
   public string Description { get; set; }
   ```

3. Make the migration to apply the changes to Database.

### Using Fluent API

Open your **Context** file and Add the following.

![image-20230909124312810](https://i.imgur.com/guB2NeL.jpg)

You have to run the migration as well.

### Annotation Vs. Fluent API

![image-20230909124614225](https://i.imgur.com/OVxMWOZ.jpg)

> Don't use both stick to any one of it. 

## Anotations

### Overriding Table Name Convention

```c#
[Table("name_of_table")]
public class Course
```

### Making a property Primary Key

```C#
[Key]
public string ISBN { get; set; }
```

Now, if we want to make sure that this fields is not autoPopulated then what we can do is we can use **DatabaseGenerated** annotation. 

```C#
[Key]
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string ISBN { get; set; }
```

### Composite key

Making two columns the primary key.

![image-20230909125724888](https://i.imgur.com/RgBtM60.png)

### Making nullable propery map to nullable DB field 

Simply apply [Required] to make field non-nullable

```C#
[Required]
public string column_name
```

### Restricting field length

```C#
[MaxLength(255)]
public string Name {get; set; }
```

By default, string fields are set to NVARCHAR(MAX).

### Making column unqiue

```C#
[Index(IsUnique=true)]
public string Name { get; set; }
```

### Foreign Key

![image-20230909130313441](https://i.imgur.com/iNflKXr.png)

By default, a foreign key is added automatically. When you have the propery `public Author Author`. However, if you want to give it a  custom name then you have to add a custom field added such as AuthorId and like it to the Navigation Propery and vice versa then the ForeignKey will be named as the custom propery name in the database.

## DatabaseGeneratedOption

There are actuall three ways to fill a column.

1. Manuall inserting a value (None)
2. Automated (Identity)
3. Computed (based on other columns says fullname based on first_name or last_name)

## objectives


Sure, here are some possible one liner SMART goals or objectives for a desktop developer:

- Develop and deploy a new feature for our desktop application that enhances the user experience and increases the customer satisfaction by the end of Q2.
- Improve the performance and security of our desktop application by implementing code optimization and encryption techniques by the end of Q3.
- Learn and apply a new programming language or framework that is relevant to our desktop application development by the end of Q4.
- Resolve at least 90% of the reported bugs and issues of our desktop application within the agreed SLA by the end of Q1.
- Conduct regular code reviews and provide constructive feedback to other desktop developers to ensure the code quality and consistency by the end of Q2.
- Create and update the technical documentation and user manuals of our desktop application to reflect the latest changes and features by the end of Q3.
- Design and execute comprehensive test cases and scenarios for our desktop application to ensure its functionality, usability, and reliability by the end of Q4.
- Mentor and coach a junior desktop developer to help them improve their skills and knowledge by the end of Q1.
- Participate in at least two professional development events or webinars related to desktop application development by the end of Q2.
- Seek feedback from the users and stakeholders of our desktop application and incorporate their suggestions and requirements into the development process by the end of Q3.

You can choose any of these goals or objectives that suit your role, responsibilities, and interests. You can also modify them to make them more specific, measurable, achievable, relevant, and time-bound. I hope this helps. 😊