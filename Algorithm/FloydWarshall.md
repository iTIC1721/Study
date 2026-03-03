# 📝Floyd-Warshall Algorithm

**모든 정점에서 모든 정점으로의 최단 거리**를 구하는 알고리즘

3중 반복문을 이용하여 구현하기 때문에 시간복잡도는 O(n^3)  
반드시 필요한 경우에만 사용하는 것이 성능상 유리

## 알고리즘

1. 각 노드들까지의 최단 거리 배열 dist를 무한대로 초기화, 같은 노드로 이동하는 경우엔 0으로 초기화
2. 모든 정점을 순회하며, start에서 end로 갈 때 inter를 거쳐서 가는 것이 더 빠르다면 최단 거리 갱신

```C#
static int N;
static int[][] dist;

static void FloydWarshall()
{
    // start에서 end로 갈 때, inter를 거쳐서 가는 것이 더 빠르다면 갱신
    for (int start = 0; start < N; start++)
        for (int end = 0; end < N; end++)
            for (int inter = 0; inter < N; inter++)
                dist[start][end] = Math.Min(dist[start][end], dist[start][inter] + dist[inter][end]);
}
```
