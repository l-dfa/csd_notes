
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Controllo del modello
   :description language=en: Notes on Complex Systems Design - Model checking
   :keywords: Complex Systems Design, model checking
   :author: Luciano De Falco Alfano

.. index:: model checking

.. _ref_model_checking:
   
Model Checking
===============

.. contents:: 
   :local:

..

  Lezione del *2019-01-24*

Il *model checking* è l'alternativa alla *equivalence checking* nel fare 
la verifica della modellazione del sistema.

Come *equivalence checking* in questo corso si è visto in particolare l'uso della 
:ref:`bisimulazione <ref_bisimulation>`.

E\ ``'`` necessario considerare il fatto che, prese nella loro accezione più
generale, i controlli di equivalenza appartengono alla classe dei problemi
non decidibili. Questo vale per la programmazione sequenziale, ma anche per
l'algebra di processi se non si impone la limitazione dello studio di 
sistemi che originano modelli finiti.

Avendo a che fare con problemi *in generale* indecidibili, si può decidere di fare 
ricorso a metodologie di test.

Queste metodologie impongono un cambio di paradigma. Un test verifica il comportamento 
del sistema in un particolare caso, confrontandolo con un risultato atteso. Se il riscontro
è positivo il test ha dimostrato il corretto funzionamento del sistema ... in *quel* caso.

Se un sistema può gestire un numero infinito di casi, per dimostrare *in toto*
il suo corretto funzionamento dovremmo applicare infiniti test. E per farlo
avremmo bisogno di una infinita quantità di tempo.

Il *model checking* è simile al testing. A differenza della bisimulazione,
che controlla tutta la descrizione del sistema, confrontandola con
un'altra di riferimento, il *model checking* verifica se una particolare 
proprietà é soddisfatta dal nostro sistema.

Dato un sistema scritto con un qualunque linguaggio formale di specifica [#]_,
possiamo modellarlo con un *labeled transition system*. A questo punto consideriamo
una sua proprietà.

Ad esempio, data una *coffee machine*, vogliamo verificare la proprietà: "eroga la bevanda 
solo dopo avere acquisito il pagamento?".

Per esprimere la proprietà da verificare si usa una formula :math:`\varphi` che lo 
esprime tramite un linguaggio formale :math:`\mathbb{L}`.

Il *model checking* è l'algoritmo che verifica se il modello *LTS* soddisfa la 
formula :math:`\varphi` predetta. 

In generale il processo di progettazione si svolgerà come segue.

Descriveremo le specifiche del sistema tramite un linguaggio formale di specifica,
ad esempio CCS.

Applicando la semantica operazionale alla specifica, genereremo (automaticamente)
il modello *LTS* del nostro sistema.

Dato il linguaggio :math:`\mathbb{L}` per esprimere le proprietà del modello [#]_,
ad esempio una *context free grammar* come segue:

.. math::
   \varphi ::= true \,\vert\, \neg\,\varphi \,\vert\, \varphi_1 \wedge \varphi_2 \,\vert\, <\!\mu\!>\cdot\varphi
   
gli elementi del linguaggio sono:

* il terminale *true*,
* l'operatore di negazione: :math:`\neg\varphi`,
* l'operatore di congiunzione: :math:`\varphi\wedge\varphi`,
* l'operatore *diamond* :math:`<\!\mu\!>\cdot\varphi`, dove :math:`\varphi` è una
  azione visibile o invisibile (:math:`\varphi\in Act_\tau`).

Intuitivamente, avremo il seguente comportamento. Un algoritmo per verificare
la formula :math:`\varphi` rispetto uno stato dello *LTS*, valuterà
se :math:`\varphi` è soddisfatta dallo stato in analisi:

* se soddisfatta vale *true*;
* se vale :math:`\neg true`, allora è soddisfatta :math:`\neg\varphi`;
* se valgono *true* sia :math:`\varphi_1` che :math:`\varphi_2`, allora vale
  *true* anche :math:`\varphi_1\wedge\varphi_2`;
* se esiste uno stato in grado di eseguire :math:`\mu` andando in uno 
  stato successivo in cui :math:`\varphi` che sia *true*, allora lo è
  :math:`<\!\mu\!>\cdot\varphi`.
  
Formalmente, "uno stato *s* soddisfa la formula :math:`\varphi` del linguaggio
:math:`\mathbb{L}` (ovvero:  :math:`s \vDash \varphi \in \mathbb{L}`)
se e solo se valgono le seguenti regole induttive su :math:`\varphi`":

* :math:`s \vDash true`, vale sempre;
* :math:`s \vDash \neg \varphi` se :math:`s \vDash true` è *false*;
* :math:`s \vDash \varphi_1 \wedge \varphi_2` se :math:`s \vDash \varphi_1` e :math:`s \vDash \varphi_2`;
* :math:`s \vDash <\!\mu\!> \cdot \varphi` se :math:`s \xrightarrow{\mu} s'` e :math:`s' \vDash \varphi`;

Si può usare De Morgan per definire il *false*, l'operatore di disgiunzione, e
così via. In particolare:

* il *next*:math:`\varphi` definito come:

  * :math:`s \vDash X_\varphi` se :math:`s \xrightarrow{\mu} s'` e :math:`s' \vDash \varphi` per
    qualunque :math:`\mu \in Act_\tau` [#]_;
    
* e lo *until*:math:`\varphi` come segue:

  * :math:`s \vDash \cup_\varphi` se :math:`s \xrightarrow{\mu_1} \cdots  \xrightarrow{\mu_n} s'`
    tale che :math:`s' \vDash \varphi` e :math:`\mu_1 \cdots \mu_n \in Act_\tau` [#]_.

Esiste un teorema che afferma: "dati i processi *P* e *Q* espressi in CCS, questi sono
bisimili se e solo se per ogni :math:`\varphi` in :math:`\mathbb{L}` avviene che
*P* soddisfa :math:`\varphi` se e solo se *Q* soddisfa :math:`\varphi`":

.. math::
   P \sim_{bisimul.based} Q \quad\text{iff}\; (\forall\varphi\in\mathbb{L},\; P \vDash \varphi \;\text{iff}\; Q \vDash \varphi)
   
intuitivamente: due processi sono equivalenti se e solo se soddisfano lo stesso set di casi di prova.





--------------------

.. [#] R.E., CCS, CSP, LOTOS, ...

.. [#] Tipicamente questi sono linguaggi logici.

.. [#] Ovvero lo stato *s* soddisfa l'attributo :math:`\varphi` dopo avere raggiunto
   un altro stato :math:`s'` tramite una qualunque azione.

.. [#] In questo caso, intuitivamente, raggiungo uno stato che soddisfa :math:`\varphi`
   con una sequenza di azioni.