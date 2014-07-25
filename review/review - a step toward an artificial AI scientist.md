last updated : 4 juillet 2014


# notes dans l'ordre de lecture

## § 1.1

Simon Colton HR system
R+ : ... using only seven general rules, which are the same for all of the domains ...

Bruce Buchanan and Gary Livingston (HAMB) :
R- : "peruses large collections of data"
R+ : "domain-independent heuristics to guide the program's choice of relationships"

R+ : "in the description of a solver for Sokoban problems [10], the steps that lead to a successful program are also given"

## §1.2
R+ : Two kinds of meta-problems are defined and solved as constraint satisfaction problems: finding symmetries, which are variables permutations, in the formulation of a problem and generating new problems.

R- : In that case, only a part of the system has been implemented: on how to find interesting data, but still not on how to use them

R+ : CAIA works from first principles
C  : est-ce vraiment le cas ? les méthodes sont déjà du hors "first principles"

## § 1.3

four agents: MALICE, MONITOR, MANAGER, ADVISOR, ZEUS
TODO : diagramme d'architecture

- MALICE cleverly tries to solve a problem
- MONITOR, which monitors the behavior of MALICE
  MONITOR takes an emergency action to correct it, for instance it forbids to use a particularly explosive derivation rule.
- MANAGER chooses which task takes priority, it allocates some means to this task, it possibly authorizes MONITOR, if required, to exceed them.
- ADVISOR, is at the highest level. It assesses MANAGER's behavior and regularly checks that it is always performing interesting tasks.

A fifth agent, ZEUS, is not in the hierarchy, it is regularly called by the interrupt mechanism. It checks that the four preceding agents function adequately and do not loop. If that happens, it restarts MANAGER after storing the characteristics of its difficulties.

R+ : solving problems given in a natural language and
R- : improving the theorem proving abilities of CAIA.

## § 2

Gap entre description en langue naturelle et la description formelle donnée.

Il serait intéressant d'avoir la représentation de :
F(1)+F(2)+F(3)+F(4)+F(5)+F(6)+F(7)+F(8)+F(9) = VAL
=> cela soulève plein de problème. Comment valide-t-on que le système comprend la représention comme nous la comprenons. qu'est-ce que comprendre ?
=> Ici les concepts dont le système doit avoir conscience sont :
- la notion de nommage ("F")
- la notion de fonction
- la notion de variable
- la notion de contrainte
- la notion de probleme (ensemble de contraintes + variable)

The only information received by CAIA for this problem is that it must find the value of VAL and define a bijection F between two sets, which are the integers from 1 to 27, so that the 15 constraints are satisfied.
=> donner la formulation donnée au système.

R+ : MANAGER begins to look for possible symmetries: among all of the different kinds of symmetries, it is only able to find permutations of the unknowns such as the set of constraints remains the same. This search has been defined as a constraint satisfaction problem and MALICE solves it using the same methods as with the main problem.

L'exemple est trop compliqué. Impossible de refaire un programme à partir de cette description. C'est plutot un exemple qui montre les capacités du système et le type de relations entre les différents acteurs. J'aurai préféré un exemple beaucoup plus simple mais qui donne une idée d'implémentation au lecteur.

## § 3

knowledge is given either as sets of conditional actions gathered in what we call MACISTE expertises or in a completely declarative form.

For each task, there is an expertise which executes this task
=> comment identifier une tâche ?

conditionnal actions : if all of the conditions of a conditional action are true, its actions must be executed

?? thus, it is easy to define MACISTE expertises which transform the declarative knowledge into new MACISTE expertises

Then MACISTE translates these last expertises into efficient C programs.

=> besoin d'un exemple de transformation d'une expertise en pseudo-code

R- : Secondly, the user can ask it (MACISTE) to start CAIA

R+ : the user is not obligated to know this basic representation because some functions enable him to work on expressions without actually using their internal representation.

A MACISTE expression may be a function with its arguments (which are also expressions), an integer, a character, an object, a variable, the attribute of an expression whose value must be an object, a set such as [X1:X2] (which is the set of integers between I1, value of expression X1, and I2, value of expression X2)
=> j'ai l'impression que cela limite ce que l'on peu exprimer. Ou plutot que cela force l'utilisateur à representer au préalable le problème dans une "bonne" représentation"

R+ : *Z indicates that the variable Z must be instantiated

je ne vois pas comment conceptualiser MATCH(X,SUM(\*Z,\*Z,5,\*\*)), expertise ?
implémentation possible par def recursive :

<pre>
Match([5,T,Y+1,T], [*Z,*Z,5,**]) => Z=5; Match([T,Y+1,T], [5,5,**]) => nothing can match 5 => KO
                                 => Z=T; Match([5,Y+1,T], [T,5,**]) => T=T; Match([5,Y+1], [5,**]) => 5=5; Match([Y+1], [**]) => ok
                                 => Z=Y+1, Match([5,T,T], [Y+1,5,**]) => Y+1 pas de match => KO
                                 => Z=T ? encore ?
</pre>

# §4

## Problem description language

MACISTE : SUM(F('A'), PRD(4, F('M'))) est équivalent à A+4*M

<pre>WITH F(I)=XNBVAL(I; F(J); J e ELEMENTS) FOR I e VALUES</pre>

pourquoi pas :
<pre>
FOR I e VALUES, WITH F(I) = NBVAL(I; For J e ELEMENTS, F(J))
=> 'For J e ELEMENTS, F(J)' devient avec ELEMENTS = {0,1,2,3}:
   F(0), F(1), F(2), F(3)
=> WITH FOR I e VALUES, F(I) = NBVAL(I; F(0), F(1), F(2), F(3)) avec VALUES = {0,1,2}
=> WITH [F(0) = NBVAL(0; F(0), F(1), F(2), F(3)),
         F(1) = NBVAL(1; F(0), F(1), F(2), F(3)),
         F(2) = NBVAL(0; F(0), F(1), F(2), F(3)) ]
</pre>

**TODO**
<pre>
(defProblemFamilly AUTOREF
   {:given [(constant P)
            (constant N)
            (set ELEMENTS [0 (inc P)])
            (set VALUES   [0 P]) ]
    :find  [(function F [ELEMENTS VALUES])]
    :with  [(= (F (inc P)) N)
            (= (F I) ...) ]})

... = XNBVAL(I; F(J); J e ELEMENTS) FOR I e VALUES

(defProblem AUTOREF-1 {:familly AUTOREF, :P 9, :N 3})
</pre>

Ce que j'aimerai à la place :
<pre>WITH F(I)=Card( Domain( RestrictRange( F, {I} )))</pre>

Avantage : plus déclaratif.
Inconvénient : je ne sais pas comment le rendre opérationnel

### Syntaxe du langage de définition des familles de problèmes

#### 1. Parameters

<pre>
GIVEN CONSTANT name
      SET name = expression
      ARRAY name E1->E2
      
expression = [X1:X2] ; range
           = [V1,V2, ..., VN] ; def par extension. Vi un entier ou un caractère ou un paramètre précédemment défini.
E1 : a set parameter
</pre>

#### 2. Correspondences
<pre>
FIND type-correspondence name-correspondence E1->E2
</pre>

type-correspondence e {FUNCTION, BIJECTION, INJECTION, SURJECTION, ...}

POSSIBLE/CERTAIN links between departure(D) and arrival(A) for each 

When all the values of the elements of the departure set using CERTAIN links satisfy all of the constraints, the problem is solved.

#### 3. algebraic constraints
<pre>
WITH X FOR C1, C2,..., Cn
</pre>

#### 4. others constraints
<pre>
KNOWING GRAPH F(I)=X
KNOWING INCOMPATIBILITY F(I)=X
</pre>

R- : From now on, I will not give the definition of families of problems as I have done for AUTOREF because this general formalism is not alway easy to understand for someone who is not accustomed to it. For a new problem, I will often give only the constraints such as they are generated by MALICE from the definition of its family of problems.
=> cela aide à concrétiser

R- : However, without them, we would have to write as many definitions as there are problems and give all the constraints successively for each problem instead of giving a general definition. For some families of problems, one single generator can generate more than one thousand constraints.
=> utilisation de langage de programmation bien adapté à ce genre d'action. Purement fonctionnel, réutilisable.

TODO : constraints generator

### §5 MALICE solver
p14

TODO : diagram de control à faire

R- : I only mention that it uses 120 rewriting rules to simplify and normalize expressions such as X*1:=X;

#### MALICE rules

1. variables declaration :
set of couples: (type-variable name)
type-variable e {CONSTRAINT, DEPARTURE, ARRIVAL, CORRESPONDENCE)

2. set of conditions
condition is a MACISTE expression

3. kind of actions
CONSTRAINT X, where X is an expression, creates a new constraint which is the value of expression X.
LINK ND NA creates a CERTAIN link between node ND from a departure set to node NA from an arrival set.
UNLINK ND NA removes a POSSIBLE link between ND and NA.
VALUE ATT X V gives the value V to the attribute ATT of object X: VALUE AMI CP 1 indicates that the value of the minimum degree of the arrival set of correspondence CP is now 1.
CONTRADICTION indicates that there is a contradiction.
ELIMINATE X eliminates the constraint that is the value of expression X.


TODO : expliciter et questionner sur le point ci-dessous :

##### R4: contrainte générale
mail envoyé à Pitrat :
"Rule R4 is particularly useful for bijections, where the set of departure nodes has the same cardinality as the set of arrival nodes, so the value of every departure node occurs exactly once in the arrival set."

==> ok

"Thus the sum of the values of the departure nodes is equal to the sum of the elements of the arrival set. In a more general form (AMI and AMA are equal, but may be greater than 1), we have:
Declaration: CORRESPONDENCE : CP
Condition:   DMI(CP)=1, DMA(CP)=1, AMI(CP)=AMA(CP)
Action:      CONSTRAINT SIGMA(VALUE(D); D e DEPARTURE(CP)) = AMI(CP)*SIGMA(VALUE(A); A e ARRIVAL(CP))

VALUE(D) is the expression representing node D: for the departure node corresponding to the character 'B' of the correspondence F, VALUE(D) is F('B'); for the arrival node A associated with integer 4, VALUE(A) is the expression representing the integer 4."

==> J'ai l'impression que la définition de VALUE(D) n'est pas celle utilisée dans MACISTE (2 définitions différentes suivant l'objet sur laquelle elle s'applique (A ou B)). J'aurai plutôt vu la définition suivante :

Action:      CONSTRAINT SIGMA(CP(D); D e DEPARTURE(CP)) = AMI(CP)*SIGMA(A; A e ARRIVAL(CP))

==> dans le cas plus spécifique d'une bijection :

Declaration: CORRESPONDENCE : CP
Condition:   DMI(CP)=1, DMA(CP)=1, AMI(CP)=AMA(CP)=1
(C1) Action :      CONSTRAINT SIGMA(CP(D); D e DEPARTURE(CP)) = SIGMA(A; A e ARRIVAL(CP))

==> Là je me pose la question de savoir pourquoi ce n'est pas la contrainte plus générale et plus informative ci-dessous qui est donnée au système :

(C2) Action:      CONSTRAINT BAG(CP(D), D e DEPARTURE(CP)) = BAG(A, A e ARRIVAL(CP))


Si le système possède aussi la contrainte (C3) : Bag1 = Bag2 => SIGMA(Bag1) = SIGMA(Bag2), on retrouve bien la contrainte C1. 

De plus, Si le système possède aussi la contrainte (C4) : Bag1 = Bag2 => PI(Bag1) = PI(Bag2), PI symbolisant le concept de produit, le système pourrait créer la contrainte :
(C5) Action :      CONSTRAINT PI(CP(D), D e DEPARTURE(CP)) = PI(A, A e ARRIVAL(CP))



"With this rule, MALICE has a global vision of the problem: it is very helpful for finding the common value of the lines or of the planes in a magic square or cube. In a 3x3 magic square, there is a bijection S between the integers
from 1 to 9 on themselves, so DMI=DMA=AMI=AMA=1. Thus rule R4 generates the constraint:
S(1)+S(2)+S(3)+S(4)+S(5)+S(6)+S(7)+S(8)+S(9) = 45"

==> Cette contrainte est moins informative que (bag S(1), S(2), S(3), S(4), S(5), S(6), S(7), S(8), S(9)) = (bag 1,2,3,4,5,6,7,8,9)


##### R5: idem contrainte générale
qq soit F, (BagRange F) = Union((RestrictBag= Y (BagRange F)), Y e (ARRIVAL F))


























