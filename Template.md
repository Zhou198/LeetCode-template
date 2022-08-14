### Backtracking -- Combination & Permutation
```py
class Combination:
    def combReturn(nums: list, k: int) -> int:
        nums.sort()
        path, result = [], []
        start = 0
        self.dfs(nums, k, path, result, start)
        return result
        
    def dfs(self, nums, k, path, result, start):
        if len(path) == k:
            result.append(path[:])
            return
            
        for i in range(start, len(nums)):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            path.append(nums[i])
            self.dfs(nums, k, path, result, i + 1)
            path.pop()
            
class Permutation:
    def permReturn(nums: list, k: int) -> int:
        nums.sort()
        path, result = [], []
        self.dfs(nums, k, path, result)
        return result
        
    def dfs(self, nums, k, path, result):
        if len(path) == k:
            result.append(path[:])
            return
            
        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            if nums[i] == "*":
                continue
                
            path.append(nums[i])
            temp, nums[i] = nums[i], "*"
            self.dfs(nums, k, path, result)
            nums[i] = temp
            path.pop()
```
- Backtracking is a brute force way to find all possible results.
- Different from the permutation, combinations (or a set) do not consider the order in the outcome. So we use **start** to avoid duplicated outcomes (like [1, 2] and [2, 1]). The **start** means that, under current result **path**, we only consider to add elements from **start** in **nums**. For example, when **path=[2]**, then it can be extended to **[2, 3], [2, 4], ...** due to 3 > 2. However, since permutation does take the order into account, the search space always starts from the very beginning index 0 in permutation, and hence we do not have to create **start**.
- Since the permutation searches from the very beginning every time, to avoid adding the value with the same index again, we need to use lines 38 -- 39, 42, and 44 to achieve this goal. However, in combination, we needn't bother it since we search for **nums[i + 1 : ....]** (see i + 1 in dfs) when we have **nums[i]** in the current **path**. 
- Lines 5 and 17 -- 18 (resp. lines 25, 36 -- 37) are used to deal with the duplications in input **nums**: let duplications appear together and skip those duplications we visited before.


### Topological Sorting
```py
### This actually is LeetCode 207 -- Course Schedule

def circleDet(edges):
    graph = defaultdict(list)
    inDegree = defaultdict(int)
    
    for node1, node2 in edges:  
        ### node2: parent; node1: child
        graph[node2].append(node1)
        inDegree[node2] = 0
        inDegree[node1] += 1
        
    n = len(inDegree)
    count = 0
    dq = deque([])
    for key, val in inDegree.items():
        if val == 0:
            dq.append(key)
            count += 1
    
    while dq:
        parent = dq.popleft()
        for child in graph[parent]:
            inDegree[child] -= 1
            if inDegree[child] == 0:
                dq.append(child)
                count += 1
    
    return n == count  ### true if no circle, false otherwise
```
Topological Sorting (BFS) usually is used to <ins>**solve the problems involved with node dependency or circle detecting**</ins>.
- Key things are to 1) build a graph (usually we are given edges), 2) build a map to record inDegree (or outDegree in some different problems), and 3) create a deque to store all nodes with 0 degrees.
- Topological Sorting's idea is working on a node with 0 indegrees (from the boundary of the graph to the center) and gradually decreasing the indegree for inner layers.
- The map in Degree can be realized by a list instead of a hashmap if nodes are encoded as 0, 1, ..., n - 1.
- Suppose the number of edges and nodes are |E| and |V|, respectively. Then the time complexity is O(|E| + |V|) and the space complexity is O(|E| + |V|).

### Dijkstra
```py
def smallestWghSum(edges, source):
    graph = defaultdict(list)
    for node1, node2, wgh in enumerate(edges):
        graph[node1].append([node2, wgh])
        graph[node2].append([node1, wgh])
    n = len(graph)
        
    gDist2Node = [inf] * n
    gDist2Node[source] = 0
    
    pq = [(wgh, source)]
    heapq.heapify(pq)
    
    while pq:
        lDist2Parent, parent = heapq.heappop(pq)
        
        ### if lDist2Parent > gDist2Node[parent]:
        ###     continue
            
        for child, wgh in graph[node]:
            lDist2Child = lDist2Parent + wgh
            
            if lDist2Child < gDist2Node[child]:
                gDist2Node[child] = lDist2Child
                heapq.heappush(pq, (lDist2Child, child))
    return gDist2Node
```
Dijkstra is applied on the <ins>**weighted bi-directional graph**</ins>. It is one variant of BFS to calculate the smallest weighted steps </ins>**from a single source to target(s)**</ins>.
Dijkstra is a greedy algorithm, where everytime the algorithm picks a current/local smallest weight sum. To gaurantee every time we start off a node with smallest weight sum from target, we use a priority queue to dynamically store those locally optimal roots.




### Union-Find / Disjoint set
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

