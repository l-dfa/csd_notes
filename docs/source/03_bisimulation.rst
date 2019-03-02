
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Bisimulation
   :description language=en: Notes on Complex Systems Design - Bisimulation
   :keywords: Complex Systems Design, bisimulation
   :author: Luciano De Falco Alfano

.. index:: bisimulation

.. _ref_bisimulation:
   
Bisimulation
==========================

.. contents:: 
   :local:

..

  Lezione del *2018-11-30*.

.. index:: a view of the design process

.. _ref_a_view_of_the_design_process:
   
A view of the design process
--------------------------------

Come abbiamo affermato al termine di :ref:`R.E. by operational semantics <ref_R_E_by_operational_semantics>`,
analizzando i sistemi reattivi [#]_ l'equivalenza tra il prodotto dell'analisi 
tramite la *operational semantics* e quello della *denotational semantics*
non ci è sufficiente, in quanto la struttura dell'automa a stati che lo 
rappresenta è significativa.

Più in generale, per i sistemi reattivi la *language equivalence* [#]_ non è adeguata perché
gli stati intermedi e la relativa struttura dell'automa sono significativi.

Quindi abbiamo una situazione in cui descriviamo il sistema con un 
linguaggio di specifica, e lo modelliamo in un *labeled transition system*
tramite la *operational semantics*:

.. math::

   P_d \, \overset{operational \, semantics}{\rightsquigarrow} \, Model
   
Il modello viene usato per vari tipi di verifiche riguardo le proprietà :math:`\varphi`
del sistema, espresse con un lingaggio di specifiche delle proprietà:

.. math::

   P_{specification} \, \overset{op.sem.}{\rightsquigarrow} \, Model  \, \overset{model \, checking}{\models} \, \varphi

I linguaggi di specifiche delle proprietà, usualmente sono formule logiche scritte con 
estensioni della :ref:`propositional logic <ref_propositional_logic>`. In tal modo, 
data la proprietà, è possibile utilizzare il modello per verificare se la proprietà è soddisfatta
dal modello stesso.

L'implementazione del codice comincia dopo le verifiche del modello.

Esistono anche tool che permettono la scrittura automatica del codice a partire
dal modello, ma l'ottimizzazione del codice lascia a desiderare. Tipicamente
sono usati per la prototipazione.

Scoprire un problema di architettura riguardo le proprietà del sistema, o un 
deadlock, durante la fase di coding, può essere disastroso. Per questo
le fasi di modellazione e di model checking sono estremamente importanti, consentono
di variare rapidamente l'architettura del sistema *prima* di avere cominciato il coding.


.. index:: refinement

.. _ref_refinement:

Effettuata con soddisfazione la fase [#]_ di model checking, è possibile proseguire
con una serie di attività di **refinement** del sistema, volte ad una
formalizzazione sempre più dettagliata del sistema, sino ad arrivare ad una *specifica
d'implementazione* (nel seguito: :math:`P_{impl}`). Questa non va confusa con il coding, è una specifica su
come fare il coding:

.. math::

   P_{spec} \, \overset{\sqsubset}{\sim} \, P_{spec1} \, \overset{\sqsubset}{\sim} \, \cdots \, \overset{\subset}{\sim} \,  P_{impl}

Ad esempio, potremmo avere la seguente specifica generica: :math:`P_{spec} \equiv (ab+ba)`,
ed arrivare ad una specifica d'implementazione del tipo: :math:`P_{impl} \equiv (a \parallel b)`.
Questo è un sistema che tratta la sequenza di azioni *ab* o il suo opposto: *ba*. La
relativa specifica d'implementazione recita: "usa due componenti in parallelo, uno in grado di eseguire
*a* e l'altro di eseguire *b*".

Durante le attività di *refinement* i vari passaggi devono dare luogo
a sistemi *equivalenti*: :math:`P_{spec} \sim P_{impl}`.

Per verificare ciò avremo due tipi di controlli:

* *model checking*, ovvero data una proprietà del sistema, deve essere
  verificata con entrambi i modelli :math:`P \models \varphi`;
* *equivalence checking*, ovvero si verifica l'equivalenza delle due 
  specifiche da confrontare: :math:`P_1 \sim P_2`;

Da cui il seguente (importante) teorema. "Per ogni sistema :math:`P_1` e :math:`P_2`,
per ogni attributo :math:`\varphi` nel linguaggio della formula, i due sistemi 
sono equivalenti *iff* per ogni attributo :math:`P_1` soddisfa l'attributo
*iff* :math:`P_2` soddisfa lo stesso attributo". Formalmente:

..
  ex
  \forall P_1, P_2, \forall \varphi \in L_f \quad P_1 \sim P_2 \; \text{iff} \; \forall \varphi \; (P_1 \models \varphi \; \text{iff} \; P_2 \models \varphi)
   
.. math::

   P_1 \sim P_2 \; \text{iff} \; \forall \varphi \in L_f \; (P_1 \models \varphi \; \text{iff} \; P_2 \models \varphi)

Questo teorema afferma che due sistemi sono equivalenti se e solo se soddisfano
la stessa classe di formule [#]_.


.. index:: bisimulation relation

.. _ref_bisimulation_relation:
   
Bisimulation relation
-------------------------

Abbiamo detto che la *trace equivalence* non è sufficiente perchè abbiamo necessità
di tenere in considerazione gli stati *intermedi* presenti nell'automa
tra lo stato iniziale e quelli finali, considerando ogni possibile
comportamento, ovvero la relativa struttura di ramificazione.

Un altro tipo di equivalenza è l\ ``'``\ **isomorfismo**: due processi sono equivalenti
se generano esattamente lo stesso *labeled transition system*. Questo è un criterio
accettabile, ma molto forte. Ad esempio si consideri il seguente semplice esempio:

.. image:: images/esempio2.*
   :align: center
   
Questi due processi hanno lo stesso comportamento: entrambi gestiscono l'azione
*a* per poi terminare con successo. Da un punto di vista di un osservatore
esterno non vi è differenza. Ma non hanno la stessa struttura, quindi
non sono isomorficamente equivalenti.

Quindi, se la *trace equivalence* è troppo debole per comparare sistemi
reattivi, l'isomorfismo è troppo forte.

La relazione di bisimulazione è un modo per comparare sistemi reattivi
considerando il comportamento degli stati intermedi, ma senza *bloccare*
la struttura di branching.

Non si confonda la *bisimulazione* con la *simulazione*, è un concetto diverso ed
originale. Per rendercene conto consideriamo il concetto di simulazione.

.. index:: simulation relation

.. _ref_simulation_relation:
   
Simulation relation
^^^^^^^^^^^^^^^^^^^^^

Nel caso di simulazione, dati due sistemi :math:`P_1` e :math:`P_2`, diciamo che uno simula l'altro 
quando per ogni attività di :math:`P_1` anche :math:`P_2` riesce ad eseguirla,
raggiungendo uno stato in cui questo processo può avere luogo nuovamente.

Formalmente "*Q* simula *P*" si scrive: :math:`P \overset{\sqsubset}{\sim} Q` [#]_.

Si ricordi che una relazione di equivalenza soddisfa le
proprietà riflessiva, simmetrica, e transitiva.

Questa **non è** una relazione di equivalenza, perché non esiste la 
proprietà simmetrica; è una relazione di preordine [#]_. Valgono le proprietà

* riflessiva ("*P* simula *P*": :math:`P \overset{\sqsubset}{\sim} P`);
* e transitiva ("se *P* simula *Q* e *Q* simula *R*, allora *P* simula *R*": 
  :math:`(P \overset{\sqsupset}{\sim} Q \wedge Q \overset{\sqsupset}{\sim} R) \Rightarrow P \overset{\sqsupset}{\sim} R`)

Se si aggiunge la proprietà simmetrica, allora otteniamo il concetto di equivalenza
basata su simulazione [#]_:

.. math::

   P \sim_s Q \; \text{if}  \; (P \overset{\sqsubset}{\sim} Q \wedge Q \overset{\sqsubset}{\sim} P)

Riprendendo il concetto di simulazione, formalizziamolo meglio. Diciamo che
"*Q* simula *P* quando per qualunque :math:`a \in Act` allora :math:`P \xrightarrow{a} P'`
implica :math:`Q \xrightarrow{a} Q'` tale che :math:`Q'` simula :math:`P'`":

.. math::

   P \overset{\sqsubset}{\sim} Q \; \text{whenever} \; \forall a \in Act, \; P \xrightarrow{a} P' \; \text{implies} \; Q \xrightarrow{a} Q' \; \text{such that} \; P' \overset{\sqsubset}{\sim} Q'

Si nota che la simulazione deve continuare finché il sistema *P* continua 
a funzionare, raggiungendo una terminazione con successo o un deadlock.

(*esempio3*) Vediamo un esempio interessante. Consideriamo i seguenti sistemi:

.. _ref_images_esempio3:

.. image:: images/esempio3.*
   :align: center
   
chiedendoci prima se il sistema a destra (stati :math:`q_n`)
può simulare il sistema a sinistra (stati :math:`s_n`) applicando
le azioni :math:`a \cdot b`. E, successivamente,
l'inverso: se il sistema a sinistra può simulare quello a destra.

Prima tesi: :math:`s_1 \overset{\sqsubset}{\sim} q_1`:

1. usiamo le definizioni quando applichiamo l'azione *a* ai due sistemi; abbiamo:
   :math:`s_1 \xrightarrow{a} s_2`, mentre a sua volta:
   :math:`q_1 \xrightarrow{a} q_2` (seguendo il ramo sx di :math:`q_1`);
#. ora ci chiediamo :math:`s_2 \overset{\sqsubset}{\sim} q_2`?
#. come sopra applichiamo l'azione *b* ai due sistemi, abbiamo:
   :math:`s_2 \xrightarrow{b} s_3`, mentre a sua volta:
   :math:`q_2 \xrightarrow{b} q_3`.
#. ora, :math:`s_3 \overset{\sqsubset}{\sim} q_3`? risposta: **sì**, per mancanza
   di ulteriori azioni.
   
Quindi la prima tesi è dimostrata.

Controllare la seconda tesi: :math:`q_1 \overset{\sqsubset}{\sim} s_1` è 
una attività più articolata.

Sequendo il ramo sinistro di :math:`q_1` abbiamo un comportamento analogo al precedente:

1. applicando *a*:
   :math:`q_1 \xrightarrow{a} q_2` e
   :math:`s_1 \xrightarrow{a} s_2`;
#. ora ci chiediamo :math:`q_2 \overset{\sqsubset}{\sim} s_2`?
#. applicando *b*:
   :math:`q_2 \xrightarrow{b} q_3`.
   :math:`s_2 \xrightarrow{b} s_3`;
#. :math:`q_3 \overset{\sqsubset}{\sim} s_3` per mancanza di ulteriori azioni.

Ma :math:`q_1` ha anche un ramo destro, che dobbiamo essere in grado di simulare:

1. applicando *a*, seguendo il ramo destro di :math:`q_1`:
   :math:`q_1 \xrightarrow{a} q_4` e
   :math:`s_1 \xrightarrow{a} s_2`;
#. ora ci chiediamo :math:`q_4 \overset{\sqsubset}{\sim} s_2`? La risposta
   è **sì** per mancanza di ulteriori azioni!
   
Si arriva alla interessante conclusione che gli stati in deadlock sono
simulati da un qualunque altro stato perché non richiedono alcuna azione.

Infatti dovrebbe essere:

.. math::
 
   \forall a \in Act \; q_4 \xrightarrow{a} q' \Rightarrow s_2 \xrightarrow{a} s'
   
ma non esiste :math:`q_4 \xrightarrow{a} q'`, di conseguenza è soddisfatta 
l'implicazione :math:`\Rightarrow`. Infatti si ricordi che l'implicazione
ha la seguente tabella di verità:

.. math::
   
       \begin{matrix}
       P_1 & P_2 & P_1 \Rightarrow P_2 \\
       0 & 0 & 1 \\
       0 & 1 & 1 \\
       1 & 0 & 0 \\
       1 & 1 & 1
       \end{matrix}

quindi l'unico caso in cui vale *False* si realizza per :math:`(1 \Rightarrow 0) = 0`.

Un modo diverso di considerare il concetto è ragionare per premessa e 
conseguenza. Se la premessa: 
"per qualunque :math:`a \in Act`, :math:`P \xrightarrow{a} P'`"
non si può avverare, non si andrà mai a controllare la conseguenza, da cui
la verità dell'implicazione.

Ora riprendiamo il concetto di introdurre nella relazione la proprietà simmetrica, ovvero:

.. math::

   (P \overset{\sqsubset}{\sim} Q \wedge Q \overset{\sqsubset}{\sim} P)

come detto, la presenza di questa proprietà, oltre la riflessiva
e la transitiva, ci porta ad affermare che i sistemi *P* e *Q* 
sono equivalenti: :math:`P \sim_s Q`.

Ricordiamo :ref:`l'esempio precedente <ref_images_esempio3>`. Abbiamo visto 
che :math:`s_1 \overset{\sqsubset}{\sim} q_1 \wedge q_1 \overset{\sqsubset}{\sim} s1`,
quindi i due sistemi sono equivalenti: :math:`s_1 \sim_s q_1`.

Considerando il fatto che stiamo considerando sistemi reattivi, siamo soddisfatti
di questa conclusione riguardo :math:`s_1` e :math:`q_1`? [#]_

In realtà la mancanza di analisi della struttura di branching per i sistemi
reattivi è causa di pericolosi bug d'implementazione.

Analizziamo la situazione dell'esempio mettendo i due sistemi sotto test.
Cioè affianchiamo al sistema da osservare un sistema di test che in modo sincrono 
invia il comando *x* e a sua volta esegue l'azione equivalente :math:`\bar{x}` su se stesso.

Il test termina con successo se raggiunge lo stato :math:`\bar{t}_3 \omega` [#]_.

(*esempio3_test1*) Iniziamo considerando il sistema a sinistra, avremo il seguente comportamento:

.. _ref_images_esempio3_test1:

.. image:: images/esempio3_test1.*
   :align: center

Ogni riquadro della figura rappresenta una fase. La numerazione dei riquadri 
indica la successione temporale e a seguire è indicata l'azione di sincronizzazione.
Ad esempio: :math:`(a, \bar{a})` indica l'azione di sincronizzazione con *a*
a sinistra e :math:`\bar{a}` per il sistema di test a destra.

Leggendo l'evoluzione del test:

1. si parte dagli stati :math:`s_1` per il sistema in osservazione e
   :math:`t_1` per il sistema di test;
#. all'esecuzione di :math:`(a, \bar{a})` il sistema in osservazione
   va in :math:`s_2`, mentre il sistema di test va in :math:`t_2`;
#. all'esecuzione di :math:`(b, \bar{b})` il sistema in osservazione
   va in :math:`s_3`, mentre il sistema di test va in :math:`t_3` indicando
   terminazione con successo.
   

(*esempio3_test2*) Ora proviamo con lo stesso sistema di test ad osservare il comportamento del ramo destro
del sistema a destra nell'esempio. Abbiamo il seguente diagramma:

.. _ref_images_esempio3_test2:

.. image:: images/esempio3_test2.*
   :align: center

Leggendo questo test:

1. si parte dagli stati :math:`q_1` per il sistema in osservazione e
   :math:`t_1` per il sistema di test;
#. all'esecuzione di :math:`(a, \bar{a})` il sistema in osservazione
   va in :math:`q_4`, mentre il sistema di test va in :math:`t_2`;
#. a questo punto siamo in una situazione di stallo: :math:`(b, \bar{b})`
   non può essere eseguito perché :math:`q_4` è un deadlock; e il test
   non è in grado di terminare con successo.
   
Da questo esempio deduciamo che i due sistemi in osservazione non 
si comportano nello stesso modo per il test erogato, di conseguenza
non possono essere considerati equivalenti.

Da qui l'idea intuitiva: dato un ambiente, se in un sistema sostituiamo
un suo componente con un altro componente, il sistema complessivo
deve continuare a funzionare come se la sostituzione non fosse avvenuta.

.. index:: bisimulation equivalence

.. _ref_bisimulation_equivalence:

Bisimulation equivalence
^^^^^^^^^^^^^^^^^^^^^^^^^^

Visto che la relazione di simulazione, pur essendo utile [#]_, è troppo debole
per assicurarci una relazione di equivalenza, la dobbiamo rafforzare, senza arrivare alla 
rigidità dell'isomorfismo.

Per fare ciò, intuitivamente, diciamo che due stati, quello del sistema in osservazione
e quello del sistema equivalente, devono essere in grado di **simularsi a vicenda**;
e che ciò deve avvenire per tutti i possibili stati del sistema in osservazione.

La equivalenza per bisimulazione deriva dalla teoria dei giochi. E per 
indicare che gli stati *p* e *q* sono *bisimulation equivalent* si usa:
:math:`p\sim q`.

.. index:: bisimulation equivalence definiton

.. _ref_bisimulation_equivalence_definition:

Questa equivalenza si definisce in due passi:


1. una relazione binaria :math:`\mathbb{R}` su un set di espressioni regolari 
   :math:`\mathcal{E}` (ovvero: :math:`\mathbb{R} \subseteq \mathcal{E} \times \mathcal{E}`)
   è una *bisimulation relation*
   tutte le volte che per qualunque coppia di processi :math:`(P, Q)` in :math:`\mathbb{R}`, per qualunque
   possibile azione *a*, valgono le proprietà:
   
   1. il passaggio di *P* a *P*\ ``'`` implica il passaggio da *Q* a *Q*\ ``'`` e la coppia
      :math:`(P', Q')` appartiene ad :math:`\mathbb{R}` [#]_ [#]_;
   2. inoltre il passaggio di *Q* a *Q*\ ``'`` implica il passaggio da *P* a *P*\ ``'`` e la coppia
      :math:`(P', Q')` appartiene ad :math:`\mathbb{R}`;
   3. *P* può terminare immediatamente se e solo se lo può fare anche *Q* [#]_;
        
2. due sistemi *P* e *Q* appartenenti al set delle R.E. (ovvero: :math:`P, Q \in \mathcal{E}`)
   sono *bisimili* (*bisimular*) se e solo se esiste una *bisimulation relation*
   :math:`\mathbb{R}` tale che la coppia (*P*, *Q*) appartiene ad :math:`\mathbb{R}`.
        
Si osservi:

* che la definizione precedente l'abbiamo data per l'insieme delle R.E.; ma
  è possibile utilizzarla per qualunque linguaggio formale di specifica di sistemi distribuiti;
* questa è una relazione di equivalenza **valida** per sistemi distribuiti; ne esistono altre,
  ma questo corso si limita a questa.

(*esempio4*) Con in mente la definizione precedente, applichiamo il secondo punto 
all'esempio relativo alla :ref:`stessa semantica <ref_esempio_stessa_semantica>`,
che riportiamo qui di seguito con gli stati nominati:

.. _ref_images_esempio4:

.. image:: images/esempio4.*
   :align: center

Quindi voglia costruite una :math:`\mathbb{R}` in cui tutte le coppie :math:`(s_x, q_x)` 
siano in grado di simularsi reciprocamente. Tentiamo con:

1. :math:`(s_1, q_1) \in \mathbb{R}`, dopo di che tentiamo:
2. :math:`(s_2, q_2) \in \mathbb{R}`, ma questa non è possibile perché :math:`s_2`
   è in grado di eseguire *b* e *c*, mentre :math:`q_2` solo *b*; allora proviamo
3. :math:`(s_2, q_4) \in \mathbb{R}`, ma anche questa non è possibile perché :math:`s_2`
   è in grado di eseguire *b* e *c*, mentre :math:`q_4` solo *c*.
   
Non potendo costruire la relazione :math:`\mathbb{R}` per tutte le coppie :math:`(s_x, q_x)` i due 
sistemi non sono bisimili.

(*esempio5*) Vediamo ora l'esempio:

.. _ref_images_esempio5:

.. image:: images/esempio5.*
   :align: center

In questo caso:

1. :math:`(s_1, q_1) \in \mathbb{R}`, dopo di che tentiamo:
2. :math:`(s_2, q_2) \in \mathbb{R}`, va bene perché sono entrambi terminali; 
   allora proviamo anche:
3. :math:`(s_2, q_4) \in \mathbb{R}`, anche questa va bene per lo stesso motivo.

per cui i due sistemi sono equivalenti in quanto vale: 

.. math::

   \mathbb{R} = \{ (s_1, q_1),  (s_2, q_2), (s_2, q_4) \}

Questo vuol dire che potremmo semplificare il sistema a destra sostituendolo con quello a
sinistra.

Se analizziamo con lo stesso metodo :ref:`l'esempio3 <ref_images_esempio3>`, arriviamo
alla conclusione che i due sistemi non sono bisimili perché gli stati
:math:`s_2` e :math:`q_4` non si possono simulare reciprocamente: :math:`s_2`
è in grado di eseguire l'azione *b*, mentre :math:`q_4` è uno stato terminale.

..

  Lezione del *2018-12-13*.

La nozione di *bisimulation equivalence* è importante anche per verificare
se le attività di :ref:`refinement <ref_refinement>` sono avvenute correttamente.
In particolare deve essere: :math:`P_{spec} \sim  P_{impl}` per essere certi che il modello
implementativo non si sia discostato dai requisiti del sistema.

Quindi, dati due sistemi da confrontare, sono importanti gli algoritmi per effettuare
la verifica della loro equivalenza, o meno. A questo fine esistono vari algoritmi.
Tra i più efficienti vi sono quelli introdotti da Kanellakis e Smolka (vedi [SANR2011]_ pag.105 e succ.), che partono
da una partizione iniziale degli stati, procedendo per raffinamenti successivi
ottenuti applicando le trasformazioni della :ref:`definizione di bisimulation equivalence <ref_bisimulation_equivalence_definition>`.

Questi algoritmi sono lineari rispetto il numero di transizioni. E sono in grado di 
indicare quali path di transizioni non possono essere simulati da un sistema
rispetto l'altro. Analizzando questi path è possibile decidere come modificare
i sistemi ed effettuare una nuova analisi di bisimulazione.




-------

.. [#] Così come i sistemi concorrenti o multiprocesso.

.. [#] Detta anche *trace equivalence*.

.. [#] Attenzione: in realtà si tratta di più attività di test, ogni attributo
   di sistema avrà la sua.
   
.. [#] Questa è una analogia rispetto il fatto che due algoritmi sequenziali sono 
   equivalenti se e solo se soddisfano la stessa classe di test.
   
.. [#] Si può anche leggere *P* è simulato da *Q*.
   
.. [#] In inglese: *preorder*.

.. [#] :math:`\sim_s` indica equivalenza tramite simulzione.

.. [#] Come vedremo nell'esempio che segue, la risposta è **no**.

.. [#] Qui :math:`\omega` indica terminazione con successo.

.. [#] Sopratutto in fase di test.

.. [#] Questa è una definizione ricorsiva: data la coppia di partenza 
   :math:`(P, Q) \in \mathbb{R}`, si richiede che la coppia di destinazione
   :math:`(P', Q')` sia a sua volta in :math:`\mathbb{R}`. Ovvero se la coppia
   di partenza è una bisimulazione, lo deve essere anche la coppia di destinazione.
   
.. [#] Attenzione a mantenere l'ordine della coppia (*P*, *Q*).

.. [#] :math:`P \surd \; \text{iff} \; Q \surd` deve valere perché noi stiamo considerando
   la sintassi delle R.E. Senza questa non possiamo distinguere tra gli stati terminali *0* e *1*.