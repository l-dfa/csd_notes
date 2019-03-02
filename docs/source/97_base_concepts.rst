
.. meta::
   :language: it
   :description language=it: appunti di Complex Systems Design - Concetti di base
   :description language=en: notes on Complex Systems Design - Base concepts
   :keywords: Complex Systems Design
   :author: Luciano De Falco Alfano


.. index:: base concepts

.. _ref_base_concepts:

Base concepts
=======================

.. contents:: 
   :local:

.. index:: formal language

.. _ref_formal_language:

Formal language
--------------------

Da un punto di vista algebrico, un **linguaggio formale** (vedi [SAKH2019b]_) è una tupla di:

* un insieme finito di simboli (alfabeto), su cui è costruito il linguaggio;
* un insieme finito di regole di produzione, che specificano 
  quali sequenze dei simboli predetti fanno parte del linguaggio (sintassi).
  
Più in generale (vedi [SLKU1995]_ pag.1) la definizione di un linguaggio
richiede l'individuazione di tre componenti: 

* la **sintassi**, ovvero come combinare i simboli per creare frasi 
  *accettabili* (**nota**: accettabili, non significative) per il linguaggio;
* la **semantica** (vedi il :ref:`capitolo relativo <ref_semantics>`), che individua
  il significato associato alle frasi sintatticamente valide;
* la **pragmatica**, ovvero gli aspetti del linguaggio che coinvolgono
  l'utente; ad esempio l'accettabilità di una frase in relazione alla
  sensibilità dell'utente (si pensi al turpiloquio o alla blasfemia).
  
La *sintassi* è il primo aspetto da definire: non è possibile fare analisi
semantica (ovvero attribuire significati) alle frasi malformate. A questo scopo 
è necessario definire la :ref:`grammatica <ref_formal_grammar>` del linguaggio.
  
  
.. index:: formal grammar

.. _ref_formal_grammar:

Formal grammar
--------------------

Una grammatica formale (vedi [SLKU1995]_ pag.2) è una quadrupla :math:`< \Sigma, N, P, S >`
composta da:

* un insieme finito :math:`\Sigma` di **simboli terminali**, che formano l\ ``'``\ **alfabeto**
  del linguaggio, usato per comporre le frasi;
* un insieme finito :math:`N` di **simboli non terminali**, che formano le **categorie sintattiche**,
  ognuna delle quali rappresenta gruppi di componenti delle frasi;
* un insieme finito :math:`P` di **regole**, o **produzioni**, che descrivono le relazioni che 
  i simboli terminali o non terminali devono avere tra loro per comporre
  frasi del linguaggio;
* un **simbolo di partenza** :math:`S` che specifica quale categoria viene definita,
  ad esempio un programma, o una frase.

Se l'alfabeto include i simboli terminali del linguaggio in osservazione, il suo 
**vocabolario** include tutti i simboli: terminali e non terminali.

Usualmente i simboli *non terminali* sono indicati nella forma 
'\ <nome della categoria>'\ .

Mentre le regole di *produzione* solitamente sono nella forma:

  α ::= β 
  
La precedente indica che l'espressione α può essere sostituita dall'espressione β.

Ad esempio una *produzione* potrebbe essere:

    <dichiarazione> ::= **var** <lista di variabili> : <tipo>;
    
dove '\ **var**\ ', '\ **:**\ ' e '\ **;**\ ' sono simboli terminali del linguaggio.
Il simbolo '\ ::=\ ' è parte del metalinguaggio [#]_ ed indica che la *categoria*
(nota: è un *non terminale*) a sinistra può essere composta come indicato a destra.

In pratica la regola precedente recita: '\ una *dichiarazione* può essere formata
dalla parola chiave **var** seguita da una *lista di variabili*, seguito dal simbolo 
**:** (due punti), seguito dalla indicazione del *tipo*\ '.

Questo modo di indicare le *produzioni* è detto **forma di Backus-Naur**, spesso
abbreviato in **BNF**.

Questi concetti sono stati formalizzati originariamente da Noam Chomsky 
(vedi [CHOM1957]_).

Lo stesso N. Chomsky ha classificato le grammatiche secondo alcune caratteristiche 
delle loro regole, definendo le seguenti classi ([SLKU1995]_ pag.3, 4):

* *type 0* o grammatiche **senza restrizioni** (*unrestricted grammars*);
* *type 1*, grammatiche **sensibili al contesto** (*context-sensitive grammars*);
* *type 2*, grammatiche **libere dal contesto** (*context-free grammars*);
* *type 3*, grammatiche **regolari** (*regular grammars*).

Le grammatiche predette vanno dal tipo più generale (le *type 0*) a quello
via via più specifico: le *type 3* sono le più restrittive.


.. index:: unrestricted grammars

.. _ref_unrestricted_grammars:

Unrestricted grammars
------------------------

Sono le grammatiche di tipo più generale. L'unica imposizione è la presenza di 
almeno un elemento non terminale nel lato sinistro di una *produzione*
α ::= β. Ad esempio:

    a <qualcosa> b ::= b <qualcosaltro>
    
dove le lettere *a* e *b* sono terminali, mentre *<qualcosa>* e 
*<qualcosaltro>* sono non terminali.

  
.. index:: context-sensitive grammars

.. _ref_context-sensitive_grammars:

Context-sensitive grammars
-----------------------------

Se aggiungiamo la condizione che la parte destra della produzione
contenga un numero di simboli uguale o superiore al numero di 
simboli nella parte sinistra, abbiamo una *context-sensitive grammar*. Ad esempio:

    <qualcosa> b ::= b <qualcosa>
    
Si possono avere anche regole del tipo: :math:`'\alpha <\negthickspace B \negthickspace > \gamma ::= \alpha \beta \gamma'`

L'appellativo *context-sensitive* deriva dal fatto che la sostituzione di un simbolo non terminale
tramite la regola di produzione, dipende dai simboli circostanti.

  
.. index:: context-free grammars

.. _ref_context-free_grammars:

Context-free grammars
----------------------

In questo caso le regole di produzione a sinistra possono avere un singolo 
non terminale, con la forma  <A> :: = α, dove <A>  e α sono non terminali, ma 
α contiene almeno un terminale.

Ad esempio, può essere:

  <expr> ::= <expr> * <term>
 
dove il simbolo "*" è un terminale.

Queste grammatiche corrispondono alle grammatiche BNF.

  
.. index:: regular grammars

.. _ref_regular_grammars:

Regular grammars
-------------------

Queste sono le grammatiche più restrittive. Impongono di avere regole di 
produzione che a destra:

* hanno un *terminale*,
* oppure un *terminale* seguito da un *non terminale*.

cioè come segue:

* <A> ::= a ;
* oppure <A> ::= a <A>.


.. index:: semantics

.. _ref_semantics:

Semantics
-----------

Come accennato nei linguaggi formali, la semantica ([WIKI2019c]_) consiste nell'assegnare
un significato alle frasi sintatticamente valide.

Esistono fondamentalmente tre diversi approcci per effettuare questa operazione
in modo formale:

* usando gli *assiomi* (*axiomatic semantics* [WIKI2019d]_);
* usando le *denotazioni* (*denotational semantics* [WIKI2019e]_);
* usando l'induzione sulle operazioni (*operational semantics* [WIKI2019f]_), che a sua volta può
  essere suddivisa in due aree:

  * *structural operational semantics* ([WIKI2019g]_, [ACFV2005]_ e [PLOT1981]_), e
  * *natural semantics* ([WIKI2019h]_).

L'approccio **axiomatic semantics** definisce il significato di una frase tramite 
affermazioni logiche (formali), ovvero predicati con variabili.

La **denotational semantics** assegna alla frase dei costrutti matematici (ad es.
insiemistici) che ne esplicitano il significato. E\ ``'`` possibile osservare un
esempio di applicazione di *denotational semantics* alla :ref:`interpretazione
delle espressioni regolari <ref_r_e_by_denotational_semantics>`.

La *operational semantics* assegna alla frase delle regole di induzione dalle quali derivare
il significato. Se nel fare questa operazione, si opera su ogni singolo costituente del 
discorso, allora si usa la *structural operational semantics*; invece se si astrae dai singoli
costituenti, per arrivare direttamente al significato finale, si è applicata la *natural semantics*.
Si può osservare un esempio d'uso di *structural operational semantics* nel capitolo relativo alla 
:ref:`interpretazione del CCS tramite la SOS <ref_CCS_by_operational_semantics>`.


.. index:: deterministic finite automaton

.. _ref_deterministic_finite_automaton:

Deterministic finite automaton
--------------------------------

Un *automa a stati finiti deterministico*, è una macchina a stati finiti
che accetta una sequenza di simboli (comandi) e produce un calcolo univoco
per ogni sequenza di input ammessa.

Formalmente (vedi [WIKI2019a]_), è una quintupla :math:`M = (Q, \Sigma, \delta, q_0, F)`
dove:

* :math:`Q` è un insieme finito di stati;
* :math:`\Sigma` è un insieme finito di simboli di input, detto *alfabeto*;
* :math:`\delta` è una funzione di transizione definita come: :math:`\delta : Q \times \Sigma \rightarrow Q`;
* :math:`q_0` è lo stato di partenza, vale :math:`q_0 \in Q`;
* :math:`F` è un insieme di stati finali, vale :math:`F \subseteq Q`.

Data una stringa :math:`w = a_1 a_2 \cdots a_n` sull'alfabeto :math:`\Sigma`, l'automa *M*
accetta la sequenza *w* se esiste una sequenza di stati :math:`r_0, r_1, \cdots r_n`
tale che:

1. :math:`r_0 = q_0`;
2. :math:`r_{i+1} = \delta(r_i, a_{i+1}), \, per \, i = 0, 1, \cdots n -1`;
3. :math:`r_n \in F`.



.. index:: nondeterministic finite automaton

.. _ref_nondeterministic_finite_automaton:

Nondeterministic finite automaton
------------------------------------

Un *automa a stati finiti non deterministico* (vedi [WIKI2019b]_), è una macchina a stati finiti
analoga :ref:`all'automa a stati finiti deterministico <ref_deterministic_finite_automaton>`,
con una differente tipo di funzione di transizione.

Abbiamo: :math:`M = (Q, \Sigma, \Delta, q_0, F)` dove:

* :math:`Q` è un insieme finito di stati;
* :math:`\Sigma` è un insieme finito di simboli di input, detto *alfabeto*;
* :math:`\Delta` è una funzione di transizione definita come: :math:`\Delta : Q \times \Sigma \rightarrow P(Q)`
  dove *P(Q)* è l'insieme delle parti di *Q*;
* :math:`q_0` è lo stato di partenza, vale :math:`q_0 \in Q`;
* :math:`F` è un insieme di stati finali, vale :math:`F \subseteq Q`.

Si nota che la funzione di transizione, qui ha come codominio *P(Q)*,
ovvero l'insieme delle parti di *Q*. Quindi la funzione di transizione
in questo caso **non** determina univocamente un solo stato nel codominio
*Q*, ma un gruppo di possibili stati: una *parte* di Q.


.. index:: equivalence classes

.. _ref_equivalence_classes:

Equivalence classes
----------------------

Dato un insieme *A*, e una *relazione di equivalenza* in esso; possiamo prendere
un elemento *a* dell’insieme e considerare tutti gli altri elementi
in esso presenti, per i quali vale la relazione di equivalenza predetta.

L’insieme formato da *a* e dai suoi equivalenti é la classe di equivalenza dell’elemento
*a* di *A*, e si indica con :math:`[a]`. Vale: :math:`[a] = \{x | x \in A \wedge x \backsim a \}`

Vedi [GASP1977]_ pag.12, oppure, informalmente,
`questo link <https://luciano.defalcoalfano.it/general-algebra/1-operazioni_tra_insiemi.html#insieme-quoziente>`_.


.. index:: rgda arbiter

.. _ref_rgda_arbiter:

RGDA arbiter
------------------

Un arbitro RGDA (Request Grant Done e Acknowledge, vedi [EDIS1998]_ e [SABL1994]_) è un componente
che controlla l'utilizzo di una risorsa condivisa permettendone l'accesso esclusivo
a un singolo processo nell'ambito di un gruppo di processi contendenti.




------


.. [#] Per *metalinguaggio* intendo il linguaggio utilizzato per definire
   quello in osservazione.
