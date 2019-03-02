
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Buffer FIFO
   :description language=en: Notes on Complex Systems Design - Buffer FIFO
   :keywords: Complex Systems Design, buffer, FIFO
   :author: Luciano De Falco Alfano

.. index:: FIFO buffer

.. _ref_fifo_buffer:
   
FIFO Buffer
=============

.. contents:: 
   :local:

..

  Lezione del *20189-01-10*.

Da qui vedremo tre diversi modi per implementare un buffer. Per riferimento si
può consultare [CODV2001]_.

Fissiamo le seguenti specifiche:

* capacità del buffer: :math:`(N > 0) + 2` (il +2 ci serve per semplificare il calcolo degli indici);
* il set dei dati memorizzabili è :math:`V = \{ 0, 1 \}` [#]_;
* le *stringhe* sono sequenze di bit: :math:`s, t, u, v \cdots \in V^*`;
* la lunghezza di una *stringa* *s* si indica con :math:`\lvert s \rvert`, dove per lunghezza si 
  intende il numero di elementi che formano la stringa [#]_;

.. index:: FIFO SW architecture

.. _ref_fifo_sw_architecture:
   
FIFO buffer: software architecture
----------------------------------------

Un buffer FIFO (acronimo di First In First Out) è alimentato da un produttore
esterno (*source*), ed alimenta un consumatore esterno (*destination*).

I dati oggetto del trattamento sono gestiti secondo una coda organizzata
in modo che il dato entrato temporalmente per primo sia anche il primo ad uscire.

Avremo la seguente architettura:

.. image:: images/fifo.*
   :align: center

dove *fifo(s)* ci indica che abbiamo una capacità di memorizzazione 
tale da poter registrare la stringa *s*.

Inizialmente avremo *fifo(s)* vuota; ovvero avremo memorizzato la stringa vuota
(:math:`\epsilon`) nel corpo del buffer.

La definizione di fifo(s) può essere la seguente.

Inizialmente, per buffer vuoto, è possibile solo l'input di un dato: zero o uno;
abbiamo una non *deterministic composition*:

.. math::
   s = \epsilon, \; fifo(s) = in(0) \cdot fifo(0) + in(1) \cdot fifo(1) = \sum_{e \in V}in(e) \cdot fifo(e)

Quando il buffer è impegnato parzialmente, potremo eseguire sia input
che output. Diciamo che il nostro buffer è composto :math:`s = e \, s'`, cioè
la stringa *s* ha il dato *e* in testa, e la coda è :math:`s'`, come 
indicato nella seguente rappresentazione grafica:

.. image:: images/fifo_2.*
   :align: center


Avremo:

.. math::
   0 < \lvert s \rvert < N+2, s = e \, s', \; fifo(s) = \sum_{e \in V}in(e) \cdot fifo(s \, e) + out(e) \cdot fifo(s')

ovvero possiamo inserire in coda un nuovo dato *e*, per poi passare la fifo
nello stato :math:`s \, e`;
oppure possiamo ricevere il dato *e* dalla testa di *s*, passando la fifo allo stato :math:`s'`.

Rimane da considerare il caso di buffer pieno: :math:`\lvert s \rvert = N+2`.
Qui l'unica azione possibile è la ricezione del dato; l'input è vietato
perché il buffer pieno. Se, come nel caso precedente, il buffer è composto 
da :math:`s = e \, s'`, abbiamo:

.. math::
   \lvert s \rvert = N+2, s = e \, s', \; fifo(s) = out(e) \cdot fifo(s')
   
Se le azioni *deposit* e *withdraw* dell\ ``'``\ :ref:`implementazione sequenziale
del producer-consumer <ref_pc_sequencial_specification>` avessero un valore
associato, questa implementazione sarebbe *bisimilar equivalent* a quella
del *producer-consumer*.

Un esempio di interazione può essere il seguente:

===========   ===================   ========   =========   =========   =========   =========   ==================
 action                              in(0)      in(1)       in(1)       out(0)      out(1)      out(1)
===========   ===================   ========   =========   =========   =========   =========   ==================
 state         :math:`\epsilon`      0          01          011         11          1           :math:`\epsilon`
===========   ===================   ========   =========   =========   =========   =========   ==================

La prima colonna della tabella identifica cosa è nelle celle seguenti della riga.

La seconda colonna riporta lo stato iniziale del buffer: vuoto (stringa nulla: :math:`\epsilon`).

Dalla terza colonna in poi sono indicate nella prima riga le azioni, nella
seconda riga il relativo stato del buffer in conseguenza dell'azione.

------------------------------

.. [#] Questa è una semplificazione. Possiamo pensare di memorizzare qualunque tipo
   di informazione di dimensione finita.
   
.. [#] Per induzione:

   .. math::
      \lvert s \rvert = \begin{cases}
              0                  \quad & \, se \, s = \epsilon, \; \text{stringa nulla} \\ 
              1+\lvert s' \rvert \quad & \, se \, s = a\,s', \; a \in V
            \end{cases}

