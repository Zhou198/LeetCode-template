### BFS

```py
def bfs(graph):
    dq = deque([nodes in the first layer])
    visited = {ele for ele in [nodes in the first layer]}
    
    step = 0 ## record the first layer
    while dq:
        size = len(dq) ## the number of nodes in the current layer to be popped
        
        for _ in range(size):
            parent = dq.popleft()
            
            for child in graph[parent]:
                ## deal edge cases: 1) cross boundaries if the graph is a matrix; 2) visited nodes; 3) other conditions based on different problems
                if child in visited:
                    continue
                dq.append(child)
                visited.add(child)
        
        step += 1
```
BFS usually is used to solve the problems involved with the shortest distance, like those variants: the Dijkstra algorithm and the Topological Sorting.
- Sometimes we need to consider the edge case at the very beginning before creating **dq**, for example, like a $1\times1$ matrix. 
- If the graph is a matrix, usually we can override the matrix in-place by some special values to denote we visited the node.
- If the graph is a tree, we need not bother creating **visited**. 
