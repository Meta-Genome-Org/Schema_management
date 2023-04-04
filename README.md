[toc]

## Understanding Schemas
### Basic Schema (.xsd) and xml overview

The MDCS back-end of meta-genome relies on xml for data storage and organisation. As such, each data submission results i a xml record that conforms to a given schema (template). In the same way as a file/directory architecture, XML records are heirarchical, allowing for nesting of content. This means elements can be contained within one another to further expand the associated data - this is often refered to as a parent-child relationship. 

xml records can be validated against a schema which define rules that each element must conform to. This forms a fundamental basis to ensure that each submission is consistent. Introducing and balancing validation rules is key. 

below is an example of a simple xsd schema the associated xml:


![simple_xsd_and_xml.jpg](:/acb5d9513f0f42798fbd73faed9460cb)

The principles observed here carry through to the meta-genome schema. Lets first breakdown what we can see in the sample schema:
 
 #### line 1 + 2: xml declaration and root element
 
 These lines describe the xml specification version and encoding (UTF-8). The root element (schema) is given in line 2. Here, xs describes the namespace of the XMLSchema. Within our schema, we define 'types', 'elements' and 'attributes' to capture our data. These are discussed in more detail later.
 
 #### line 4: element
 
 The 'xs:element' tag declares an element within the xml document. In context of meta-genome, this means the content of this element will appear in the submission form. Note that this element  closed on line 14 via the closing tag </xs:element>.

#### line 5: Complextype

This is an example of a xml schema type. In a complex type, we can define a structure, context and constraint for xml elements and attributes. In a simple type, we can only define 'atomic values' such as floats, ints and strings. 

Types can be named and reused. This is a critical feature. Note this is closed on line 13.

#### line 6: sequence

xs:sequence defines the order appearance within the complex type.

#### line 7-11: elements:

Here we are working with a simple schema containing only string type elements - so almost anything can be input. Declaring the type of data for a given element enables schema validation of a xml document. The name of these elements can be seen to be carried forward into the xml document.

#### line 11 +: closing tags

Where a tag starts with </, the object is closed. All elements must have a closing tag. Elements can be closed in line as such:
`<xsd:complexType name="attribute-base"/>`

It is a common error to miss a closing tag. So when uploading a template to the portal.meta-genome site, this is a good check.

## Meta-Genome Context

The xml schema validation procedure is ubiquitous in many aspects of software. For our purposes, we use it for an initial check for material data prior to database submission.

As mentioned, the current meta-genome schema (mecha-metagenome-schema31 version 1) is significantly more complex than the above example. Whilst building this schema, I have tried to maintain some level of order and coherency within its internal structure. However, due to the branching nature of xml and collaborative and iterative nature of its development, this has not been strictly adhered to. To help, I have left comments highlighting the larger sections containing types refering to specific sections e.g. `Creating type for testing conditions`.

Within this section, I will discuss the schema structure and some tips on its navigation. 

### Schema structure:

If new to this schema, I would first suggest exploring its structure via portal.meta-genome.org/curate. Here you can make a dummy submission and investigate how everything has been tied together. This will provide a better experience for whoever is developing this schema.

Upon loading the schema in the data curation tab and starting to edit a document, you will see this architecture:

|-map
|--- version-control
|---publication-details
|---stress-strain-convention
|---base-material-properties / metamaterial-material-properties / component-info
|---dev-section

Each section has different fields that inform on the title of the section. You will see that some fields appear by default and some have a (+) or (-) icon adjacent to them. Typically, those that appear by default are mandatory and can only appear once. Those with the (+) or (-) icons are optional. If the (+) icon remains after the first (+) click, then multiple entries of the same field type can be given. E.g. Publication Authors has this functionality. 

The 4th entry of the base elements in a "choice" type. This means the nature of the schema changes based on a choice as different elements and types can be called.

### Using complexTypes

As mentioned, the nesting of complex types is fundamental within this schema. Below is a relatively easy to understand complextype within the meta-genome schema:

![stress-strain-compleType.jpg](:/671160f023d94d9e8f485ede68daf831)

Here, the complexType is named "stress-strain-convention". Within it we have  sequence that contains two elements, strain-convention and stress-convention. These elements contain attributes - form, minOccurs, maxOccurs and name.
 - form = requirement for element to to be qualified with a namespace prefix.
 - minOccurs = minimum times this element is required
 - maxOccurs = maximum times this element is required
 - name = element name

Each element contains tags for annotation, app info and label. These fields are cosmetic in that they do not show on the final schema, but help to make data entry more user friendly. In short, label replaces the element name at data entry i.e. strain-convention appears as Strain convention.

Then we have a simpleType. SimpleTypes only allow the definition of simple data types e.g. string, int or float. Here we are generating a restriction element - thus producing a dropdown menu - restricting the input to a single choice.

In the same way that string, int or float types can be called, this "stress-strain-convention" type can now be called in an element outside of itself.

### Nesting complexTypes

Here I will demo how to nest complexTypes. I will use a simple example to highlight this.

Below is a complex type that enables the publication author name to be input:
![57ea44957e590895d5e122cfa2a81ba7.png](:/388e2430702f45b99054ef7a6d67d8a9)

Here we are naming the type "authors" and taking two string type elements: author-initials and author-surname. These elements are mandatory and can only appear once.

Now, we can call the authors type:
![13d813d75feed3b1265fdff1d072006e.png](:/324e731719f048878a66c9c6be3b8842)

Within the "publication-details" complexType, we call for 3 elements (here) - id, publication-authors and publication-title. We will focus on the authors element. Within this element we call the "authors" type show above, and we do so with maxOccurs="unbounded". This allows as many authors as necessary to be input. This results in the following structure in the submission page:

![16edd12dea9da424cf9b4acc2f64cd5f.png](:/bcbfc0a6e07543089842c8a960124cc1)

Here we can see the (+) icon allows us to add more authors as necessary.

## Macro-scale nesting and Structure

As mentioned, nesting is key. So far we have generated complexTypes that contain elements. This is the fundamental way to build up a schema. However, we have not yet declared a structure for our schema. At the very bottom of mecha-metagenome-schema31 (line 5154) we see the fundamental structure of mecha-metagenome-schema31. In the image below, we see a snippet of this code:

![0f320ad3bffa6e38fd51649fee29ee78.png](:/57f6f3df7cd7440889ce549f00e0e7d1)

As shown, this is  still a complexType ("mecha-genome-map"), which must be called as a type within an element to appear within the schema: as shown in the final few lines of the schema:

![d4c99bd72d5eaaa919e7c1a06ed6c38b.png](:/2b2809f60afd4cedb3cf476bc68156ef)

Here it is quite clear that each section is separated into distinct elements and types. Publication-info is the simplest element to inspect. 

### Text editor inspection of schema

When working with this schema, it is important to remember that all non-standard types are declared within the schema as complexTypes. This means, when a type is called e.g. 

`<xsd:element form="unqualified" minOccurs="1" maxOccurs="1" name="component-info" type="component-testing" >`

you can key-word search for the type by looking for its name. i.e.
`$ name="component-testing"`

This will take you to the section of schema which is defining the complexType you are interested in. 

This method of using a text editor to construct the schema is the method I have used throughout this project. Within portal.meta-genome.org, there is capacity to construct your schema via a UI. However, I have found the text editor method to be the best.

## Closing remarks

It is important to remember that this schema is highly branching, so focusing on specific regions rather than trying to understand the whole text document is going to be important. 

Generic types (e.g. file upload) may be used at multiple points within the schema, meaning you may encounter knock on effects when changing types. I would recommend mapping where the type appears within the schema prior to making big changes.

In this repo, I have included my older version of this schema. However, there is not utilised within them that is not apparent within mecha-metagenome-schema31.

I have also included some schemas I found very useful for learning how to construct and utilise xml.

Good luck.