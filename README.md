# STI-Polymorphic
Single-table Inheritance and Polymorphic Associations

<strong> Single Table Inheritance (STI) </strong>

What is Single Table Inheritance?

Single Table Inheritance is when many subclasses inherit from one superclass with all the data in the same table in the database.
STI is appropriate when your models have shared data or state.


<strong><p>Example</p></strong>
<p>Let's pretend we are creating an app for
a dealership that sells cars,motocycles, and bicycles.</p>
<p>For each vehicle we cant to track <strong>price</strong>, <strong>color</strong>, and <strong>purchased</strong>.
We want to use the same data for each class.</p>

<p>We can create a superclass Vehicle with attributes <strong>price</strong>, <strong>color</strong>, and <strong>purchased</strong> and our subclasses will inherit all those attributes.</p>

<p>Migration to create the table:</p>
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
<p>The <strong>type</strong> column for the superclass tells Rails that we are using STI and want all the data for <strong>Vehicle</strong> and subclasses to be in the same table in the database.</p>

<p>Model classes look like this:</p>

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
