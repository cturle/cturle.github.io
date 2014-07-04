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

je ne vois pas comment conceptualiser MATCH(X,SUM(*Z,*Z,5,**)), expertise ?
implémentation possible par def recursive :
Match([5,T,Y+1,T], [*Z,*Z,5,**]) => Z=5; Match([T,Y+1,T], [5,5,**]) => nothing can match 5 => KO
                                 => Z=T; Match([5,Y+1,T], [T,5,**]) => T=T; Match([5,Y+1], [5,**]) => 5=5; Match([Y+1], [**]) => ok
                                 => Z=Y+1, Match([5,T,T], [Y+1,5,**]) => Y+1 pas de match => KO
                                 => Z=T ? encore ?












































