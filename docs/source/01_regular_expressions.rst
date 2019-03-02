
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Espressioni Regolari
   :description language=en: Notes on Complex Systems Design - Regular Expressions
   :keywords: Complex Systems Design, regular expressions
   :author: Luciano De Falco Alfano

.. index:: regular expressions

.. _ref_regular_expressions:
   
Regular Expressions
==========================

.. contents:: 
   :local:

..

  Lezione del *2018-11-08*.

Le **espressioni regolari** (vedi [SAKH2019a]_) definiscono un 
:ref:`linguaggio formale <ref_formal_language>` come un insieme di 
stringhe su un alfabeto finito di simboli.

Più in particolare, le espressioni regolari definiscono una grammatica 
(vedi [SLKU1995]_ pag.2) di tipo :ref:`context free <ref_context-free_grammars>`
([SLKU1995]_ pag.3)

La seguente

.. math::

  \mathcal{L} := 0 \vert 1 \vert a \vert e_1 + e_2 \vert e_1 \cdot e_2 | e^*
  
definisce il linguaggio formale :math:`\mathcal{L}`.

Da un punto di vista insiemistico, l'espressione regolare *e* definisce un insieme
di parole *L*, sottoinsieme di tutte le possibili parole che si possono
formare con l'alfabeto *Alph*: 
:math:`e \mapsto L \subseteq Alph^* \quad Alph = \{a, b, c, \cdots \}`

.. _ref_r_e_by_denotational_semantics:

In *denotational semantics*, ponendo a sinistra la *regular expression* e a destra il suo
significato, inteso come *set di parole*, ottenuto per induzione. Abbiamo le seguenti:

* :math:`[\![ 0 ]\!] = \text{empty set of words: } \emptyset` ; 
* :math:`[\![ 1 ]\!] = \text{set che contiene una sola stringa, la stringa vuota: } \{\lambda\}` ; 
* :math:`[\![ a ]\!] = \text{insieme di stringhe che contengono un solo elemento: } \{a\} \\ \, \text{con} \, a \, \text{elemento dell'alfabeto:} \, a \in Alph \, \text{;}` 
* **plus** operator: :math:`[\![ e_1 + e_2 ]\!] = [\![ e_1 ]\!] \cup [\![ e_2 ]\!]`;
 
  dove l'unione di due insiemi è espressa da :math:`L_1 \cup L_2 = \{u \vert u \in L_1 \vee u \in L_2\}` ;
  :math:`L_1` è il linguaggio (ovvero le parole) generato dall'espressione regolare :math:`e_1`, mentre
  :math:`L_2` è il linguaggio generato dall'espressione :math:`e_2`.

* **concatenation** operator: :math:`[\![ e_1 \cdot e_2 ]\!] = [\![ e_1 ]\!] \cdot [\![ e_2 ]\!]` ;

  dove la concatenazione tra insiemi è formata dalle stringhe ottenute concatenando gli elementi dei due insiemi:
  :math:`L_1 \cdot L_2 = \{u \, v \vert u \in L_1 \wedge v \in L_2\}` ;
  
  **composizione sequenziale** (sequential composition) è un sinonimo dell'operatore di concatenazione;
  
  attenzione all'ordine degli argomenti, questo operatore non ha la proprietà simmetrica;
  
* **Kleene**\ ``'``\ s operator (or Kleene's star) : :math:`[\![ e^* ]\!] = ( [\![ e ]\!] )^*` ;

  osserviamo che è una *Kleene's closure* del corpo dell'espressione;

  la *stella* denota *iterazione*; similmente ad un *while* ma senza il
  test booleano:
  :math:`L \subseteq (Alph)^* \quad L^* = \bigcup_{i \geq 0} L^i = L^0 \cup L^1 \cup L^2 \ \dots` ;
  dove   :math:`L^i = L \cdot L \cdot \dots L \quad \text{i times}` ;
  
  volendo definire :math:`L^i` per induzione:
  
  .. math::
  
     L^i = \begin{cases}
             \{\lambda\}          & \text{se } i = 0 \\ 
             L \cdot L^{i-1}  & \text{se } i > 0
           \end{cases}
       
  ovvero: per *i=0* è il set con la stringa vuota, per *i>0*
  è la concatenazione di *L* con L di *i-1*.
  
  Si nota che la stringa vuota é *sempre inclusa*. Per analogia con il *while*
  potremmo dire che si considera l'eventualità di saltare il loop.
  
Le espressioni regolari sono finite, complete e decidibili. Inoltre Kleene ha dimostrato che possono 
generare :ref:`automi finiti non deterministici <ref_nondeterministic_finite_automaton>` (detti NDFA: non deterministic finite automata).

Sempre Kleene, inoltre, nel 1956 dimostrò l'equivanza (kleene equivalence) tra 
automi finiti non deterministici 
e :ref:`automi finiti deterministici <ref_deterministic_finite_automaton>`
(detti DFA: deterministic finite automaton).

In tal modo si osservano le relazioni espresse nel seguente diagramma:

.. image:: images/relazioni_automi.*
   :align: center

Nel diagramma si nota il riferimento alle :ref:`grammatiche regolari<ref_regular_grammars>` [#]_.

Queste equivalenze sono estremamente utili. Perché quando si progetta un nuovo linguaggio,
l'insieme di parole chiave, costanti, valori, possono formare un insieme infinito di elementi.
D'altro canto abbiamo bisogno di un modo finito per esprimere il linguaggio. E l'uso
delle *espressioni regolari* per la definizione del linguaggio è
uno strumento finito in grado di generare insiemi infiniti [#]_.

A questo punto nasce 
la necessità di leggere con un computer le stringhe scritte (ovvero i programmi)
tramite un analizzatore lessicografico in grado di verificare che le stringhe siano
corrette.

Per fare ciò si esegue la procedura mostrata nel precedente diagramma:

* date le  *regular expressions*;
* si costruisce al volo il relativo NDFA;
* si trasforma con apposito algoritmo il NDFA in DFA;
* si implementa il DFA, che essendo finito e deterministico è decidibile;
* si sottopongono al DFA le stringhe da analizzare per vedere se sono
  accettabili dall'automa.

Attenzione al fatto che siamo a livello *lessicografico*. Ovvero *prima* del
controllo sintattico. Questo viene dopo: per l'analisi sintattica le
*espressioni regolari* non sono sufficienti, sevono tool più potenti.

Altro aspetto cui fare attenzione, è il fatto che abbiamo parlato di analisi 
di linguaggi. Diverso è il caso se vogliano utilizzare le espressioni regolari
per analizzare sistemi reattivi o concorrenti. In questo caso è necessario
utilizzare nozioni di equivalenza diversi.


.. index:: regular expressions examples

.. _ref_regular_expressions_examples:

regular expressions examples
-----------------------------------

* :math:`[\![ a \cdot b ]\!] = \{ab\}`;
* :math:`[\![ a^* \cdot ( 0 + 1 + 2 + \cdots 9 )^* ]\!] = \{ \lambda, \cdots, a, aa, \cdots a0, a00, \cdots, aa11, \cdots, a11, \cdots \}`;
* :math:`[\![ a^* ]\!] = \{ \lambda, a, aa, aaa, \cdots \}`;
* nota: :math:`a^+ = a^* - \{ \lambda \}`;
 
 
.. index:: regular expressions trace equivalence

.. _ref_regular_expressions_trace_equivalence:

R.E. trace Equivalence
---------------------------


Osservando le seguenti:

* :math:`[\![ e_1 ]\!] = [\![ a \cdot ( b + c ) ]\!] = \cdots` :

  .. math::
  
     \cdots &  [\![ a ]\!] \cdot  [\![ b + c ]\!] = \\
            & \{a\} \cdot ([\![ b ]\!] \cup [\![ c ]\!]) = \\
            & \{a\} \cdot (\{b\} \cup \{c\}) = \\
            & \{a\} \cdot \{ b, c \} = \{ab, ac\}
  
     
* :math:`[\![ e_2 ]\!] = [\![ a b + a c ]\!] = \cdots` :

  .. math::
  
     \cdots & [\![ a b ]\!] \cup  [\![ a c ]\!] = \\
            & [\![ a ]\!] \cdot [\![ b ]\!] \cup  [\![ a ]\!] \cdot [\![ c ]\!] = \\
            & \{ a \} \cdot \{ b \} \cup \{ a \} \cdot \{ c \}  = \\
            & \{ a b \}  \cup \{ a c \}  = \{ab, ac\}`;
  
si nota che *la semantica è la stessa*, ma le due *sintassi sono diverse*. Capita spesso.

.. _ref_esempio_stessa_semantica:

I seguenti alberi illustrano l'interpretazione sintattica delle due formule predette:
  
.. image:: images/stessa_semantica.*
   :align: center
   
Quindi :math:`e_1` e :math:`e_2` sono *trace equivalent* (o *language equivalent*)
se e solo se denotano la stessa grammatica:

 .. math::
   
    e_1 = e_2 \iff [\![e_1]\!] = [\![e_2]\!] 
    
ovvero generano lo stesso linguaggio [#]_. Questa è una vera equivalenza algebrica, 
infatti valgono le proprietà riflessiva, simmetrica e transitiva.

Ma attenzione alla decidibilità. Le equivalenze di espressioni regolari sono decidibili
perché le regular expressions sono meno
espressive della macchina di Touring. Invece l'equivalenza nei linguaggi di programmazione
in generale non è decidibile. Questo perché i linguaggi sequenziali sono equivalenti 
a macchine di Touring. E in queste in generale non è possibile decidere se 
due programmi sono equivalenti.

Vale anche l'osservazione che le espressioni regolari definiscono linguaggi di specifiche.
Inoltre sono sufficienti per definire i token nei linguaggi di programmazione.
   
Dal fatto che esiste il concetto di (trace) equivalenza per le espressioni
regolari segue che è possibile avere un algoritmo che può dirci se due
espressioni regolari in input sono equivalenti o no, ovvero se generano lo stesso
linguaggio.

  lezione del *2018-11-09*
  
L'applicazione del concetto di equivalenza all'insieme di tutte le espressioni
regolari, permette di suddividerle in :ref:`classi di equivalenza <ref_equivalence_classes>`.
Ogni classe di equivalenza ospita i *termini* equivalenti.


.. index:: axiomatic equivalence

.. _ref_axiomatic_equivalence:

R.E. Axiomatic Equivalence
-----------------------------------------------

E\ ``'`` possibile usare un insieme di assiomi e regole di inferenza per definire
l'equivalenza di regular expressions.

Abbiamo:

* idempotenza :math:`X + X = X`
* commutativa :math:`X + Y = Y + X`
* associativa a sinistra :math:`(X + Y) + Z = X + (Y + Z)`
* esistenza dello zero [#]_

  .. math::
  
     & x + 0 = x \\ 
     & 0 + x = x \\ 
     & x \cdot 0 = 0 \\ 
     & 0 \cdot x = 0

* esistenza dell'uno

  .. math::
  
     & x \cdot 1 = x \\ 
     & 1 \cdot x = x

* distributiva della sequencial composition rispetto il +:

  .. math:: 
    
     & X \cdot (Y + Z) = X \cdot Y + X \cdot Z \\
     & (X + Y) \cdot Z = X \cdot Z + Y \cdot Z 
     
* kleene's star: :math:`X^* = 1 + X \cdot X^*`

  Nota. Applicando la precedente: 
     
  .. math::
     
     0^* & = 1 + 0 \cdot 0^* \\
         & = 1 + 0 \\
         & = 1
         
.. note:: Non esiste la proprietà commutativa per la sequential composition, perché
   per questa operazione l'ordine dei termini è significativo.

.. note:: E\ ``'`` possibile provare la idempotenza provando che :math:`X + X` e 
   :math:`X` hanno la stessa semantica, ovvero:

   .. math::
   
      & \text{tesi: } [\![ X + X ]\!] = [\![ X ]\!] \\
      & \text{vale: } [\![ X + X ]\!] = [\![ X ]\!] \cup [\![ X ]\!] \\
      & \text{insiemisticamente: } [\![ X ]\!] \cup [\![ X ]\!] = [\![ X ]\!] 
      
   da cui la tesi. In pratica :math:`[\![ X ]\!] \cup [\![ X ]\!]` indica l'unione 
   di un linguaggio (un insieme) con se stesso: questa operazione restituisce
   il linguaggio stesso.
   
E\ ``'`` interessante osservare che la :math:`0^* = 1` permette di semplificare la sintassi
delle espressioni regolari eliminando l'1:

.. math::

  \mathcal{L} := 0 \vert a \vert e_1 + e_2 \vert e_1 \cdot e_2 | e^*
   
quindi la semantica delle espressioni regolari è tale da semplificare la sintassi.

Esiste un teorema che enuncia: date due espressioni regolari, queste sono trace equivalent
se e solo se i due termini possono essere resi equivalenti applicando
gli assiomi:

.. math::

   \forall e_1, e_2 \in E \quad e_1 =_{te} e_2 \iff e_1 =_{A} e_2
   
dove *E* è l'insieme delle espressioni regolari, :math:`=_{te}` indica
*trace equivalence*, ovvero :math:`[\![ e_1 ]\!] = [\![ e_2 ]\!]`, mentre
:math:`=_{A}` indica equivalenza secondo gli assiomi precedentemente indicati.

Una equivalenza secondo gli assiomi si prova partendo dall'espressione
:math:`e_1`, e applicando a questa gli assiomi presentati in precedenza, 
fino a trasformarla, se si riesce, nell'espressione :math:`e_2`.
   
Il teorema in osservazione afferma che la *trace equivalence* e la *axiomatic equivalence*
generano le stesse classi di equivalenza: suddividono l'insieme *E* nelle stesse partizioni.   
   
La proposizione *se e solo se* del precedente teorema, significa che valgono:

* sia :math:`e_1 =_{te} e_2 \Leftarrow e_1 =_{A} e_2`;
* che :math:`e_1 =_{te} e_2 \Rightarrow e_1 =_{A} e_2`.

La prima relazione afferma che due espressioni regolari assiomaticamente
equivalenti, implicano l'equivalenza semantica. Questa è detta **correctness**
(o *soundness*).

Considerando la seconda relazione, ovvero l'equivalenza semantica implica 
quella assiomatica, è detta **completeness**.

Kleene nel 1956 fu in grado di provare solo il *soundness*. La *completeness*
fu provata nel 1999 da Dexter Kozen, utilizzando l'univocità dell'automa 
minimo per :math:`e_1, e_2`: dato l'automa minimo, Kozen lo utilizzò per 
dimostrare gli assiomi.

Il teorema predetto vale per le espressioni regolari come generatori di linguaggi.
Per il tema più generale dell'uso delle espressioni regolari per i sistemi
concorrenti, il teorema predetto ancora non è completamente dimostrato. 
E\ ``'`` stato dimostrato per una sintassi semplificata. Quindi vi è il sospetto che 
gli assiomi non siano sufficienti per definire l'equivalenza per sistemi
concorrenti rappresentabili con la sintassi completa delle espressioni
regolari.

.. note:: Mentre da un punto di vista matematico l'equivalenza semantica
   e quella assiomatica possono essere interpretate come due modi diversi
   di esprimere lo *stesso* concetto di equivalenza, da un punto di vista 
   della computer scienze, sono due interpretazioni diverse dell'informazione:
   una (la semantica) orientata all'algoritmo, l'altra alla sintassi.

.. index:: non deterministic interpretation of regular expressions intutition

.. _ref_non_deterministic_interpretation_of_regular_expressions_intuition:

Non deterministic interpretation of R.E.: intuition
----------------------------------------------------

  lezione del *2018-11-15*
  
Riprendiamo la sintassi delle espressioni regolari:

.. math::

   & Alph = \{a, b, c, \cdots \} \\
   & \mathcal{L} ::= 0 \vert 1 \vert a \vert e_1 + e_2 \vert e_1 \cdot e_2 | e^*

Consideriamo l'alfabeto come astrazioni di azioni. Ad esempio: *send*, *receive*,
*input*, *output*, ...

E interpretiamo le espressioni regolari come descrizioni di sistemi reattivi. 
Ovvero sistemi che reagiscono alle condizioni esterne: sempre attivi, eseguono
i comandi senza terminare, ma rimangono in attesa del prossimo comando da 
eseguire.

.. note:: In questa sintassi, per ora manca l'operatore di *parallel composition*.

Gli *operatori* mostrati possono essere interpretati informalmente come segue.

.. warning:: La terminologia può ingannare. Questi sono sistemi elementari. 
   Considera che in inglese *operator* si traduce con il termine italiano
   *operatore*, intendendo una persona o una cosa che esegue
   compiti, verosimilmente in modo continuativo.
   
   Quindi, attenzione a non confondere il concetto di *operatore*, che in italiano 
   assume anche il significato di simbolo che esprime una operazione. Gli anglosassoni
   per questo significato utilizzano spesso il termine *operation*.

* *0* è un *unsuccesfull terminal operator* ovvero un sistema che ha 
  concluso l'attività senza successo.
* *1* è un *successfull terminal operator*: un sistema che ha concluso
  l'attività con successo.
   
  Quindi sia l\ ``'``\ *1* che lo *0* indicano uno stop dell'attività, ma con diversi significati:
  soddisfacimento, o meno, della richiesta.
  
     
* *a* é un sistema che può eseguire l'azione *a* e poi terminare con successo.
  Notiamo che la terminazione con successo è successiva alla esecuzione
  dell'azione *a*.
* :math:`e_1 + e_2` è la *non deterministic composition*. Un sistema in grado di
  eseguire le azioni :math:`e_1` **oppure** quelle :math:`e_2`. E\ ``'`` una 
  *scelta* tra :math:`e_1` ed :math:`e_2`, ma non condizionata da un test
  booleano. La scelta se eseguire l'una o l'altra è determinata dall'ambiente
  esterno. Alcune volte l'ambiente richiederà :math:`e_1`, altre volte :math:`e_2`.
  E quando è stata fatta la scelta, l'alternativa non è più possibile, viene 
  eliminata fino al prossimo run.
  
  I sistemi paralleli hanno  sempre un comportamento di questo tipo, esplicito o implicito.
  Si immagini un gruppo di sistemi, ognuno in grado di eseguire alcune
  attività. Ogni sistema elabora autonomamente, salvo comunicare ogni
  tanto con un altro sistema del gruppo.

* :math:`e_1 \cdot e_2` è la *sequential composition*. Un sistema in grado di
  eseguire le azioni :math:`e_1` e, quando queste sono pronte a terminare con successo, 
  il controllo passa a :math:`e_2`. Quindi **fornisce un ordine**: prima :math:`e_1` 
  e poi :math:`e_2`, ma solo se :math:`e_1` è terminato con successo.
  
  La *sequencial composition* è sempre presente, in ogni linguaggio di specifiche.
  Questo perché ogni algoritmo ha necessità di indicare un ordine delle 
  operazioni da effettuare.
  
* :math:`e^* = 1 + e \cdot e^*` è simile ad un sistema ricorsivo. In pratica è un 
  sistema *non deterministic composition* che può decidere di terminare immediatamente 
  con successo (salta il corpo del processo), oppure eseguire il processo e, **se non fallisce**,
  decidere di ripartire nuovamente.
  
  Il seguente è un diagramma descrittivo di un sistema :math:`e^*`:
  
  .. image:: images/e_star.*
     :align: center

Nel complesso le espressioni regolari sono un linguaggio di descrizione di
sistemi reattivi piuttosto completo. Manca la parallel composition,
che può essere in qualche modo simulata tramite comportamenti paralleli.

Manca anche la possibilità di specificare il tempo e la localizzazione:
vengono descritti solo gli aspetti funzionali.

D'altro canto permettono di esprimere la *non deterministic composition*
tramite l'operatore :math:`e_1 + e_2`, e questo è un concetto fondamentale
nella programmazione parallela. Ad esempio se si hanno due processi che effettuano
assegnazioni diverse in una variabile, quale dei due *vince* lasciando
il proprio valore nella variabile al termine del run, dipende da una serie di 
fattori non controllabili: temperatura, presenza di altri processi, frequenza
del clock, cache, ... Quindi eseguendo più test avremo risultati diversi:
alcune volte vincerà il primo processo, altre volte vincerà il secondo.
   
   
.. index:: non deterministic interpretation of regular expressions semantic

.. _ref_non_deterministic_interpretation_of_regular_expressions_semantic:

Non deterministic interpretation of R.E: semantics
---------------------------------------------------

Per l'interpretazione classica delle R.E. abbiamo usato la *denotation semantics*
perché ci ha permesso di concentraci sugli stati iniziali e finali.

Ora, avendo a che fare con processi concorrenti, limitare l'analisi ai soli
stati iniziali e finali non è più sufficiente. Per questo motivo si usa una
*structural operational semantics* che permette di spiegare il comportamento
del sistema passo per passo.

Per comprendere il motivo di questa necessità, si riprendano i sistemi
illustrati qui di seguito.

.. image:: images/stessa_semantica.*
   :align: center

Se ci limitiamo ad osservare funzionalmente le sequenze di azioni che i due
sistemi sono in grado di eseguire (ovvero le loro *trace*), sappiamo che 
sono equivalenti: soddisfano il criterio di *trace equivalence*. Entrambi
sono in grado di eseguire sequenze del tipo *ab* oppure *ac*.

Ma considerandoli sistemi reattivi, supponiamo che:

* l'azione *a* sia una *read*, quindi una sincronizzazione con un sistema
  esterno che alimenta un dato in lettura,
* l'azione *b* sia una stampa del dato ricevuto su un device (*printer1*);
* l'azione *c* sia una stampa del dato ricevuto su un secondo device (*printer2*).

Questo è uno scenario molto comune. Un processo solitamente dispone di più canali
di uscita, e un dato può essere emesso su uno di questi canali a richiesta
dell'ambiente.

In questo scenario il processo a sinistra (:math:`e_1`) legge il dato (azione *a*)
**e poi** l'utente sceglie se stamparlo su *printer1* **oppure** su *printer2*.

Invece il processo a destra (:math:`e_2`) l'utente deve decidere **subito**
su quale printer scrivere perché deve iniettare la sequenza *ab* **oppure**
la sequenza *ac*. Non può decidere se *b* o *c* dopo la lettura del dato (*a*).

Questo punto é molto importante. 

Premesso che potremo considerare i due sistemi equivalenti se si comportano nello stesso 
modo quando inseriti nello stesso ambiente.

Quindi immaginiamo un test dei due sistemi :math:`e_1` ed :math:`e_2` in un ambiente formato con un 
ulteriore sistema in grado di alimentarli con il dato da stampare, ad esempio la sequenza 
:math:`\bar{a} \cdot \bar{b} \cdot 1`, dove :math:`\bar{a}` e :math:`\bar{b}` sono i 
segnali di sincronizzazione con i relativi comandi, mentre *1* indica
terminazione con successo.

Il comportamento di :math:`e_1` è mostrato qui sotto:

.. image:: images/e1.*
   :align: center

Nel diagramma precedente ogni riquadro rappresenta a sinistra il sistema
:math:`e_1` e a destra, **in parallelo**, il sistema che lo alimenta.

* Al primo comando di sincronizzazione (:math:`\bar{a}`: *step 1*)
  il sistema :math:`e_1` raggiunge
  il nodo di branch e si pone in attesa del comando successivo. In parallelo
  l'alimentatore raggiunge lo stato :math:`\bar{b} \cdot 1`.
* Al secondo comando di sincronizzazione (:math:`\bar{b}`: *step 2*)
  il sistema :math:`e_1` raggiunge
  il nodo di terminazione con successo. In parallelo anche
  l'alimentatore raggiunge lo stato *1*. Quindi tutto ok.

Vediamo ora i comportamenti possibili di :math:`e_2`; sono mostrati qui di seguito:

.. image:: images/e2.*
   :align: center

In questo diagramma, come nel precedente, ogni riquadro rappresenta a sinistra il sistema
in osservazione (qui è :math:`e_2`) e a destra, **in parallelo**, il sistema che lo alimenta.

Inoltre qui abbiamo due colonne: una per ogni possibile comportamento di 
:math:`e_2`.

Consideriamo la colonna sinistra.

* Al primo comando di sincronizzazione (:math:`\bar{a}`: *step 1* nella colonna sinistra)
  il sistema :math:`e_2` prende il ramo a sinistra
  e si pone in attesa del comando successivo. In parallelo
  l'alimentatore raggiunge lo stato :math:`\bar{b} \cdot 1`.
* Al secondo comando di sincronizzazione (:math:`\bar{b}`: *step 2* della colonna sinistra)
  il sistema :math:`e_2` raggiunge
  il nodo di terminazione con successo. In parallelo anche
  l'alimentatore raggiunge lo stato *1*. Qui è ok.
  
Ma è possibile anche il comportamento descritto nella colonna a destra.

* Al primo comando di sincronizzazione (:math:`\bar{a}`: *step 1* nella colonna destra)
  il sistema :math:`e_2` prende il ramo a destra
  e si pone in attesa del comando successivo. In parallelo
  l'alimentatore raggiunge lo stato :math:`\bar{b} \cdot 1`.
* Dopo di che, siamo in una situazione di stallo (*deadlock state*). Perché
  il sistema :math:`e_2` è pronto a ricevere il comando *c*, mentre il sistema
  di alimentazione deve inviare un comando *b*. Questo non va bene.
  
La presenza di possibili deadlock è un problema tipico dei sistemi distribuiti/paralleli.
É necessario evitarli accuratamente, e vanno scoperti nelle prime fasi di progettazione,
perché correggerli dopo l'implementazione del sistema può essere estremamente oneroso [#]_.

D'altro canto un sistema distribuito può essere formato da centinaia, o migliaia
di componenti. In queste condizione una analisi manuale non è possibile. É
necessario utilizzare tool automatici di analisi, che verifichino il comportamento
di tutto il sistema all'accadere di tutte le possibili condizioni. E per fare 
questo devono considerare la struttura di branching dei diversi componenti.

Quindi non possiamo usare l'equivalenza linguistica per effettuare questo tipo
di verifiche.

Per lo stesso motivo non è possibile utilizzare un approccio puramente funzionale.
Perché anche le funzioni si concentrano su due soli punti nevralgici: l'input alla
funzione e il suo output.
  
  lezione del *2018-11-29*
  
Il fenomeno del deadlock è stato illustrato con il 
`problema dei filosofi a cena <https://en.wikipedia.org/wiki/Dining_philosophers_problem>`_
nel 1965 da E. Dijkstra. Si supponga di avere un gruppo di filosofi, che non possono parlare tra loro,
seduto ad un tavolo rotondo. Ogni filosofo ha a disposizione una forchetta, ma per mangiare
deve avere in mano due forchette. Quindi potrà mangiare solo se il suo compagno
a lato gli lascia usare la sua forchetta. Il dedloack avviene se tutti i filosofi
decidono di mangiare e prendono la propria forchetta, senza lasciarla a disposizione
del vicino.

Nota: ci concentriamo su linguaggi di specifiche con handshacking (messagge passing
between processes). Ma gli stessi ragionamenti si possono applicare a linguaggi
di specifica che utilizzano memorie condivise per effettuare la sincronizzazione.
Quindi l'approccio *input/output* non è l'unico usabile.

Consideriamo il seguente diagramma. Abbiamo 
sia a destra che a sinistra un sistema che risponde al comando *a* e termina con 
successo.

.. image:: images/a.*
   :align: center

A sinistra il sistema :math:`A_1` è deterministico, invece a destra :math:`A_2` ha un comportamento
non deterministico. Dall'esterno non possiamo eseguire un test che ci permetta
di capire con quale di questi due abbiamo a che fare, e il comportamento appare
sempre del tutto equivalente. Possiamo pensare di semplificare il progetto 
sostituendo il sistema :math:`A_2` con il sistema :math:`A_1`.

In generale avremo il seguente processo.

Dato un programma scritto in un linguaggio di specifica che supporti il non
determinismo, lo traduciamo in un automa con transizioni:

.. math::

   P_1 \rightsquigarrow A_1

Quindi possiamo avere, ad esempio, un programma scritto con la sintassi delle
espressioni regolari, modellato tramite un automa che rappresenta un
*labeled transition system*\ [#]_. L'automa in questione solitamente
è dotato di un numero elevatissimo di stati e transizioni, al limite anche infinito.
Perciò il passo successivo consiste nel trasformate l'automa in una 
versione equivalente, possibilmente più piccola e con numero finito
di stati:

.. math::

   P_1 \rightsquigarrow A_1 \rightsquigarrow A_2

La trasformazione verso un automa più piccolo avviene utilizzando relazioni
di equivalenza, che sono divise in due classi:

* *testing equivalences*;
* *bisimulation equivalences*.

Le equivalenza tramite test utilizzano le attività di test per definire
l'equivalenza tra automi diversi (model checking). In questo ambito, a causa del fatto che il non 
determinismo del sistema può generare automi a stati infiniti, i test possono
(e devono) essere rappresentati con formule logiche, oppure, nel caso di elaborazioni
in parallelo, si possono collezionare tutti i possibili risultati, e 
decidere se un test ha avuto successo, o meno, verificando se il risultato
ottenuto rientra nel set di quelli attesi.

La *bisimulation equivalence*, su cui ci concentreremo, ha un approccio diverso.
Ma, prima di capire cos'è la bisimulation equivalence, dobbiamo comprendere 
come trasformare delle specifiche formali in un *labeled transition system*.

In questo scenario (di processi paralleli) il non determinismo deve essere accettato.
A differenza dei programmi scritti in linguaggi formali sequenziali, che è possibile
trasformare in automi a stati fini deterministici, in questo caso il *non determinismo*
**non è eliminabile**.

.. index:: R.E. by operational semantics

.. _ref_R_E_by_operational_semantics:

R.E. by operational semantics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Il modo più efficiente di passare dalla formalizzazione delle specifiche all'automa
consiste nell'usare la *operational semantics*. **Ma** questa considera solo
il passaggio da uno stato del sistema (input), ad uno stato finale (output) 
per una data azione *a*:

.. math::

   e_{initial\, state} \xrightarrow{a} f_{final\, state}

Questa relazione mappa ogni possibile stato iniziale per ogni possibile azione.
Quindi, possiamo pensare allo stato iniziale come una configurazione del sistema.
In ultima analisi i due stati (iniziale e finale) sono un *labeled transition
system* con label *a* (l'azione).

Vedendolo come *labeled transiton system*, gli stati possono essere considerati
astraendo dal loro nome, perché ci interessa osservare il comportamento. Si ricordi 
che stiamo osservando un sistema che interagisce con il suo ambiente, e l'interazione è 
veicolata dall'azione.

Quindi la *operational semantics* definisce una relazione binaria :math:`(e, a, f)`
che può essere espressa tramite regole d'inferenza (*inference rules*) della forma:

.. math::

   \frac{premise_1, \cdots premise_n}{consequence}
   
Le regole d'inferenza definiscono la struttura del nostro sistema.

Riprendendo il ns linguaggio di specifica [#]_ [#]_: 
:math:`\mathcal{L} ::= 0 \vert 1 \vert a \vert e_1 + e_2 \vert e_1 \cdot e_2 | e^*`
vediamo la sua *operational semantics* considerando come tradurre ogni termine 
del linguaggio in un automata che lo modella.
   
Avendo *regular expressions*, i modelli associati ai suoi termini sono finiti (**? why ?**).
Quindi la *operational semantics* definisce per ogni termine automi a stati finiti
non deterministici [#]_.

Di seguito diamo l'interpretazione di :math:`\mathcal{L}`. La colonna sx
indica il numero di riga, la seconda colonna 
indica il termine in analisi, la terza colonna riporta la relativa regola d'inferenza, 
la colonna dx [#]_ indica
la terminazione immediata (con successo) del sistema.

+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
| row | operator                  | rule                                                                                            | terminazione immediata                                               |
+=====+===========================+=================================================================================================+======================================================================+
|  1  | :math:`0`                 | no rule                                                                                         |                                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
|  2  | :math:`1`                 | no rule                                                                                         | :math:`1 \surd`                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
|     | basic activity            |                                                                                                 |                                                                      |
|     |                           |                                                                                                 |                                                                      |
|  3  | :math:`a`                 | :math:`\frac{\diagup}{a \xrightarrow{a} 1}`                                                     |                                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
|  4  | non deterministic choice  |                                                                                                 |                                                                      |
|     |                           | :math:`\frac{e_1 \xrightarrow{a} e_1'}{(e_1 + e_2) \xrightarrow{a} e_1'}`,                      | :math:`\frac{e_1 \surd \, or \, e_2 \surd}{(e_1 + e_2) \surd}`       |
|     | :math:`e_1 + e_2`         |                                                                                                 |                                                                      |
|     |                           | :math:`\frac{e_2 \xrightarrow{a} e_2'}{(e_1 + e_2) \xrightarrow{a} e_2'}`                       |                                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
|     |                           |                                                                                                 |                                                                      |
|  5  | sequential composition    | .. index:: operational semantics sequential composition                                         |                                                                      |
|     |                           |                                                                                                 |  :math:`\frac{e_1 \surd \, and \, e_2 \surd}{(e_1 \cdot e_2) \surd}` |
|     | :math:`e_1 \cdot e_2`     | .. _ref_operational_semantics_sequential_composition:                                           |                                                                      |
|     |                           |                                                                                                 |                                                                      |
|     |                           | :math:`\frac{e_1 \xrightarrow{a} e_1'}{(e_1 \cdot e_2) \xrightarrow{a} (e_1' \cdot e_2)}`       |                                                                      |
|     |                           |                                                                                                 |                                                                      |
|     |                           | :math:`\frac{e_1 \surd \, , \, e_2 \xrightarrow{a} e_2'}{(e_1 \cdot e_2) \xrightarrow{a} e_2'}` |                                                                      |
|     |                           |                                                                                                 |                                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
|  6  | Kleene's star             |                                                                                                 |                                                                      |
|     |                           | :math:`\frac{e \xrightarrow{a} e'}{e^* \xrightarrow{a} (e' \cdot e^*)}`                         | :math:`\frac{\diagup}{e^* \surd}`                                    |
|     | :math:`e^*`               |                                                                                                 |                                                                      |
+-----+---------------------------+-------------------------------------------------------------------------------------------------+----------------------------------------------------------------------+
   
La prima riga della precedente tabella rappresenta un sistema in stato *0*, ovvero in **deadlock**, quindi non ha terminazione immediata.

La seconda riga rappresenta un sistema in stato *1*, ovvero in terminazione immediata con successo.

La terza riga analizza il sistema in grado di eseguire l'azione *a*. Qui non vi sono precondizioni, e la
conseguenza è l'esecuzione dell'azione. Il sistema **terminerà** (attenzione al futuro) con successo *dopo avere eseguito*
l'azione richiesta. Per questo motivo *ora* non termina con successo.

Alla riga 4  il sistema :math:`e_1 + e_2` puo eseguire sia :math:`e_1` che 
:math:`e_2`. **Ma** uno dei due deve essere deciso dal contesto. Per questo
abbiamo due regole: per dare il comportamento in entrambi i casi. Ad esempio,
se nell'eseguire *a* il sistema passa da :math:`e_1` ad :math:`e_1'`, allora
il percorso alternativo (in questo caso da :math:`e_2` ad :math:`e_2'`) viene
eliminato.
   
Sempre alla riga 4, il sistema :math:`e_1 + e_2` termina immediatamente, se lo stato di partenza
:math:`e_1` o :math:`e_2` termina immediatamente. Con operatore *or* inclusivo,
quindi se uncomponente può terminare immeditamente, lo può anche il sistema.
   
Alla riga 5, il sistema :math:`e_1 \cdot e_2` è in grado di eseguire prima
:math:`e_1`, e quando questo termina si passa ad eseguire :math:`e_2`. In generale
però :math:`e_1` può passare in :math:`e_1'`, senza terminare. In questo caso
il sistema evolve in :math:`e_1' \cdot e_2`. Se invece :math:`e_1` termina
(seconda inferenza), allora parte l'elaborazione di :math:`e_2`, che in generale
evolve nel nuovo stato :math:`e_2'`.

Alla riga 5, la terminazione immediata di :math:`e_1 \cdot e_2` avviene solo se 
terminano immediatamente sia :math:`e_1` che :math:`e_2` (operatore *and*).
Ad esempio :math:`(a + 1) \cdot (b + 1)` termina immediatamente, perché
lo fanno entrambi gli *1* dei due termini che sono in *non deterministic 
choise* (operatore *+*). Alla stessa conclusione si arriva applicando all'esempio le 
regole assiomatiche.

Alla riga 6, la Kleene's star :math:`e^*` è un sistema in grado di terminare
immediatamente con successo, oppure di eseguire nuovamente se stesso. In generale 
eseguendo l'azione *a* passerà dallo stato :math:`e` allo stato :math:`e'`
per poi decidere se terminare o riprendere il ciclo, come espresso da: :math:`e' \cdot e^*`,

La terminazone immediata della riga 6 vale per definizione: 
:math:`e^*` può terminare con successo immediatamente senza precondizioni.

Ad esempio :math:`(a \, 0 \, b)^*`, per la presenza dell'operatore star può
terminare immediatamente con successo, oppure eseguire l'azione *a* e poi andare 
in deadloack per la presenza dello stato *0*.

A questo punto abbiamo le regole d'inferenza che ci permettono di interpretare 
i termini delle *regular expressions*. Applicandole ad un termine per volta
possiamo costruire così l'automata del sistema.

Ad esempio, prendiamo :math:`a \cdot (b + c)`. Consideriamo:

* :math:`a \equiv e_1`, e 
* :math:`(b + c) \equiv e_2`.

Con la precondizione che *a* possa terminare immediatamente per l'azione
*a* (espressa da :math:`a \xrightarrow{a} 1`), allora la regola d'inferenza è:

.. math::

   \frac{a \xrightarrow{a} 1}{a \cdot (b + c) \xrightarrow{a} 1 \cdot (b + c)}

Iteriamo il ragionamento su questa regola considerando:

* :math:`1 \equiv e_1`, e 
* :math:`(b + c) \equiv e_2`.

Ora dovremo analizzare la *non deterministic composition*, che ci porta a due diverse 
regole d'inferenza, una ottenuta applicando l'azione *b* (espressa da :math:`b \xrightarrow{b} 1`):

.. math::

   \frac{1 \surd , \frac{b \xrightarrow{b} 1}{(b + c) \xrightarrow{b} 1}}{1 \cdot (b + c) \xrightarrow{b} 1}

e l'altra applicando l'azione *c*  (espressa da :math:`c \xrightarrow{c} 1`):

.. math::

   \frac{1 \surd , \frac{c \xrightarrow{c} 1}{(b + c) \xrightarrow{c} 1}}{1 \cdot (b + c) \xrightarrow{c} 1}

Le regole predette portano a disegnare il seguente automa a stati finiti:

.. image:: images/esempio1.*
   :align: center
   
Sono stati scritti programmi che, data una formula scritta come espressione regolare,
sono in grado di produrre il relativo automa a stati, usualmente nella forma: 

  (stato iniziale, azione, stato finale)
  
ovvero :math:`(e, a, f)`.

Ricordiamo la *denotational semantics* associa ad un termine 
l'insieme delle frasi: :math:`e \xrightarrow{[\vert \diagup \vert]} [\![ e ]\!]`.

La *operational semantics* invece analizza un insieme di parole (ovvero: azioni) in sequenza,
considerando l'insieme dei relativi stati ottenuti eseguendo le azioni:

.. math::

   L(e) = \{ a_1, \cdots a_n \vert e \xrightarrow{a_1} e_1 \xrightarrow{a_2} \cdots \xrightarrow{a_n} 1 \}
   
Se ci si limita all'ambito sintattico, è possibile provare che per ogni espressione
regolare appartenente all'insieme
di tutte le E.R., la *denotational semantics* equivale alle *operational 
semantics* associate con essa:

.. math::

   \text{th.} \quad \forall e \in \xi, \quad [\![ e ]\!] = L(e)

quindi la *operational semantics* è solo un modo diverso di eseguire
la *denotational semantics*.

Ma quando si analizzano sistemi reattivi, l'aspetto è molto più complesso.
Perché per ogni stato del sistema si analizzano tutte le possibili risposte che
si possono ottenere in quello stato esercitanto le azioni possibili in esso.
A differenza dell'analisi sintattica, che analizza
una sola risposta conseguenza di una azione determinata.
      
-------

.. [#] Memo. Grammatica regolare: 

   .. math::
      & A \rightarrow a\\
      & A \rightarrow b C
      
   dove *a* e *b* sono elementi terminali, mentre *A* e *C* sono elementi non
   terminali.

.. [#] Grazie all'esistenza della Kleene's star.

.. [#] Per questo si parla di language (o trace) equivalence.

.. [#] Per comprendere :math:`x \cdot 0 = 0`, si consideri la definizione di 
   concatenazione: :math:`L_1 \cdot L_2 = \{uv \vert u \in L_1 \; \mathbf{and} \; v \in L_2\}`.
   Ma se :math:`L_2` è l'insieme vuoto (si ricordi la definizione di 0), non esiste
   alcun :math:`v \in L_2`. Quindi l'operatore *and* è sempre *false* e 
   :math:`x \cdot 0` è l'insieme vuoto, ovvero 0.
.. [#] Ammesso sia possibile.

.. [#] Le etichette delle transizioni tra gli stati sono relative alle attività.

.. [#] Attenzione al fatto che *a* **non è** un sistema che termina immediatamente;
   *prima* esegue l'azione *a*, *poi* termina con successo (*1*).

.. [#] Ancora: questo linguaggio è il core di qualunque sistema reattivo, 
   comprendendo gli operatori di composizione non deterministica (+), 
   di composizione sequenziale (·) e di iterazione (la Kleene's star). Più in generale,
   vi è sempre un operatore di *stop* con successo (*1*) o senza successo (*0*); e
   una serie di comandi (*a*). Può mancare la composizione sequenziale, sostituita
   ad es. da *action prefixing*. Oppure può mancare la Kleene's star, sostituita
   da altri meccanismi di iterazione. Qui ci manca la *parallel composition*.
   
.. [#] Se ci fosse anche la *parallel composition*, vi sarebbe la possibilità
   di avere un set infinito. Per ora ignoriamo questa possibilità.
   
.. [#] Questo è un predicato, vale **True** se *e* è un sistema che può
   terminare immediatamente con successo.

