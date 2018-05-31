# SOLID_Principals
Overview of what I learned about the SOLID Principles

# Single Responsibility
Classes should only be doing one thing. Meaning there should only be one reason for the class to change.

Example - Problem 

Here is some text that has person information. The code below reads the text, parses it and maps the information to a person object. Once the information is mapped is outputs it in a specific format.
```csharp
/* @"(Name)John Doe
                                    (Age)20
                                    (City)Ashtabula, OH
                                    (Flags)NYN

                                    (Name)Jane Doe
                                    (Flags)YNY
                                    (City)N Kingsville, OH

                                    (Name)Sally Jones
                                    (Age)25
                                    (City)Paris
                                    (Flags)YYY"; */
                                    
public class TextToPersonMapper
    {
        public void ReadText()
        {
         
        }

        public void MapTextToPerson()
        {

        }

        public void WritePerson()
        {

        }
    }
```
There are many reasons the TextToPersonMapper class can change

1) Incoming text format will change
2) More fields/info will be added to the text
3) Business wants the output format to change

Example - Solution

Here we seperate the reading functions and writing functions to seperate classes. Now if changes are needed in one are it will be easy to implement.
```csharp
    public class TextReader : IReader
    {
        public void ParseText()
        {

        }
    }
    
    public class TextWriter : IWriter
    {
        public void WriteText()
        {

        }
    }
    
    public class TextToPersonMapper
    {
      // Inject Text Reader and OutputWriter
        public void MapTextToPerson()
        {
           var person  = textReader.ReadText();
           outputWriter.WriteText(person);
        }
    }
```    
# Open Closed

Classes, methods, properties etc. should be open to extension but not modification. Code in a way that you can add new behaviour without changing code. The way I understand it is with a house analogy. If you need to fix a bad pipe in your house you should not take apart your house just to fix the pipe. Also, if you follow SRP then you're most likely to follow the open/closed principle as well

Example - Problem
```csharp
        public void ParseText(string line)
        {
            dynamic lineArray;

            if (line.StartsWith("("))
            {
                //multiple if conditions inside here
                // Do parethesis stuff
            }
            else if (line.StartsWith("<"))
            {
                 //multiple if conditions inside here
                // do xml or html stuff
            }
            else if (line.StartsWith("{"))
            {
                //multiple if conditions inside here
                // do json stuff
            }
        }
```        
Example - Solution

Create a class that determines the input type
```csharp
     public class InputTypeDeterminator
    {
        public static InputType DetermineInputType(char firstCharacter)
        {
            return InputType.TryGetInputType(firstCharacter);
        }
    }
```
Create different parser classes that will handle each text differntley. All parser classes will inherit from a base parser class that will do the parser work common to all parsings. Use dpendency injection to serve up the right Parser


![alt text](https://github.com/finekite/SOLID_Principals/blob/master/ParserPic.PNG)

```csharp
// Ideally this would be done differently if it was in a DI container
 private void GetParserInstance()
        {
            if (inputType.Equals(InputType.XML))
            {
                parser = new XmlParser();
            }
            else if (inputType.Equals(InputType.JSON))
            {
                parser = new JsonParser();
            }
            else if (inputType.Equals(StringInputType.Parathesis))
            {
                parser = new ParenthesesParser();
            }
        }
  ```
# Liskov

When using inheritance try thinking if the derived class is a substitute of the base class. Often times the derived class can still be defined as a 'IS A' of the parent but it still lacks certain capabilities that the parent has and therefore cannot be substituted.

Example - Problem

```csharp
    public class Vehicle
    {
        void Drive()
        {

        }

        void PlayRadio()
        {

        }
    }

    public class Car  :  Vehicle
    {
        // Can Drive() and PlayRadio()
    }

    public class Scooter  :  Vehicle
    {
        // Can Drive() but cannot PlayRadio()
    }
```
A Scooter is a vehicle but it still cannot play radio

Example - Solution

In this solution, we added an Automobile class and derive Car from it. Now we can take out PlayRadio() and stick it in Automobile.

This now accomplishes 2 things
1) Car IS A substitute for Automobile
2) Scooter IS A substitute for Vehicle
```csharp
    public class Vehicle
    {
        void Drive()
        {

        }
    }

    public class Automobile: Vehicle
    {
        void PlayRadio()
        {

        }
    }

    public class Scooter: Vehicle
    {
        
    }
```
# Interface Segregation

Try to make your interfaces small so that whatever code is using it (aka client code) uses all of it.

Example - Problem

Many unit tests will mock the HttpContextBase class. If you look at the source code for the HttpContext class you will see it is quite large with many of the methods not implemented.

![alt text](https://github.com/finekite/SOLID_Principals/blob/master/HttpContextPic.PNG)

Typically we're just interested in mocking the HttpContextBase class for one or two reasons and we do not need any of the other methods from the HttpContextBase class. Mocking up an entire HttpContextBase class would violate this principle.

Example  - Solution

Create a class or interface that inherits from HttpContext but only utilizes the one or two properties/methods you need for your testing.

```csharp
    public class HttpContextBaseHelper : HttpContextBase
    {
        /// <summary>
        /// The user
        /// </summary>
        public IPrincipal Principal {get; set;}

        /// <summary>
        /// the request base helper
        /// </summary>
        public HttpRequestBaseHelper RequestBaseHelper {get ; set;}

        /// <summary>
        /// overrides the request to return the HttpRequestBaseHelper instead of the actual HttpRequestBase class
        /// </summary>
        public override HttpRequestBase Request 
        {
            get
            {
                return RequestBaseHelper;
            }
        }

        /// <summary>
        /// The user
        /// </summary>
        public override IPrincipal User
        {
            get
            {
                return Principal;
            }
        }

        /// <summary>
        /// overrides the response
        /// </summary>
        public override HttpResponseBase Response
        {
            get
            {
                return new HttpResponseBaseHelper();
            }
        }

        /// <summary>
        /// overrides the server base
        /// </summary>
        public override HttpServerUtilityBase Server
        {
            get
            {
                return new HttpServerUtilityBaseHelper();
            }
        }


        /// <summary>
        /// Constructor 
        /// </summary>
        /// <param name="principal">the principal</param>
        /// <param name="addWebHeader">whether to add a web header</param>
        public HttpContextBaseHelper(IPrincipal principal = null, bool addWebHeader = true)
        {
            Principal = principal;
            RequestBaseHelper = new HttpRequestBaseHelper(addWebHeadline: addWebHeader);
        }
    }
```
Now instead of of mocking the entire HttpContextBaseClass you can just call the helper class

```csharp
HttpContextBaseHelper context = new HttpContextBaseHelper(principal);
```
# Dependency Inversion

High-level modules should not depend on low-level modules; both should depend on abstractions. Abstractions should not depend on details.  Details should depend upon abstractions.
You do not want your higher level class to depend on lower level classes. This makes your classes very dependent on each other and if you need to change something down the road you're gonna have to change every single class.

Example  - Problem

```csharp
    class Repository
    {

    }

    class Service
    {
        public Service()
        {
            Repository repo = new Repository();
        }

        void DoStuffWithRepository()
        {

        }
    }

    class Controller
    {
        public Controller()
        {
            Service service = new Service();
        }

        void DoStuffWithService()
        {

        }
    }
```
In this example, if you change things in the repository you might not only break your service but even the controller. This is because each class is dependent on each other.

Example - Solution

Create abstract classes or interfaces for each layer and let your classes depend on the abstracted classes instead of the concrete ones. Use DI to create instances of your specific concrete classes you need.

```csharp
    class Repository : IRepository
    {

    }

    class Service : IService
    {
        public IRepository repository;

        public Service(IRepository repository)
        {
            this.repository = repository;
        }

        void DoStuffWithRepository()
        {

        }
    }

    class Controller
    {
        public IService service;

        public Controller(IService service)
        {
            this.service = service;
        }

        void DoStuffWithService()
        {

        }
    }

    class DIContainer
    {
        void StartApplication()
        {
            var repository = new Repository();
            var service = new Service(repository);
            var controller = new Controller(service);
            StartProgram(controller);
        }
    }

    interface IRepository
    {

    }

    interface IService
    {

    }
```
