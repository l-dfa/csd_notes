
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - CCS
   :description language=en: Notes on Complex Systems Design - CCS
   :keywords: Complex Systems Design, CCS, calculus of communication systems
   :author: Luciano De Falco Alfano

.. index:: CCS

.. _ref_CCS:
   
CCS
==========================================

.. contents:: 
   :local:

..

  Lezione del *2018-12-13*.


.. index:: about ccs

.. _ref_about_ccs:
   
About CCS: a Calculus of Communicating Systems
-----------------------------------------------
  
CCS (acronimo di *a Calculus of Communicating Systems*) è un linguaggio di specifica di
sistemi distribuiti molto conosciuto, ideato da Robin Milner ([MILN1989]_) [#]_.

Usa primitive diverse da quelle che abbiamo visto in R.E. In particolare:

.. math::

   p ::= nil \quad\vert\quad \mu \cdot p \quad\vert\quad p + q \quad\vert\quad p \parallel_A q \quad\vert\quad x = p \,\vert \, x \quad\vert\quad p[\phi]

con i seguenti significati:

* :math:`nil` indica *deadlock* [#]_; CCS non ha il concetto di terminazione con successo;
  esiste solo il concetto di arresto, e conseguente *deadlock*;
* :math:`\mu \cdot p` si chiama *action prefixing*; è una forma semplificata di
  *sequential composition*, con :math:`\mu \in Act`; intuitivamente il sistema
  esegue l'attività :math:`\mu` e poi passa il controllo a :math:`p` [#]_;
* :math:`p + q` indica  *non determinismo*;
* :math:`p \parallel_A q` indica *parallel composition* per un sottoinsieme *A* [#]_ di 
  azioni: :math:`A \subseteq Act`; ovvero il fatto
  che i due processi lavorano in parallelo e che ogni tanto comunicano 
  l'uno con l'altro tramite le azioni che appartengono ad *A*; ad esempio:
  :math:`a \, nil \parallel_{\{a\}} a \, nil` sono due processi che si sincronizzano,
  quindi eseguono l'azione *a* e poi terminano; i due processi :math:`p` e 
  :math:`q` sono autonomi nell'eseguire le azioni che non appartengono ad *A*;
  ad esempio: :math:`b \, a \, nil \parallel_{\{a\}} a \, nil`, il processo a 
  sinistra (sx) esegue l'azione *b*, mentre il processo a destra (dx) rimane in attesa 
  di sincronizzazione sull'azione *a*, quando sx termina l'attività per *b*,
  parte la sincronizzazione per *a* tra sx e dx, quindi si procede eseguendo 
  **entrambi** *a*;
* :math:`x = p \vert x` è una equazione di processo che può
  essere utilizzata per indicare *ricorsione*; :math:`x` è una variabile del processo
  :math:`p` [#]_, quando viene raggiunta nell'elaborazione del processo viene sostituita
  dal processo stesso; è analogo a quello che accade nella programmazione sequenziale,
  con la differenza [#]_ che in questo caso possiamo voler definire processi ricorsivi divergenti,
  ovvero che non terminano mai, ad esempio: :math:`x = a \, x, a \in Act` definisce
  un processo in grado di ripetere l'azione *a* senza mai arrivare ad uno stop;
* :math:`x = p [\phi]` indica *relabeling*; :math:`\phi` è detta *relabeling function*,
  è una funzione da azione ad azione (:math:`\phi : Act \rightarrow Act`)
  e rinomina azioni del processo *p* in altre azioni; in generale, se *p* può
  eseguire una azione *a*, allora esegue una azione :math:`\phi(a)`; questa è spesso applicata
  in progettazione quando vi sono componenti simili che differiscono solo
  perché cambia il mezzo del canale su cui operano, ma la struttura dei diversi componenti 
  è la stessa, in questi casi si definisce un solo componente e si specifica la 
  funzione di *relabeling* adatta alle diverse situazioni; un'altra applicazione
  consiste nel rendere private determinate azioni.

In questa trattazione ci concentriamo sulle specifiche funzionali, quindi non consideriamo
vincoli temporali o di localizzazione; le attività sono considerate atomiche.

Precisiamo il discorso riguardo le azioni. Queste sono classificate come:

* *azioni visibili*, sono agite dall'ambiente esterno al (o dal) sistema,
  formano un insieme: :math:`Act = \{a, b, c, \cdots \}`;
* *azioni invisibili*, chi è all'esterno del sistema non le agisce (né le riceve),
  osserva un cambiamento di stato del sistema, ma non è in grado di relazionarlo 
  ad una azione dall'esterno; si indica con un singolo elemento :math:`\tau`
  perché anche se fossero più di una il fatto di non poterle distinguere
  non ne permette la discriminazione.
  
Quindi l'insieme delle azioni (le visibili più le invisibili) sarà formato da
:math:`Act_\tau = Act \cup \{ \tau \}`.

In questo ambito è possibile la *relabeling function*
viene usata per rimappare su :math:`\tau` le azioni che vogliamo mantenere private:
:math:`\phi : Act_\tau \rightarrow Act_\tau`
con la condizione che le azioni invisibili rimangano tali: :math:`\phi(\tau) = \tau`.


.. index:: ccs examples_1

.. _ref_ccs_examples_1:
   
CCS examples (1)
-----------------

Abbiamo visto che :math:`x = a \, x; \, a \in Act` definisce
un processo in grado di ripetere l'azione *a*.

.. _ref_esempio_04_01:

(*Esempio 1*) Ma è anche possibile definire più variabili con più processi. Come nel seguente:

.. math::

   \begin{cases} 
     x = a \, x + b \, y \\
     y = b \, y + b \, nil + a \, x
   \end{cases} 

che sarà rappresentato dalla sequente automa a stati finiti:

.. image:: images/esempio6.*
   :align: center
   
il processo *x* è in grado di eseguire ripetutamente l'azione *a*: quando si comanda 
l'azione *b*, il controllo passa ad *y* che può eseguire ripetutamente *b*
oppure terminare (*nil*), o con una *a* tornare su *x*.

.. _ref_esempio_04_02:

(*Esempio 2*) Riguardo la *relabeling function*, dato il sistema :math:`(a\,b\,\vert\,a)[\phi]`
con :math:`\phi(a)=\tau, \phi(b)=b`

.. image:: images/esempio7.*
   :align: center

ovvero *non osserveremo* l'attività *a* in quanto nascosta dal *relabeling*, mentre
protremo osservare l'attività *b* ad essa successiva.

.. index:: CCS by operational semantics

.. _ref_CCS_by_operational_semantics:

CCS by operational semantics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..

  Lezione del *2018-12-21*.

Quel che abbiamo visto finora è la definizione della sintassi del CCS, 
tramite una grammatica context free, i cui elementi terminali sono :math:`\mu` e :math:`nil`.

E abbiamo introdotto informalmente la sua semantica; ora per formalizzarla,
usiamo la semantica operazionale, riassunta nella seguente tabella; in essa 
la prima colonna è un identificativo, segue l'operatore in osservazione, e
la terza colonna riporta le regole d'induzione da applicare.

+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| row | operator                   |  rule                                                                                                                                                                                                           |
|     |                            |                                                                                                                                                                                                                 |
+=====+============================+=================================================================================================================================================================================================================+
|  1  | *nil*                      | no rule                                                                                                                                                                                                         |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  2  | :math:`\mu \cdot p`        | .. math::                                                                                                                                                                                                       |
|     |                            |    \frac{\diagup}{\mu \cdot p \xrightarrow{\mu} p}                                                                                                                                                              |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  3  | :math:`p + q`              | .. math::                                                                                                                                                                                                       |
|     |                            |    \frac{p \xrightarrow{\mu} p'}{p + q \xrightarrow{\mu} p'};\quad \frac{q \xrightarrow{\mu} q'}{p + q \xrightarrow{\mu} q'}                                                                                    |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  4  | :math:`p \parallel_A q`    | .. math::                                                                                                                                                                                                       |
|     |                            |    \frac{p \xrightarrow{\mu} p', \, \mu \notin A}{p \parallel_A q \xrightarrow{\mu} p' \parallel_a q};\quad \frac{q \xrightarrow{\mu} q', \, \mu \notin A}{p \parallel_A q \xrightarrow{\mu} p \parallel_a q'}; |
|     |                            |    \quad \frac{p \xrightarrow{a} p', \, q \xrightarrow{a} q', \, a \in A}{p \parallel_A q \xrightarrow{a} p' \parallel_a q'}                                                                                    |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  5  | :math:`x = p \,\vert \, x` | .. math::                                                                                                                                                                                                       |
|     |                            |    \frac{p \{ p \diagup x \} \xrightarrow{\mu} p'}{p \xrightarrow{\mu} p' } \; x = p                                                                                                                            |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  6  | :math:`p[\phi]`            | .. math::                                                                                                                                                                                                       |
|     |                            |    \frac{p \xrightarrow{\mu} p'}{p[\phi] \xrightarrow{\phi(\mu)} p'[\phi] }                                                                                                                                     |
+-----+----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

  
La **riga 1** descrive *nil*, cioè un processo terminato. In quanto tale non può 
eseguire alcuna azione, quindi non ha regole.
  
La **riga 2** descrive la *action prefixing*. Questo è un assioma: non vi sono precondizioni:
il sistema esegue :math:`\mu` e passa a comportarsi come *p* [#]_.

La **riga 3** riporta le due regole per la composizione non deterministica. Come al solito,
quando si esegue l'azione :math:`\mu`, si esegue una scelta, o per :math:`p \xrightarrow{\mu} p'`
o per :math:`q \xrightarrow{\mu} q'`, scardinando l'altra.

La composizione parallela, alla **riga 4**, si sviluppa con tre regole d'induzione.
Dovremo considerare i casi di azioni di sincronizzazione e non di sincronzzazione.

Consideriamo prima il caso in cui :math:`\mu` non sia una azione di sincronizzazione.
In questa condizione, la prima regola esplicita il caso in cui *p* esegua :math:`\mu`:
:math:`p \xrightarrow{\mu} p'`, in tal caso il sistema procede eseguendo :math:`p'`
in parallelo con *q*: :math:`p' \parallel_A q`. Qui il secondo processo non viene 
scartato: nella composizione parallela, a differenza della scelta non deterministica,
il processo non coinvolto nella esecuzione dell'azione, continua ad essere presente,
pronto comunque ad operare per quanto di sua competenza.

La seconda regola descrive lo stesso comportamento, ma simmetrico rispetto il 
processo coinvolto: qui è *q* che esegue l'azione :math:`\mu`.

Con la terza regola cambiamo contesto, e consideriamo una azione di sincronizzazione.
Quindi in questo caso per una azione :math:`a \in A`, i due processi
reagiscono entrambi e contemporaneamente portandosi rispettivamente in 
:math:`p \xrightarrow{a} p'` e in :math:`q \xrightarrow{a} q'`, il sistema
procederà con questi due sistemi in parallelo.

.. _ref_esempio_04_03:

(*Esempio 3*) Si osservi come questa regola sia mandatoria: la sincronizzazione *deve*
avvenire. Ad esempio si consideri il caso :math:`a \parallel_{a} nil`, 
qui il sistema a sx può eseguire l'azione di sincronizzazione *a*, ma il sistema
dx *no*; e questo porta al blocco di tutto il sistema perché sx per eseguire *a* 
si mette in attesa dell'avvio di dx, che non avverrà in quanto sordo ad *a*.


.. _ref_esempio_04_04:

(*Esempio 4*) Altro esempio: :math:`a \parallel_{\{a\}} b \cdot a`. 
Qui il sistema a sx può eseguire l'azione di sincronizzazione *a*, ma il sistema
a dx deve *prima* eseguire una azione *b*, e *poi* sarà in grado di sincronizzare 
con *a*: :math:`(a \parallel_{a} b \cdot a) \xrightarrow{b} (a \parallel_{\{a\}} a) \xrightarrow{a} (nil \parallel_{\{a\}} nil)`. 

La **riga 5** descrive la regola di ricorsione. La forma :math:`p \{ p \diagup x \}`
significa: "dato il processo *p*, nel suo contesto sostituisco la variabile *x*
con il processo *p*" [#]_. Se il processo, così modificato, esegue una azione :math:`\mu`
andando in *p*\ ``'`` allora il nostro processo è in grado di andare in *p*\ ``'``.

.. _ref_esempio_04_05:

(*Esempio 5*) Ad esempio, prendiamo la seguente *definizione*: :math:`x \, \overset{def}{=} \, a \, x + b \, nil`.
Qui *x* è la nostra variabile, e il processo *p* é :math:`a \, x + b \, nil`.
Prendiamo questo processo e facciamo *unfolding* sulla *x* sostituendoci *p*, 
otteniamo :math:`a \, (a \, x + b \, nil) + b \, nil`.

Questo processo può eseguire una azione *a* o una *b*. Prendiamo la *a*, otteniamo
che la precondizione va in :math:`a \, x + b \, nil`, quindi l'induzione vale:

.. math::

   \frac{a \, (a \, x + b \, nil) + b \, nil \xrightarrow{a} (a \, x + b \, nil)}{(a \, x + b \, nil) \xrightarrow{a} (a \, x + b \, nil)}

Sullo stesso esempio, eseguendo una *b* andremmo in *nil*:

.. math::

   \frac{a \, (a \, x + b \, nil) + b \, nil \xrightarrow{b} nil}{(a \, x + b \, nil) \xrightarrow{b} nil}

La *riga 6* descrive il *relabeling*. Se il processo *p*, eseguendo :math:`\mu`
va in *p*\ ``'``, allora il processo sottoposto a relabeling (ovvero: :math:`p[\phi]`)
esegue l'azione :math:`\phi(\mu)` e va nel relativo stato di arrivo: :math:`p'[\phi]`.

.. index:: ccs examples_12

.. _ref_ccs_examples_2:
   
CSS Examples (2)
-----------------

.. _ref_esempio_04_06:

(*Esempio 6*) Riguardo il *relabeling*, riprendiamo :ref:`l'esempio 4 <ref_esempio_04_04>`
(per comodità, era: :math:`a \parallel_{\{a\}} b \cdot a`), diciamo di voler
rendere privata l'attività *a* che è l'attività di sincronizzazione,
ma non l'attività di preprocessamento *b*, che è conosciuta da tutto l'ambiente.

Per raggiungere questo scopo, definiamo la funzione di *relabeling* come
segue:

.. math::

   \phi(\mu)= \begin{cases}
                \mu  \quad & \forall \mu \neq a \\
                \tau \quad & \mu = a
              \end{cases}

in pratica :math:`\phi` replica se stesso salvo quando l'argomento è *a*, in tal caso 
nasconde la funzione richiesta.

In queste condizioni abbiamo:
:math:`(a \parallel_{a} b \cdot a)[\phi] \xrightarrow{b} (a \parallel_{\{a\}} a)[\phi] \xrightarrow{\tau} (nil \parallel_{\{a\}} nil)`. 
Ovvero il preprocessing è pubblico, noto a tutti, ma l'azione di sincronizzazione, pur avvenendo,
è invisibile all'esterno.

.. _ref_esempio_04_07:

(*Esempio 7*) Ad esempio, applichiamo al sistema: :math:`a \cdot b \parallel_{\{b\}} c \cdot b`
le regole di semantica operazionale viste in precedenza. Otteniamo il seguente
*labeled transition system*:

.. image:: images/esempio_04_07.*
   :align: center

che mostra la forma a diamante classica dell\ ``'``\ *interleaving* [#]_.

.. _ref_esempio_04_08:

(*Esempio 8*) Volendo esemplificare il caso base della *parallel composition*,
abbiamo: :math:`a \parallel b`, dove si osserva che *non* abbiamo
indicato operazioni di sincronizzazione. In queste condizioni abbiamo il seguente
*labeled transition system*:

.. image:: images/esempio_04_08.*
   :align: center

.. _ref_esempio_04_09:

(*Esempio 9*) Confrontiamolo con il sistema :math:`a \cdot b + b \cdot a` che, invece di 
usare due processi in parallelo, applica la *nondeterministic composition*. Abbiamo:

.. image:: images/esempio_04_09.*
   :align: center

Questo *labeled transition system* è isomorfo con il precedente. Cambiano solo le
denominazioni degli stati, che, ai nostri fini, non è influente. L'isomorfismo
ci permette di affermare che :math:`a \parallel b \sim a \cdot b + b \cdot a`, ovvero
i due sistemi sono equivalenti. Milner dimostrò che è sempre possibile sostituire
ad un sistema dotato di componenti in parallelo, un sistema *non dotato* di componenti in parallelo.

Questo ci fa comprendere che la *parallel composition* non è una operazione
fondamentale. Le operazioni fondamentali sono la sequenzialità, il non determinismo
e la ricorsione (si riveda la sintassi delle :ref:`espressioni regolari <ref_regular_expressions>`,
che non era dotata di *parallel composition*).

.. _ref_esempio_04_10:

(*Esempio 10*) Attenzione al fatto che per fare la conversione in sintassi *interleaving* vanno 
considerate tutte le possibili azioni, per ogni stato. Ad esempio consideriamo
tre processi in parallelo: :math:`a \parallel b \parallel c`, abbiamo la seguente LTS:

.. image:: images/esempio_04_10.*
   :align: center

siamo passati da 4 stati e altrettante transazioni, a 8 stati e 12 transazioni.

.. index:: considerazioni sul parallelismo

.. _ref_considerazioni_sul_parallelismo:
   
Considerazioni sul parallelismo
----------------------------------

Nel considerare gli esempi precedenti, con il concetto di equivalenza tra 
processi in parallelo e in *interleaving*, ci siamo attenuti agli aspetti 
funzionali, ovvero: cosa il sistema è in grado di fare.

Ma vi sono altre possibili considerazioni che possono influire sulle scelte 
architetturali [#]_. In questo caso si considera la necessità di fare
*noninterleaving semantics* per progettare:

1. sistemi temporizzati;
#. sistemi localizzati;
#. sistemi causali (Nota: **casuali**, *non* causali).

Per i **sistemi temporizzati** [#]_, usualmente vi è un vincolo temporale da rispettare,
quindi l'aspetto prestazionale è fondamentale. E i sistemi con vera architettura parallela
sono più veloci di sistemi in *interleaving*, a parità di hardware.
Ovvero: :math:`(a \parallel b) \overset{\sqsubset}{\sim} (a\, b + b \, a)`.

E' possibile capirlo osservando le relative durate con le diverse architetture.
Ad esempio se la durata di *a* è 1 (:math:`d(a) = 1`), e la durata di *b*
è 2 (:math:`d(b) = 2`), allora la durata del parallelo sarà pari al massimo delle
durate dei processi in parallelo, ovvero 2: (:math:`d(a \parallel b) = max(d(a), d(b)) = 2`) [#]_.

Invece considerando l'architettura in *interleaving*, la durata sarà la somma
delle durate dei due processi, perché sono eseguiti in sequenza:
:math:`d(a\, b + b \, a) = (d(a)+d(b)) \oplus (d(b)+d(a)) = 3` [#]_.

Nei sistemi *real time* l'equivalenza per bisimulazione viene estesa 
considerando anche i vincoli temporali.

Un esempio di sistema temporizzato molto diffuso sono i sistemi di login,
che tipicamente impongono un timeout all'utente. Altri esempi sono i sistemi
di controllo di processi industriali, i sistemi di puntamento o i sistemi
di ausilio alla guida.
   
Anche i **sistemi localizzati** impongono vincoli, in questo caso i processi 
sono localizzati in ambienti fisici. Si pensi a stampanti con caratteristiche 
diverse poste in locali differenti di un edificio. Ma potrebbero essere anche 
sistemi posizionati in città distanti tra loro.

In questo caso non vi è equivalenza tra i due sistemi (vale
:math:`l_1 :: a \parallel l_2 :: b \nsim l_3 :: (a\, b + b \, a)`) perché
non è la stessa cosa avere una tipografia a colori a Roma e una 3D a Torino
piuttosto che una tipografia in grado di fare colori e 3D a Firenze, sia pure più lentamente
di due processi in parallelo.

Nei sistemi con **relazioni di causalità** abbiamo relazioni di dipendenza.
Tipicamente :math:`a \parallel b` non hanno dipendenze: ogni processo gira 
liberamente per conto proprio. Invece in :math:`(a \cdot b + b \cdot a)`
le relazioni di cusalità vi sono: a sx *b* è causato da *a*, mentre a dx
*a* dipende da *b*.



-------

.. [#] Robin Milner è stato anche l'ideatore del linguaggio `ML (Meta Language) <https://en.wikipedia.org/wiki/ML_(programming_language)>`_.

.. [#] :math:`nil` non appartiene all'insieme delle azioni: :math:`nil \notin Act`.

.. [#] In altri termini: non vi è un processo a monte che deve terminare
   con successo per passare il controllo a valle; e se va in deadlock 
   il tutto si ferma; qui il sistema esegue :math:`\mu` e sicuramente
   prosegue con :math:`p`.
   
.. [#] Il sottoinsieme *A* è detto insieme di sincronizzazione. Sincronizzazione 
   in questo ambito è un sinonimo di comunicazione tra i due processi.
   
.. [#] Esisterà un insieme di variabili del processo: :math:`x \in Var = \{x, y, z, \cdots \}`.

.. [#] Nella programmazione sequenziale non si programma mai in modo divergente:
   avremmo un codice che non termina mai (o va in stack overflow), quindi dato 
   un input non otterremmo un output: comportamento non desiderabile.
   
.. [#] Notare il fatto che la :ref:`sequential composition <ref_operational_semantics_sequential_composition>` 
   era più complessa; richiedeva due regole: una per gestire il caso generale 
   :math:`e_1 \xrightarrow{a} e_1'`, l'altro per gestire il caso di terminazione di 
   :math:`e_1`:  :math:`e_1 \surd \, , \, e_2 \xrightarrow{a} e_2'`. L\ ``'``\ *action
   prefixing* non considera la terminazione: il sistema *è in grado* di eseguire
   :math:`\mu`, quindi lo esegue e va avanti comportandosi come *p*.
   
.. [#] Questa attività di sostituzione è detta *unfolding*.

.. [#] Per interleaving si intende l'esecuzione di processi concorrenti facendo
   ricorso al *time sharing* delle risorse (cpu). Se abbiamo due processi da eseguire,
   e una sola cpu, dovremo eseguire prima un processo e poi l'altro. Quale dei due viene
   eseguito prima non deve essere importante, quindi lo schema deve essere 
   simmetrico, permettendo entrambi i percorsi. Nel nostro caso questo schema
   si evidenzia perché la semantica operazionale mette in rilievo una 
   operazione per volta, operando un *time sharing* delle attività.
   
.. [#] Ovvero decidere di usare dei processi in parallelo in un sistema 
   multiprocessore, piuttosto che eseguire una serie di processi in interleaving.
   
.. [#] In inglese: *real time*.

.. [#] Con la condizione che i due processi siano realmente in parallelo:
   usano due processori diversi.
   
.. [#] Con :math:`\oplus` abbiamo indicato una somma esclusiva: o viene eseguita
   la somma a dx, o quella a sx; dipende dal ramo scelto dalla macchina
   che sta eseguendo il sistema.