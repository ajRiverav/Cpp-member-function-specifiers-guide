# A list of specifiers for C++ member functions

It may come natural to a seasoned C++ programmers which specifiers are applicable. For those
who are not as experienced, it may serve as a checklist for what could be used. Specifiers can express developer intent, 
restrictions, help the compiler optimize the compiled code, among other things. 

For example, if one knows that a member function will not be modifying the object for which it is called, 
one is encouraged to add the specifier **const**. 

Some of these specifiers can be used in contexts other than member functions and will not be covered. For example, the keyword const can be used in the context of constant values as well as constant member functions. 

<a name="toc"></a>
# Table of Contents
1. [Virtual](#virtual)
2. [Overriding, Overloading, and Function Hiding](#overriding_and_overloading)

<a name="virtual"></a>
## VIRTUAL ([Go back to Table of Contents](#toc))
Keyword **virtual** is used to specify dynamic (or late) binding, as opposed to static (or early) binding.
One clear issue with dynamic vs. static binding can be shown in the example provided at https://stackoverflow.com/a/2392656/5597960. 

```cpp
class Animal {
public:
	void eat();
}

class Cat : public Animal {
public:
	void eat();
}

void Animal::eat() { std::cout << "I'm eating generic food."; }
void Cat::eat() { std::cout << "I'm eating a rat."; }

Animal *animal = new Animal;
Cat *cat = new Cat;
```
Calling the eat member function, one gets:
```cpp
animal->eat(); // outputs: "I'm eating generic food."
cat->eat();    // outputs: "I'm eating a rat."
```

So far, so good. Let's define a function that forces a downcast of a pointer:
```cpp
void func(Animal *xyz) { xyz->eat(); }
func(animal); // outputs: "I'm eating generic food."
func(cat);    // outputs: "I'm eating generic food."
```
We don't want cats to eat generic food. To solve this, one can define
```cpp
void func(Cat *xyz) { xyz->eat(); }

func(cat);    // outputs: "I'm eating a rat."
```

which outputs what we expect, but one would have to create a func() for each Animal-derived class. *This does not reuse code.* The correct solution is to add the specifier **virtual** to Animal::eat() so that we instruct the compiler that we want late binding of eat().

```cpp
class Animal {
public:
	virtual void eat(); 
}

void Animal::eat() { std::cout << "I'm eating generic food."; } //The definition does not change. 

func(animal); // outputs: "I'm eating generic food."
func(cat);    // outputs: "I'm eating a rat."
```

Here, cat is downcasted to an animal but because we have added the virtual specifier to the member function Animal::eat(), late binding is used. 

When dynamic binding is used, the decision of which member function to call occurs during run-time depending on the object passed in to func() (hence dynamically). In this case it was a Cat which caused func() to call Cat::eat()). 

When static binding occurs, the member function to call is decided during compile time (hence the word static).

[Go back to Table of Contents](#toc)

<a name="overriding_and_overloading"></a>
## Overriding, Overloading, and function hiding ([Go back to Table of Contents](#toc))

#### Overloading
A member function is overloaded when another member function of the same class has a different signature. A function signature is comprised of its its name, and the number and type of its parameters.

```cpp
class Myclass {
    public:
        int returnSomething(int a){ //...}
        int returnSomething(double a){ //... }
};
```

In the code above, returnSomething has the same name and number of parameters, but the types are different. Therefore, MyClass::returnSomething is overloaded. 

The example below will not compile because, even though you may think you are overloading returnSomething by having a different return type, the signature -which is what matters when overloading- is the same. 
```cpp
class Myclass{
    public:
        int returnSomething(int a){ //... }
        double returnSomething(int a){ //... }
};
```
#### Overriding

Overriding occurs when one redefines an existing member function. This implies a change in the body of such function and NOT its signature. 

```cpp
class Animal {
public:
	virtual void eatThisFood( string s ){ //... }
}

class Human : public Animal {
public:
	void eatThisFood( string s ) override {// ... }
}
```

Here, Human::eatThisFood overrides Animal::eatThisFood. Note the signature is the same but how an Animal and a Human may eat food in different ways. It is advisable to add the specifier **override** because it helps with code maintenance and bugs. For example, if a developer changes Animal::eatThisFood's signature, as long as the specifier override is there, the compile will let you know Human::eatThisFood is not overriding anything. 


[Go back to Table of Contents](#toc)

