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
public class A<T implements Comparable>   === 
public class A<T implements Comparable<T>>  === 

things to check again
how iterator and iterable are implemented to the collections
difference between Comparator and Comparable interfaces
different type of collections and their impl and their according impl in dsa
why do we use the final keyword for different args in the functions



