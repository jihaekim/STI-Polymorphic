# STI-Polymorphic
Single-table Inheritance and Polymorphic Associations

<strong> Single Table Inheritance (STI) </strong>

What is Single Table Inheritance?

Single Table Inheritance is when many subclasses inherit from one superclass with all the data in the same table in the database.
STI is appropriate when your models have shared data or state.


<strong>Example</strong>
Let's pretend we are creating an app for a dealership that sells cars,motocycles, and bicycles.
For each vehicle we cant to track <strong>price</strong>,<strong>color</strong>,and <strong>purchased</strong>.
We want to use the same data for each class.

We can create a superclass Vehicle with attributes <strong>price</strong>,<strong>color</strong>,and <strong>purchased</strong> and our subclasses will inherit all those attributes.

<div>class CreateVehicles < ActiveRecord::Migration[5.1]
  def change                           
    create_table :vehicles do |t|                             
      t.string :type, null: false                         
      t.string :color                             
      t.integer :price                            
      t.boolean :purchased, default: false                                                      
    end                         
  end                       
end</div>
