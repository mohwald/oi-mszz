----------
## Notation of asymptotic complexity of algorithms. Basic notation of graph problems - degree, path, circuit, cycle. Graph representations by adjacency, distance, Laplacian and incidence matrices. Adjacency list representation.

Asymptotická náročnost
- Viz [[tal#Asymptotic growth of functions, time and space complexity of algorithms. Correctness of algorithms - variant and invariant.]]

Grafy
- Graf je dvojice $(V, E)$, kde $V$ je množina vrcholů, a $E$ je množina hran.
- Hrana je:
    - Neorientovaná $\{x,y\}$
    - Neorientovaná $(x,y)$
    - Vážená - s cenou $w: E \to \mathbb{R}$
- Stupeň vrcholu - $\delta_+$ počet výstupních hran vrcholu, $\delta_-$ počet vstupních hran vrcholu
- Cesta - posloupnost vrchol - hrana - vrchol ... vrchol, kde se hrany i vrcholy neopakují.
- Circuit (nevím česky) - uzavřená posloupnost vrchol - hrana - vrchol ... vrchol, kde se hrany neopakují, ale vrcholy se mohou opakovat.
- Cyklus - uzavřená cesta, kde se opakuje jen první (poslední) vrchol.
- Strom - spojený graf bez cyklů.

Reprezentace:
- **Matice sousednosti** (Adjacency matrix)
    - $a_{i,j} = 1 \iff \{v_, v_j\} \in E, ~\text{otherwise}~ a_{i,j} = 0$ 
    - ![](adjmat.jpg) ![](adjmat_g.jpg)
- **Matice vzdáleností** (Distance matrix)
    - Stejná, jako matice sousednosti, ale na pozice dává ceny hran.
- **Laplacian matrix**
    - $l_{i,j} = \text{deg}(v_i)$ pro $i=j$
    - $l_{i,j} = -1$ pro $\{v_i, v_j\} \in E$
    - $l_{i,j} = 0$ otherwise
    - ![](laplacianmat.jpg) ![](laplacianmat_g.jpg)
- **Matice incidence**
    - Řádky -> vrcholy, Sloupce -> hrany.
    - Na pozici i,j je 1, pokud pokud hrana j vstupuje do vrcholu i, -1, pokud pokud hrana j vystupuje z vrcholu i, jinak 0
    - ![](incmat.jpg) ![](incmat_g.jpg)
    - Může být výhodná, pokud má graf málo hran.
- **List sousednosti** (Adjacency list)
    - Každý vrchol si udržuje linked list jeho hran.
    - Obecně nejvýhodnější representace - zabírá O(|V|+|E|) paměti a procházení sousedů zabere $deg(v)$ času.

----------
## Algorithms for minimum spanning tree (Prim-Jarník, Kruskal, Borůvka), strongly connected components (Kosaraju-Sharir, Tarjan), Euler trail. Union-find problem. Graph isomorphism, tree isomorphism.

Minimální kostra
- Kostra - podgraf grafu, který obsahuje všechny vrcholy grafu.
- Minimální kostra - kostra ohodnoceného grafu s minimálním součtem cen.
- **Jarník-Primův algoritmus**:
    1. Vybere libovolný vrchol.
    2. Dokud nemá podgraf stejný počet hran jako graf:
        1. Vybere nejmenší hranu k vrcholu, který není ještě v podrafu.
        2. Přidá hranu do podrafu.
    - Má $|V|$ iterací.
    - Čas: O(|V|.|E|) naivně, technicky s Fibbunačiho haldou O(|E| +|V| log |V|) = O(|V| log |V|)
- **Borůvkův algoritmus**:
    1. K = (V, None)
    2. Dokud má K alespoň 2 komponenty souvislosti:
        1. Pro každou komponentu souvislosti přidá nejemenší incidenční hranu.
    - Každá iterace zabere O(|E|), protože pro každou hranu stačí najít, ke které komponentě patří a uloží se nejmenší hrana.
    - Čas: O(|E| log |V|).
- **Kruskalův (hladový) algoritmus**:
    1. Seřadí hrany podle ceny.
    2. Provede |V|-1 krát:
        1. Přidá nejmenší dosud nevybranou hranu do kostry, pokud neutvoří cyklus.
    - Ohlídání cyklů se nejrychleji naimplementuje přes *union-find strukturu*:
        - Každý vrchol má přiřazený "boss" vrchol. Na začátku je bossem každý vrchol sám sobě.
        - *find(u)*: vrátí bosse vrcholu.
            - *Path compression* - každému vrcholu dá rank, který udává hloubku vrcholů, co se na něj odkazují.
        - *union(u, v)*: provede x = find(u), y = find(v)
            - Pokud x == y, skončí (v Kruskalově alg. by utvořil cyklus)
            - Pokud x != y, nastaví všechny bosse vrcholů menšího podgrafu na bosse většího (s path compression je toto konstatně složitá operace).
    - Čas: O(|E| log |V|), a dokonce s implementací U-F zabere $O(\alpha |V|)$, kde $\alpha$ je inverse Ackermann function.

Komponenta silné souvislosti
- Pro orinteovaný graf, je to množina vrcholů, kde pro každou dvojici vrcholů existuje cesta tam i zpět.
- Vrcholy mají 3 stavy: unvisited, open, closed.
- Pro fungování algoritmů [slidy](https://cw.fel.cvut.cz/old/_media/courses/a4m33pal/2012pal03.pdf)
- *Kosaraju-Sharir algoritmus*
    1. Vytvoří prázdný zásobník S
    2. Dokud neobsahuje S všechny hrany:
        1. Vybere libovolnou hranu, která není v S a provede DFS průchod.
        2. Ve chvíli kdy vrchol přejde do closed (je opuštěn), vloží se do S.
    3. Přehodí orientaci hran.
    4. Dokud není S prázdný:
        1. Vyndá vrchol z S a provede na něj DFS průchod. (Pokud už je vrchol v nějaké komponentě, continue)
        2. Všechny vrcholy takto prošlé tvoří jednu komponentu silné souvislosti.
    - Čas: $\Theta(|V| + |E|)$ - list sousednosti, $O(|V|^2)$ - matice sousednosti
- *Tarjanův algoritmus*
    - Každý vrchol v průběhu dostane 2 proměnné: `p`, `z` 
    1. i = 0, provede DFS.
    2. V průchodu pro každý vrchol nastanou tyto možnosti:
        - *"vpřed"*: `i += 1`, pro vrchol: `p = i; z = i;`
        - *"nahlédnutí"*:
            - do neuzavřené komponenty:
                - máme vrchol `x` nahlížející na vrchol `y` a jejich odpovídající proměnné dvojice `(p_x, z_x)` a `(p_y, z_y)`
                - `z_x = min(z_x, p_y)`
            - do uzavřené komponenty:
                - nic.
        - *"couvání"*:
            - pokud `p == z`:
                - Uzavřít komponentu (provede pop ze zásobníku, dokud se nedostane na vrchol, ze kterého se couvá)
            - pokud `p != z`:
                - máme vrchol `x` couvající z vrcholu `y` a jejich odpovídající proměnné dvojice `(p_x, z_x)` a `(p_y, z_y)`
                - `z_x = min(z_x, z_y)`
    - Čas: $\Theta(|V| + |E|)$, a prakticky rychlejší, než Kosaraju-Sharir, protože provede 1 průchod.

Eulerův tah
- Průchod neorinetovaným grafem bez opakování hran (obtáhnutí jedním tahem)
- Platí, že orientovaný graf má eulerovský tah $\iff$ 0 nebo 2 vrcholy mají lichý stupeň.
```
trail(u, graph):
    stack = new Stack()
    for v in succ(u):
        graph.remove(u,v)
        trail(v, graph)
        stack.push(v,u)
```

Izomorfismus
- 2 grafy jsou izomorfní, pokud existuje bijekce $f, \forall x,y \in V_1: \{x,y\} \in E_1 \iff \{f(x), f(y)\} \in E_2$
- *Invariant*
    - Funkce $\Phi$ s doménou prostoru grafů, která: $G_1 ~\text{je izomorfní s}~ G_2 \implies \Psi(G_1) = \Psi(G_2)$
    - Např. $\Psi(G) = |V|$, setříděná posloupnost stupňů hran, ...
- *Certifikát*
    - Funkce $Cert$ s doménou prostoru grafů, která: $G_1 ~\text{je izomorfní s}~ G_2 \iff Cert(G_1) = Cert(G_2)$
    - Pro stromy lze spočítat binárním zápisem takto:
        1. Každému vrcholu přiřadí 01
        2. Od listů do přeposlední vrstvy vloží certifikáty všech potomků mezi 0 1.
        - ![](cert1.jpg) ![](cert2.jpg)
        - (Od kořene, 0 reprezentuje vpřed, 1 reprezentuje couvání v DFS průchodu.)
    - Izomofismus stromů lze jednoznačně určit certifikáty.

----------
## Generation and enumeration of combinatorial objects - subsets, k-element subsets, permutations. Gray codes. Prime numbers, sieve of Eratosthenes. Pseudorandom numbers properties. Linear congruential generator.

**Rank**
- Máme množinu $M$ velikosti $n$ nějakých obtížných objektů. Chceme s nimi pracovat jednodušeji - např. indexovat v poli,...
- *Rank* je bijekce $M \leftrightarrow \{0, \dots, n-1\}$
- Charakteristický vektor - nějak seřadí všechny možné prvky na pozice a dá na pozici 1, pokud je v $M$, jinak 0
    - Např. pokud tímto reprezentujeme měření napětí, tak v 255-256V se změní 11111111-100000000.

**Grayovy kódy** - po seřazení stavů stavů se při přechodu charakterický vektor změní jen na 1 pozici.
- Sestaví se rekurzivně takto:
    - ![](graycode.jpg)
    - Další velikost se na konci symetricky překlopí a na začátku se první půlka vyplní 0 a druhá 1.
- Převody:
    - G_5(28) = 10010, protože 28_2 = 11100, a postupně sčítá n-tý a n+1 řád:
        - 1 = 0+1
        - 0 = 1+1
        - 0 = 1+1
        - 1 = 1+0
        - 0 = 0+0
    - Inverze grayova kódu do bin zápisu jde sčítáním:
        - 1 = 1
        - 1 = 1 + 0
        - 1 = 1 + 0 + 0
        - 0 = 1 + 0 + 0 + 1
        - 0 = 1 + 0 + 0 + 1 + 0
- Existují i jiné definice.

*Rank permutace*:
- $Rank(3,1,0,2) = 3 \cdot 3! + Rank(1,0,2) = 3 \cdot 3! + 1 \cdot 2! + Rank(0,2) = 3 \cdot 3! + 1 \cdot 2! + 0 \cdot 1! + Rank(2) = 3 \cdot 3! + 1 \cdot 2! + 0 \cdot 1! + 0 = 20$
- $Unrank(15, \{0,1,2,3\}) = Unrank(3, \{0,1,3\}) = Unrank(1, \{0,3\}) = Unrank(0, \{0\}) \implies (2, 1, 3, 0)$
    - $15 - 2 \cdot 3! = 3$
    - $3 - 1 \cdot 2! = 1$
    - $1 - 1 \cdot 1! = 0$

*Rank $k$-prvkové podmnožiny z $n$ prvků*:
- Vybírají se podmnožiny, nezáleží na pořadí, a tak v rekuzi nebude faktoriál, ale jen kombinační číslo bez faktoriálu.
- \*Pokud n=5, k=3, tak
    - Počet podmnožin začínajících 0 je $\binom{4}{2}$, protože vybíráme 2 prvky z množiny $\{1,2,3,4\}$, což je 6
    - Počet podmnožin začínajících 1 je $\binom{3}{2}$, protože vybíráme 2 prvky z množiny $\{2,3,4\}$, což je 3
- $Rank(\{0,3,4\}, \{0,1,2,3,4\}) = 0 + Rank(\{3,4\}, \{1,2,3,4\}) = 0 + \binom{3}{1} + \binom{2}{1} + Rank(\{4\}, \{4\}) = 0 + 3 + 2 + 0$
    - V každém kroku se snaží spočítat, kolik je podmnožin před následujícím prvkem:
        - Před 0 není žádná podmnožina
        - Před 3 jsou 2 výběry s podmnožinami: $(1, \{2,3,4\}), (2, \{3,4\})$
- $Unrank(8, \{0,1,2,3,4\}) = Unrank(2, \{2,3,4\}) = Unrank(0, \{4\}) \implies (1,3,4)$
    - Viz \*

**Prvočísla**
- Test prvočíselnosti: viz [[mkr#Primes and their properties, generating random primes. Probabilistic primality testing, Fermat test, Miller-Rabin test - basic principles and error probability.]]
- Generování seznamu prvočísel: *Eratostenovo síto*:
    1. Vytvoří pole o velikosti $n$.
    2. Pro $p = 1 \dots \sqrt{n}$ vyhazuje násobky $p$ v poli.

**Geneorvání pseudonáhodných čísel**
- Funkce, která dostane vstup $x_n$, vytvoří $x_{n+1}$, které může sloužit jako další vstup.
- *Lehmerův generátor*: $x_{n+1} = a \cdot x_n ~\text{mod}~ m$
- *Lineární kongruentní generátor*: $x_{n+1} = a \cdot x_n + c ~\text{mod}~ m$
- Požadované vlastnosti:
    - Rovnoměrné rozdělení
    - Může vrátit všechny zbytky po dělení $m$; pro to musí platit:
        - $c,m$ nesoudělné
        - $p | m \implies p | (a-1), \forall p$
        - $4 | m \implies 4 | (a-1)$
- Fisher-Yates shuffle: O(n)
```
def shuffle(a):
    for i = n-1 ... 1:
        j = randint(0, i)
        swap(a[i], a[j])
```

----------
## Search trees - data structures, operations, and their complexities. Binary tree, AVL tree, red-black tree (RB-tree), B-tree and B+ tree, splay tree, k-d tree. Nearest neighbor searching in k-d trees. Skip list.

Pro insert, delete, find, viz nahrané přednášky:
- [Skip list, Vyhledávací stromy: B, B+.](https://bbb04.felk.cvut.cz//playback/presentation/2.0/playback.html?meetingId=abe762e743b8d10935d8ee032415ec79fd3efd5b-1606902601680)
- [Vyhledávací stromy: 2-3-4, R-B, splay.](https://bbb04.felk.cvut.cz//playback/presentation/2.0/playback.html?meetingId=3a9c2e63053ac3a7105dae6655a4388c256f6301-1607507401053)

**BST** -> Složitost:
- Find, insert, delete: O(log n), pokud není vyvážený.
- Find, insert, delete: O(n), pokud není vyvážený.

**AVL strom**
- Provádí rotace pro vyvážení:
    - Nahoru doprava: R rotace (podobně pro L)
    - Nahoru doleva, pak doprava: LR rotace (podobně pro RL)
    - Po insertu je po vyvážení strom ok, ale *po delete se musí ohlídat všechny vrcholy až do kořene*.
- Čas: find $O(\log n)$, insert $\Theta(\log n)$, delete $\Theta(\log n)$.

**RB strom**
- Má tyto vlastnosti:
    - Listy jsou černé.
    - Červený vrchol má černé potomky.
    - Každá cesta z kořene do listu má stejný počet černých vrcholů.
    - Kořen je černý.
- Čas: find $O(\log n)$, insert $\Theta(\log n)$, delete $\Theta(\log n)$.
    - Prakticky rychlejší, než AVL.

**B strom**
- k -> kolik minimálně klíčů musí být v 1 uzlu (kromě kořene)
- kapacita uzlu = 2k
- *Multiphase strategy* - je lazy při štěpení uzlu, při insert/delete ohledává kapacity od kořene k listu nahoru i dolu.
- *Single-phase strategy* - štěpí uzly preventivně, při insert/delete ohledává kapacity od kořene k listu jen dolu.

**B+ strom**
- Všechny data má v listech, v jednotlivých úrovních se nachází routeři.
- Při insertu v zaplněném listovém uzlu propaguje *kopii mediánu*, jinak funguje stejně, jako B strom
- Při delete se musí zkontrolovat, zda mazaná hodnota není jen o 1 hladinu výše jako router.
    - Pro navigaci mohou exisatovat routeři, kteří nejsou data.
- Umožňuje rychlý range search.
- Čas: $\Theta(b \log_b n)$ pro find, insert, delete

**Splay strom**
- Splayování: cílem je posunout uzel o úroveň výše, celkem až do kořene.
    - *Zig* - stejné jako L, R rotace AVL stromu.
    - *Zig-Zig* - stejné jako LL nebo RR rotace AVL stromu *(Bacha! Rotuje vždy v nejvyšším uzlu, to není jak AVL.)*
    - *Zig-Zag*- stejné jako LR nebo RL rotace AVL stromu.
- Insert: vloží uzel (podle BVS pravdla) a pak ho splayue do kořene.
- Find: najde uzel a pak ho splayue do kořene.
- Delete: najde uzel, pak ho splayue do kořene, vymaže kořen a vysplayue největší hodnotu v levém podstromu.
- Čas: nejhoší O(n), amortizovaně $\Theta(\log n)$
- Jednoduchá implementace, podobné vlastnosti AVL, ale mění strom už při čtení (prakticky nevhodné pro flasky).

**Skip list**
- Jako linked list, ale uzel má skoky o více hladin dále.
- Aby šel udržet, jak určit hladiny?
    - p=0.5 pro každý další level
        - ![](skiplist1.jpg)
    - p=0.25 experimentálně nejlepší
- Find, insert, delete: očekávaně O(log n), není to amortizovaně.

----------
## Finite automata, regular expressions, operations over regular languages. Bit representation of nondeterministic finite automata. Text search algorithms - exact pattern matching, approximate pattern matching (Hamming and Levenshtein distance), dictionary automata.

Pojmy:
- *Abeceda* - množina symbolů
- *Slovo* - posloupnost symbolů nad abecedou
- *Jazyk* - množina slov
    - Popat lze: 1. literálně, 2. slovně, 3. automatem
    - Operace nad jazyky jsou uzavřeny na:
        - Průnik
        - Sjednocení
        - Zřetězení
        - Kleeneho operaci
        - Doplňek
- *Automat* $(\Sigma, Q, q_0, F, \delta)$
    - Pro DFA: $\delta: Q \times \Sigma \to Q$
    - Pro NFA: $\delta: Q \times \Sigma \to P(Q)$
    - Přechod DFA -> NFA

**Hamming distance**
- je to počet náhrad znaků, které potřebujeme ze stringu $a$ do stringu $b$.

**Levenshtein distance**
- je to počet náhrad, odebrání nebo přidání znaků, které potřebujeme ze stringu $a$ do stringu $b$.

Zajímá nás nejkratší počet operací.
- Pro slovo "patt" lze sestavit automat n všechny Leventeinovy (Hammingovy) vzdálenosti takto:
    - ![](distautomata.jpg)
- Jsou to metriky, pro slova $u,v,w$ a vzdálenosti $d_1 = d(u,v), d_2 = d(v,w), d_3 = d(u,w)$ platí:
    - $d_3 \leq d_1 + d_2$
    - $d_3 \geq |d_1 - d_2|$
- Hammingova $\leq$ Levensteinova a Hammingova nemůže být větší, než Levensteinova.
    1. protože v "grafu" automatu hledáme nejktraší cestu
    2. ale Levensteinova pouze přidává další hrany, neubírá, takže není, jak zvětšit Hammingovu vzdálenost.

Výpočet Levensteinovy vzdálenosti (DP):
![](ld_example.jpg)
```
D[0, j] = j
D[i, 0] = i
for i = 1 .. n:
    for j = 1 .. m:
        if u[i] == v[j]:
            D[i, j] = D[i-1, j-1]
        else:
            D[i, j] = 1 + min(D[i-1, j], D[i, j-1], D[i-1, j-1])
```