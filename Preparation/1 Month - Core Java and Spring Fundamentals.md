3 Paths
	Java Deep Dive
	Java based DSA
	Spring and Spring Fundamentals

Java based DSA

Resources
	https://leon-wtf.github.io/doc/java-concurrency-in-practice.pdf

Generics

type parameter "<T>" is added to the classname. It is used to conver the runtime error of type casting into the compile time error by using the property of the java i.e its statically typed
for instantiation, we can use List<Person> = new ArrayList<Person> and from java 7, its  List<Person> = new ArrayList<> (which can infer from the context)

Inheritance with generics

public class AgeComparator implements Comparator<Person>  => the person type is necessary over there coz the extending class has generics and the methods which are overriden are automatically implemented  
generally  the comparator cls is implemented as a anonymous class, within the Collections.sort(<collection to be sorted>, <custom comparator used>)

if we have 
All the generic type cls can extend to another cls or a interface, such as if a type T is present as a generic, it can extend to others creating a upper bound to it
if the same type t extends to multiple classes or interfaces, the class must come first
<T extends A&B&C>, where a=class and b,c=interfaces

if we a have the genric to extend to a generic interface, then we can simply write as
public class A<T implements Comparable> , 
which means the type t passed must have implemented the interface

it works, but not fully fool proof as we can call the interface methods with random objects, hence 
public class A<T implements Comparable<T>>  is the right way, as we are saying that the parameters passed to the methods of the interface must also be of type T 

things to check again
how iterator and iterable are implemented to the collections
difference between Comparator and Comparable interfaces
different type of collections and their impl and their according impl in dsa
why do we use the final keyword for different args in the functions



