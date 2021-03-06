# Single-table Inheritance and Polymorphic Associations

# Single Table Inheritance (STI)

What is Single Table Inheritance?

Single Table Inheritance is when many subclasses inherit from one superclass with all the data in the same table in the database.
STI is appropriate when your models have shared data or state.


<strong>Example</strong>

Let's pretend we are creating an app for a dealership that sells cars, motocycles, and bicycles.

For each vehicle we cant to track <strong>price</strong>, <strong>color</strong>, and <strong>purchased</strong>.
We want to use the same data for each class.

We can create a superclass `Vehicle` with attributes <strong>price</strong>, <strong>color</strong>, and <strong>purchased</strong> and our subclasses will inherit all those attributes.

Migration to create the table:

```
class CreateVehicles < ActiveRecord::Migration[5.1]
  def change                           
    create_table :vehicles do |t|                             
      t.string :type, null: false                         
      t.string :color                             
      t.integer :price                            
      t.boolean :purchased, default: false                                                      
    end                         
  end                       
end
```

The `type` column for the superclass tells Rails that we are using STI and want all the data for `Vehicle` and subclasses to be in the same table in the database.

Model classes look like this:

```
class Vehicle < ApplicationRecord
end
class Bicycle < Vehicle
end
class Motorcycle < Vehicle
end
class Car < Vehicle
end
```


Subclasses share the same data fields which allows us to make the same calls on objects from different classes:

```
mustang = Car.new(price: 50000, color: red)
harley = Motorcycle.new(price: 30000, color: black)
mustang.price
=> 50000
harley.price
=> 30000
```

<strong>Adding functionality</strong>

What if we want more information about the vehicles?

For `Bicycles`, we want to know if the bike is a road, mountain or hybrid bike.
For `Cars` and `Motorcycles`, we want to know the horsepower

We can create a migration to add `bicycle_type` and `horsepower` to the `Vehicles` table

<!-- --------------------------------------------------------------------------------- -->

# Polymorphic Association
<p align="center">
  <img src="https://www.ict.social/images/1/csp/oop/polymorphism.png" alt="Example of polymorphic associations using animals" />
</div>

Polymorphism, in plain English, consists of “poly” (many) and “morph” (form). If there are enough differences between like-groups, then a polymorphic association would be a better fit than single-table inheritance (STI).

Polymorphic Association vs STI is an example of composition vs inheritance.

```
To favor composition over inheritance is a design principle that gives the design higher flexibility. 
```

A polymorphic association consists of different classes pointing towards the same class, through a belongs_to and a has_one or has_many relationship. 

Without polymorphic associations (which is perfectly valid):
```
class Post
  belongs_to :person
  belongs to :group
end
class Person
  has_many :posts
end
class Group
  has_many :posts
end
```

With polymorphic associations:
```
class Post
  belongs_to :postable, polymorphic: true
end
class Person
  has_many :posts, as :postable
end
class Group
  has_many :posts, as :postable
end
```

It is “Rails-like” to suffix the polymorphic association with “-able”, so that it is explicit to other people reading the code that there is a polymorphic association involved; however, it is not mandatory.

The polymorphic association, Post in this case, will have at least two columns: “X_type” and “X_id”, where “X” is the name of the polymorphic association. Type refers to the type of class that it is being associated with (Person or Group in this case), and id refers to the id of that respective type. As an example, the polymorphic association is able to find all the posts of type Person with the ID of 1. In this scenario, Posts might also have a column called “Content” which contains the content of the post so that there is a meaningful role for the polymorphic association. 

<!-- --------------------------------------------------------------------------------- -->

<h1><strong>STI vs. Polymorphic Association</strong></h1>

<h3> When and why you should use STI:</h3>
  <ul>
    <li> When you have many similar models that are unlikely going to change and share similar data, ex: 
      <p><strong>Vehicle => Bicycle, Car, Motorcycle (all have price, color, and age)</string></p> </li>
    <li>Simple to implement</li>
    <li>DRY — saves replicated code using inheritance and shared attributes</li>
    <li>Allows subclasses to have own behavior as necessary</li>
   </ul>

<h3>Cons:</h3>
  <ul>
    <li>Doesn’t scale well: as data grows, table can become large and possibly difficult to maintain and query</li> 
    <li>Requires care when adding new models or model fields that deviate from the shared fields</li>
    <li>Can be difficult to validate or query if many null values exist in table or values overlap</li>
  </ul>


<h3>When and why you should use Polymorphic Association:</h3>

<p>Polymorphism is the property in a programming language that allows objects of different types to be substituted for one another in program flow without needing to know ahead of time what the object’s type is.</p>
  <ul>
    <li>Easy to scale in amount of data: information is distributed across several database tables to minimize table bloat</li>
    <li>Easy to scale number of models: more models can be easily associated with the polymorphic class</li>
    <li>DRY: creates one class that can be used by many other classes</li>
    <li>Having many models that have many different behaviors ( creating extra columns that will not be used or will have null         value, also can create conflict when two tables have the same value)</li>
  </ul>



<h3>Cons:</h3>
  <ul>
    <li>More tables can make querying more difficult and expensive as the data grows. (Finding all posts that were created in a       certain time frame would need to scan all associated tables)</li>
    <li>Cannot have foreign key. The id column can reference any of the associated model tables, which can slow down querying.         It must work in conjunction with the type column.</li>
    <li>If your tables are very large, a lot of space is used to store the string values for postable_type</li>
 </ul>
