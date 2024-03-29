# Graphs

A graph is a non-linear data-structure for describing relationships between `nodes (vertices)` via `edges`.

A `node (vertex)` is a single value, and an `edge` is the connection between those values (nodes).

Ex. In Instagram, every `user` is a `node`, and every `follow` is an `edge`. Instagram is a `directed` graph i.e. follows go one way, while Facebook is `undirected` as a friendship goes both ways.

Ex. In Google Maps, every intersection is a `node`, and every road is a `weighted edge` (distance).

![Graphs](../pics/graphTheory/graphTheory_graphs.jpg)

# Adjacency list

The best way to represent graphs programatically is with the `hash map` data structure i.e. an `object`.

```js
let graph = {
    a: ["b", "c"], // neighbors
    b: ["d"],
    c: ["e"],
    d: [],
    e: ["b"],
    f: ["d"],
};
```

**weighted**

```js
let weightedGraph = {
    a: { b: 1, c: 2 },
    b: { d: 3 },
    c: { e: 4 },
    d: {},
    e: { b: 5 },
    f: { d: 6 },
};
```

# Traversal algorithms

They show if we can travel to some node.

![Traversal](../pics/graphTheory/graphTheory_traversal.jpg)

## Depth First Search (DFS)

Exploring one direction as far as possible before switching directions.

Best data structure: **Stack** (FILO)

![Depth traversal](../pics/graphTheory/graphTheory_traversal_depth.jpg)

### Iterative

```js
function depthFirst(graph, source) {
    const stact = [source];

    while (stack.length > 0) {
        const current = stack.pop();
        console.log(current);

        for (let neighbor of graph[current]) {
            stack.push(neighbor);
        }
    }
}

depthFirst(graph, "a"); // abdfce
```

### Recursive

```js
function depthFirst(graph, source) {
    console.log(source);
    for (let neighbor of graph[source]) {
        depthFirst(graph, neighbor);
    }
}

depthFirst(graph, "a"); // abdfce
```

## Breadth First Search (BFS)

Explore all directions evenly.

Best data structure: **Queue** (FIFO)

![Breadth](../pics/graphTheory/graphTheory_traversal_breadth.jpg)

### Iterative

```js
function breadthFirst(graph, source) {
    const queue = [source];

    while (queue.length > 0) {
        const current = queue.shift();
        console.log(current);

        for (let neighbor of graph[current]) {
            queue.push(neighbor);
        }
    }
}

depthFirst(graph, "a"); // abcedf
```

## Dijkstra (weighted)

Specialized for shortest-path finding in weighted graphs.

```js
function dijkstra(graph, startNode) {
    const distances = {};
    const parents = {};
    const visited = new Set();

    // Initialize distances and parents
    for (const node in graph) {
        distances[node] = Infinity;
        parents[node] = null;
    }
    distances[startNode] = 0;

    let currentNode = startNode;

    while (currentNode) {
        // Mark the current node as visited
        visited.add(currentNode);

        // Update distances to each neighbor
        for (const neighbor in graph[currentNode]) {
            if (!visited.has(neighbor)) {
                const newDistance = distances[currentNode] + graph[currentNode][neighbor];
                if (newDistance < distances[neighbor]) {
                    distances[neighbor] = newDistance;
                    parents[neighbor] = currentNode;
                }
            }
        }

        // Find the next node to process
        currentNode = null;
        let smallestDistance = Infinity;
        for (const node in distances) {
            if (!visited.has(node) && distances[node] < smallestDistance) {
                smallestDistance = distances[node];
                currentNode = node;
            }
        }
    }

    return { distances, parents };
}

// Example usage:
const graph = {
    a: { b: 1, c: 4 },
    b: { a: 1, d: 2, e: 3 },
    c: { a: 4, e: 1 },
    d: { b: 2, f: 5 },
    e: { b: 3, c: 1, f: 1 },
    f: { d: 5, e: 1 },
};

const startNode = "a";
const result = dijkstra(graph, startNode);
console.log(result);
```

# Airports example

```js
// Data
const airports = "PHX BKK OKC JFK LAX MEX EZE HEL LOS LAP LIM".split(" ");

const routes = [
    ["PHX", "LAX"],
    ["PHX", "JFK"],
    ["JFK", "OKC"],
    ["JFK", "HEL"],
    ["JFK", "LOS"],
    ["MEX", "LAX"],
    ["MEX", "BKK"],
    ["MEX", "LIM"],
    ["MEX", "EZE"],
    ["LIM", "BKK"],
];

// Graph
const adjacencyList = new Map();

// Add node
function addNode(airport) {
    adjacencyList.set(airport, []);
}

// Add edge, undirected
function addEdge(origin, destination) {
    adjacencyList.get(origin).push(destination);
    adjacencyList.get(destination).push(origin);
}

// Poplating the graph
airports.forEach(addNode);
routes.forEach((route) => addEdge(...route));

console.log(adjacencyList);

/*

Map(11) {
    'PHX' => [ 'LAX', 'JFK' ],
    'BKK' => [ 'MEX', 'LIM' ],
    'OKC' => [ 'JFK' ],
    'JFK' => [ 'PHX', 'OKC', 'HEL', 'LOS' ],
    'LAX' => [ 'PHX', 'MEX' ],
    'MEX' => [ 'LAX', 'BKK', 'LIM', 'EZE' ],
    'EZE' => [ 'MEX' ],
    'HEL' => [ 'JFK' ],
    'LOS' => [ 'JFK' ],
    'LAP' => [],
    'LIM' => [ 'MEX', 'BKK' ]
}

*/

// Find all possible routes to determine most efficient one
function breadthFirstSearch(startingAirport, destinationAirport) {
    let visited = new Set();
    let queue = [startingAirport];

    while (queue.length > 0) {
        console.log("queue:", queue);
        let airport = queue.shift(); // mutates queue // 'PHX'

        let destinations = adjacencyList.get(airport); // [ 'LAX', 'JFK' ]

        console.log("airport:", airport, "-----> destinations:", destinations);
        console.log("-------------------------");

        for (let destination of destinations) {
            // 'BKK' == 'BKK'
            if (destination == destinationAirport) {
                console.log("\n");
                console.log(`********************************************`);
                console.log(`Found ${destinationAirport} via ${airport}`);
                console.log(`********************************************`);
                console.log("\n");
            }

            if (!visited.has(destination)) {
                visited.add(destination); // { 'LAX' }, { 'LAX', 'JFK' }

                queue.push(destination); // 'LAX'
                console.log("            ADD", destination, ">", queue);
            } else {
                console.log("ALREADY VISITED", destination);
            }
        }
        console.log(`\n`);
    }
}

breadthFirstSearch("PHX", "BKK");
// PHX > LAX > MEX > LIM > BKK
// PHX > LAX > MEX > BKK

// Find the first route
function depthFirstSearch(startingAirport, destinationAirport, visited = new Set()) {
    console.log(startingAirport);

    visited.add(startingAirport);

    let destinations = adjacencyList.get(startingAirport);

    for (let destination of destinations) {
        if (destination == destinationAirport) {
            console.log("found");
            return;
        }

        if (!visited.has(destination)) {
            depthFirstSearch(destination, destinationAirport, visited);
        }
    }
}

depthFirstSearch("PHX", "BKK");
```
