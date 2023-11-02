# Train Watch - Ex04 - Application Server (Back-End) Setup and Database Access 

> This is the **second** in a series of exercises where you will be building a website to manage information on trains. In this exercise you will add to your existing web application project. **Train Watch** is a site to keep up-to-date on trains across North America. 
>
## Objectives

This exercise will allow you to demonstrate:

- your understanding of basic Client-Server architecture
- your ability to create an application class library
- set up your application class library to interact with a database using EntityFramework
- create a series of server services to return data from the class library to your web page
- implement a tabular report from a collection of data supplied by a service call to your class library
- demonstrate your understanding of filter searches to obtain data from a database using appropriate LINQ queries

## Overview

You are to create an additional project within the `TrainWatchSolution` web application solution. This project will be a .Net Core 7 class library. The library will contain Entity, DLL, and BLL classes. You will configure your web application to use the class library via dependency injection. You will create a new component(s) to test that will display a tabular report using data returned from a class library service.

**Use the demos presented in class as a guide to implementing this exercise._**

## Install Database

A new database called [TrainWatch](./TrainWatch.bacpac) has been supplied to you for this exercise. 

Use Microsoft SQL Management Studio (use Import Data-tier Application) to deploy the `TrainWatch.bacpac` file to the database.

## Application Library (Back-End)

In this task, you will be creating a project for the "back-end" of the application and ensuring your solution follows rudimentary Client-Server architecture.

Add a Class library that supports .Net Core or .Net Standard to your solution called `TrainWatchSystem`. Use .Net Core 7 as your framework. Add 3 subfolders to this project: BLL, DAL, and Entities. *Remember that Github does not track empty folders, so you might wish to add a dummy.txt file in each folder if you intend to commit and push at this time. You can remove these dummy files as you add other files to the folder.*

### Entity Framework (NuGet packages)

We will be using the **Microsoft.EntityFrameworkCore.SqlServer lastest version** and **Microsoft.EntityFrameworkCore lastest version** NuGet packages to connect/interact to/with this database. Add these NuGet packages to the class library project. You will also need to add the NuGet packages to your web application project.

### Entities

You will use reverse engineering to create your entity classes that map to the database. This may be complted via EF Core Power Tools (EF Core verion is EF Core 7) or via CLI. 
- If done via CLI, install the **Microsoft.EntityFrameworkCore.Design lastest version** NuGet package and complete the scaffolding as demonstrated by your instructor.
- If done via EF Core Power Tools, select all tables and take the default Context name and namespace.
Place the entity classes in your Entities folder and the context class in your DAL folder.  

![ERD](./TrainWatch.png)

The system should add a default namespace to your entity classes. Check it is the of follows:  `namespace TrainWatchSystem.Entities`. 

### DAL Context

In the "DAL" folder, a file called `TrainWatchContext.cs` was created which will contain the `TrainWatchContext` class. Ensure it inherits from `DbContext`. Your class should have a constructor for DbContextOptions<> options injection. Change the access level of the context class to **internal**. Your DbSet properties for your entity classes should also be present.

### Extension class creation

#### **Your instructor may demonstrate a different technique in registering your services. Use the technique demonstrated by your instructor.**

The extension method will contain the dbcontext and transient services that will be available for dependency injection in your web application. This method will be called in the web application's `Program.cs`. The extension method will add your DbContext option and add the services (methods) of your BLL class (see below).

Create a class called `TrainWatchExtensions` at the root of the application library. Change the class to be public static. 

Add the static method `TrainWatchDependencies`. Check your in-class demo for appropriate parameters. Within the method, you will register your DbContext and the AddTransient factory for your BLL service classes. 

As you code, you will need to resolve references to needed namespaces holding your context class and service classes.

### BLL Services

Each service class will need an appropriate internal constructor that requires an instance of the `TrainWatchContext` as a parameter. Save the parameter value into a private readonly variable. As you code, you will need to resolve references to needed namespaces holding your context class and entity class. Once you have created the class, register the class in your extension method via **AddTransient**. If a method has a parameter, ensure one was passed and if not throw an `ArgumentNullException`.

#### **TrainWatchServices**

In this class, create a public method called `GetDbVersion()` that has no parameters and returns an instance of the `DbVersion` entity. The related database table should only have one row, so you can return that first item from the database context. 


#### **RailCarTypeServices**

In this class, create a public method called `GetRailCarTypes()` that has no parameters and returns a ordered collection of all records of the RailCarTypes entity. 


#### **RollingStockServices**

In this class, create a public method called `GetStockByReportMark(string)` that has one parameter and returns a collection of all RollingStock records where the ReportMark contains the parameter value. The parameter value will be a partial ReportMark. 


In this class, create a public method called `GetStockByCarType(int)` that has one parameter and returns a collection of all RollingStock records where the RailCarType matches the parameter value. 


Rebuild your Application Class Library. You should get a successful build.

## Web Application
### Create your database connection string in the appsettings.json file.


```csharp
"ConnectionStrings": {
    "TWDB" : "Server=xxxx;Database=TrainWatch;Trusted_Connection=true;MultipleActiveResultSets=true"
  }
```

### Setup the service dependency registration for the web application  

This will be done within your web application's `Program.cs` file. Use your in-class demonstration to complete this process. You will need to add a project reference to the web application pointing to the class libray.

### Overview Query Page

Create a `Query.razor`/`Query.razor.cs` Razor Page. The component class must declare in its dependency on the `RailCarTypeServices` and `RollingStockServices` classes using property injection. Details of the display can be found below. Be sure to add a menu item so that this page can be navigated to using the main menu (NavMenu); use the text "Query" for the link. Add an appropriate title to the page **and** the title of the browser tab.

The `Query` page will display summary information on the rolling stock data in an HTML table. Display the `ReportingMark`, `Owner`, `Capacity` and `InService` information. This query page will have two filters: Partial Search String and a Drop Down List (select control). The return query data will be of the same layout from both queries. Each filter will have its own `search` button requiring an event handler. Add a `Clear` button to reset both search arguments.

#### Partial Search String Filter

Allow the user to enter a partial search string to filter on any portion of the reporting mark data (e.g.: "BN" or "50"). Present all of the records that have any of the partial data in the reporting mark in a table.

#### Drop Down List Filter

Allow the user to pick a car type from a dropdown menu, and retrieve all of the cars of that type. Present all of the records of that car type in a table.


Only present the data columns as shown (in this sample) below:

| Reporting Mark | Owner               | Capacity | InService |
|----------------|---------------------|----------|-----------|
| BN 19117       | Burlington Northern | 192200   | True
| BN 95782       | Burlington Northern | 192200   | True
| BN 95831       | Burlington Northern | 192200   | True
| BN 95887       | Burlington Northern | 192200   | True
| BN 95914       | Burlington Northern | 192200   | True



To ensure that your web application works, build and run your project.
