# C Sharp

## Extract Property from a collection of objects.
LINQ
```C#
using System;
using System.Collections.Generic;
using System.Linq;

public class MyObject
{
    public string Name { get; set; }
}

class Program
{
    static void Main()
    {
        var objects = new List<MyObject>
        {
            new MyObject { Name = "Object1" },
            new MyObject { Name = "Object2" },
            new MyObject { Name = "Object3" }
        };

        // Extract names using LINQ
        var names = objects.Select(obj => obj.Name).ToList();

        // Output names
        names.ForEach(Console.WriteLine);
    }
}
```
PLINQ
```C#
using System.Linq;

var names = objects.AsParallel().Select(obj => obj.Name).ToList();

// Output names
names.ForEach(Console.WriteLine);
```
Foreach
```C#
var names = new List<string>();
foreach (var obj in objects)
{
    names.Add(obj.Name);
}

// Output names
foreach (var name in names)
{
    Console.WriteLine(name);
}
```
Generator
```C#
  // Generator to extract the 'Name' property
    static IEnumerable<string> ExtractNames(IEnumerable<MyObject> objects)
    {
        foreach (var obj in objects)
        {
            yield return obj.Name;
        }
    }
```

## Linked List
```C#
using System;

public class Node<T>
{
    public T Data { get; set; }
    public Node<T> Next { get; set; }

    public Node(T data)
    {
        Data = data;
        Next = null;
    }
}

public class LinkedList<T>
{
    private Node<T> head;

    public LinkedList()
    {
        head = null;
    }

    // Add a new node at the end of the list
    public void Add(T data)
    {
        var newNode = new Node<T>(data);
        if (head == null)
        {
            head = newNode;
        }
        else
        {
            var current = head;
            while (current.Next != null)
            {
                current = current.Next;
            }
            current.Next = newNode;
        }
    }

    // Remove the first occurrence of a node with the specified value
    public bool Remove(T data)
    {
        if (head == null) return false;

        // If the head node is to be removed
        if (head.Data.Equals(data))
        {
            head = head.Next;
            return true;
        }

        // Traverse the list to find the node to remove
        var current = head;
        while (current.Next != null && !current.Next.Data.Equals(data))
        {
            current = current.Next;
        }

        if (current.Next == null) return false;

        // Skip the node to be removed
        current.Next = current.Next.Next;
        return true;
    }

    // Print all the nodes in the list
    public void PrintAll()
    {
        var current = head;
        while (current != null)
        {
            Console.Write($"{current.Data} -> ");
            current = current.Next;
        }
        Console.WriteLine("null");
    }

    // Find if a value exists in the list
    public bool Contains(T data)
    {
        var current = head;
        while (current != null)
        {
            if (current.Data.Equals(data)) return true;
            current = current.Next;
        }
        return false;
    }

    // Get the size of the list
    public int Count()
    {
        int count = 0;
        var current = head;
        while (current != null)
        {
            count++;
            current = current.Next;
        }
        return count;
    }
}

class Program
{
    static void Main(string[] args)
    {
        var list = new LinkedList<int>();

        list.Add(10);
        list.Add(20);
        list.Add(30);

        Console.WriteLine("Initial List:");
        list.PrintAll();

        Console.WriteLine("\nDoes the list contain 20?");
        Console.WriteLine(list.Contains(20)); // Output: True

        Console.WriteLine("\nRemoving 20...");
        list.Remove(20);
        list.PrintAll();

        Console.WriteLine("\nSize of the list:");
        Console.WriteLine(list.Count()); // Output: 2

        Console.WriteLine("\nAdding 40...");
        list.Add(40);
        list.PrintAll();
    }
}
```