---
title: "Leetcode28"
date: 2019-06-02T05:31:06+08:00
archives: "2019"
tags: ['leetcode']
author: Mr King
---



#### Dijkstra 算法 PHP 实现

单源最短路径算法，核心思想为把起点到所有点的距离存在一个数组中，然后广度优先遍历每次触及距离最短的点，然后更新接触到的点与起点的距离，下面是算法图示:

![dijkstra](https://hurryking.github.io/img/dijkstra.gif)



下面是的我的PHP版本实现

```php
<?php
Class Vertex
{
    protected $id;
    protected $neighbors;
    protected $neighbor_weights;

    public function __construct($node)
    {
        $this->id = $node;
    }

    public function addNeighbor($vertex, int $weight)
    {
        $this->neighbors[] = $vertex;
        $this->neighbor_weights[$vertex] = $weight;
    }

    public function getNeighbors()
    {
        return $this->neighbors;
    }

    public function getId()
    {
        return $this->id;
    }

    public function getWeight($vertex)
    {
        return $this->neighbor_weights[$vertex];
    }
}

Class Graph
{
    protected $vertexes;
    protected $vertex_num;

    public function __construct()
    {
        $this->vertexes = [];
        $this->vertex_num = 0;
    }

    public function addVertex($node)
    {
        $vertex = new Vertex($node);
        $this->vertexes[$node] = $vertex;
        $this->vertex_num += 1;

        return $vertex;
    }

    public function getVertex($node)
    {
        return $this->vertexes[$node]?:NULL;
    }

    public function addEdge($from, $to, int $weight)
    {
        if (!array_key_exists($from, $this->vertexes)) {
            $this->addVertex($from);
        }

        if (!array_key_exists($to, $this->vertexes)) {
            $this->addVertex($to);
        }

        $this->vertexes[$from]->addNeighbor($to, $weight);
    }

    public function getVertexes()
    {
        return $this->vertexes;
    }

    public function findCirclesThroughVertex($vertex)
    {
        $circles = [];

        $this->dfs($vertex, $vertex, [], $circles);
        
        return $circles;
    }

    private function dfs($start, $vertex, $stack, &$circles)
    {
        $stack[] = $vertex;
        foreach ($this->getVertex($vertex)->getNeighbors() as $key => $value) {
            if (!in_array($value, $stack)) {
                $this->dfs($start, $value, $stack, $circles);
            } else {
                if ($start == $value) {
                    array_push($stack, $start);
                    $circles[] = $stack;
                }
            }
        }
    }


    public function findLinesPtp($start, $end, $deep)
    {
        $lineArr = [];
        $this->ptpDfs($start, $end, $sonDeep = 0, $deep, $stack = [], $lineArr);
        return $lineArr;
    }

    public function ptpDfs($start, $end, $sonDeep, $deep, $stack = [], &$lineArr)
    {
        var_dump($start . '->' . $end . ' deep:' . $sonDeep);
        $stack[] = $start;
        $sonDeep++;
        foreach ($this->getVertex($start)->getNeighbors() as $key => $value) {
            if ($sonDeep < $deep) {
                $this->ptpDfs($value, $end, $sonDeep, $deep, $stack, $lineArr);
            } elseif($value == $end && $sonDeep == $deep) {
                array_push($stack, $end);
                $lineArr[] = $stack;
            }
        }
    }

    /*
     * 最短路径 Dijkstra 算法（单源多路径算法）
     */
    public function findShortestPath($source, $destination)
    {
        // To do: 判断source 是否在集合里
        // 最短路径是否不存在
        $distances = [];
        $shortest_path = [$source];
        foreach ($this->getVertexes() as $id => $vertex) {
            $distances[$id] = $this->weightFromTwoVertex($source, $id);
        }

        $distances[$source] = 0;
        $S = [];
        $Q = array_keys($this->getVertexes());
        unset($Q[array_search($source, $Q)]);

        while ($Q) {
            $source = $this->ExtractMin($Q, $source);
            unset($Q[array_search($source, $Q)]);
            $S[] = $source;

            foreach ($this->getVertex($source)->getNeighbors() as $neighbor) {
                $multi_vertex_distance = $distances[$source] + $this->weightFromTwoVertex($source, $neighbor);
                if ($distances[$neighbor] > $multi_vertex_distance) {
                    $distances[$neighbor] = $multi_vertex_distance;
                    if ($neighbor == $destination) {
                        $shortest_path = array_merge($shortest_path, $S);
                    }
                }
            }
        }

        array_push($shortest_path, $destination);
        return $shortest_path;
    }

    private function weightFromTwoVertex($start, $end)
    {
        $neighbors = $this->getVertex($start)->getNeighbors();

        return in_array($end, $neighbors) ? $this->getVertex($start)->getWeight($end) : PHP_INT_MAX;
    }

    /**
     * 取出最近的点
     * @param [type] $Q         [description]
     * @param [type] $distances [description]
     */
    public function ExtractMin($Q, $source)
    {
        $min = PHP_INT_MAX;
        $vertex = $this->getVertex($source);
        $neighbors = $vertex->getNeighbors();
        $min_node = count($Q) == 1 ? array_pop($Q) : $source;
        foreach ($neighbors as $neighbor) {
            if ($vertex->getWeight($neighbor) < $min && in_array($neighbor, $Q)) {
                $min_node = $neighbor;
                $min = $vertex->getWeight($neighbor);
            }
        }

        // unset($Q[$min_node]);
        return $min_node;
    }
}


$graph = new Graph;
$graph->addEdge('A', 'B', 5);
$graph->addEdge('B', 'C', 4);
$graph->addEdge('C', 'D', 8);
$graph->addEdge('D', 'C', 8);
$graph->addEdge('D', 'E', 6);
$graph->addEdge('A', 'D', 5);
$graph->addEdge('C', 'E', 2);
$graph->addEdge('E', 'B', 3);
$graph->addEdge('A', 'E', 7);

// $neighbors = $graph->getVertex('A')->getNeighbors();
//$circles = $graph->findCirclesThroughVertex('C');

//var_dump($circles);

// A-C深度是4的路径
// $lines = $graph->atmostMaxLengthCircleCount($start = 'C', $end = 'C', $distance = 30);
// var_dump($lines);

// A-C最短路径
$shortest_path = $graph->findShortestPath('A', 'C');
var_dump($shortest_path);

```



折腾了一晚上，还是挺有成就感的，自己看wiki的简介然后实现，没有去看别人的代码。

这里有一个质量比较高的算法[讲解资料](<http://www-math.mit.edu/~rothvoss/18.304.3PM/Presentations/1-Melissa.pdf>)，推荐给看到的人。

>Simpliety is prerequisite of reliability. 	   ---- Edsger Dijkstra