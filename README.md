# SOLID Principles in C# [with Examples]

Classes are the building blocks of any C# application. If these blocks are not strong, the application is going to face the tough time in future. This essentially means that not so well-written code can lead to very difficult situations when the application scope widens or application faces certain design issues either in production or maintenance.

On the other hand, set of well designed and written classes can speed up the coding process by leaps and bounds, while reducing the number of bugs in comparison.

In this tutorial, We will discuss SOLID principles with examples which are 5 most recommended design principles, we should keep in mind while writing our classes. They also form the best practices to be followed for designing our application classes.

Table of Contents
=================

<!--ts-->
* [Single Responsibility Principle](#single-responsibility-principle)
* [Open Closed Principle](#open-closed-principle)
* [Liskov's Substitution Principle](#liskovs-substitution-principle)
* [Interface Segregation Principle](#interface-segregation-principle)
* [Dependency Inversion Principle](#dependency-inversion-principle)
<!--te-->

![screenshot of conversion](https://raw.githubusercontent.com/gopikrishnareddy93/SOLID-Principles/master/images/solid_class_design_principles.png)

Lets drill down all of them one by one.

Single Responsibility Principle
===============================

The name of the principle says it all:

```
“One class should have one and only one responsibility”
```

In other words, we should write, change and maintain a class for only one purpose. If it is model class then it should strictly represent only one actor/ entity. This will give us the flexibility to make changes in future without worrying the impacts of changes for another entity.

Similarly, If we are writing service/manager class then it should contain only that part of method calls and nothing else. Not even utility global functions related to module. Better separate them in another globally accessible class file. This will help in maintaining the class for that particular purpose, and we can decide the visibility of class to specific module only.

We can find plenty of examples in popular C# libraries which follow single responsibility principle. For example, in log4j, we have different classes with logging methods, different classes representing  different logging levels and so on.

In our application level code, we define model classes to represent real time entities such as person, employee, account etc. Most of these classes are examples of SRP principle because when we need to change the state of a person, only then we will modify a person class. and so on.

In given example, we have two classes `Person` and `Account`. Both have single responsibility to store their specific information. If we want to change state of Person then we do not need to modify the class `Account` and vice-versa.

#### Person.cs

``` cs
public class Person
{
    private long personId;
    private string firstName;
    private string lastName;
    private string age;
    private List<Account> accounts;
}
```

#### Account.cs
``` cs
public class Account
{
    private long guid;
    private string accountNumber;
    private string accountName;
    private string status;
    private string type;
}
```

Open Closed Principle
======================
This is second important rule which we should keep in mind while designing our application. Open closed principle states:

```
“Software components should be open for extension, but closed for modification”
```

What does this mean?? This means that our classes should be designed such a way that whenever fellow developers wants to change the flow of control in specific conditions in application, all they need to extend our class and override some functions but not alter the base class.

If other developers are not able to design desired behavior due to constraints put by our class, then we should reconsider changing our class. I do not mean here that anybody can change the whole logic of our class, but he/she should be able to override the options provided by software in unharmful way permitted by software.

Suppose we have a Rectangle class with the properties Height and Width.

``` cs
public class Rectangle{  
   public double Height {get;set;}  
   public double Wight {get;set; }  
}   
```

Our app needs the ability to calculate the total area of a collection of Rectangles. Since we already learned the Single Responsibility Principle (SRP), we don't need to put the total area calculation code inside the rectangle. So here I created another class for area calculation.

``` cs
public class AreaCalculator {  
   public double TotalArea(Rectangle[] arrRectangles)  
   {  
      double area;  
      foreach(var objRectangle in arrRectangles)  
      {  
         area += objRectangle.Height * objRectangle.Width;  
      }  
      return area;  
   }  
}  
``` 

Hey, we did it. We made our app without violating SRP. No issues for now. But can we extend our app so that it could calculate the area of not only Rectangles but also the area of Circles as well? Now we have an issue with the area calculation issue, because the way to do circle area calculation is different. Hmm. Not a big deal. We can change the TotalArea method a bit, so that it can accept an array of objects as an argument. We check the object type in the loop and do area calculation based on the object type.

``` cs
public class Rectangle{  
   public double Height {get;set;}  
   public double Wight {get;set; }  
}  
public class Circle{  
   public double Radius {get;set;}  
}  
public class AreaCalculator  
{  
   public double TotalArea(object[] arrObjects)  
   {  
      double area = 0;  
      Rectangle objRectangle;  
      Circle objCircle;  
      foreach(var obj in arrObjects)  
      {  
         if(obj is Rectangle)  
         {  
            objRectangle = (Rectangle)obj;  
            area += obj.Height * obj.Width;  
         }  
         else  
         {  
            objCircle = (Circle)obj;  
            area += objCircle.Radius * objCircle.Radius * Math.PI;  
         }  
      }  
      return area;  
   }  
}   
```

Wow. We are done with the change. Here we successfully introduced Circle into our app. We can add a Triangle and calculate it's area by adding one more "if" block in the TotalArea method of AreaCalculator. But every time we introduce a new shape we need to alter the TotalArea method. So the AreaCalculator class is not closed for modification. How can we make our design to avoid this situation? Generally we can do this by referring to abstractions for dependencies, such as interfaces or abstract classes, rather than using concrete classes. Such interfaces can be fixed once developed so the classes that depend upon them can rely upon unchanging abstractions. Functionality can be added by creating new classes that implement the interfaces. So let's refract our code using an interface.

``` cs
public abstract class Shape  
{  
   public abstract double Area();  
}   
```
Inheriting from Shape, the Rectangle and Circle classes now look like this:

``` cs
public class Rectangle: Shape  
{  
   public double Height {get;set;}  
   public double Width {get;set;}  
   public override double Area()  
   {  
      return Height * Width;  
   }  
}  
public class Circle: Shape  
{  
   public double Radius {get;set;}  
   public override double Area()  
   {  
      return Radius * Radus * Math.PI;  
   }  
}   
```

Every shape contains its area show with it's own way of calculation functionality and our AreaCalculator class will become simpler than before.

``` cs
public class AreaCalculator  
{  
   public double TotalArea(Shape[] arrShapes)  
   {  
      double area=0;  
      foreach(var objShape in arrShapes)  
      {  
         area += objShape.Area();  
      }  
      return area;  
   }  
}   
```

Now our code is following SRP and OCP both. Whenever you introduce a new shape by deriving from the "Shape" abstract class, you need not change the "AreaCalculator" class. Awesome. Isn't it?

Liskov’s Substitution Principle
======================================
This principle is a variation of previously discussed open closed principle. It says:

```
“Derived types must be completely substitutable for their base types”
```

It means that the classes other developers create by extending our class should be able to fit in application without failure. This requires the objects of your subclasses to behave in the same way as the objects of your superclass. This is mostly seen in places where we do run time type identification and then cast it to appropriate reference type.

This principle is just an extension of the Open Close Principle and it means that we must ensure that new derived classes extend the base classes without changing their behavior. I will explain this with a real world example that violates LSP.

A father is a doctor whereas his son wants to become a footballer. So here the son can't replace his father even though they both belong to the same family hierarchy.

Now jump into an example to learn how a design can violate LSP. Suppose we need to build an app to manage data using a group of SQL files text. Here we need to write functionality to load and save the text of a group of SQL files in the application directory. So we need a class that manages the load and save of the text of group of SQL files along with the SqlFile Class. 

``` cs
public class SqlFile  
{  
   public string FilePath {get;set;}  
   public string FileText {get;set;}  
   public string LoadText()  
   {  
      /* Code to read text from sql file */  
   }  
   public string SaveText()  
   {  
      /* Code to save text into sql file */  
   }  
}  
public class SqlFileManager  
{  
   public List<SqlFile> lstSqlFiles {get;set}  
  
   public string GetTextFromFiles()  
   {  
      StringBuilder objStrBuilder = new StringBuilder();  
      foreach(var objFile in lstSqlFiles)  
      {  
         objStrBuilder.Append(objFile.LoadText());  
      }  
      return objStrBuilder.ToString();  
   }  
   public void SaveTextIntoFiles()  
   {  
      foreach(var objFile in lstSqlFiles)  
      {  
         objFile.SaveText();  
      }  
   }  
}   
```

We are done with our part and the functionality looks good for now. After some time our lead might tell us that we may have a few read-only files in the application folder, so we need to restrict the flow whenever it tries to do a save on them.

We can do that by creating a "ReadOnlySqlFile" class that inherits the "SqlFile" class and we need to alter the SaveTextIntoFiles() method by introducing a condition to prevent calling the SaveText() method on ReadOnlySqlFile instances.

``` cs
public class SqlFile  
{  
   public string LoadText()  
   {  
   /* Code to read text from sql file */  
   }  
   public void SaveText()  
   {  
      /* Code to save text into sql file */  
   }  
}  
public class ReadOnlySqlFile: SqlFile  
{  
   public string FilePath {get;set;}  
   public string FileText {get;set;}  
   public string LoadText()  
   {  
      /* Code to read text from sql file */  
   }  
   public void SaveText()  
   {  
      /* Throw an exception when app flow tries to do save. */  
      throw new IOException("Can't Save");  
   }  
}   
```

To avoid an exception we need to modify "SqlFileManager" by adding one condition to the loop.

``` cs
public class SqlFileManager  
{  
   public List<SqlFile? lstSqlFiles {get;set}  
   public string GetTextFromFiles()  
   {  
      StringBuilder objStrBuilder = new StringBuilder();  
      foreach(var objFile in lstSqlFiles)  
      {  
         objStrBuilder.Append(objFile.LoadText());  
      }  
      return objStrBuilder.ToString();  
   }  
   public void SaveTextIntoFiles()  
   {  
      foreach(var objFile in lstSqlFiles)  
      {  
         //Check whether the current file object is read only or not.If yes, skip calling it's  
         // SaveText() method to skip the exception.  
  
         if(! objFile is ReadOnlySqlFile)  
         {
             objFile.SaveText();  
         }         
      }  
   }  
}   
```

Here we altered the SaveTextIntoFiles() method in the SqlFileManager class to determine whether or not the instance is of ReadOnlySqlFile to avoid the exception. We can't use this ReadOnlySqlFile class as a substitute of it's parent without altering SqlFileManager code. So we can say that this design is not following LSP. Let's make this design follow the LSP. Here we will introduce interfaces to make the SqlFileManager class independent from the rest of the blocks.

``` cs
public interface IReadableSqlFile  
{  
   string LoadText();  
}  
public interface IWritableSqlFile  
{  
   void SaveText();  
}   
```

Now we implement IReadableSqlFile through the ReadOnlySqlFile class that reads only the text from read-only files.

``` cs
public class ReadOnlySqlFile: IReadableSqlFile  
{  
   public string FilePath {get;set;}  
   public string FileText {get;set;}  
   public string LoadText()  
   {  
      /* Code to read text from sql file */  
   }  
}   
```

Here we implement both IWritableSqlFile and IReadableSqlFile in a SqlFile class by which we can read and write files. 

``` cs
public class SqlFile: IWritableSqlFile,IReadableSqlFile  
{  
   public string FilePath {get;set;}  
   public string FileText {get;set;}  
   public string LoadText()  
   {  
      /* Code to read text from sql file */  
   }  
   public void SaveText()  
   {  
      /* Code to save text into sql file */  
   }  
}   
```

Now the design of the SqlFileManager class becomes like this:

``` cs
public class SqlFileManager  
{  
   public string GetTextFromFiles(List<IReadableSqlFile> aLstReadableFiles)  
   {  
      StringBuilder objStrBuilder = new StringBuilder();  
      foreach(var objFile in aLstReadableFiles)  
      {  
         objStrBuilder.Append(objFile.LoadText());  
      }  
      return objStrBuilder.ToString();  
   }  

   public void SaveTextIntoFiles(List<IWritableSqlFile> aLstWritableFiles)  
   {  
   foreach(var objFile in aLstWritableFiles)  
   {  
      objFile.SaveText();  
   }  
   }  
}   
```

Here the GetTextFromFiles() method gets only the list of instances of classes that implement the IReadOnlySqlFile interface. That means the SqlFile and ReadOnlySqlFile class instances. And the SaveTextIntoFiles() method gets only the list instances of the class that implements the IWritableSqlFiles interface, in other words SqlFile instances in this case. Now we can say our design is following the LSP. And we fixed the problem using the Interface segregation principle by (ISP) identifying the abstraction and the responsibility separation method.

Interface Segregation Principle
===============================

This principle is my favorite one. It is applicable to interfaces as single responsibility principle holds to classes. ISP says:

```
“Clients should not be forced to implement unnecessary methods which they will not use”
```

We can define it in another way. An interface should be more closely related to the code that uses it than code that implements it. So the methods on the interface are defined by which methods the client code needs than which methods the class implements. So clients should not be forced to depend upon interfaces that they don't use.

Like classes, each interface should have a specific purpose/responsibility (refer to SRP). You shouldn't  force a class to implement an interface when it doesn't share that purpose. The larger the interface, the more likely it includes methods that not all implementers can do. That's the essence of the Interface Segregation Principle. Let's start with an example that breaks ISP. Suppose we need to build a system for an IT firm that contains roles like TeamLead and Programmer where TeamLead divides a huge task into smaller tasks and assigns them to programmers or can directly work on them.

Based on specifications, we need to create an interface and a TeamLead class to implement it. 

``` cs 
public Interface ILead  
{  
   void CreateSubTask();  
   void AssginTask();  
   void WorkOnTask();  
}  
public class TeamLead : ILead  
{  
   public void AssignTask()  
   {  
      //Code to assign a task.  
   }  
   public void CreateSubTask()  
   {  
      //Code to create a sub task  
   }  
   public void WorkOnTask()  
   {  
      //Code to implement perform assigned task.  
   }  
}   
```

OK. The design looks fine for now. Later another role like Manager, who assigns tasks to TeamLead and will not work on the tasks, is introduced into the system. Can we directly implement an ILead interface in the Manager class, like the following?

``` cs
public class Manager: ILead  
{  
   public void AssignTask()  
   {  
      //Code to assign a task.  
   }  
   public void CreateSubTask()  
   {  
      //Code to create a sub task.  
   }  
   public void WorkOnTask()  
   {  
      throw new Exception("Manager can't work on Task");  
   }  
}   
```

Since the Manager can't work on a task and at the same time no one can assign tasks to the Manager, this WorkOnTask() should not be in the Manager class. But we are implementing this class from the ILead interface, we need to provide a concrete Method. Here we are forcing the Manager class to implement a WorkOnTask() method without a purpose. This is wrong. The design violates ISP. Let's correct the design.

``` cs
public interface IProgrammer  
{  
   void WorkOnTask();  
}   
```

An interface that provide contracts to manage the tasks: 

``` cs
public interface ILead  
{  
   void AssignTask();  
   void CreateSubTask();  
}   
```

Then the implementation becomes:

``` cs
public class Programmer: IProgrammer  
{  
   public void WorkOnTask()  
   {  
      //code to implement to work on the Task.  
   }  
}  
public class Manager: ILead  
{  
   public void AssignTask()  
   {  
      //Code to assign a Task  
   }  
   public void CreateSubTask()  
   {  
   //Code to create a sub taks from a task.  
   }  
}   
```

TeamLead can manage tasks and can work on them if needed. Then the TeamLead class should implement both of the IProgrammer and ILead interfaces.

``` cs
public class TeamLead: IProgrammer, ILead  
{  
   public void AssignTask()  
   {  
      //Code to assign a Task  
   }  
   public void CreateSubTask()  
   {  
      //Code to create a sub task from a task.  
   }  
   public void WorkOnTask()  
   {  
      //code to implement to work on the Task.  
   }  
}   
```

Wow. Here we separated responsibilities/purposes and distributed them on multiple interfaces and provided a good level of abstraction too.

Dependency Inversion Principle
==================================

Most of us are already familiar with the words used in principle’s name. DI principle says:

```
“Depend on abstractions, not on concretions”
```

In other words. we should design our software in such a way that various modules can be separated from each other using an abstract layer to bind them together.

High-level modules/classes implement business rules or logic in a system (application). Low-level modules/classes deal with more detailed operations, in other words they may deal with writing information to databases or passing messages to the operating system or services.

A high-level module/class that has dependency on low-level modules/classes or some other class and knows a lot about the other classes it interacts with is said to be tightly coupled. When a class knows explicitly about the design and implementation of another class, it raises the risk that changes to one class will break the other class. So we must keep these high-level and low-level modules/class loosely coupled as much as we can. To do that, we need to make both of them dependent on abstractions instead of knowing each other. Let's start an with an example.

Suppose we need to work on an error logging module that logs exception stack traces into a file. Simple, isn't it? The following are the classes that provide functionality to log a stack trace into a file. 

``` cs 
public class FileLogger  
{  
   public void LogMessage(string aStackTrace)  
   {  
      //code to log stack trace into a file.  
   }  
}  
public class ExceptionLogger  
{  
   public void LogIntoFile(Exception aException)  
   {  
      FileLogger objFileLogger = new FileLogger();  
      objFileLogger.LogMessage(GetUserReadableMessage(aException));  
   }  
   private GetUserReadableMessage(Exception ex)  
   {  
      string strMessage = string. Empty;  
      //code to convert Exception's stack trace and message to user readable format.  
      ....  
      ....  
      return strMessage;  
   }  
}   
```

A client class exports data from many files to a database.

``` cs
public class DataExporter  
{  
   public void ExportDataFromFile()  
   {  
   try {  
      //code to export data from files to database.  
   }  
   catch(Exception ex)  
   {  
      new ExceptionLogger().LogIntoFile(ex);  
   }  
}  
}   
```

Looks good. We sent our application to the client. But our client wants to store this stack trace in a database if an IO exception occurs. Hmm... okay, no problem. We can implement that too. Here we need to add one more class that provides the functionality to log the stack trace into the database and an extra method in ExceptionLogger to interact with our new class to log the stack trace. 

``` cs
public class DbLogger  
{  
   public void LogMessage(string aMessage)  
   {  
      //Code to write message in database.  
   }  
}  
public class FileLogger  
{  
   public void LogMessage(string aStackTrace)  
   {  
      //code to log stack trace into a file.  
   }  
}  
public class ExceptionLogger  
{  
   public void LogIntoFile(Exception aException)  
   {  
      FileLogger objFileLogger = new FileLogger();  
      objFileLogger.LogMessage(GetUserReadableMessage(aException));  
   }  
   public void LogIntoDataBase(Exception aException)  
   {  
      DbLogger objDbLogger = new DbLogger();  
      objDbLogger.LogMessage(GetUserReadableMessage(aException));  
   }  
   private string GetUserReadableMessage(Exception ex)  
   {  
      string strMessage = string.Empty;  
      //code to convert Exception's stack trace and message to user readable format.  
      ....  
      ....  
      return strMessage;  
   }  
}  
public class DataExporter  
{  
   public void ExportDataFromFile()  
   {  
      try {  
         //code to export data from files to database.  
      }  
      catch(IOException ex)  
      {  
         new ExceptionLogger().LogIntoDataBase(ex);  
      }  
      catch(Exception ex)  
      {  
         new ExceptionLogger().LogIntoFile(ex);  
      }  
   }  
}   
```

Looks fine for now. But whenever the client wants to introduce a new logger, we need to alter ExceptionLogger by adding a new method. If we continue doing this after some time then we will see a fat ExceptionLogger class with a large set of methods that provide the functionality to log a message into various targets. Why does this issue occur? Because ExceptionLogger directly contacts the low-level classes FileLogger and and DbLogger to log the exception. We need to alter the design so that this ExceptionLogger class can be loosely coupled with those class. To do that we need to introduce an abstraction between them, so that ExcetpionLogger can contact the abstraction to log the exception instead of depending on the low-level classes directly. 

``` cs
public interface ILogger  
{  
   void LogMessage(string aString);  
}   
```

Now our low-level classes need to implement this interface.

``` cs
public class DbLogger: ILogger  
{  
   public void LogMessage(string aMessage)  
   {  
      //Code to write message in database.  
   }  
}  
public class FileLogger: ILogger  
{  
   public void LogMessage(string aStackTrace)  
   {  
      //code to log stack trace into a file.  
   }  
}   
```

Now, we move to the low-level class's intitiation from the ExcetpionLogger class to the DataExporter class to make ExceptionLogger loosely coupled with the low-level classes FileLogger and EventLogger. And by doing that we are giving provision to DataExporter class to decide what kind of Logger should be called based on the exception that occurs.

``` cs
public class ExceptionLogger  
{  
   private ILogger _logger;  
   public ExceptionLogger(ILogger aLogger)  
   {  
      this._logger = aLogger;  
   }  
   public void LogException(Exception aException)  
   {  
      string strMessage = GetUserReadableMessage(aException);  
      this._logger.LogMessage(strMessage);  
   }  
   private string GetUserReadableMessage(Exception aException)  
   {  
      string strMessage = string.Empty;  
      //code to convert Exception's stack trace and message to user readable format.  
      ....  
      ....  
      return strMessage;  
   }  
}  
public class DataExporter  
{  
   public void ExportDataFromFile()  
   {  
      ExceptionLogger _exceptionLogger;  
      try {  
         //code to export data from files to database.  
      }  
      catch(IOException ex)  
      {  
         _exceptionLogger = new ExceptionLogger(new DbLogger());  
         _exceptionLogger.LogException(ex);  
      }  
      catch(Exception ex)  
      {  
         _exceptionLogger = new ExceptionLogger(new FileLogger());  
         _exceptionLogger.LogException(ex);  
      }  
   }  
}   
```

We successfully removed the dependency on low-level classes. This ExceptionLogger doesn't depend on the FileLogger and EventLogger classes to log the stack trace. We don't need to change the ExceptionLogger's code any more for any new logging functionality. We need to create a new logging class that implements the ILogger interface and must add another catch block to the DataExporter class's ExportDataFromFile method. 

``` cs
public class EventLogger: ILogger  
{  
   public void LogMessage(string aMessage)  
   {  
      //Code to write message in system's event viewer.  
   }  
}   
```

And we need to add a condition in the DataExporter class as in the following:

``` cs
public class DataExporter  
{  
   public void ExportDataFromFile()  
   {  
      ExceptionLogger _exceptionLogger;  
      try {  
         //code to export data from files to database.  
      }  
      catch(IOException ex)  
      {  
         _exceptionLogger = new ExceptionLogger(new DbLogger());  
         _exceptionLogger.LogException(ex);  
      }  
      catch(SqlException ex)  
      {  
         _exceptionLogger = new ExceptionLogger(new EventLogger());  
         _exceptionLogger.LogException(ex);  
      }  
      catch(Exception ex)  
      {  
         _exceptionLogger = new ExceptionLogger(new FileLogger());  
         _exceptionLogger.LogException(ex);  
      }  
   }  
}   
```

Looks good. But we introduced the dependency here in the DataExporter class's catch blocks. Yeah, someone must take the responsibility to provide the necessary objects to the ExceptionLogger to make the work done.

Let me explain it with a real world example. Suppose we want to have a wooden chair with specific measurements and the kind of wood to be used to make that chair from. Then we can't leave the decision making on measurements and the wood to the carpenter. Here his job is to make a chair based on our requirements with his tools and we provide the specifications to him to make a good chair.

So what is the benefit we get by the design? Yes, we definitely have a benefit with it. We need to modify both the DataExporter class and ExceptionLogger class whenever we need to introduce a new logging functionality. But in the updated design we need to add only another catch block for the new exception logging feature. Coupling is not inherently evil. If you don't have some amount of coupling, your software will not do anything for you. The only thing we need to do is understand the system, requirements and environment properly and find areas where DIP should be followed.

Great, we have gone through the all five SOLID principles successfully. And we can conclude that using these principles we can build an application with tidy, readable and easily maintainable code.

Here you may have some doubt. Yes, about the quantity of code. Because of these principles, the code might become larger in our applications. But you need to compare it with the quality that we get by following these principles. Hmm, but anyway 27 lines are much less than 200 lines. I am not saying that these principles should be followed 100%, you need to draw a line so that you can hold the control over the things like quality and delivery to maintain their balance.

This is my little effort to share the uses of SOLID principles. I hope you enjoyed this article.