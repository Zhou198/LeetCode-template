### BFS

```py
def bfs(graph):
    dq = deque([nodes in the first layer])
    visited = {ele for ele in [nodes in the first layer]} ### immediately create visited after dq
    
    step = 0 ### record the first layer
    while dq:
        size = len(dq) ## the number of nodes in the current layer to be popped
        
        for _ in range(size):
            parent = dq.popleft()
            
            ### return step here if you want to
            
            for child in graph[parent]:
                ### deal edge cases: 1) cross boundaries if the graph is a matrix; 2) visited nodes; 3) other conditions based on different problems
                if child in visited:
                    continue
                dq.append(child)
                visited.add(child)
                
                ### return step + 1 here if you want to
        
        step += 1
```
BFS usually is used to <ins>**solve the problems involved with the shortest distance**</ins>, like those variants: the Dijkstra algorithm and the Topological Sorting.
- Sometimes we need to consider the edge case at the very beginning before creating **dq**, for example, like a 1 x 1 matrix. 
- If the graph is a matrix, usually we can override the matrix in-place by some special values to denote we visited the node.
- If the graph is a tree, we need not bother creating **visited**. 



### Union-Find/Disjoint set
```py
def numOfgroups(n, edges):
    parent = [i for i in range(n)]
    rank = [0] * n
    
    num = n
    for node1, node2 in edges:
        num -= union(node1, node2, parent, rank)
    return num

### return the root of node, and compress the path meanwhile
def find(node, parent):  
    if node != parent[node]:
        parent[node] = find(parent[node], parent)
    return parent[node]

def union(node1, node2, parent, rank):
    root1 = find(node1, parent)
    root2 = find(node2, parent)
    
    if root1 == root2:
        return 0 ### or we can custum the output based on the setting
    
    if rank[root1] > rank[root2]:
        parent[root2] = root1
    elif rank[root1] < rank[root2]:
        parent[root1] = root2
    else:
        parent[root1] = root2
        rank[root2] += 1
    return 1 ### or we can custum the output based on the setting
```
Union-Find is useful when we <ins>**want to determine how many groups**</ins> are in a graph. At the very beginning, we use **parent** to treat each node as a group itself (its parent is itself). Plus, we initialize **rank** to somehow measure the size of each group. 

For each pair in **edges**, we will union them together if they haven't been, accordingly, the number of group **num** decreases by 1 (otherwise 0). Finally, **num** tells how many groups there are.

- Union-Find is used to deal with an <ins>**undirected (bi-directional) graph**</ins>.
- Many times we need to construct **edges** by ourselves.
- **num < n** also denotes there is a loop in the graph
- Function **find** makes the height of each group (tree) is 2 at most, which makes it efficient when we call this function later. Use **find** (NOT **parent**) to return the representor (root) of each group.
- **rank** is another trick to improve efficiency further.



