Describes how to write a class used to parse the XML and convert it into python objects.

Recently updated to use the new data model classes which will be standard in version 2.0 and later of the library.

# Introduction #

This document begins with a short overview on how to extend the classes that parse XML without worrying about how the parsing works.

# Short Example (Pizza Party) #

Lets say that you have a service (lets call it the Pizza Party Feed) and you want to create modules which work on top of the gdata-python-client library. As with all of the Google Data API feeds, your feed begins with Atom and adds additional elements to represent information unique to your application (like the type of pizza that will be at your party, the number of people you can house, etc.). The atom module will handle parsing for the standard Atom elements, but it's up to you to provide a module to parse the elements specific to your feed (Pizza Party pizza, toppings, etc.).

## XML example ##

Your hypothetical feed has entries that look like this:
```
<entry xmlns='http://www.w3.org/2005/Atom' xmlns:p='http://example.com/pizza/1.0'>
  <id>http://www.example.com/pizzaparty/223</id>
  <title type='text'>Pizza at my house!</title>
  <author>
    <name>Joe</name>
    <email>joe@example.com</email>
  </author>
  <content type='text'>
    Join us for a fun filled evening of pizza and games!
  </content>
  <link rel='alternate' type='text/html'
        href='http://www.example.com/joe_user/pizza_at_my_house.html'/>
  <p:pizza toppings='pepperoni, sausage' size='large'>Pepperoni with cheese and 
sausage</p:pizza>
  <p:pizza toppings='mushrooms' size='medium'>Mushroom</p:pizza>
  <p:pizza toppings='ham, pineapple' size='extra large'>Hawaiian</p:pizza>
  <p:capacity>25</p:capacity>
  <p:location>My place.<p:address>123 Imaginary Ln, Sometown MO 63000</p:address></p:location>
</entry>
```

## XML classes ##

To define classes that parse this XML, we need to begin by defining the classes for the inner elements first. Lets start with the `capacity` element since it is simple.
```
import atom.core

PIZZA_TEMPLATE = '{http://example.com/pizza/1.0}%s'

class Capacity(atom.core.XmlElement):
  _qname = PIZZA_TEMPLATE % 'capacity'
```
That's all we need for capacity. Since this element only has a text node, we don't need to define anything more that it's local tag name and namespace. We do not need to define an init constructor or any other methods because the processing for the XML and the text content is inherited from XmlElement.

Next we define a class for `pizza` notice that this element has some XML attributes.
```
class Pizza(atom.core.XmlElement):
  _qname = PIZZA_TEMPLATE % 'pizza'
  toppings = 'toppings'
  size = 'size'
```
The above class specified the XML attributes (using `member = 'XML attribute qname'` and the class members which store their values. There is no need to define a custom constructor for the class because XmlElement will map all named arguments to members. For example you can do `p = Pizza(size='Large', toppings='cheese, pepperoni')`.

Next we'll define classes for `location` and `address` which are nested elements.
```
class Address(atom.core.XmlElement):
  _qname = PIZZA_TEMPLATE % 'address'

class Location(atom.core.XmlElement):
  _qname = PIZZA_TEMPLATE % 'location'
  address = Address
```
The new concept introduced in the `Location` class is the mapping of a child element to the class used to parse it's XML and the member the child element is stored in. The classes are mapped by setting the member to the XML element's class (member = XmlClass). Instantiating a location element with an address could look like this:
`l = Location(address=Address(text='my house'), text='Meet here!')`

We have now defined everything but the Entry which contains the data.
```
import gdata.data
import atom.data

class PizzaEntry(gdata.data.GDEntry):
  _qname = atom.data.ATOM_TEMPLATE % 'entry'
  pizza = [Pizza]
  capacity = Capacity
  location = Location
```
Note in the above that the mapping for the `pizza` XML element specifies `[Pizza]` to represent a list of `Pizza` class instances. The entry can contain multiple `pizza` elements, and this is represented by enclosing a list of Classes instead of just the Class.

You should also define a feed to contain the new entry you defined. This might look something like this:
```
class PizzaFeed(gdata.data.GDFeed):
  _qname = atom.data.ATOM_TEMPLATE % 'feed'
  entry = [PizzaEntry]
```

## Example Usage ##

You can now use the Google Data client object to communicate with a Pizza Party server.
```
import gdata.client

pizza_client = gdata.client.GDClient()
# Authorize this app to act on the user's behalf.
# In this example we'll use ClientLogin with the username and password.
pizza_client.client_login(username, password, 'pizza', 'Jeff\'s Pizza Client!')

# Find pizza parties.
feed = pizza_client.get_feed('http://example.com/PizzaParties', 
                             desired_class=PizzaFeed)
for party in feed.entry:
  print party.location.address.text
  ...

# Create a new pizza party.
new_party = PizzaEntry(location=Location(text='over there'))
new_party.pizza.append(Pizza(size='L', toppings='mushrooms'))
new_party.pizza.append(Pizza(size='M', toppings='sausage'))
created = pizza_client.post(new_party, 'http://example.com/PizzaParties')

# Modify and update the party.
# We needed more pizza!
created.pizza.append(Pizza(size='XL', toppings='pepperoni'))
updated = pizza_client.update(created)

# Then we decided to cancel, so we delete the entry.
pizza_client.delete(updated)
```

# Next Steps #

Now that you've written your data model classes, why not write some unit tests? To help you get started, some examples are provided on the CodeTemplates page.

# Code snippets for v1 classes #

This wiki was updated in anticipation of the v 2.0 release of the library, the old code samples for the version 1.x are below and correspond to the above classes.

```
import atom

PIZZA_NAMESPACE = 'http://example.com/pizza/1.0'

class Capacity(atom.AtomBase):
  _tag = 'capacity'
  _namespace = PIZZA_NAMESPACE


class Pizza(atom.AtomBase):
  _tag = 'pizza'
  _namespace = PIZZA_NAMESPACE
  _attributes = atom.AtomBase._attributes.copy()
  _attributes['toppings'] = 'toppings'
  _attributes['size'] = 'size'
  
  def __init__(self, toppings=None, size=None, text=None, 
      extension_elements=None, extension_attributes=None):
    self.toppings = toppings
    self.size = size
    # Call the constructor function for AtomBase to initialize
    # inherited members.
    atom.AtomBase.__init__(self, extension_elements=extension_elements,
        extension_attributes=extension_attributes, text=text)
```
The above class specified the XML attributes (using `_attributes['XML_Attribute_Name']` and the class members which store their values (the attributes dictionary gets the value `'member_name'`). The constructor for the class needs to accept values for the attribute members as parameters (self.member\_name).

```
class Address(atom.AtomBase):
  _tag = 'address'
  _namespace = PIZZA_NAMESPACE

class Location(atom.AtomBase):
  _tag = 'location'
  _namespace = PIZZA_NAMESPACE
  _children = atom.AtomBase._children.copy()
  _children['{%s}address' % PIZZA_NAMESPACE] = ('address', Address)
  
  def __init__(self, address=None, text=None, extension_elements=None,
      extension_attributes=None):
    self.address = address
    atom.AtomBase.__init__(self, extension_elements=extension_elements,
        extension_attributes=extension_attributes, text=text)
```

Here is the code for the entry class for our Pizza feed.

```
import gdata

class PizzaEntry(gdata.GDataEntry):
  _tag = 'entry'
  _namespace = atom.ATOM_NAMESPACE
  _children = gdata.GDataEntry._children.copy()
  _children['{%s}pizza' % PIZZA_NAMESPACE] = ('pizza', [Pizza])
  _children['{%s}capacity' % PIZZA_NAMESPACE] = ('capacity', Capacity)
  _children['{%s}location' % PIZZA_NAMESPACE] = ('location', Location)
  
  def __init__(self, pizza=None, capacity=None, location=None, author=None,
      category=None, content=None, contributor=None, atom_id=None, link=None,
      published=None, rights=None, source=None, summary=None, title=None, 
      control=None, updated=None, text=None, extension_elements=None, 
      extension_attributes=None):
    self.pizza = pizza or []
    self.capacity = capacity
    self.location = location
    gdata.GDataEntry.__init__(self, author=author, category=category, 
        content=content, contributor=contributor, atom_id=atom_id, 
        link=link, published=published, rights=rights, source=source,
        summary=summary, control=control, title=title, updated=updated,
        extension_elements=extension_elements, 
        extension_attributes=extension_attributes, text=text)
```
Functions to convert an XML string to the desired object.
```
def CapacityFromString(xml_string):
  return atom.CreateClassFromXMLString(Capacity, xml_string)


def PizzaFromString(xml_string):
  return atom.CreateClassFromXMLString(Pizza, xml_string)


def AddressFromString(xml_string):
  return atom.CreateClassFromXMLString(Address, xml_string)


def LocationFromString(xml_string):
  return atom.CreateClassFromXMLString(Location, xml_string)


def PizzaEntryFromString(xml_string):
  return atom.CreateClassFromXMLString(PizzaEntry, xml_string)
```
You should also define a feed to contain the new entry you defined. This might look something like this:
```
class PizzaFeed(gdata.GDataFeed):
  _tag = 'feed'
  _namespace = atom.ATOM_NAMESPACE
  _children = gdata.GDataFeed._children.copy()
  _children['{%s}entry' % atom.ATOM_NAMESPACE] = ('entry', [PizzaEntry])


def PizzaFeedFromString(xml_string):
  return atom.CreateClassFromXMLString(PizzaFeed, xml_string)
```