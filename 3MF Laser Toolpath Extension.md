#
# 3MF Toolpath Extension

## Specification & Reference Guide











| **Version** | 0.8 |
| --- | --- |
| **Status** | Draft |

## Table of Contents

[Preface](#preface)

[About this Specification](#11-about-this-specification)

[Document Conventions](#12-document-conventions)

[Language Notes](#13-language-notes)

[Software Conformance](#14-software-conformance)

[Part I: 3MF Documents](#part-i-3mf-documents)

[Chapter 1. Overview of Additions](#chapter-1-overview-of-additions)

[Chapter 2. Object](#chapter-2-object)

[Chapter 3. Object](#chapter-3-toolpathlayer)

[Part II. Appendixes](#part-ii-appendixes)

[Appendix A. Glossary](#appendix-a-glossary)

[Appendix B. 3MF XSD Schema](#appendix-b-3mf-xsd-schema)

[Appendix C. Standard Namespace](#appendix-c-standard-namespace)

[Appendix D: Example file](#appendix-d-example-file)

[References](#references)



# Preface

## 1.1. About this Specification

This 3MF toolpath specification is an extension to the core 3MF specification. This document cannot stand alone and only applies as an addendum to the core 3MF specification. Usage of this and any other 3MF extensions follow an a la carte model, defined in the core 3MF specification.

Part I, "3MF Documents," presents the details of the primarily XML-based 3MF Document format. This section describes the XML markup that defines the composition of 3D documents and the appearance of each model within the document.

Part II, "Appendixes," contains additional technical details and schemas too extensive to include in the main body of the text as well as convenient reference information.

The information contained in this specification is subject to change. Every effort has been made to ensure its accuracy at the time of publication.

This extension MUST be used only with Core specification 1.x. and Production extension specification 1.x.

## 1.2. Document Conventions

Except where otherwise noted, syntax descriptions are expressed in the ABNF format as defined in RFC 4234.

Glossary terms are formatted like _this_.

Syntax descriptions and code are formatted in `monospace` type.

Replaceable items, that is, an item intended to be replaced by a value, are formatted in _`monospace cursive`_ type.

Notes are formatted as follows:

>**Note:** This is a note.

## 1.3. Language Notes

In this specification, the words that are used to define the significance of each requirement are written in uppercase. These words are used in accordance with their definitions in RFC 2119, and their respective meanings are reproduced below:

- _MUST._ This word, or the adjective "REQUIRED" means that the item is an absolute requirement of the specification.
- _SHOULD._ This word, or the adjective "RECOMMENDED" means that there may exist valid reasons in particular circumstances to ignore this item, but the full implications should be understood and the case carefully weighed before choosing a different course.
- _MAY._ This word, or the adjective "OPTIONAL" means that this item is truly optional. For example, one implementation may choose to include the item because a particular marketplace or scenario requires it or because it enhances the product. Another implementation may omit the same item.

## 1.4. Software Conformance

Most requirements are expressed as format or package requirements rather than implementation requirements.

In order for consumers to be considered conformant, they must observe the following rules:

- They MUST NOT report errors when processing conforming instances of the document format except when forced to do so by resource exhaustion.
- They SHOULD report errors when processing non-conforming instances of the document format when doing so does not pose an undue processing or performance burden.

In order for producers to be considered conformant, they must observe the following rules:

- They MUST NOT generate any new, non-conforming instances of the document format.
- They MUST NOT introduce any non-conformance when modifying an instance of the document format.

Editing applications are subject to all of the above rules.



# Part I: 3MF Documents

# Chapter 1. Overview of Additions

![Toolpath image](images/overview_2.png)

This specification defines the Toolpath Extension for the 3MF file format. It introduces a standardized way to describe toolpath-based fabrication processes within 3MF documents, extending the existing slice-based representation to include precise motion and exposure instructions.

The Toolpath Extension enables detailed modeling of additive manufacturing processes across laser based technologies, with provisions for future use in deposition and hybrid systems. It supports 2.5-axis (planar), 3-axis, and 6-axis toolpaths, as well as multi-head synchronization and machine-specific parameters.

The extension is designed with flexibility and scalability in mind, allowing for:

- High-resolution geometric and exposure data per layer (e.g. laser power).

- Random layer access with minimal loading and memory overhead.

- Support for deterministic binary encoding.

- Extensibility through profiles, metadata, and proprietary namespaces

- Efficient compression and streaming of large datasets.

This specification MUST be supported by consumers that advertise support for the Toolpath Extension. While all toolpath elements are optional for producers, any producer using this extension MUST declare it as required in the 3MF package, as specified in the core 3MF standard. 

Consumers MAY choose to recalculate or reinterpret the toolpath data, but they MUST retain compatibility with the declared structure and semantics of the extension. Further customization is possible via private extensions or print ticket annotations.

The goal of this extension is to offer a structured, vendor-neutral, and scalable foundation for toolpath data that can bridge the gap between 3D geometry and machine execution, supporting the evolving ecosystem of industrial and open-source additive manufacturing systems.

A producer producing a 3MF document with toolpath extension MUST also include the production extension as required. A consumer of the toolpath extension MUST  also understand the production extension in the document. This ensures the availability of proper referencable UUIDs throughout the document.


##### Figure 2-1: Overview of model XML structure of 3MF with toolpath additions.

#####
![Overview of model XML structure of 3MF with toolpath additions](images/toolpath/slice.png)

##### Figure 2-2: Overview of model XML structure of the toolpathlayer document.

#####
![Overview of model XML structure of the toolpathlayer document](images/toolpath/toolpathlayer.png)

# Chapter 2. Planar toolpathes and 3-6 axis deposition toolpathes.

Additive manufacturing systems employ a wide range of motion and exposure technologies, from traditional planar layer-by-layer approaches to advanced multi-axis deposition strategies. The 3MF Toolpath Extension is designed to represent both categories in a unified and extensible framework.

## 2.1 Planar Toolpaths

Planar toolpaths describe geometry and process instructions that are confined to discrete, parallel layers along the Z-axis. This approach is commonly used in technologies such as:

- Stereolithography (SLA)

- Selective Laser Melting (SLM)

- Selective Laser Sintering (SLS)

Each layer consists of one or more 2D geometric primitives such as loops, polylines, hatches, arcs, and stripes. These are defined in the XY-plane and associated with a Z-height. This representation provides high resolution in XY and discrete control in Z, aligning closely with the native processing model of many industrial AM systems.

Planar toolpaths are especially well suited for:

- Layer-based exposure processes (e.g., galvanometer-driven laser systems)

- Parallelization of path execution (e.g., multi-laser platforms)

- Efficient compression and random access per layer

## 2.2 Multi Axis Deposition Toolpaths (3-6 axes)

Beyond planar systems, many additive processes now involve continuous motion in 3D space or require dynamic orientation of the tool or part. The Toolpath Extension supports:

- 3-axis toolpaths: Represented as spatial 3D curves or polylines. Used for freeform material deposition or point-wise energy delivery on non-planar surfaces.

- 6-axis toolpaths: Extend 3-axis motion with an orientation vector or normal field per point. This is required in robotic or multi-DOF systems where tool orientation is dynamically controlled (e.g., Directed Energy Deposition with a robot arm).

Each axis mode introduces increasing geometric and processing complexity but allows for:

- Non-planar slicing

- Conformal toolpaths

- Deposition on curved or dynamically repositioned surfaces

- Support for rotating or tilting recoaters and tool heads

3MF supports both representations under the same <toolpathlayers> construct, allowing a consistent approach to synchronization, metadata assignment, and profile referencing.

## 2.3 Interoperability and Use

The structure allows producers to define hybrid toolpaths combining planar and spatial segments. Consumers are expected to interpret the dimensionality and semantics of each toolpath element from its type and associated metadata. Implementers should ensure deterministic parsing and robust support for layer-specific attributes and profile overrides.

## 2.4 Machine specific profile codecs

While the Toolpath Extension defines a set of standardized profile parameters suitable for general use, Additive Manufacturing machines often require specialized, hardware-dependent instructions to fully utilize their capabilities. To accommodate this, the extension supports machine-specific profile codecs through the use of custom XML namespaces and schema-defined profile structures.

#### Namespaced Custom Profiles

Producers MAY define additional profile attributes or structures using one or more unique XML namespaces. These extensions allow encoding of vendor-specific or machine-specific instructions, such as advanced scan strategies, dynamic focus control, synchronized motion patterns, or material-specific tuning.

Such machine-specific profiles:

- MUST be namespaced to avoid collisions with standard 3MF elements.

- MAY include mandatory parameters that are required by the target machine or control stack.

- MAY be accompanied by a schema definition external to the 3MF core, enabling validation by compatible tools.

#### Integration and Enforcement

A producer targeting a specific machine configuration:

- MUST declare the required namespace(s) in the profile section.

- SHOULD mark the associated extension as required within the 3MF package if it is essential for correct fabrication.

- MAY combine standardized and custom parameters within the same profile, as long as the semantics are unambiguous.

#### Consumer Behavior

Consumers that do not recognize a machine-specific namespace:

 - MUST ignore unrecognized parameters unless the extension is declared as required, in which case the consumer MUST reject the job.

- SHOULD provide a warning or fallback mechanism if partial interpretation is possible.

This mechanism ensures that 3MF remains extensible and practical for a wide range of hardware platforms while preserving compatibility and predictability across implementations.

# Chapter 3. OPC Packet Structure

The 3MF Toolpath Extension is structured using the Open Packaging Convention (OPC), allowing efficient organization, referencing, and streaming of toolpath data. The OPC architecture enables the separation of layer data, profiles, and binary geometry into modular, independently accessible parts.

## 3.1 Layer-Scoped XML Parts

Each individual build layer is represented by a dedicated XML part, which contains the geometric and metadata information required to fabricate that specific layer. These per-layer XML documents allow for:

- Random access to individual layers

- Scalable handling of large datasets

- Efficient parallelization and streaming

The layer files are typically named and located under a defined folder structure, e.g.:

    /Toolpath/Layers/layer00001.xml  
    /Toolpath/Layers/layer00002.xml  
    /Toolpath/Layers/layer00003.xml  
    ...

>**Note:**
Each layer file contains geometry segments (e.g., loops, polylines, hatches) as well as metadata, references to toolpath profiles and build items. See Chapter 5 for the exact definitions.


## 3.2 Relationships and Binding

Each layer XML is referenced via an explicit OPC relationship from the main toolpath resource part. These relationships define the role, content type, and URI of each layer file. The root model (3dmodel.model) declares a relationship to the Toolpath Resource part, which in turn declares relationships to each layer.

This structure ensures:
- Deterministic resolution of resource dependencies

- Clear scoping of each layer’s content

- Compatibility with standard OPC parsers and tooling

#### Example Relationship Flow

    [3dmodel.model]
       └── rels/toolpathresource.model (Toolpath Resource)
               └── rels/layer00001.xml (Layer 1)
               └── rels/layer00002.xml (Layer 2)

#### Best Practices

Producers SHOULD avoid embedding all layer data directly in the toolpath resource to maximize scalability and reusability.

Each layer part SHOULD use consistent naming and indexing.

Consumers MUST follow OPC relationship resolution rules to locate and load layer parts.

This design supports efficient inspection, editing, and partial build strategies, and it allows hardware vendors to implement layer-specific optimizations without requiring full-model parsing.


# Chapter 4. Toolpath Resource XML Structure

Element **\<tp:toolpathresource>**

![Toolpath Resource XML structure](images/toolpathresource.png)

| Name   | Type   | Use   | Default   | Annotation |
| --- | --- | --- | --- | --- |
| id | **ST\_ResourceID** | required |  | Must be a unique resource ID in the model document. |
| uuid | **ST\_UUID** | required |  | Global unique identifier . |
| unitfactor | **ST\_UUID** | required |  | Unit scaling factor to be applied to toolpath coordinates. In millimeters. |
| toolpathtype | **ST_ToolpathType** | optional | planar | Type of toolpath described. Possible values are planar, 3axis, 6axis. |

## Chapter 4.1 Toolpath profiles

![Toolpath Profiles XML Structure](images/toolpathprofiles.png)

Element **\<tp:toolpathprofiles>**

The \<toolpathprofiles\> element contains list of toolpath profiles that allow a machine process to convert the geometric toolpath definition into low level machine instructions.

Element **\<tp:toolpathprofile>**

| Name   | Type   | Use   | Default   | Annotation |
| --- | --- | --- | --- | --- |
| uuid | **ST\_UUID** | required |   | Unique identifier for this profile |
| name   | **ST\_String** | required |   | Name for this profile. Must be unique in the toolpath. |
| laserpower | **ST\_PositiveNumber** | optional | | Laser processes: specifies the power of the laser used, measured in W. |
| laserspeed   | **ST\_PositiveNumber** | optional   |   | Laser processes: speed at which the projected laser spot moves while marking, measured in mm/s |
| jumpspeed   | **ST\_PositiveNumber** | optional   |   | Laser processes: speed at which the projected laser spot moves while not marking, measured in mm/s |
| laserfocus   | **ST\_PositiveNumber** | optional   |   | Laser processes: Offset for the focal plane of the laser in mm. Positive means above the powder bed. |
| laserindex | **ST\_Integer** | optional  |   | Laser processes: ID of the laser to be used. |
| depositionspeed   | **ST\_PositiveNumber** | optional   |   | Deposition processes: speed at which the deposition head while extruding, measured in mm/s |

The \<toolpathprofile\>-elements are used to specify the properties of a laser melting or deposition process. This specification declares the above list of generic values to use. A producer MUST ensure that all necessary profile values are properly declared for a specific application, or as enforced by a profile codec.

In case of a custom profile value, the name MUST be namespaced with a properly defined XML namespace.


## Chapter 4.2 Toolpath profile modifiers

TODO

## Chapter 4.3 Toolpath layers


![Toolpath Layers XML Structure](images/toolpathlayers.png)

Element **\<tp:toolpathlayers>**

The \<toolpathlayers\> element contains a list of references to OPC packages that contain the specific layers.

| Name   | Type   | Use   | Default   | Annotation |
| --- | --- | --- | --- | --- |
| zbottom | **ST\_NonNegativeInteger** | required, if toolpathtype is "planar" |  0 | Bottom Z Value of the first layer. Not applicable for non-planar toolpaths. |


Element **\<tp:toolpathlayer>**

| Name   | Type   | Use   | Default   | Annotation |
| --- | --- | --- | --- | --- |
| ztop | **ST\_PositiveInteger** | required, if toolpathtype is "planar" |   | Not applicable for non-planar toolpaths. MUST be larger than zbottom, as well as the ztop of the previous layer. |


## Chapter 2.4 Custom Toolpath Metadata




# Part II. Layer Data

# Part III. Appendixes

## Appendix A. Glossary

**3D model.** The markup that defines a model for output.

**3D Model part.** The OPC part that contains a 3D model.

**3D Texture part.** A file used to apply complex information to a 3D object in the 3D Model part. In this extension spec, it is specifically a TBMP file.

**3MF.** The 3D Manufacturing Format described by this specification, defining one or more 3D objects intended for output to a physical form.

**3MF Document.** The digital manifestation of an OPC package that contains a 3D payload that conforms with the 3MF specification.

**Composite material.** A material that is comprised of a ratio of other materials.

**Consumer.** A software, service, or device that reads in a 3MF Document.

**Editor.** A software, service, or device that both reads in and writes out 3MF Documents, possibly changing the content in between.

**Material.** The description of a physical substance that can be used to output an object.

**Material resource.** A potential resource that might be referenced by an object to describe what the object will be made of.

**Producer.** A software, service, or device that writes out a 3MF Document.

**Resource.** A texture, color, material, action, or object that could be used by another resource or might be necessary to build a physical 3D object according to build instructions.

**Texture resource.** A resource that describes a subset of the 3D data to be used and how it is to be tiled.

**XML namespace.** A namespace declared on the \<model> element, in accordance with the XML Namespaces specification.

## Appendix B. 3MF XSD Schema

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<xs:schema xmlns="http://schemas.microsoft.com/3dmanufacturing/lasertoolpath/2018/05" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xml="http://www.w3.org/XML/1998/namespace" 
targetNamespace="http://schemas.microsoft.com/3dmanufacturing/lasertoolpath/2018/05" elementFormDefault="unqualified" attributeFormDefault="unqualified" blockDefault="#all"> 
	<xs:import namespace="http://www.w3.org/XML/1998/namespace" schemaLocation="http://www.w3.org/2001/xml.xsd"/>
  <xs:annotation> 
		<xs:documentation><![CDATA[   Schema notes: 
 
  Items within this schema follow a simple naming convention of appending a prefix indicating the type of element for references: 
 
  Unprefixed: Element names 
  CT_: Complex types 
  ST_: Simple types 
   
  ]]></xs:documentation> 
	</xs:annotation> 
	<!-- Complex Types --> 
	<xs:complexType name="CT_Slice"> 
		<xs:attribute name="toolpath" type="ST_UriReference" use="optional"/>
		<xs:anyAttribute namespace="##other" processContents="lax"/>
	</xs:complexType>
	<!-- Simple Types -->
	<xs:simpleType name="ST_UriReference"> 
		<xs:restriction base="xs:anyURI"> 
			<xs:pattern value="/.*"/> 
		</xs:restriction> 
	</xs:simpleType>
	<!-- Elements --> 
</xs:schema>
```


# Appendix C. Standard Namespace

| | |
| --- | --- |
|LaserToolpath    |  [http://schemas.microsoft.com/3dmanufacturing/lasertoolpath/2018/05](http://schemas.microsoft.com/3dmanufacturing/lasertoolpath/2018/05 ) |
|LaserToolpathLayer | [http://schemas.microsoft.com/3dmanufacturing/lasertoolpathlayer/2018/05](http://schemas.microsoft.com/3dmanufacturing/lasertoolpathlayer/2018/05 ) |


# Appendix D: Example file

## 3D model
```xml
<?xml version="1.0" encoding="utf-8"?>
<model xmlns="http://schemas.microsoft.com/3dmanufacturing/core/2015/02" unit="millimeter" xmlns:b="http://schemas.microsoft.com/3dmanufacturing/beamlattice/2017/02" requiredextensions="b">
    <resources>
        <s:slicestack>
            <slice ztop="10" l:toolpath="/toolpath/layer1.xml">
                <s:vertices>
                    <s:vertex x="30" y="30"/>
                    <s:vertex x="70" y="30"/>
                    <s:vertex x="70" y="70"/>
                    <s:vertex x="30" y="70"/>
                </s:vertices>
                <s:polygon startv="0">
                    <s:segment v2="1"/>
                    <s:segment v2="2"/>
                    <s:segment v2="3"/>
                    <s:segment v2="0"/>
                </s:polygon>
            <s:slice/>
        <s:/slicestack>
        <object id="1" name="Box" partnumber="e1ef01d4-cbd4-4a62-86b6-9634e2ca198b" type="model">
            <mesh>
                <vertices>
                    <vertex x="45.00000" y="55.00000" z="55.00000"/>
                    <vertex x="45.00000" y="45.00000" z="55.00000"/>
                    <vertex x="45.00000" y="55.00000" z="45.00000"/>
                    <vertex x="45.00000" y="45.00000" z="45.00000"/>
                    <vertex x="55.00000" y="55.00000" z="45.00000"/>
                    <vertex x="55.00000" y="55.00000" z="55.00000"/>
                    <vertex x="55.00000" y="45.00000" z="55.00000"/>
                    <vertex x="55.00000" y="45.00000" z="45.00000"/>
                </vertices>
                <triangles>
                    <vertex x="45.00000" y="55.00000" z="55.00000"/>
                </triangles>
            </mesh>
        </object>
    </resources>
    <build>
        <item objectid="1"/>
    </build>
</model>
```

## Toolpathlayer
```xml
<?xml version="1.0" encoding="utf-8"?> ...
```

# References

**BNF of Generic URI Syntax**

"BNF of Generic URI Syntax." World Wide Web Consortium. http://www.w3.org/Addressing/URL/5\_URI\_BNF.html

**Open Packaging Conventions**

Ecma International. "Office Open XML Part 2: Open Packaging Conventions." 2006. http://www.ecma-international.org

**sRGB**

Anderson, Matthew, Srinivasan Chandrasekar, Ricardo Motta, and Michael Stokes. "A Standard Default Color Space for the Internet-sRGB, Version 1.10." World Wide Web Consortium. 1996. http://www.w3.org/Graphics/Color/sRGB

**Unicode**

The Unicode Consortium. The Unicode Standard, Version 4.0.0, defined by: _The Unicode Standard, Version 4.0_. Boston, MA: Addison-Wesley, 2003.

**XML**

Bray, Tim, Eve Maler, Jean Paoli, C. M. Sperlberg-McQueen, and François Yergeau (editors). "Extensible Markup Language (XML) 1.0 (Fourth Edition)." World Wide Web Consortium. 2006. http://www.w3.org/TR/2006/REC-xml-20060816/

XML C14N

Boyer, John. "Canonical XML Version 1.0." World Wide Web Consortium. 2001. http://www.w3.org/TR/xml-c14n.

XML Namespaces

Bray, Tim, Dave Hollander, Andrew Layman, and Richard Tobin (editors). "Namespaces in XML 1.0 (Second Edition)." World Wide Web Consortium. 2006. http://www.w3.org/TR/2006/REC-xml-names-20060816/

XML Schema

Beech, David, Murray Maloney, Noah Mendelsohn, and Henry S. Thompson (editors). "XML Schema Part 1: Structures," Second Edition. World Wide Web Consortium. 2004. http://www.w3.org/TR/2004/REC-xmlschema-1-20041028/

Biron, Paul V. and Ashok Malhotra (editors). "XML Schema Part 2: Datatypes," Second Edition. World Wide Web Consortium. 2004. http://www.w3.org/TR/2004/REC-xmlschema-2-20041028/
