---
title: Mesh顶点的连接性
date: 2025-1-22 16:18:51
categories:
- Unity
tags:
- 短篇
- C#
- 工具
toc: true
---

盘问GPT榨出来的代码，测过了，非常好用。

输入一个Mesh，返回一个和Vertices等长的Int列表，代表Vertex属于Mesh中各分离的Parts的哪一块。

```c#
public static List<int> GetConnectedComponents(Mesh mesh)
{
    Vector3[] vertices = mesh.vertices;
    int[] triangles = mesh.triangles;

    // Step 1: Map unique vertex positions to indices
    Dictionary<Vector3, int> uniqueVertexMap = new Dictionary<Vector3, int>();
    int[] uniqueIndices = new int[vertices.Length];
    int uniqueIndexCounter = 0;

    for (int i = 0; i < vertices.Length; i++)
    {
        if (!uniqueVertexMap.ContainsKey(vertices[i]))
        {
            uniqueVertexMap[vertices[i]] = uniqueIndexCounter++;
        }
        uniqueIndices[i] = uniqueVertexMap[vertices[i]];
    }

    // Step 2: Build adjacency list based on unique indices
    Dictionary<int, List<int>> adjacencyList = new Dictionary<int, List<int>>();

    for (int i = 0; i < triangles.Length; i += 3)
    {
        int[] triUniqueIndices = {
            uniqueIndices[triangles[i]],
            uniqueIndices[triangles[i + 1]],
            uniqueIndices[triangles[i + 2]]
        };

        for (int j = 0; j < 3; j++)
        {
            int v0 = triUniqueIndices[j];
            int v1 = triUniqueIndices[(j + 1) % 3];

            if (!adjacencyList.ContainsKey(v0))
            {
                adjacencyList[v0] = new List<int>();
            }
            if (!adjacencyList.ContainsKey(v1))
            {
                adjacencyList[v1] = new List<int>();
            }

            if (!adjacencyList[v0].Contains(v1))
            {
                adjacencyList[v0].Add(v1);
            }
            if (!adjacencyList[v1].Contains(v0))
            {
                adjacencyList[v1].Add(v0);
            }
        }
    }

    // Step 3: Use DFS to find connected components
    int[] componentIDs = new int[uniqueVertexMap.Count];
    bool[] visited = new bool[uniqueVertexMap.Count];
    int componentID = 0;

    void DFS(int v)
    {
        Stack<int> stack = new Stack<int>();
        stack.Push(v);

        while (stack.Count > 0)
        {
            int current = stack.Pop();
            if (!visited[current])
            {
                visited[current] = true;
                componentIDs[current] = componentID;
                foreach (int neighbor in adjacencyList[current])
                {
                    if (!visited[neighbor])
                    {
                        stack.Push(neighbor);
                    }
                }
            }
        }
    }

    for (int i = 0; i < uniqueVertexMap.Count; i++)
    {
        if (!visited[i])
        {
            DFS(i);
            componentID++;
        }
    }

    // Map back to original vertices
    List<int> vertexClasses = new List<int>(new int[vertices.Length]);
    for (int i = 0; i < vertices.Length; i++)
    {
        vertexClasses[i] = componentIDs[uniqueIndices[i]];
    }

    return vertexClasses;
}
```

