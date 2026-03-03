# 📝Bellman-Ford Algorithm

**음의 간선이 존재할 때** 특정한 하나의 정점에서 모든 정점으로의 최단 거리를 구하는 알고리즘  
다익스트라 알고리즘의 최적해를 항상 포함하지만, 음의 간선이 없을 때는 다익스트라 알고리즘이 더 빠르므로 사용을 지양

음의 간선을 무한히 통과하는 음수 사이클이 존재할 수 있음  
이 경우 반드시 음의 무한대의 가중치를 갖게 되므로, 최단 거리 배열에는 반영하지 않지만, 사이클의 존재 여부는 확인 가능

시간복잡도는 O(V * E)  
(V: 정점 수, E: 간선 수)

## 알고리즘

1. 각 노드들까지의 최단 거리 배열 dist를 무한대로 초기화
2. 시작 정점의 dist 값을 0으로 바꿈
3. 다음 과정을 N - 1 번 반복 (사이클이 없다면 최대 방문 가능한 간선 수는 N - 1)  
   3-1. 모든 간선을 하나씩 확인  
   3-2. 각 간선을 거쳐서 다른 노드로 가는 비용을 계산하여 dist를 갱신
4. 다시 모든 간선을 확인하며 더 짧은 경로가 있는지 확인 (더 짧은 경로가 있다면 음수 사이클이 존재한다는 의미)
5. 최종 dist 배열이 시작 정점에서부터 각 정점으로의 최단 거리, hasNegativeCycle은 음수 사이클의 존재 여부

```C#
public List<int> BellmanFord(List<(int weight, int node)>[] maps, int start, out bool hasNegativeCycle)
{
    int N = maps.Length;
    List<int> dist = new List<int>(new int[N]);    // 노드 개수만큼의 리스트 선언

    for (int i = 1; i < dist.Count; i++)
    {
        dist[i] = INF;        // 모든 정점까지의 거리를 무한대로 초기화
    }

    dist[start] = 0;

    // 사이클을 고려하지 않기 때문에 경로의 최대 방문 가능한 간선 수는 N-1이므로, N-1만큼만 반복
    for (int i = 0; i < N - 1; i++)
    {
        // 모든 간선을 확인
        for (int s = 0; s < N; s++)
        {
            foreach(var line in maps[s])
            {
                // 아직 도달하지 않은 출발지점은 스킵
                // 더 짧은 경로가 있으면 갱신
                if (dist[s] != INF && dist[s] + line.weight < dist[line.node])
                {
                    dist[line.node] = dist[s] + line.weight;
                }
            }
        }
    }

    // 한 번 더 모든 간선을 검사해서 음수 사이클이 존재하는지 확인
    hasNegativeCycle = false;
    for (int s = 0; s < N; s++)
    {
        foreach (var line in maps[s])
        {
            // 도달할 수 없는 출발지점은 스킵
            // 더 짧은 경로가 있으면 음수 사이클이 존재하는 것!
            if (dist[s] != INF && dist[s] + line.weight < dist[line.node])
            {
                hasNegativeCycle = true;
                break;
            }
        }
    }

    return dist;
}
```
