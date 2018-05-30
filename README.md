# SOLID_Principals
Overview of what I learned about the SOLID Pricipals


# Single Responsibilty
Classes should only be doing one thing. Meaning there should only be one reason for the class to change.

Example
```csharp
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

