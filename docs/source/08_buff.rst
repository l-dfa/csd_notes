
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Buffer BUFF
   :description language=en: Notes on Complex Systems Design - Buffer BUFF
   :keywords: Complex Systems Design, buffer, BUFF
   :author: Luciano De Falco Alfano

.. index:: BUFF buffer

.. _ref_buff_buffer:
   
BUFF Buffer
=============

.. contents:: 
   :local:

..

  Lezione del *2019-01-17*

Sempre facendo riferimento alle specifiche illustrate nel :ref:`fifo buffer <ref_fifo_buffer>`,
consideriamo una implementazione a BUFF, piuttosto
diffusa per i buffer in hardware.

Come nel caso della :ref:`PIPE <ref_pipe_buffer>`, abbiamo una serie di celle,
ognuna con la capacità di memorizzare una informazione: :math:`c_0, \cdots, c_{N+1}`.

Ma è diverso il modo di funzionamento. Nella PIPE l'informazione, dal momento
dell'input, a quello dell'output, attraversava tutte le celle: da :math:`c_{N+1}`
a :math:`c_0`.

Per ottimizzare le prestazioni del buffer, vogliamo evitare questa attività
di trasporto da una cella all'altra, quindi utilizzarle come un array monodimensionale
ad accesso diretto, organizzato come un buffer circolare.

L'organizzazione come buffer circolare ci permette di memorizzare le informazioni
secondo il loro ordine di arrivo, eventualmente impegnando solo parzialmente
la memoria a disposizione (alcune celle sono vuote, non contengono informazioni).

Per fare ciò, ad un determinato istante, ci servono:

* un indice *i* che punta alla posizione dell'elemento più vecchio presente nell'array;
* una dimensione *m* che riporta il numero di dati validi memorizzati;

Si preleva utilizzando l'indice *i*, mentre si inserisce un nuovo dato
usando l'indice :math:`(i+m) mod (N+2)`.
L'uso della aritmetica modulo *N+2* è necessario per riutilizzare
le celle in testa al lbuffer quando l'indice per registrare supera 
la capacità del buffer (:math:`N+2`).

La seguente immagine da\ ``'`` una idea della situazione:

.. image:: images/buff.*
   :align: center

Ad esempio: :math:`i = 3, m = 10` significa che il dato da prelevare (il più anziano) è
in :math:`c_2`, e che i dati validi sono nelle celle da :math:`c_2` a :math:`c_{12}`.
:math:`c_{12}` è il dato più *giovane*, quello entrato per ultimo. Se il buffer
avesse capacità complessiva 12, il dato più giovane sarebbe in :math:`(3+10) mod (12) = 1`
ovvero :math:`c_0`.

Ad esempio, con un buffer di 3 celle (:math:`N=1`) possiamo avere la seguente sequenza
di eventi:

==============   ===================   =======   =========   ================   ================   ================
 time             action                 i         m          1: :math:`c_0`     2: :math:`c_1`     3: :math:`c_3` 
==============   ===================   =======   =========   ================   ================   ================
 :math:`t_0`                            0         0
 :math:`t_1`       in(0)                1         1            0
 :math:`t_2`       in(1)                1         2            0                 1
 :math:`t_3`       in(1)                1         3            0                 1                   1
 :math:`t_4`       out(0)               2         2                              1                   1
 :math:`t_5`       in(1)                2         3            1                 1                   1
 :math:`t_6`       out(1)               3         2            1                                     1
==============   ===================   =======   =========   ================   ================   ================

Inizialmente (:math:`t_0`) il buffer sarà vuoto. Di conseguenza :math:`i = 0` e :math:`m = 0`.

Se inseriamo uno *0*, la posizione *i* punterà all'inizio del buffer, mentre *m*
assumerà il valore *1* (c'è un dato valido nel buffer).

Alle righe :math:`t_1` e :math:`t_2` osserviamo l'inserimento di due dati *1*.
A questo punto il buffer è pieno; possiamo solo prelevare.

In :math:`t_3` preleviamo il dato più anziano: il primo *0* inserito, dalla posizione *i=1*. Di conseguenza
diviene *i=2* perché il dato successivo diventa il più anziano, e *m=2* perché
avendo prelevato un dato ora ne abbiamo solo due validi nel buffer.

Se in :math:`t_5` inseriamo un *1*, il calcolo di dove metterlo è: :math:`(2+2) \, mod\,  3 = 1`.


.. index:: BUFF SW architecture

.. _ref_buff_sw_architecture:
   
BUFF buffer: software architecture
----------------------------------------

..

  Lezione del *2019-01-18*

Consideriamo l'architettura software illustrata nel seguente
diagramma:

.. image:: images/buff_arch_sw.*
   :align: center
   
dove *BC* indica un *buffer controller* caratterizzato da questi parametri:

* *x* è il valore in input;
* *y* è il valore in output;
* *i* è la posizione del dato più anziano;
* *m* è il numero di dati validi nell'array.

Le celle :math:`B = B_0, B_1, \cdots B_{N-1}` registrano le informazioni
ricevute in input, che transitano tramite il *buffer controller*. Questo gruppo
di celle nel seguito lo riferiamo con i termini *array* o, genericamente, buffer.

La capacità di memoria richiesta, pari a *N+2*, è dettata dalla presenza 
dei registri *x* e *y* del *buffer controller*. Oltre le *N* celle da 
:math:`B_0` a :math:`B_{N-1}`, dobbiamo aggiungere anche le due predette.

I componenti indicati (*BC*, :math:`B_0`, ...) formano il sistema *buff*
caratterizzato dai parametri *x*, *y*, *i*, *m* e dalla stringa
*s* memorizzata nel buffer.

Formalizziamo lo schema usando il CCS, e ricordando che:

* :math:`x \in V` indica il dato in input. Usiamo *bottom* (:math:`\bot`) per indicare
  la mancanza del dato.

* :math:`y \in V` indica il dato in output. Anche qui *bottom* indica
  la mancanza del dato.

* :math:`1 \leq i \leq N` è la posizione della cella contenente il dato più vecchio.

* :math:`m` è il numero di dati nelle celle.

Definiamo il buffer controller nelle diverse situazioni.

1. **Buffer controller vuoto**: :math:`BC( \bot, \bot, i, 0)`. Quindi senza dati in ingresso 
   o in uscita. La posizione iniziale non è importante, in quanto essendo 
   vuoto, può puntare a qualunque cosa. Non vi sono dati validi nel buffer
   (:math:`m = 0`).
      
   In queste condizioni, l'unica azione che il buffer può eseguire è un input di un dato in *V*:
   
   .. math::
      BC( \bot, \bot, i, 0) = \sum_{d \in V} in(d) \cdot BC(d, \bot, i, 0)
   
La precedente mostra che il dato *d* è posto nella variabile *x* del buffer controller,
non è ancora passato alle celle dell'array. Questo passaggio vale sia in input che in output:
il dato in transito prima viene memorizzato nel *BC* e poi passa nell'array, o viceversa
se in output.

2. **Buffer con dati validi**:  :math:`BC( \bot, \bot, i, m)`. Qui abbiamo
   :math:`m > 0`.

   Le operazioni possibili sono: inserimento di un dato, o richiesta di un dato:
   
   .. math::
      BC(\bot, \bot, i, m) =  \sum_{d \in V} in(d) \cdot BC(d, \bot, i, m) + \sum{a \in V} \rho_i(a) \cdot BC(\bot, a, (i+1) \,mod\, N, m-1)
      
   La prima parte dell'espressione è analoga al punto (1.), ovvero dato *d* entrato nel registro 
   di input del *BC*.
   
   La seconda parte dell'espressione mostra la ricezione del dato dall'array
   del buffer (elemento alla posizione *i*) al registro di output del *BC*.
   
   La posizione dell'elemento più anziano passa da *i* (uscito) a *i+1* modulo *N*.
   
   Il numero di elementi validi si decrementa di una unità, divenendo *m-1*.

3. **Buffer controller con input, senza dati validi**: :math:`BC( d, \bot, i, 0)`.
   Questa è la situazione dopo l'accadimento del punto (1.).
   
   In questa condizione dobbiamo trasmettere il dato all'array:

   .. math::
      BC(d, \bot, i, m) =  \omega_i(d) \cdot BC(\bot, \bot, i, 1)
      
   Quindi si svuota il registro di input del *BC*, e il numero di elementi
   validi si incrementa di 1. Il dato *d* viene messo nell'elemento alla
   posizione :math:`(i+m)\, mod \, N = (i+0) = i`, ovvero la posizione
   *i* non si muove perché prima non vi erano dati validi.

4. **Buffer controller con input, con dati validi e spazio libero**: :math:`BC( d, \bot, i, m)`.
   Se :math:`0 < m < N`, ovvero vi è spazio libero nel buffer, e definendo
   :math:`J = (i+n) \, mod \, N` la posizione dove mettere il dato.
   
   Possiamo fare due cose: mettere il dato in input nel buffer, oppure prepararci
   ad un output caricando il registro di output:
   
   .. math::
      BC(d, \bot, i, m) = \omega_j(d) \cdot BC(\bot, \bot, i, m+1) + \sum{a \in V} \rho_i(a) \cdot BC(d, a, (i+1) \,mod\, N, m-1)

   La prima parte dell'espressione mostra il passaggio del dato in input dal registro di *BC* a
   al buffer. Quindi i dati validi si incrementano di 1 (*m+1*) ed *i* non cambia.
   
   La seconda parte dell'espressione indica il caricamento nel *BC* del dato *a*
   in output. Di conseguenza si incrementa *i* perché passa al dato valido più
   anziano successivo a quello estratto. E si decrementa di 1 il numero di
   dati validi presenti nel buffer.
   
5. **Buffer pieno**: :math:`BC( d, \bot, i, N)`.
   Se :math:`m = N`, il buffer è pieno. L'input non può essere trasmesso
   al buffer.
   
   In questo caso possiamo solo caricare il registro di output:
   
   .. math::
      BC(d, \bot, i, N) = \sum_{a \in V} \rho_i(a) \cdot BC(d, a, (i+1) \, mod \, N, N-1)

   Di conseguenza si incrementa *i* perché passa al dato valido più
   anziano successivo a quello estratto. E si decrementa di 1 il numero di
   dati validi presenti nel buffer.

6. **Buffer controller pronto all'output**: :math:`BC( \bot, a, i, m)`.
   Se il registro di input è libero, possiamo acquisire un input, oppure 
   effettuare l'output del relativo registro:
   
   .. math::
      BC(\bot, a, i, m) = \sum_{d \in V} in(d) \cdot BC(d, a, i, m) + out(a) \cdot BC(\bot, \bot, i, m)

   Il buffer non viene toccato da queste operazioni. Quindi *i* ed *m* non sono 
   modificate.
   
7. **Buffer controller con entrambi i registri caricati**: :math:`BC( d, a, i, m)`.
   Se :math:`0 < m < N`, ovvero vi è spazio libero nel buffer, e definendo
   :math:`J = (i+n) \, mod \, N` la posizione dove mettere il dato.

   Possiamo passare l'input al buffer, oppure effettuare l'output del relativo registro:
   
   .. math::
      BC(d, a, i, m) = \omega_j(d) \cdot BC(\bot, a, i, m+1) + out(a) \cdot BC(d, \bot, i, m)
      
   La prima espressione indica la registrazione dell'input nel buffer, in questo 
   caso si registra alla posizione *j* e il numero di dati validi si incrementa
   di uno.
   
   La seconda espressione indica l'output del dato nel relativo registro del *BC*.
   In questo caso il buffer non è implicato: *i* e *m* non variano.

8. **Buffer controller con entrambi i registri caricati e buffer pieno**: :math:`BC( d, a, i, N)`.
   Se :math:`m = N`, il buffer non può caricare dati. L'unica possibilità 
   consiste nel fare l'output del dato nel relativo registro.
   
   .. math::
      BC(d, a, i, N) = out(a) \cdot BC(d, \bot, i, N)
   
   Anche in questo caso l'operazione non influenza il buffer, quindi *i* ed *m*
   non vengono modificati.
   
Le espressioni indicate nei punti da (1.) a (8.) esprimono il sistema in forma
di elenco di equazioni, la cui soluzione sono i processi che formano il sistema.

La modellazione in parallelo delle celle del buffer può essere la seguente:

.. math::
    Mem(s) = B_0(s_0) \parallel_{\omega_0, \rho_0} B_1(s_1) \parallel_{\omega_1, \rho_1} \cdots  B_{N-1}(s_{N-1}) 
    
dove :math:`s = s_0 \, s_1 \, \cdots s_{N-1}`. Inoltre il buffer complessivo sarà:

.. math::
    Buff(s, x, y, i, m) = (BC(x, y, i, m) \parallel_B Mem(s)) \, \diagup \, B
    
con :math:`B = \{ \omega_0, \rho_0, \omega_1, \rho_1, \cdots \omega_{N-1}, \rho_{N-1} \}`.
Quindi il *buffer controller* lavora in parallelo con l'array delle celle del buffer,
e i due sottosistemi si sincronizzano tramite i segnali che formano l'inseme 
*B* delle azioni di sincronizzazione.

Notiamo che abbiamo espresso il relabeling sulle azioni di sincronizzazione [#]_
per farne l'hiding. Quindi *Buff* mostrerà solo le azioni *in* e *out*,mentre le 
attività interne sarano mascherate a :math:`\tau`.

Attenzione al fatto che in modellazione del sistema, ad esempio con Concurrency
Workbench, dovremo fissare la sua capacità, indicando un valore per *N*.

--------------

.. [#] Il formalismo :math:`\cdots \diagup \, B` esprime il relabeling
   sull'insieme delle azioni in *B*, nascondendole.
    



