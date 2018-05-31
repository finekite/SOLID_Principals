# SOLID_Principals
Overview of what I learned about the SOLID Pricipals


# Single Responsibilty
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

There are many reasons class can change
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
Classes, methods, properties etc. should be open to extension but not midification. Code in a way that you can add new behviour without 
changing code. The way I understand it is with a house analogy. If you need to fix a bad pipe in your house you should not take apart your house just to fix the pipe. Also, if you follow SRP then you're most likely to follow the open/closed pricipal as well

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

Create class that determines the input type

```csharp
 public class InputTypeDeterminator
    {
        public static InputType DetermineInputType(char firstCharacter)
        {
            switch (firstCharacter)
            {
                case '<':
                    return InputType.XML;
                case '{':
                    return InputType.JSON;
               case ':':
                    return StringInputType.Colon;
               case ';':
                    return StringInputType.SemiColon;
               case '-':
                    return StringInputType.Dash;
               case '(':
                    return StringInputType.Parathesis;
               default:
                    return InputType.BADINPUT;
            }
        }
    }
```
Create different classes that will handle each text differntley 
```csharp

// This can and really should be done through dependecny injection
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


