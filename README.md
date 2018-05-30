# SOLID_Principals
Overview of what I learned about the SOLID Pricipals


# Single Responsibilty
Classes should only be doing one thing. Meaning there should only be one reason for the class to change.

Example - Problem

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

Many reasons class can change
1) Incoming text will change
2) More fields will be added to the text
3) Business wants the output format to change

Example - Solution

```csharp
    public class TextReader : IReader
    {
        public void ReadText()
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
           var text  = textReader.ReadText();
           outputWriter.WriteText();
        }
    }
    
```
# Open Closed
Classes, methods, properties etc. should be open to extension but not midification. Code in a way that you can add new behviour without 
changing code

Example - Problem

```csharp
        public void ParseText(string line)
        {
            dynamic lineArray;

            if (line.StartsWith("("))
            {
                // Do parethesis stuff
            }
            else if (line.StartsWith("<"))
            {
                // do xml or html stuff
            }
            else if (line.StartsWith("{"))
            {
                // do json stuff
            }
        }
```


