+++
date = "2026-01-20T12:00:00+01:00"
draft = false
title = "Constructor in Rust"
tags = ["constructor", "design patterns"]
categories = ["programming", "rust"]
description = "Different approch to Constructor in Rust"
image = "thumbnail.png"
+++

# An approch to Constructor in Rust :

Some of my friends are learning Rust, and they are coming from langage like C++ or Java. One things that one of them told me is:

> It's crap, the new function only has one declaration. You can't declare it based on the number of arguments like in C++ or Java, but on the other hand, there's no problem with macros...

After answering with a lot of message about the different way to make good constructor in Rust, I decided to write this blog about it.

# Why do we need constructors:

A constructor in general have multiples usages:

- **Instantiation** : It is the most common usage : creating an instance from smaller piece, like a `struct`, an `enum` or an `class` (for C++/Java developers).

Primitive types like integers or floats already have a native syntax for construction (eg `42` for an integer, `true` or `false` for a boolean). More complex types, especially composite types like structures, tuples, or arrays, require a mechanism or syntax to initialize themselves.

- **Encapsulation** : It's about invariant and control. Deciding what value are valid or what relation the structure should hold. The goal is to avoid any invalid state, typically achieved through data hiding and encapsulation.

- **Placement** : In C++, the constructor also determines where in memory the object is initialized.

# Struct Instantiation in Rust

```rs
#[derive(Debug, Clone, Default)]
pub struct Person
{
    pub age: i32,
    pub name: String,
    pub hobbies: Vec<String>,
}
```

If you want to create/instanciate a *new* `Person`, you can just use the [struct literal syntax](<https://doc.rust-lang.org/book/ch05-01-defining-structs.html#:~:text=Creating%20an%20instance%20of%20the%20User%20struct>).

```rs
let bobby = Person { age: 42, name: "Bobby".to_owned(), hobbies: vec!["programming".to_owned()] };
```

Even better, you can create another person based on `bobby` for all the missing field.

```rs
let alice = Person { name: "Alice".to_owned(), .. bobby };
```

This is the [struct update syntax](<https://doc.rust-lang.org/book/ch05-01-defining-structs.html#:~:text=Using%20struct%20update%20syntax%20to%20set%20a%20new%20email%20value%20for%20a%20User%20instance%20but%20to%20use%20the%20rest%20of%20the%20values%20from%20user1>) described in the Rust book.

Doing this will *move* bobby field, so the moved first will not be available after it :

```rs
let bobby = Person { age: 42, name: "Bobby".to_owned(), hobbies: vec!["programming".to_owned()] };
let alice = Person { name: "Alice".to_owned(), .. bobby };

dbg!(&bobby.name); // ok because Alice constructor have her own name
// dbg!(&bobby.hobbies); // error: borrow of moved value: `bobby.hobbies`. It was moved to Alice
dbg!(&bobby.age); // ok, because the type `i32` is Copy, so it was copied, not moved
```

If you still want to be able to use bobby, one easy way is to clone it:

```rs
let alice = Person { name: "Alice".to_owned(), .. bobby.clone() };
dbg!(&bobby); // ok
```


Ok great, this is great for satisafying the first point *instantiating* new structure, but it completely break the *encapsulation*. Anyone can create a new person with a negative age:

```rs
pub struct Person
{
    pub age: i32, // should be >= 0 and <= 150
    pub name: String,
}

// somewhere in the main function...
let alice = Person { age: -100, name: "Alice".to_owned() };
alice.age = -200;
```

Even and intialization, anyone can read, and edit the `age` field.

If you are coming from Java/C#/C++ the anwser is simple just make the field private, and write a constructor to be able to initialize the struct, and add some getter/setter to control the access to the field. Let's focusing on the constructor part for the moment:

```rs
pub struct Person
{
    // the field are no longer public
    age: i32,
    name: String,
}

impl Person
{
    pub fn new(age: i32, name: String) -> Self
    {
        Self { age, name }
    }
}
```

Right now, we don't check the age. Notice that the constructor is just a plain function in Rust. I don't have any special Syntax, you can take it's adress, and even write a *constructor* inside a `trait`/interface.


To make sure the instance of the person we are building is valid, we can have a **faillible** constructor that return an `Option<Self>` or a `Result<Self,MyConstructorErrorType>` if you want to specify why the struct can't be constructed with the current parameter. (If the parameter are costly to compute, like the string `name`, we can also return them properly in the error of the result).

```rs
impl Person
{
    pub fn try_new(age: i32, name: String) -> Option<Self>
    {
        if age < 0 || age > 150 { return None };
        Some(Self { age, name })
    }
}

let alice = Person::try_new(42, "Alice".to_owned()).expect("it should be good");
```

A lot of constructors /functions in the standard library are faillible, and their names start with [`try_`](<https://doc.rust-lang.org/std/?search=try_>).

Faillible constructor is somethings that can't be directly done in C++/Java purely by using the constructor, because constructor in those langages can't fail. Surely you can throw an exception. But you can emulate it by making the constructor private, and write a static function to be able to initialize the class, and add some getter/setter to control the access to the field:

Example in Java this time:

```java

public class Person {
    private int age;
    private String name;

    // Private constructor prevents direct instantiation
    private Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    // Static factory method: returns null or throws if invalid
    public static Person tryCreate(int age, String name) {
        if (age < 0 || age > 150) {
            return null; // or throw new IllegalArgumentException("Invalid age");
        }
        return new Person(age, name);
    }
}

// Usage
Person alice = Person.tryCreate(42, "Alice");
if (alice == null) {
    System.out.println("Invalid person");
}
```

or you can use the `Optional` type instead or returning `null` on faillure:

```java
public class Person {
    private int age;
    private String name;

    private Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public static Optional<Person> tryCreate(int age, String name) {
        if (age < 0 || age > 150) {
            return Optional.empty();
        }
        return Optional.of(new Person(age, name));
    }
}
```

Ok le'ts go back to Rust.
We can have constructor that auto-correct invalid input, or constructor that panic on invalid input.

```rs
impl Person
{
    // Note: the naming of the constructor is just for the example, idk how to name it properly
    pub fn new_auto_corrected(mut age: i32, name: String) -> Self
    {
        age = age.max(0).min(150);
        Self { age, name }
    }

    /// Panics on invalid input
    pub fn new(age: i32, name: String) -> Self
    {
        Self::try_new(age, name).expect("it should be good") // panic on None
    }
}

let alice = Person::new_auto_corrected(42, "Alice".to_owned());
let alice = Person::new(42, "Alice".to_owned());
```

It’s fine to have helper functions or constructors that can panic, as long as:
- It is clearly documented,
- The underlying mechanism (here, the `try_new` function/constructor) is public. This allows others to build a non-failing abstraction on top that won’t panic.

We just need to add a few getter/setter and we are done about encapsulation:

```rs
impl Person
{
    pub fn age(&self) -> i32 { self.age }
    // The result type can also be a `bool` in this case,
    // but a `Result` make it explicit that this function can fail
    // Ok return the original mutable reference to self to
    // method chaining . It is not required, but I like it
    pub fn set_age(&mut self, age: i32) -> Result<&mut Self,()>
    {
        if age < 0 || age > 150 { return Err(()) };
        self.age = age;
        Ok(())
    }

    pub fn name(&self) -> &str { &self.name }
    pub fn rename(&mut self, name: String) -> &mut Self { self.name = name; self }
}


let mut p = Person::new();
p.set_age(42).unwrap().rename("Alice");
```

Ok now it's is time to adress the rude original critique

> It's crap, the new function only has one declaration. You can't declare it based on the number of arguments like in C++ or Java, but on the other hand, there's no problem with macros...

The point is conveniance.

In Java, you probably write:

```java
public class Person
{
    private int age;
    private String name;

    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    // Redefining another constructor
    public Person(String name) {
        this.age = 0;
        this.name = name;
    }

    // Delegate to another existing constructor
    public Person(int age) {
        this(age, "Unknown");
    }

    // + getter and setter
}
```

It's better to delegate all constructor to only one constructor, that way only this one need to check the invariants.

If you try the same in Rust it will fail:

```rs
impl Person {
    pub fn new(age: i32, name: String) -> Self {
        Self { age, name }
    }

    pub fn new(name: String) -> Self {
        Self { age: 0, name }
    }

    pub fn new(age: i32) -> Self {
        Self::new(age, "Unknown".to_string())
    }
}
```

You can't have multiple function with the same name `new`, there is no overloading in Rust. (at least not like that).

The soluce is simple: be more creative, and use different name:

```rs
impl Person {
    pub fn new(age: i32, name: String) -> Self {
        Self { age, name }
    }

    pub fn from_name(name: String) -> Self {
        Self::new(0, name)
    }

    pub fn from_name(age: i32) -> Self {
        Self::new(age, "Unknown".to_string())
    }
}
```

## Large structure

Constructor are simple to use when they have little parameters, but it is not convenient when you have a lot of field:

```rs
pub struct Person {
    name: String,
    age: u32,
    email: String,
    phone: String,
    address: String,
    city: String,
    country: String,
}

impl Person {
    pub fn new(
        name: String,
        age: u32,
        email: String,
        phone: String,
        address: String,
        city: String,
        country: String,
    ) -> Self {
        Self {
            name,
            age,
            email,
            phone,
            address,
            city,
            country,
        }
    }
}

// How conveniant... We can do better
let p = Persone::new("John Doe".to_string(), 42, "john.doe@example.com".to_string(), "1234567890".to_string(), "On the street because the rent are expensive".to_string(), "Anytown".to_string(), "USA".to_string());
```

*Note*: I use a lot the `String` type for the example, but it's not the best choice for a real world application for modeling a `country`, an `email`... Those should have their own type that validate the value.

If you have a structure with a lot of field, it because barely usable:
- It's hard to read the code,
- The constructor is long,
- The field order really matter if you don't want to accidentally swap the city and the country,
- And it's easy to make a mistake.


And if, in the future, you want to add a new field, you will have to update all the constructor. Imagine doing this as a library/crate developer, you can't ask every user to update their code because you decided to add a new field.

Constructor in this case is not the best way to initialize it. It's better to use a builder pattern.

## Builder pattern

If you have a large structure that need to verify the invariants, it's better to use a builder pattern. Typically, you can initialize a Person from another structure that have the same field in public.

```rs
#[derive(Default)]
pub struct PersonBuilder {
    // Notive the public field
    pub name: String,
    pub age: u32,
    pub email: String,
    pub phone: String,
}

pub struct Person {
    name: String,
    age: u32,
    email: String,
    phone: String,
}

impl Person
{
    pub fn new(builder: PersonBuilder) -> Self { builder.build() }
}
impl Builder
{
    pub fn build(self) -> Person {
        Person { name: self.name, age: self.age, email: self.email, phone: self.phone }
    }
}

let p = Person::new(PersonBuilder{age:42, name:"John Doe".to_string(), email:"john.doe@example.com".to_string(), phone:"1234567890".to_string()});
// or
let p = PersonBuilder{age:42, name:"John Doe".to_string(), email:"john.doe@example.com".to_string(), phone:"1234567890".to_string()}.build();
// or event better:

let p = Person::new(PersonBuilder{age:42, name:"John Doe".to_string(), .. Default::default()});
```

This work well because:

- all field are now named, and the order doesn't matter
- it respect encapsulation for the `Person` struct.

Right now this is an infaillible builder, it always return a `Person`, but we can make it fail if needed and return an `Option<Person>` or a `Result<Person, Error>`.

Notice how, by making the same structure in public, we can now use the struct literal to initialize the builder.

It is also possible to define some kind of profile that will edit multiple parameter at once:
```rs
impl PersonBuilder
{
    pub fn avoids_technology(self) -> Self { Self { phone: "no phone".to_owned(), email: "no email".to_owned(), .. self, } }
}

let p = PersonBuilder::new().name("Kevin").avoids_technology().age(7).build();
```
Maybe this example is a bit too simple, but it is really useful to be able to quickly define new profile like that, think about a structure describing some complexe configuration.


Some crate like wgpu use this pattern a lot to initialize all kind of structure using the name [`XDescriptor`](<https://docs.rs/wgpu/latest/wgpu/?search=Descriptor>):
```rs
let instance = wgpu::Instance::new(
    &wgpu::InstanceDescriptor // right here :)
    {
        #[cfg(not(target_arch = "wasm32"))]
        backends: wgpu::Backends::PRIMARY,
        #[cfg(target_arch = "wasm32")]
        backends: wgpu::Backends::GL,
        ..Default::default()
    }
);
```
(Example taken from [Learn Wgpu](<https://sotrh.github.io/learn-wgpu/beginner/tutorial2-surface/#first-some-housekeeping-state:~:text=WebGPU-,let,Default%3A%3Adefault%28%29%20%7D%29%3B>))


### Conveniant method

We can even made some conveniant method on the builder to init each field

```rs
impl PersonBuilder
{
    pub fn new() -> Self { Self::default() }

    pub fn age(self, age: i32) -> Self { Self { age, .. self } }
    pub fn name(self, name: String) -> Self { Self { name, .. self } }
    // other syntax possible
    pub fn email(mut self, email: String) -> Self { self.email = email; self }
    
    // or even making the string creation more conveniant
    pub fn phone(self, phone: impl Into<String>) -> Self { Self { phone: phone.into(), .. self } }
}

let p = PersonBuilder::new()
        .age(42)
        .name("John Doe".to_owned())
        .phone("1234567890") // No `to_owned()` because of Into
        .build();
```

### Backward compatibility

If we need to add more field later, without restrucing the API, we can mark the struct [non_exhaustive](<https://doc.rust-lang.org/reference/attributes/type_system.html>):

```rs
#[non_exhaustive]
#[derive(Default)]
pub struct PersonBuilder {
    // Notice the public field
    pub name: String,
    pub age: u32,
    pub email: String,
    pub phone: String,
}
```

This mean that we can't use the struct literal outside of the current crate to initialize the builder, because adding new field can be added in the future.

You can still use the struct update syntax:

```rs
PersonBuilder{age:42, .. PersonBuilder::new()};
```

or the conveniance method to set the field.

```rs
PersonBuilder::new()
    .age(42)
    .name("John Doe".to_owned())
    .phone("1234567890");
```

That way, even if we add new field in the future, it will not break the API and old code will still work without any change.

### Builder Trait Are Useless ?

If we want we can even made a trait for buider:

```rs
pub trait Builder<T> {
    fn build(self) -> T;
}

impl Builder<Person> for PersonBuilder
{
    fn build(self) -> Person {
        Person { name: self.name, age: self.age, email: self.email, phone: self.phone }
    }
}

impl Person
{
    // Accept any type that implement the Builder trait, 
    // not just PersonBuilder, even we don't need more
    // than one builder type.
    pub fn new(builder: impl Builder<Self>) -> Self { builder.build() }
}
```

```rs
pub trait FaillibleBuilder<T> {
    type Error;
    fn try_build(self) -> Result<T, Self::Error>;
}
```

But wait ! This look exactly like the [Try](<https://doc.rust-lang.org/std/convert/trait.From.html>) and [TryFrom](<https://doc.rust-lang.org/std/convert/trait.TryFrom.html>) traits in the standard library !

So we can remove these uncessary `Builder` and `FaillibleBuilder` trait and use these traits instead:

```rs
impl From<PersonBuilder> for Person {
    fn from(builder: PersonBuilder) -> Self {
        // ...
    }
}

impl Person
{
    pub fn new(value: impl Into<PersonBuilder>) -> Self { value.into() }
}
```

The inconveniance is that it is hard to know what we can pass to the `new` function by just looking at the type signature.

### The From and Into trait

The `From` and `Into` trait are a way to convert a type into another type. The convertion must be infallible/can't fail, without any loss of information.

For exemple `From<u8> for u32` is possible because it's a lossless conversion/*integer promotion*, but `From<u32> for u8` is not possible, because it can't convert a number that is too big to fit in a u8.

For small type, it's conveniant to use the `From` and `Into` trait.

From a code perspective, you just need to implement `From` trait, and the `Into` trait will be automatically implemented for you as stated  in the std:

`From` trait description:
> [One should always prefer implementing From over Into because implementing From automatically provides one with an implementation of Into thanks to the blanket implementation in the standard library.](<https://doc.rust-lang.org/std/convert/trait.From.html#:~:text=One%20should%20always%20prefer%20implementing%20From%20over%20Into%20because%20implementing%20From%20automatically%20provides%20one%20with%20an%20implementation%20of%20Into%20thanks%20to%20the%20blanket%20implementation%20in%20the%20standard%20library>)

`Into` trait description:
> One should avoid implementing `Into` and implement `From` instead.

```rs
struct MyCustomI32(i32);

impl From<i32> for MyCustomI32 { fn from(value: i32) -> Self { MyCustomI32(value) } }
impl From<MyCustomI32> for i32 { fn from(value: MyCustomI32) -> Self { value.0 } }

let x = MyCustomI32::from(i32);
```

Another verison of the `From`/`Into` trait is the [`TryFrom`](<https://doc.rust-lang.org/std/convert/trait.TryFrom.html>)/[`TryInto`](<https://doc.rust-lang.org/std/convert/trait.TryInto.html>) trait, that allow to convert a type into another type, but can fail and return a custom error.

# Back to the Builder

If the struct don't have a default value, we can avoid writing a builder and use the same struct for the builder!

```rs
#[derive(Default)]
pub struct Person
{
    age: i32,
    name: String,
}

impl Person
{
    pub fn new() -> Self { Self::default() }

    pub fn with_age(self, age: i32) -> Self { Self { age, .. self } }
    pub fn set_age(&mut self, age: i32) -> &mut Self { self.age = age; self }
    pub fn age(&self) -> i32 { self.age }

    pub fn with_name(self, name: String) -> Self { Self { name, .. self } }
    // + name(_) and set_name(_)...
}

let p = Person::new().with_age(42).with_name("John Doe");
```

This work well if the default value is not too complex to compute, and if the logic for editing every field `with_age()`/`with_name()`, etc... is not too complex.

Notice how `fn with_age(self, age: i32) -> Self` and `fn set_age(&mut self, age: i32) -> &mut Self` are very similar, and we can use the same logic for both. It's maybe a bit annoying to write the same logic twice, and to have 2 names almost similar for the same thing, except the first one is call by value and the second one is by reference.

## About Placement New

Currently Rust don't support placement new. I guess this is one of the situation where C++ constructor are more polyvalent than the Rust one.

All value in Rust are always *created* on the stack, and moved to the heap if necessary.

There is a RFC for it <https://github.com/PoignardAzur/rust-rfcs/blob/placement-by-return/text/0000-placement-by-return.md>, but I can't tell more about it.


# Other Useful Link

[Rust Book : Defining and Instantiating Structs](<https://doc.rust-lang.org/book/ch05-01-defining-structs.html>)

[Idiomatic Rust (for C++ Devs): Constructors & Conversions](https://geo-ant.github.io/blog/2023/rust-for-cpp-developers-constructors/), it also cover more `Copy` constructor.


# Code that don't compile

It is not possible to unify the `set_age` and `with_age` function, (and it is overkill)

```rs
pub trait SetAge
{
    fn set_age(self, age: i32) -> Self;
}

impl SetAge for &mut Person
{
    // error: method `set_age` has an incompatible type for trait
    fn set_age(&mut self, age: i32) -> &mut Self { self.age = age; self }
    //         ^^^^^^^^^ expected `Person`, found `&mut Person`
}
impl SetAge for Person
{
    fn set_age(mut self, age: i32) -> Self { self.age = age; self }
}

let mut p = Person::new().set_age(42); // by value
assert_eq!(p.age(), 42);
p.set_age(43); // by mutable reference
assert_eq!(p.age(), 43);
```