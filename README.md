# SOLID_Principals
Overview of what I learned about the SOLID Principles

# Single Responsibility
Classes should only be doing one thing. Meaning there should only be one reason for the class to change.

Example - Problem Here is some text that has person information. The code below reads the text, parses it and maps the information to a person object. Once the information is mapped is outputs it in a specific format.
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

Example - Problemt
Many unit tests will mock the HttpContext class. If you look at the source code for the HttpContext class you will see it is quite large.



