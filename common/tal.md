----------
## Asymptotic growth of functions, time and space complexity of algorithms. Correctness of algorithms - variant and invariant.

Asymptotická složitost:
- Definice:
    - $f(n) \in O(g(n))$, pokud $\exists c > 0, \forall n \geq n_0: f(n) \leq c~g(n)$
    - $f(n) \in \Omega(g(n))$, pokud $\exists c > 0, \forall n \geq n_0: f(n) \geq c~g(n)$
    - $f(n) \in \Theta(g(n))$, pokud $\exists c_1 > 0, \exists c_2 > 0, \forall n \geq n_0: c_1~g(n) \leq f(n) \leq c_2~g(n)$
    - $f(n) \in o(g(n))$, pokud $\exists c > 0, \forall n \leq n_0: 0 \leq f(n) < c~g(n)$
    - $f(n) \in \omega(g(n))$, pokud $\exists c > 0, \forall n \geq n_0: 0 \leq c~g(n) < f(n)$
- Jak odhadnout:
    - $f(n) \in o(g(n)) \iff \lim_{n \to \infty} \frac{f(n)}{g(n)} = 0$ 
    - $f(n) \in \omega(g(n)) \iff \lim_{n \to \infty} \frac{f(n)}{g(n)} = \infty$

Odhad složitosti rekurzivní vztahů:
- 3 metody: Přímo, Stromy rekurze, Master Theorem.
- *Master Theorem*: Předpokládejme, že funkce $f$ splňuje: $f(n) = a \cdot f\left(\frac{n}{b}\right) + cn^d$ ($f$ je nezáporná, neklesající funkce) na množině $\{b^k ; k \in \mathbb{N}\}$ a platí: $b \in \mathbb{N}, a, c \in \mathbb{R}, d \in \mathbb{N}_0$, které splňují $a \geq 1, b \geq 2, c \geq 0$. Pak platí:
    - Jestliže $d < \log_b(a)$ nebo $c = 0$, pak $f(n) \in \Theta(n^{\log_b(a)})$.
    - Jestliže $d = \log_b(a)$, pak $f(n) \in \Theta(n^d \log_2(n)))$.
    - Jestliže $d > \log_b(a)$, pak $f(n) \in \Theta(n^d)$.

Správnost algoritmu:
1. Na každém vstupu se zastaví (konečný).
    - *Variant* - hodnota udána přirozeným číslem, která se během práce algoritmu snižuje až nabude nejmenší možnou hodnotu (a tím zaručuje ukončení algoritmu po konečně mnoha krocích).
2. Po každém zastavení vydá správné řešení (správný).
    - *Invarinat* - tvrzení, které:
        - platí před vykonáním prvního cyklu algoritmu, nebo po prvním vykonání cyklu,
        - platí-li před vykonáním cyklu, platí i po jeho vykonání,
        - při ukončení práce algoritmu zaručuje správnost řešení.

----------
## Deterministic Turing machines, multitape Turing machines, and Nondeterministic Turing machines.

Definice Turingova stroje zabere věčnost a práce s TM je zdlouhavá a nudná. Řešení úlohy většinou lze vyjádřit programem, který se potom může přepsat do TM. Pro podporu tohoto tvrzení se dá odkázat na Church-Turing Thesis:
> Everything computable is computable by a Turing machine.

*Definice TM*: Turingův stroj je sedmice $(Q, \Sigma, \Gamma, \delta, q_0, B, F)$, kde
- $Q$ je konečná množina stavů,
- $\Sigma$ je konečná množina vstupních symbolů,
- $\Gamma$ je konečná množina páskových symbolů a platí $\Sigma \subset \Gamma$,
- $B$ je konečná prázdný symbol (blank), je to páskový symbol, který není vstupním symbolem,
- $\delta: (Q \setminus F) \times \Gamma \to Q \times \Gamma \times \{L, R\}$ je přechodová funkce,
- $q_0 \in Q$ je počáteční stav,
- $F \subseteq Q$ je množina koncových stavů.

Jazyk
- TM se *úspěšně zastaví*, pokud skončí v koncovém stavu.
- *TM $M$ příjímá slovo $w \in \Sigma^\star$*, pokud se $M$ na slově $w$ úspěšně zastaví.
- *Jazyk $L(M)$ příjímaný TM $M$* je množina všech slov $w$, které $M$ příjímá.

Realizace zobrazení
- *Výstupem TM* je obsah pásky po zastavení TM nad vstupním slovem $w$.
- TM $M$ realizuje funkci $f: \Sigma^\star \to \Sigma^\star$, pokud pro každé slovo $w$, pro které je $f(w)$ definován:
    1. $M$ se úspěšně zastaví,
    2. výstupem $M$ je $f(w)$.

Jazyk příjímaný a rozhodnováný TM:
- *Jazyk je příjímán* nějakým TM, pokud existuje nějaký TM, který ho vytvoří.
- *Jazyk je rozhodován* TM, pokud tento TM jazyk příjímá a navíc se na každém vstupu úspěšně zastaví.
    - jazyk je rozhodován $\implies$ jazyk je příjímán

*Definice TM s $k$ páskami* - stejná jako jednopáskový TM, ale:
- Stoj má navíc $k$ pásek a $k$ hlav.
- Přechodová funkce se změní na: $\delta: (Q \setminus F) \times \Gamma^k \to Q \times \Gamma^k \times \{L, R\}^k$.
- Při vstupu je vstupní slovo na první pásce, ostatní pásky obshují $B$, řídící jednotka je ve stavu $q_0$ a hlava čte první symbol vstupního slova.
- V jednom kroku se řídí jednotka posune do dalšího stavu, každá hlava přepíše obsah pole, které čte a posune se (nezávisle na ostatních hlavách).
- Ke každému TM s $k$ páskami $M_1$ existuje TM s jednou páskou $M_2$, který má stejné chování jako $M_1$.
    - Jestliže $M_1$ potřeboval $n$ kroků k úspěšnému zastavení, pak $M_2$ potřebuje $O(n^2)$ kroků.

*Definice nedeterministického TM* - stejná jako jednopáskový TM, ale:
- Přechodová funkce se změní na: $\delta: (Q \setminus F) \times \Gamma \to P(Q \times \Gamma \times \{L, R\})$, kde $P(X)$ je konečná podmnožina množiny $X$.
- Je to jako mít funkci `guess()`, která správně odhadne nejlepší branch.
- Triviálně: jazyk je příjímán NTM $\implies$ jazyk je příjímán TM.

----------
## Decision problems and languages. Complexity classes P, NP, co-NP. Reduction and polynomial reduction, class NPC. Cook theorem. Heuristics and approximate algorithms for solving NP complete problems.

Třídy složitosti:
- $\mathbf{DTIME}(f(n)) = \{L; L ~\text{může být rozhodnut TM v}~ O(f(n)) ~\text{krocích pro vstup velikosti}~n\}$
- $\mathbf{P} = \bigcup_{c \in \mathbb{N}} \mathbf{DTIME}(n^c)$
- $L \in \mathbf{NP}$, pokud existuje nějaká relace $R \subseteq \Sigma^\star \times \Sigma^\star$ a polynomiální funkce $p: \mathbb{R} \to \mathbb{R}$ tak, že platí zároveň:
    1. $x \in L \iff \exists y: |y| \leq p(|x|), (x,y) \in R$
    2. $R \in \mathbf{P}$.
    - Znamená to, že pokud dostanu vstup $x$, jeho správnost řešení nebo důkaz $y$ lze ověřit v polynomiálním čase.
    - Alternativní definicí může být, že $L$ řeší nějaký NTM.
- $L \in \text{co-}\mathbf{C}$ pokud jeho doplněk $\hat{L}\in C$.

Redukce
- *Karp redukce*: $L \leq_{Karp} L'$, pokud existuje polynomiálně časová funkce $g: \Sigma^\star \to \Sigma^\star$, že platí: $x \in L \iff g(x) \in L'$.
    - Pro redukci, musí se najít funkce $g$ a ukázat, že má polynomiální čas.
    - Ukázat oběma směry, že pokud vstup $x$ je v jazyku $L$, pak je $g(x)$ v jazyku $L'$.
- (Cook redukce: $L \leq_{Cook} L'$, pokud existuje orákulum $M$ s polynomiální časovou náročností, které s použitím indikátorové funkce $\chi_{L'}$ vrací $M^{\chi_{L'}} = 1 \iff x \in L$.)
- *NP-hard*: $L$ je NP-těžký, pokud $\forall L' \in \mathbf{NP}: L' \leq_{\text{Karp}} L$
- *NP-complete*: $L$ je NP-úplný, pokud je NP-těžký a $L \in \mathbf{NP}$.
- *Cook-Levinova věta*: SAT je NP-úplný.
    - To vede ke stromu redukcí NP-complete úloh až na kořenový SAT:
![](img/npc_web.jpg)

Otevřené:
- $\mathbf{P} \neq \mathbf{NP}$?

----------
## Classes based on space complexity: PSPACE and NPSPACE. Savitch Theorem.

Třídy složitosti:
- $\mathbf{SPACE}(s(n)) = \{L; L ~\text{může být rozhodnut TM, jehož hlava navštíví}~ O(s(n)) ~\text{pozic pro jakýkoliv vstup velikosti}~n\}$
- $\mathbf{NSPACE}(s(n)) = \{L; L ~\text{může být rozhodnut nedeterministickým TM, jehož hlava navštíví}~ O(s(n)) ~\text{pozic pro jakýkoliv vstup velikosti}~n\}$
- $\mathbf{PSPACE} = \bigcup_{c \in \mathbb{N}} \mathbf{SPACE}(n^c)$ 
- $\mathbf{NPSPACE} = \bigcup_{c \in \mathbb{N}} \mathbf{NSPACE}(n^c)$
- $\mathbf{L} = \mathbf{SPACE}(\log n)$
- $\mathbf{NL} = \mathbf{NSPACE}(\log n)$

Proč NL a L? -> Úloha streamování
- Obrovský vstup je možné přečíst jen jednou a nejde ho celý dát do paměti.
- I kdybychom měli polynomiální paměť, může hrozit, že paměť dojde a zasekne to počítač.
- Proto je rozumné předpokládat, že je k dispozici log paměti.

Redukce:
- *log redukce*: $A \leq_{\log} B$, pokud existuje logaritmicky paměťově náročná funkce $g: \Sigma^\star \to \Sigma^\star$, že platí: $x \in A \iff g(x) \in B$.
    - $A$, $B$ jsou rozhodovací úlohy.
- Podobně jako NP-úplnost se definuje NL-úplnost.
- DIRCONN - rozhodovací úloha, kde pro 2 vrcholy v orientovaném grafu chceme vědět, zda existuje cesta z vrcholu $s$ do $t$.
    - DIRCONN je NL-úplná.
    - UCONN je L-úplná (neorintovaný graf, jinak stejné jako v DIRCONN)

Platí:
- $\mathbf{L} \subseteq \mathbf{NL} \subseteq \mathbf{P}$
- $\mathbf{NP} \subseteq \mathbf{PSPACE}$
- *Savitchova věta*: $\mathbf{NL} \subseteq \mathbf{SPACE}(\log^2 n)$
    - Důsledkem je $\mathbf{NPSPACE} = \mathbf{PSPACE}$
    - Říká, že nedeterministický program používající paměť $s$ může být převeden na deterministický program používající paměť $O(s^2)$.
- $\mathbf{NL} = \text{co-}\mathbf{NL}$

Otevřené:
- $\mathbf{L} \neq \mathbf{NL}$?

-----------
## Randomized algorithms. Randomized Turing machines. Classes based on randomization: RP, ZPP, co-RP.

Randomizovaný Turingův stroj
- Stejný jako TM, ale obsahuje navíc pásku s $\{0,1\}$, oba mají pravděpodobnost 1/2.

Pravděpodobnostní třídy složitosti:
-  $L \in \mathbf{BPP}$, pokud existuje polynomiálně časově složitý algoritmus $A$ a polynomiální funkce $p$ tak, že $\forall x \in \Sigma^\star$ platí $$P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = \chi_L(x)] \geq \frac{2}{3},$$ kde $\chi_L$ je indikátorová funkce jazyka $L$.
    - Znamená to, že pokud $x \in L$, pak jsou tu alespoň 2/3 hodnot $r$, po kterých $A$ vrací 1, ale pokud $x \notin L$, pak jsou tu alespoň 2/3 hodnot $r$, po kterých $A$ vrací 0.
    - Třída reprezentuje algoritmy s oboustrannou chybou.
-  $L \in \mathbf{RP}$, pokud existuje polynomiálně časově složitý algoritmus $A$ a polynomiální funkce $p$ tak, že $$\begin{aligned}\forall x \in L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = 1] \geq \frac{1}{2} \\ \forall x \notin L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = 1] = 0\end{aligned}$$
    - Kdykoliv $A$ vrátí 1, jistě víme, že $x \in L$.
-  $L \in \text{co-}\mathbf{RP}$, pokud existuje polynomiálně časově složitý algoritmus $A$ a polynomiální funkce $p$ tak, že $$\begin{aligned}\forall X \in L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = 1] = 1 \\ \forall X \notin L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = 1] \geq \frac{1}{2}\end{aligned}$$
    - Kdykoliv $A$ vrátí 0, jistě víme, že $x \notin L$.

- $L \in \mathbf{ZPP}$, pokud existuje polynomiálně časově složitý algoritmus $A: \Sigma^\star \to \{0,1,\bot\}$ a polynomiální funkce $p$ tak, že $$\begin{aligned}\forall X \in L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = \bot] < \frac{1}{2} \\ \forall X \notin L,& P_{r \in \{0,1\}^{p(|x|)}}[A(x,r) = \chi_L(x)|A(x,r) \neq \bot] = 1\end{aligned}$$
    - $\bot$ znamená, že algoritmus neví zda $x \in L$ nebo $x \notin L$.
    - $A$ může vrátit $\bot$, v méně než 1/2 hodnot $r$, zatímco pokud $A$ nevrátí $\bot$, pak rozhodne vždy správně, zda $x \in L$ nebo $x \notin L$.

Platí:
- $\mathbf{RP} \subseteq \mathbf{NP}$
- $\mathbf{RP} \subseteq \mathbf{BPP}$, a tak $\text{co-}\mathbf{RP} \subseteq \text{co-}\mathbf{BPP} = \mathbf{BPP}$
- $\mathbf{RP} \cap \text{co-}\mathbf{RP} = \mathbf{ZPP}$
- $\mathbf{P} \subseteq \mathbf{ZPP}$

Otevřené:
- $\mathbf{P} = \mathbf{BPP}$?
    - Ví se, že nenastane zároveň $\mathbf{P} \neq \mathbf{NP}$ a $\mathbf{P} \neq \mathbf{BPP}$.

----------
## Decidability and undecidability. Recursive and recursively enumerable languages. Diagonal language. Universal language and Universal Turing machine.

Třídy složitosti
- *Rekursivní jazyk*: $L \in R$, pokud $L$ rozhoduje nějaký TM.
    - TM jazyk příjímá a na každém vstupu se úspěšně zastaví.
    - Existuje algoritmus, který dokáže určit, zda každé $w \in L$.
- *Rekursivně spočetný jazyk*: $L \in RE$, pokud existuje nějaký TM, který $L$ příjímá.
    - TM se pro každé slovo z $L$ úspěšně zastaví, ale pokud slovo nepatří do $L$, buď se zastaví úspěšně nebo nezastaví vůbec.
    - Existuje algoritmus, který brute-force hledá zda $w \in L$, pokud ano, příjímá, pokud ne, hledá...

Platí:
- $L \in R \implies \overline{L} \in R$
- $L \in RE \land \overline{L} \in RE \implies L \in R$
- Pro jazyk $L$ může nastat jedna z možností:
    - Oba $L, \overline{L} \in R$
    - Jeden z $L, \overline{L}$ je v $RE$ a druhý není.
    - Oba $L, \overline{L} \notin RE$.

**Diagonalizace**
- *Spočetnost*: Možina $X$ je spočetná, pokud existuje surjektivní zobrazení $f: \mathbb{N} \to X$.
    - $\Sigma^\star$ je spočetná množina.
    - $RE$ je spočetná.

Každý přehod TM lze zakódovat jako binární slovo jako $w = 0^i10^j10^k10^l10^r$ odpovídající $\delta(q_i, X_j) = (q_k, X_l, D_r)$
- $X_1 = 0, X_2 = 1, X_3 = B$
- $D_1 = L, D_2 = R$
- TM jsou spočetné.

*Diagonální jazyk* $L_d$ je jazyk, který který obshuje všechna slova, která neodpovídají kódům nějakého TM.
- Také říká, že exituje jazyk $L \notin RE$
- Důkaz: Pro $f: \Sigma^\star \to \{0,1\}$, vybereme různé vstupy $x_1, x_2, \dots$ z $RE$
|     |x_1|x_2|x_3|...|
|-----|:-:|:-:|:-:|:-:|
| f_1 | 1 | 0 | 1 |   |
| f_2 | 1 | 1 | 0 |   |
| f_3 | 0 | 1 | 0 |   |
- Pokud se flipnou čísla na diagonále, vznikne jazyk, který nepříjímá žádný TM zastoupený f_1, f_2, ...
- Sestavit tabulku a diagonalizaci lze udělat proto, že $RE$ i TM jsou spočetné.

*Univerzální jazyk* $L_{UN}$ je množina slov tvaru $<M>w$, kde $<M>$ je kód nějakého TM a $w$ je slovo takové, že $w \in L(M)$.
- Také říká, že exituje jazyk $L \in RE \setminus R$. (tímhle si nejsem jistý)
- Důkaz přes diagonalizaci:
    1. řádky odpovídají jazykům v $R$, sloupce všem kódům TM, které jsou v $R$.
    2. Pozice tabulky je vyhodnocení kódu turingova stroje jiným turingovým strojem.
    3. Protože jsou oba (množina jazyků i množina turingových strojů) v $R$, jsou spočetné a navíc každý turingův stroj se zastaví.
    4. Ale flipnutím diagonály vznikne nový jazyk, který žádný TM nemusí rozhodnout.
- Např. Halting problem, Postův korespondenční problém.