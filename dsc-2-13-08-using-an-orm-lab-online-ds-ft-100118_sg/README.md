
# Using an ORM - Lab

## Introduction

In this lab, we'll make use of SQLAlchemy to execute CRUD operations on a SQL database!

## Objectives

You will be able to:

* Identify the steps needed to use SQLAlchemy with a database
    * Build the database using declarative_base class - Britta
    * Define features such as id, name, etc, create a primary key and any other relationships - Brian/Britta
    * Connect to the database with the engine - Britta
    * Build it - Rafael
    * Add in rows by using session_maker - Rafael
    * add_all(), .new (gives us status update), .commit() commits the changes
    
* Understand and explain the concept of an Object Relational Mapper
    * define overlapping classes (relational)
    * we like ORMs so we can use OOP instead of SQL queries
    * security purposes
    
* Execute CRUD operations on a database using SQLAlchemy
    * CRUD (Create, Read, Update and Delete) - Emily

## Getting Started

In this lesson, we'll make use of our newfound SQLAlchemy knowledge to create a database, populate it with data, and write queries to retrieve objects containing the information we want. 

We'll start by setting up our database. For this lesson, we're going to create then database described in the following ERD:

<img src='erd.png'>


**_Question:_**

What sort of relationship do customers have with shopping carts? What sort of relationship do shopping carts have with items?

Write your answer below this line:
________________________________________________________________________________________________________________________________

Customers have a 1-to-1 relationship with shopping carts, while shopping carts have 1-to-many relationship with items.


### Defining Our Mappings

We'll begin by importing everything we need to create our database and structure our mappings so that they look like the tables in the ERD.  

In the cell below:

* Import everything from sqlalchemy
* Import `declarative_base` 
* Create a `Base` object


```python
from sqlalchemy import *
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from sqlalchemy.orm import Session, sessionmaker
```


```python
Base = declarative_base()
```

Good! Now, since we'll need to define relationships between our tables, we'll need to import one more thing. In the cell below, import `relationship` from sqlalchemy's `orm` module. 

**_Note_**: Make sure you import `relationship`, not the plural `relationships`!


```python
# imported above
```

### Creating Our Class Mappings

Now that we've created a `Base` object, we can define our classes! 

In order to set up our classes, we'll need to define:

* The `__tablename__` for each class
* The attributes of each class, which will be `Column` objects
* The `relationship` that each class has to other classes

Although we haven't explicitly covered how to create relationships, it's not hard--just a single line of code.  This is a great opportunity to get some practice finding what you need from documentation, and the SQLAlchemy documentation is really informative and easy to understand. 

We'll be creating a 1-to-1 relationship (Customer <--> ShoppingCart), and a 1-to-many relationship (ShoppingCart <--> Item). Take a look at the documenation for creating relationships and see if you can figure out how to set this up!

In the cell below: 

* Complete the `Customer`, `ShoppingCart`, and `Item` classes. 
* Give each class the correct table name ('customer', 'shoppingCart', and 'Item')
* Define the correct columns for each class, with the appropriate data types, and set the appropriate primary key and foreign keys.
* Set the appropriate relationships between classes.

**_Hint:_** When setting the relationships, pay attention to the capitalization in the documenation--in some parts, you reference the name of the class, while in others, you reference the name of the table!

**_Note:_** Running a cell more than one time will cause a "Table is already defined" error.  To fix this, just restart the kernel and run everything again. 


```python
class Customer(Base):
    """
    This class
    id, name, cart_id -> ShoppingCart.id
    """
    __tablename__ = 'customers' # plural for schema
    __table_args__ = {"extend_existing": True}
    
    id = Column(Integer, primary_key=True)
    name = Column(String)
    cart_id = Column(Integer, ForeignKey('shoppingCarts.id')) # creates the relational relationship
    
    # Create 1-to-1 relationship with ShoppingCart, as shown in the SQLAlchemy documentation
    
    # relationship between customer and shopping cart
    # Call attribute
    # Customer.shoppingCart() -> Looks at ShoppingCart table
    # uselist = True -> One to Many
    # uselist = False -> One to One
    # defaults to primary key of relationship table
    shoppingCarts = relationship('ShoppingCart', uselist=False, back_populates='customers')
```


```python
class ShoppingCart(Base):
    __tablename__ = "shoppingCarts"
    __table_args__ = {"extend_existing": True}

    
    id = Column(Integer, primary_key=True)
    item_id = Column(Integer, ForeignKey('items.id'))
    # Create 1-to-1 relationship with Customer
    customers = relationship('Customer', uselist=False, back_populates='shoppingCarts')
    
    # Create 1-to-many relationship with Item
    # 1 shopping cart -> many items
    items = relationship('Item')
```


```python
class Item(Base):
    __tablename__ = "items"
    
    # we don't ever look at an item in many shopping carts
    # if we wanted to look at how many shopping carts an item is in
    # we would add a foreign shoppingCart.id key
    id = Column(Integer, primary_key=True)
    description = Column(String)
    price = Column(Float)
```

## Creating Our Database

Now that we've successfully defined our mappings, we can actually create our database. We'll call our database `shopping_cart.db`. 

When creating the engine, remember to put `sqlite:///` in front of the name of the database we'll be creating, and also set `echo=True`!

In the cell below:

* Create an `engine` using the appropriate method. 
* Use the `create_all()` method found inside of `Base.metadata` and pass in the engine object to create our database!


```python
engine = create_engine('sqlite:///shopping_cart.db', echo=True, )
Base.metadata.create_all(engine)
```

    2018-11-01 11:07:01,230 INFO sqlalchemy.engine.base.Engine SELECT CAST('test plain returns' AS VARCHAR(60)) AS anon_1
    2018-11-01 11:07:01,231 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,234 INFO sqlalchemy.engine.base.Engine SELECT CAST('test unicode returns' AS VARCHAR(60)) AS anon_1
    2018-11-01 11:07:01,235 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,237 INFO sqlalchemy.engine.base.Engine PRAGMA table_info("customers")
    2018-11-01 11:07:01,239 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,241 INFO sqlalchemy.engine.base.Engine PRAGMA table_info("shoppingCarts")
    2018-11-01 11:07:01,243 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,245 INFO sqlalchemy.engine.base.Engine PRAGMA table_info("items")
    2018-11-01 11:07:01,245 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,248 INFO sqlalchemy.engine.base.Engine 
    CREATE TABLE items (
    	id INTEGER NOT NULL, 
    	description VARCHAR, 
    	price FLOAT, 
    	PRIMARY KEY (id)
    )
    
    
    2018-11-01 11:07:01,248 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,251 INFO sqlalchemy.engine.base.Engine COMMIT
    2018-11-01 11:07:01,252 INFO sqlalchemy.engine.base.Engine 
    CREATE TABLE "shoppingCarts" (
    	id INTEGER NOT NULL, 
    	item_id INTEGER, 
    	PRIMARY KEY (id), 
    	FOREIGN KEY(item_id) REFERENCES items (id)
    )
    
    
    2018-11-01 11:07:01,253 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,256 INFO sqlalchemy.engine.base.Engine COMMIT
    2018-11-01 11:07:01,257 INFO sqlalchemy.engine.base.Engine 
    CREATE TABLE customers (
    	id INTEGER NOT NULL, 
    	name VARCHAR, 
    	cart_id INTEGER, 
    	PRIMARY KEY (id), 
    	FOREIGN KEY(cart_id) REFERENCES "shoppingCarts" (id)
    )
    
    
    2018-11-01 11:07:01,258 INFO sqlalchemy.engine.base.Engine ()
    2018-11-01 11:07:01,262 INFO sqlalchemy.engine.base.Engine COMMIT


## CRUD Operations

We've now created a database, but our tables don't contain any data yet!

We'll need to create some objects, and then populate the database with them.

Run the cell below to some sample data for our tables.


```python
customer1 = Customer(name="Jane")
item1 = Item(description="widget", price=9.99)
cart1 = ShoppingCart(customers=customer1, items = item1)
customer1.shoppingCart = cart1
```

Note that this data has not yet been put into the database.  Before that happens, we need to create a `session` object, then add these objects and commit them. We can double check this by examining the items and seeing that they don't yet have primary keys.  Run the cell below now to demonstrate this. 


```python
customer1.id, item1.id

# why don't we get an id? 
# id is there but is a null value yet
# haven't saved in database yet so it won't assign a primary key
# just in memory
```




    (None, None)



You may have noticed that we defined values for certain attributes such as the customer's name, or the item's description and price, but never attributes that act as ids.  There's a reason for this--SQLAlchemy takes care of this for us! Since every primary key has to be unique, this means that defining the integer values for primary keys would be really cumbersome, since we would need to keep track of every primary key that's been created so far--a much better task for a computer than for us!

Another thing you might have noticed is that to create relationships between objects, we just assign them to attributes that were defined as `relationship` objects when we created our mappings!

## Creating a Session Object

In order to add our new data to our database tables, we first need to create a session object. 

In the cell below:

* import `Session` and `sessionmaker` from `sqlalchemy.orm`
* create a `sessionmaker` and set the `bind=` parameter to our `engine` object. Store this in `Session`
* Instantiate a `Session()` object and store it in the variable `session`


```python
Session = sessionmaker(bind=engine)

session = Session()
```

Great! Now we have a session object that we can use to interact with our database.

We can add items to our database one at a time by passing them in as a parameter to `session.add()`. We can also add multiplem items by passing them as a list into the `add_all()` method. In the cell below, use `add_all()` to add `customer1`, `cart1`, and `item1` into our database.


```python
session.add_all([customer1, cart1, item1])
```

Adding something multiple times will not throw an error or cause duplicates.  We can see all the items that have been added by checking the session object's `.new` attribute. Do this now in the cell below.


```python
session.new
```




    IdentitySet([<__main__.Customer object at 0x105251c50>, <__main__.ShoppingCart object at 0x105251a90>, <__main__.Item object at 0x10523dd30>])



Now, commit our objects to push them to the database. 

In the cell below, call `session.commit()`.


```python
session.commit() # this adds them to db
```

    2018-11-01 11:08:46,444 INFO sqlalchemy.engine.base.Engine BEGIN (implicit)
    2018-11-01 11:08:46,446 INFO sqlalchemy.engine.base.Engine INSERT INTO items (description, price) VALUES (?, ?)
    2018-11-01 11:08:46,447 INFO sqlalchemy.engine.base.Engine ('widget', 9.99)
    2018-11-01 11:08:46,450 INFO sqlalchemy.engine.base.Engine INSERT INTO "shoppingCarts" (item_id) VALUES (?)
    2018-11-01 11:08:46,452 INFO sqlalchemy.engine.base.Engine (1,)
    2018-11-01 11:08:46,454 INFO sqlalchemy.engine.base.Engine INSERT INTO customers (name, cart_id) VALUES (?, ?)
    2018-11-01 11:08:46,456 INFO sqlalchemy.engine.base.Engine ('Jane', 1)
    2018-11-01 11:08:46,458 INFO sqlalchemy.engine.base.Engine COMMIT


If we check the object ids again, we'll see that they now have values for their primary keys.

In the cell below, check the `.id` attribute of `customer1`.


```python
item1.id
```

    2018-11-01 11:08:53,682 INFO sqlalchemy.engine.base.Engine BEGIN (implicit)
    2018-11-01 11:08:53,684 INFO sqlalchemy.engine.base.Engine SELECT items.id AS items_id, items.description AS items_description, items.price AS items_price 
    FROM items 
    WHERE items.id = ?
    2018-11-01 11:08:53,685 INFO sqlalchemy.engine.base.Engine (1,)





    1




```python
session.close()
```

    2018-11-01 11:09:09,452 INFO sqlalchemy.engine.base.Engine ROLLBACK


## Summary

In this lab, we created a database with SQLAlchemy, defined our mappings to structure the tables, and even added some data to the database. Great job! 
