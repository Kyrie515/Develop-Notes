## 1. compareTo method in Java

The most common usage of compareTo method in Java is to compare Integers and Strings. For example.

```Java
public class Test {
    public static void main(String[] args) {
        Integer base = 3;
        //Only Integer class has compareTo method
        System.out.println(base.compareTo(5)); //3 < 5 so the output is -1
        
        System.out.println(base.compareTo(3)); // 3 == 3 so the output is 0

        System.out.println(base.compareTo(1)); // 3 > 1 so the output is 1
    }
}
```

When we use internal sorting in Java such as `Arrays.sort()`for user-defined classes arrays and `collections.sort()` for collections of user-defined classes, the `compareTo()` method is called. If we want the internal sorting to work rightly for our self-defined objects, we must implement the `Comparable`interface and overiFor example

```Java
package Leetcode;

import java.util.Arrays;


class Person implements Comparable<Person> {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person o) {
        if (this.age > o.age) {
            return 1;
        }
        else if (this.age == o.age) {
            return 0;
        }
        else {
            return -1;
        }
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}


public class Test {
    public static void main(String[] args) {
        Person[] persons = new Person[4];
        persons[1] = new Person("k", 12);
        persons[0] = new Person("g", 15);
        persons[2] = new Person("a", 10);
        persons[3] = new Person("f", 20);

        Arrays.sort(persons);

        for (Person p: persons) {
            System.out.println(p);
        }

    }
}


//output:
Person{name='a', age=10}
Person{name='k', age=12}
Person{name='g', age=15}
Person{name='f', age=20}
```



## 2. Different between interface Comparable and Comparator

When a class implements the `Comparable` interface and override the`compareTo()` method  , it means the instances of this class are capable of comparing themselves with another instance, it also means "**I can compare myself with another instance**". 

But when a class implements the `Comparator` interface and override the `compare()`method, it means the instances of this class are capable of comparing two other intances of another object, it also means "**I can compare two other instances**". And we can use the class which implements the `Comparator` interface as the standard for some kinds of internal sorting such as `collections.sort()` or `Arrays.sort()`. For example, 

```java
//A Java program to demonstrate Comparator interface
import java.io.*;
import java.util.*;

// A class 'Movie' that implements Comparable
class Movie implements Comparable<Movie>
{
	private double rating;
	private String name;
	private int year;

	// Used to sort movies by year
	public int compareTo(Movie m)
	{
		return this.year - m.year;
	}

	// Constructor
	public Movie(String nm, double rt, int yr)
	{
		this.name = nm;
		this.rating = rt;
		this.year = yr;
	}

	// Getter methods for accessing private data
	public double getRating() { return rating; }
	public String getName() { return name; }
	public int getYear()	 { return year; }
}

// Class to compare Movies by ratings
class RatingCompare implements Comparator<Movie>
{
	public int compare(Movie m1, Movie m2)
	{
		if (m1.getRating() < m2.getRating()) return -1;
		if (m1.getRating() > m2.getRating()) return 1;
		else return 0;
	}
}

// Class to compare Movies by name
class NameCompare implements Comparator<Movie>
{
	public int compare(Movie m1, Movie m2)
	{
		return m1.getName().compareTo(m2.getName());
	}
}

// Driver class
class Main
{
	public static void main(String[] args)
	{
		ArrayList<Movie> list = new ArrayList<Movie>();
		list.add(new Movie("Force Awakens", 8.3, 2015));
		list.add(new Movie("Star Wars", 8.7, 1977));
		list.add(new Movie("Empire Strikes Back", 8.8, 1980));
		list.add(new Movie("Return of the Jedi", 8.4, 1983));

		// Sort by rating : (1) Create an object of ratingCompare
		//				 (2) Call Collections.sort
		//				 (3) Print Sorted list
		System.out.println("Sorted by rating");
		RatingCompare ratingCompare = new RatingCompare();
		Collections.sort(list, ratingCompare);
		for (Movie movie: list)
			System.out.println(movie.getRating() + " " +
							movie.getName() + " " +
							movie.getYear());


		// Call overloaded sort method with RatingCompare
		// (Same three steps as above)
		System.out.println("\nSorted by name");
		NameCompare nameCompare = new NameCompare();
		Collections.sort(list, nameCompare);
		for (Movie movie: list)
			System.out.println(movie.getName() + " " +
							movie.getRating() + " " +
							movie.getYear());

		// Uses Comparable to sort by year
		System.out.println("\nSorted by year");
		Collections.sort(list);
		for (Movie movie: list)
			System.out.println(movie.getYear() + " " +
							movie.getRating() + " " +
							movie.getName()+" ");
	}
}

```

