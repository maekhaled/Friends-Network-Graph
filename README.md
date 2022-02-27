# Friends
Rutgers CS112: Assignment 4

This project implements useful graph algorithms that apply to friendship graphs of the Facebook kind.

## Description

This application takes as input a text file representing a frienship graph and implements three useful algorithms. A friendship graph is an undirected graph without any weights on the edges. It is a simple graph because there are no self loops (a self loop is an edge from a vertex to itself), or multiple edges (a multiple edge means more than edge between a pair of vertices).

The vertices in the graphs for this assignment represent two kinds of people: students and non-students. Each vertex will store the name of the person. If the person is a student, the name of the school will also be stored.

Here's a sample friendship graph:

```
     (sam,rutgers)---(jane,rutgers)-----(bob,rutgers)   (sergei,rutgers)
                          |                 |             |
                          |                 |             |
                     (kaitlin,rutgers)   (samir)----(aparna,rutgers)
                          |                            |
                          |                            |
  (ming,penn state)----(nick,penn state)----(ricardo,penn state)
                          |
                          |
                     (heather,penn state)


                   (michele,cornell)----(rachel)
                          |
                          |
     (rich,ucla)---(tom,ucla)
```

Note that the graph may not be connected, as seen in this example in which there are two "islands" or cliques that are not connected to each other by any edge. Also see that all the vertices represent students with names of schools, except for `rachel` and `samir`, who are not students.

## Algorithms

1. **Shortest path: Intro chain**

   `sam` wants an intro to `aparna` through friends and friends of friends. There are two possible chains of intros:

   ```
   sam--jane--kaitlin--nick--ricardo--aparna

               or

   sam--jane--bob--samir--aparna
   ```

   The second chain is preferable since it is shorter.

   If `sam` wants to be introduced to `michele` through a chain of friends, he is out of luck since there is no chain that leads from `sam` to `michele` in the graph.

   Note that this algorithm does NOT have any restriction on the composition of the vertices: a vertex along the shortest chain need NOT be a student at a particular school, or even a student. In other words, this algorithm is not about students, let alone students at a particular school. So, for instance, you may need to find the shortest path (intro chain) from `nick` to `samir`, which will be:

   ```
   nick--ricardo--aparana--samir
   ```

   which consists of two penn state students, one rutgers student, and one non-student.

2. **Cliques: Student cliques at a school**

   Students tend to form cliques with their friends, which creates islands that do not connect with each other. If these cliques could be identified, particularly in the student population at a particular school, introductions could be made between people in different cliques to build larger networks of friendships at that school.

   In the sample graph, there are two cliques of students at rutgers:

   ```
   (sam,rutgers)---(jane,rutgers)-----(bob,rutgers)    (sergei,rutgers)
                        |                                |
                        |                                |
                  (kaitlin,rutgers)             (aparna,rutgers)
   ```

   Note that in the full graph these are not islands since `samir` connects them. However, since `samir` is not a student at rutgers, it results in two cliques of rutgers students that don't know each other through another rutgers student.

   At `penn state`, there is a single clique of students:

   ```
   (ming,penn state)----(nick,penn state)----(ricardo,penn state)
                          |
                          |
                     (heather,penn state)
   ```

   Also, a single clique of students at `ucla`:

   ```
   (rich,ucla)---(tom,ucla)
   ```

   And a single clique of students at `cornell`:

   ```
   (michele,cornell)
   ```

3. **Connectors: Friends who keep friends together**

   If `jane` were to leave rutgers, `sam` would no longer be able to connect with anyone else--`jane` was the "connector" who could pull `sam` in to hang out with her other friends. Similarly, aparna is a connector, since without her, sergei would not be able to "reach" anyone else. (And there are more connectors in the graph...)

   On the other hand, `samir` is not a connector. Even if he were to leave, everyone else could still "reach" whoever they could when `samir` was there, even though they may have to go through a longer chain.

   **Definition: In an undirected graph, vertex `v` is a connector if there are at least two other vertices `x` and `w` for which every path between `x` and `w` goes through `v`.**

   For example, `v=jane`, `x=sam`, and `w=bob`.

   Finding all connectors in an undirected graph can be done using DFS (depth-first search), by keeping track of two additional quantities for every vertex `v`. These are:

   Here are some examples that show how this works.

   -  Example 1: (`B` is a connector)

      ```
      A--B--C
      ```

   -  Example 2: (`B` is a connector)

      ```
      A--B--C
      ```
      
   -  Example 3: (`B` and `D` are connectors)

      ```
      A---B---C
      |   |
      E---D---F
      ```
      
   -  Example 4: (`B` and `D` are connectors)

      ```
      A---B---C
         |   |
         E---D---F
      ```


### Implementation

1. `shortestChain`

   -  Input: Name of person who wants the intro, and the name of the other person. For instance, inputs could be `"sam"` and `"aparna"` for the graph in the [`Background`](#background) section. (Neither of these, nor any of the intermediate people in the chain, are required to be students, in the same school or otherwise.)
   -  Result: The shortest chain (list) of people in the graph starting at the first and ending at the second, returned in an array list.

      For example, if the inputs are `sam` and `aparna` (`sam` wants an intro to `aparna`), then the shortest chain from `sam` to `aparna` is `[sam,jane,bob,samir,aparna]`

      (This represents the path `sam--jane--bob--samir--aparna`)

      **If there is more than one shortest path, ANY of them is acceptable.**

      If there is no way to get from the first person to the second person, then the returned list is empty (`null` or zero-sized array list).

2. `cliques`

   -  Input: Name of school for which cliques are to be found, e.g. `"rutgers"`
   -  Result: The names of people in each of the cliques, in any order, returned in an array list of array lists. Moreover, the cliques themselves could be in any order in the top level array list.

      For the example cited in the `Cliques` part of the [`Algorithms`](#algorithms) section above, with input `rutgers`, the result is:

      ```
      [[sam,jane,bob,kaitlin],[sergei,aparna]]
      ```

      In other words, an array list that has two cliques, each of which is an array list.

      The names in the clique array list can be in any order. So, the same cliques could have been returned as:

      ```
      [[jane,sam,kaitlin,bob],[aparna,sergei]]
      ```

      and it would be correct.

      The cliques themselves can be in any order within the top level array lists, so the following variation (for example) is also acceptable:

      ```
      [[sergei,aparna],[sam,jane,bob,kaitlin]]
      ```

      **However, names must not be repeated in a clique.**

      If there are no students in the input school, the result is empty (`null` or zero-sized array list).

3. `connectors`

   -  Input: None
   -  Result: Names of all connectors, in any order, returned in an array list. If there are no connectors, the result is empty (`null` or zero-sized array list).

      In the sample friendship graph of the [`Background`](#background) section, the connectors list is `[jane,aparna,nick,tom,michele]`. Any other permutation of the names in the list is fine, since the order does not matter.

      **Names in the list must not be repeated.**
	 
The example text file of the graph modeled in the Background is provided: sampletestcase.txt



All use of this code must comply with the [Rutgers University Code of Student Conduct](http://eden.rutgers.edu/%7Epmj34/media/AcademicIntegrity.pdf).
