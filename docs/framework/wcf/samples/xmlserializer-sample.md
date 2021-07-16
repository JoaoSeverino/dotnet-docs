---
description: "Learn more about: XMLSerializer Sample"
title: "XMLSerializer Sample"
ms.date: "03/30/2017"
ms.assetid: 7d134453-9a35-4202-ba77-9ca3a65babc3
---
# XMLSerializer Sample

The [XmlSerializer sample](https://github.com/dotnet/samples/tree/main/framework/wcf) demonstrates how to serialize and deserialize types that are compatible with the <xref:System.Xml.Serialization.XmlSerializer>. The default Windows Communication Foundation (WCF) formatter is the <xref:System.Runtime.Serialization.DataContractSerializer> class. The <xref:System.Xml.Serialization.XmlSerializer> class can be used to serialize and deserialize types when the <xref:System.Runtime.Serialization.DataContractSerializer> class cannot be used. This is often the case when precise control over the XML is required - for example, if a piece of data must be an XML attribute and not an XML element. Also, the <xref:System.Xml.Serialization.XmlSerializer> often gets automatically selected when creating clients for non-WCF services.

In this sample, the client is a console application (.exe) and the service is hosted by Internet Information Services (IIS).

> [!NOTE]
> The setup procedure and build instructions for this sample are located at the end of this topic.

The <xref:System.ServiceModel.ServiceContractAttribute> and <xref:System.ServiceModel.XmlSerializerFormatAttribute> must be applied to the interface as shown in the following sample code.

```csharp
[ServiceContract(Namespace="http://Microsoft.ServiceModel.Samples"), XmlSerializerFormat]
public interface IXmlSerializerCalculator
{
    [OperationContract]
    ComplexNumber Add(ComplexNumber n1, ComplexNumber n2);
    [OperationContract]
    ComplexNumber Subtract(ComplexNumber n1, ComplexNumber n2);
    [OperationContract]
    ComplexNumber Multiply(ComplexNumber n1, ComplexNumber n2);
    [OperationContract]
    ComplexNumber Divide(ComplexNumber n1, ComplexNumber n2);
}
```

The public members of `ComplexNumber` class are serialized by <xref:System.Xml.Serialization.XmlSerializer> as XML attributes. The <xref:System.Runtime.Serialization.DataContractSerializer> cannot be used to create this kind of XML instance.

```csharp
public class ComplexNumber
{
    private double real;
    private double imaginary;

    [XmlAttribute]
    public double Real
    {
        get { return real; }
        set { real = value; }
    }

    [XmlAttribute]
    public double Imaginary
    {
        get { return imaginary; }
        set { imaginary = value; }
    }

    public ComplexNumber(double real, double imaginary)
    {
        this.Real = real;
        this.Imaginary = imaginary;
    }
    public ComplexNumber()
    {
        this.Real = 0;
        this.Imaginary = 0;
    }

}
```

The service implementation calculates and returns the appropriate result—accepting and returning values of the `ComplexNumber` type.

```csharp
public class XmlSerializerCalculatorService : IXmlSerializerCalculator
{
    public ComplexNumber Add(ComplexNumber n1, ComplexNumber n2)
    {
        return new ComplexNumber(n1.Real + n2.Real, n1.Imaginary +
                                                      n2.Imaginary);
    }
    …
}
```

The client implementation also uses complex numbers. Both the service contract and the data types are defined in the generatedClient.cs source file, which was generated by the [ServiceModel Metadata Utility Tool (Svcutil.exe)](../servicemodel-metadata-utility-tool-svcutil-exe.md) from service metadata. Svcutil.exe can detect when a contract is not serializable by the <xref:System.Runtime.Serialization.DataContractSerializer> and reverts to emitting `XmlSerializable` types in this case. If you want to force the use of the <xref:System.Xml.Serialization.XmlSerializer>, you can pass the /serializer:XmlSerializer (use XmlSerializer) command option to the Svcutil.exe tool.

```csharp
// Create a client.
XmlSerializerCalculatorClient client = new
                         XmlSerializerCalculatorClient();

// Call the Add service operation.
ComplexNumber value1 = new ComplexNumber();
value1.Real = 1;
value1.Imaginary = 2;
ComplexNumber value2 = new ComplexNumber();
value2.Real = 3;
value2.Imaginary = 4;
ComplexNumber result = client.Add(value1, value2);
Console.WriteLine("Add({0} + {1}i, {2} + {3}i) = {4} + {5}i",
    value1.Real, value1.Imaginary, value2.Real, value2.Imaginary,
    result.Real, result.Imaginary);
    …
}
```

When you run the sample, the operation requests and responses are displayed in the client console window. Press ENTER in the client window to shut down the client.

```console
Add(1 + 2i, 3 + 4i) = 4 + 6i
Subtract(1 + 2i, 3 + 4i) = -2 + -2i
Multiply(2 + 3i, 4 + 7i) = -13 + 26i
Divide(3 + 7i, 5 + -2i) = 0.0344827586206897 + 1.41379310344828i

Press <ENTER> to terminate client.
```

### To set up, build, and run the sample

1. Ensure that you have performed the [One-Time Setup Procedure for the Windows Communication Foundation Samples](one-time-setup-procedure-for-the-wcf-samples.md).

2. To build the C# or Visual Basic .NET edition of the solution, follow the instructions in [Building the Windows Communication Foundation Samples](building-the-samples.md).

3. To run the sample in a single- or cross-machine configuration, follow the instructions in [Running the Windows Communication Foundation Samples](running-the-samples.md).