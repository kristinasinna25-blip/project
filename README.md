import random
import time
class Graph:
    def __init__(self,n):
        self.n = n
        self.aij = []
        for x in range(n):
            self.aij.append(set())
        self.matrix = []
        for x in range(n):
            line = []
            for j in range(n):
                line.append(0)
            self.matrix.append(line)
    def plus_edge(self, u, v):  # додано неорієнтовне ребро
        if u != v:
            if u not in self.aij[v]:
                self.aij[v].add(u)
            if v not in self.aij[u]:
                self.aij[u].add(v)
            for i in range(self.n):
                for j in range(self.n):
                    if (i == u and j == v) or (j == u and i == v):
                        self.matrix[i][j] = 1
    def neighbors(self, v,): #множина сусідів вершини v
        return self.aij[v]
    def print_graph(self):
        print("граф(списки суміжності)")
        for i in range(self.n):
            print(f"{i}: {sorted(self.aij[i])}")
    def print_matrix(self):
        print("матриця суміжності")
        for line in self.matrix:
            print(line)
    def bron_kerbosch(self, R, P, X, click): #R-поточна кліка, Р-кандидати для розширення, Х-вже використанні
        if not P and not X:
            click.append(R)
            return
        for v in list(P):
            self.bron_kerbosch(
                R | {v}, #або
                P & self.neighbors(v),
                X & self.neighbors(v),
                click
            )
            P.remove(v)
            X.add(v)
    def main(self):
        result = []
        self.bron_kerbosch(set(),set(range(self.n)), set(), result)
        return result
    def random_graph( n, density):
        g = Graph(n)
        for i in range(n):
          for j  in range(i +1, n):
             if random.random() < density:
                 g.plus_edge(i, j)
        return g
    def max_click(self):
        clickes = self.main()
        return max(clickes, key = len)

sizes = [20, 50, 100, 150]#розмір графу
density = [0.1, 0.2, 0.3]#щільність
for s in sizes:
    for d in density:
      g = Graph.random_graph(s, d)
      print(g.print_matrix())
      print(g.print_graph())
      start = time.time()
      g.main()
      end_time = time.time()
      run_time = end_time - start
      print("максимальні кліки", g.main())
      for i, click in enumerate(g.main(), 1):
        print(f"{i}: {click} , розмір{len(click)}")
    print("найбільша", g.max_click())
    print("час виконання алгоритму:", run_time,"секунд")
