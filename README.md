# A list of keywords for C++ member functions

It may come natural to a seasoned C++ programmers which specifiers are applicable. For those
who are not as experienced, it may serve as a checklist for what could be used. Specifiers can express developer intent, 
restrictions, help the compiler optimize the compiled code, among other things. 

For example, if the developer knows that the member function will not be modifying the object for which it is called, 
it is encouraged to add the specifier **const**. Similarly, if a member function is being overloaded, it is wise
to add the specifier **override**.

Some of these specifiers can be used in contexts other than member functions and will not be covered. For example, the
keyword const can be used in the context of constant values as well as constant member functions. 


### LIST
virtual
constexpr (since C++11)
const
virtual = 0
override (since C++11)
final (since C++17)


## VIRTUAL
Keyword **virtual** is used to specify dynamic (or late) binding, as opposed to static (or early) binding.
One clear issue with dynamic vs. static binding can be shown in the example provided at https://stackoverflow.com/a/2392656/5597960. 

```
class Animal
{
public:
	void eat() { std::cout << "I'm eating generic food."; }
}

class Cat : public Animal
{
public:
	void eat() { std::cout << "I'm eating a rat."; }
}

Animal *animal = new Animal;
Cat *cat = new Cat;
```
Calling the eat member function, one gets:
```
animal->eat(); // outputs: "I'm eating generic food."
cat->eat();    // outputs: "I'm eating a rat."
```

So far, so good. Let's define a function that forces a downcast of a pointer:
```
void func(Animal *xyz) { xyz->eat(); }
func(animal); // outputs: "I'm eating generic food."
func(cat);    // outputs: "I'm eating generic food."
```
We don't want cats to eat generic food. To solve this, one can define
```
void func(Cat *xyz) { xyz->eat(); }

func(cat);    // outputs: "I'm eating a rat."
```

which outputs what we expect, but one would have to create a func() for each Animal-derived class. This does not reuse code. The correct solution is to add the specifier **keyword** to Animal::eat() so that we instruct the compiler that we want late binding of eat().

```
class Animal
{
public:
virtual void eat() { std::cout << "I'm eating generic food."; }
}

func(animal); // outputs: "I'm eating generic food."
func(cat);    // outputs: "I'm eating a rat."
```

Here, Cat is downcasted to an Animal but because we have added the virtual specifier to the member function Animal::eat(), late binding is used. 

When dynamic binding is used, which member function to call occurs during run-time depending on who calls func()  (hence dynamically). In this case it was a Cat which caused func() to call Cat::eat()). 

When static binding occurs, the member function to call is decided during compile time (hence the word static=does not change). In the original code it was Aminal::eat(). 
