# Java

## Extract Property from a collection of objects.
Using Java 8 Streams
```java
import java.util.List;
import java.util.stream.Collectors;

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
                new Product("Apple", 50),
                new Product("Banana", 30),
                new Product("Orange", 20)
        );

        List<String> names = products.stream()
                                     .map(Product::getName) // Extract the name
                                     .collect(Collectors.toList()); // Collect into a new list

        System.out.println(names); // Output: [Apple, Banana, Orange]
    }
}
```
Foreach
```Java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
                new Product("Apple", 50),
                new Product("Banana", 30),
                new Product("Orange", 20)
        );

        List<String> names = new ArrayList<>();
        products.forEach(product -> names.add(product.getName()));

        System.out.println(names); // Output: [Apple, Banana, Orange]
    }
}
```
Simple loop
```Java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
                new Product("Apple", 50),
                new Product("Banana", 30),
                new Product("Orange", 20)
        );

        List<String> names = new ArrayList<>();
        for (Product product : products) {
            names.add(product.getName());
        }

        System.out.println(names); // Output: [Apple, Banana, Orange]
    }
}
```
## Linked List
```Java
// Node class representing an element in the linked list
class Node<T> {
    T data;       // Data stored in the node
    Node<T> next; // Reference to the next node

    public Node(T data) {
        this.data = data;
        this.next = null;
    }
}

// LinkedList class
public class LinkedList<T> {
    private Node<T> head; // Reference to the first node of the linked list

    public LinkedList() {
        this.head = null;
    }

    // Add a new node at the end of the list
    public void add(T data) {
        Node<T> newNode = new Node<>(data);
        if (head == null) {
            head = newNode;
        } else {
            Node<T> current = head;
            while (current.next != null) {
                current = current.next;
            }
            current.next = newNode;
        }
    }

    // Add a new node at the beginning of the list
    public void addFirst(T data) {
        Node<T> newNode = new Node<>(data);
        newNode.next = head;
        head = newNode;
    }

    // Remove a node by its value
    public void remove(T data) {
        if (head == null) return;

        // If the head node contains the value
        if (head.data.equals(data)) {
            head = head.next;
            return;
        }

        Node<T> current = head;
        while (current.next != null && !current.next.data.equals(data)) {
            current = current.next;
        }

        // If the value was found, skip the node
        if (current.next != null) {
            current.next = current.next.next;
        }
    }

    // Get the size of the linked list
    public int size() {
        int count = 0;
        Node<T> current = head;
        while (current != null) {
            count++;
            current = current.next;
        }
        return count;
    }

    // Check if the linked list contains a specific value
    public boolean contains(T data) {
        Node<T> current = head;
        while (current != null) {
            if (current.data.equals(data)) {
                return true;
            }
        }
        return false;
    }

    // Print all elements in the linked list
    public void printList() {
        Node<T> current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }
}
```