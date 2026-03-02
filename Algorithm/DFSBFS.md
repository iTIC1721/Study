# 📝DFS / BFS

```
0
| \
1  2
  / \
 3   4
```

## DFS
깊이 우선 탐색  
한 정점에서 시작하여 분기를 하나 선택하고, 해당 분기를 완전히 탐색한 뒤 빠져나옴  
**Stack 사용**

```C#
public List<int> DFS(Dictionary<int, List<int>> graph, int start)
{
    List<int> visited = new List<int>();
    Stack<int> stack = new Stack<int>();

    // 시작 노드 추가
    stack.Push(start);

    // 탐색
    while (stack.Count > 0)
    {
        int node = stack.Pop();

        // 조건 확인 및 탈출 / 순서 기록
        visited.Add(node);

        // 인접 노드를 방문하며 스택에 추가
        List<int> neighbors = graph[node];
        for (int i = 0; i < neighbors.Count; i++)
        {
            // 방문하지 않은 경우만 스택에 추가
            if (!visited.Contains(neighbors[i]))
            {
                stack.Push(neighbors[i]);
            }
        }
    }

    return visited;
}
```

```
방문한 노드 순서:
0 2 4 3 1
```

## BFS
너비 우선 탐색  
한 정점에서 시작하여 깊이가 같은 모든 정점을 반복하여 탐색한 뒤 다음 깊이로 들어가 탐색을 진행  
**Queue 사용**

```C#
public List<int> BFS(Dictionary<int, List<int>> graph, int start)
{
    List<int> visited = new List<int>();
    Queue<int> queue = new Queue<int>();

    // 시작 노드 추가
    queue.Enqueue(start);

    // 탐색
    while (queue.Count > 0)
    {
        int node = queue.Dequeue();
        visited.Add(node);

        // 인접 노드를 방문하며 큐에 추가
        List<int> neighbors = graph[node];
        for (int i = 0; i < neighbors.Count; i++)
        {
            // 방문하지 않은 경우만 큐에 추가
            if (!visited.Contains(neighbors[i]))
            {
                queue.Enqueue(neighbors[i]);
            }
        }
    }

    return visited;
}
```

```
방문한 노드 순서:
0 1 2 3 4
```
