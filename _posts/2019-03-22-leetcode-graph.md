---
layout: article
title: Leetcode —「图」系列题解
tags: Leetcode

lang: zh-Hans
key: Leetcode_Graph
pageview: true
toc: true
show_subscribe: false
---

## 课程表

[Leetcode - 207 Course Schedule (Medium)](https://leetcode.com/problems/course-schedule/)

题目描述：选课时总共有 n 门课程，但是有些课程需要先修其它课程，给定课程数量与先修课程关系，判断是否能够将课程修完。

```
Input: 2, [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0, and to take course 0 you should
             also have finished course 1. So it is impossible.
```

解题思路：把每节课看成一个结点，这道题可以转换成求解有向图中是否有环，有环代表无法修完所有课程。判断是否有环可以使用拓扑排序的思想。

解法一：BFS 。依次从图中去除入度为 0 的结点并使与该结点相连的结点入度减一，如果最后没有额外的结点则无环，否则图中剩余的结点即为环。

```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
    ArrayList[] graph = new ArrayList[numCourses];
    int[] degree = new int[numCourses];
    Queue queue = new LinkedList();
    int count=0;
    
    for(int i=0;i<numCourses;i++)
        graph[i] = new ArrayList();
        
    for(int i=0; i<prerequisites.length;i++){
        degree[prerequisites[i][1]]++;
        graph[prerequisites[i][0]].add(prerequisites[i][1]);
    }
    for(int i=0; i<degree.length;i++){
        if(degree[i] == 0){
            queue.add(i);
            count++;
        }
    }
    
    while(queue.size() != 0){
        int course = (int)queue.poll();
        for(int i=0; i<graph[course].size();i++){
            int pointer = (int)graph[course].get(i);
            degree[pointer]--;
            if(degree[pointer] == 0){
                queue.add(pointer);
                count++;
            }
        }
    }
    if(count == numCourses)
        return true;
    else    
        return false;
}
```

解法二：DFS。依次通过 DFS 判断每个结点是否能走回来。

```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
    ArrayList[] graph = new ArrayList[numCourses];
    for(int i=0;i<numCourses;i++)
        graph[i] = new ArrayList();
        
    boolean[] visited = new boolean[numCourses];
    for(int i=0; i<prerequisites.length;i++){
        graph[prerequisites[i][1]].add(prerequisites[i][0]);
    }

    for(int i=0; i<numCourses; i++){
        if(!dfs(graph,visited,i))
            return false;
    }
    return true;
}

private boolean dfs(ArrayList[] graph, boolean[] visited, int course){
    if(visited[course])
        return false;
    else
        visited[course] = true;;

    for(int i=0; i<graph[course].size();i++){
        if(!dfs(graph,visited,(int)graph[course].get(i)))
            return false;
    }
    visited[course] = false;
    return true;
}
```