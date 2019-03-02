
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Edinburgh Concurrency Workbench
   :description language=en: Notes on Complex Systems Design - Edinburgh Concurrency Workbench
   :keywords: Complex Systems Design, Edinburgh Concurrency Workbench, CWB
   :author: Luciano De Falco Alfano

.. index:: concurrency workbench of the new century

.. _ref_concurrency_workbench_of_the_new_century:
   
Concurrency WorkBench of the New Century
==========================================

.. contents:: 
   :local:

Il `Concurrency Workbench of the New Century <https://www3.cs.stonybrook.edu/~cwb/>`_
è un programma derivato dal :ref:`Edinburgh Concurrency Workbench <ref_edinburgh_concurrency_workbench>`.

Inizialmente sviluppato nella `North Carolina State University <https://www.csc.ncsu.edu/>`_ [#]_,
successivamente il suo gruppo di sviluppo si è trasferito in blocco alla
`Stony Brook State University of New York <http://www.cs.sunysb.edu/>`_.

*CWB-NC* ha un numero di comandi inferiori a quelli esposti dal *CWB*.

D'altro canto:

* gestisce un numero superiore di linguaggi di specifica;
* la sintassi è leggermente semplificata [#]_;
* esiste una versione precompilata per MS Windows di cui parliamo in questo capitolo.


.. index:: cwb-nc installation

.. _ref_cwb-nc_installation:
   
CWB-NC installation
-----------------------

Il programma per installare in MS Windows è in un archivio (``.zip``) che può essere scaricato da questo link:
https://sourceforge.net/projects/cwb-nc/.

Come usuale quando si lavora in MS Windows, è necessario:

* estrarre i file dall'archivio,
* eseguire, con diritti di amministrazione, il file ``setup.exe`` estratto.

Scegliere la directory su cui installare, ad esempio: ``c:\bin\cwb-nc``.

Mettere la sottodirectory ``bin`` nel *path* della macchina. Nel caso in esempio
aggiungeremo al *path* la directory ``c:\bin\cwb-nc\bin``.

.. index:: cwb-nc note d'uso

.. _ref_cwb-nc_notes:
   
CWB-NC notes
-----------------------

Eseguita l'installazione:

* aprire una shell comando,
* portarsi nella directory in cui sono i file con la definizione dei processi,
  ad esempio ``C:\bin\cwb-nc\examples\ccs\abp`` dove è un esempio di *alternate bit
  protocol* in linguaggio ccs (file ``abp.ccs``),
* eseguire ``cwb-nc ccs`` [#]_,

quindi ci si troverà nell'ambiente di comando del *CWB-NC*, come nel seguente esempio,
in cui si esegue l'help dei suoi comandi:

.. code:: console

   Microsoft Windows [Versione 10.0.17134.590]
   (c) 2018 Microsoft Corporation. Tutti i diritti sono riservati.
   
   C:\Users\...>cd "C:\Bin\cwb-nc\bin
   
   C:\Bin\cwb-nc\bin>cwb-nc ccs
   Currently supported languages are : ccs, pccs, sccs, tccs, csp, lotos

   The Concurrency Workbench of the New Century
   (Version 1.2 --- June, 2000)
   
   cwb-nc> help
   
   Available CWB-NC commands are:
     caching {on | off}
     cat identifier
     cd directory
     chk agent formula
     compile agent [ agent ... ]
     eq [-S {obseq | bisim | may | must}] agent1 agent2
     es filename1 [filename2]
     fd agent
     help [command-name]
     le [-S {may | must}] agent1 agent2
     load filename
     ls
     min [-S {obseq | bisim}] agent identifier
     quit
     save filename [ identifier ... ]
     search agent formula
     sim agent
     size agent
     sort agent
     trans [-S {obseq | bisim}] agent
   
   Execution time (user,system,gc,real):(0.008,0.000,0.000,0.008)
   cwb-nc>

Il comando di base da ricordare é **load**, che permette il caricamento del file con le definizioni
dei processi. Ad esempio: load 


.. code:: console

   C:\Bin\cwb-nc\examples\ccs\abp>cwb-nc ccs
   Currently supported languages are : ccs, pccs, sccs, tccs, csp, lotos
   Current directory will be C:\bin\cwb-nc
   
   The Concurrency Workbench of the New Century
   (Version 1.2 --- June, 2000)
   
   cwb-nc> load abp.ccs
   Execution time (user,system,gc,real):(0.005,0.000,0.000,0.005)
   cwb-nc> ls
   
   ===Agent===
   
   Spec
   S0
   S0'
   S1
   S1'
   R0
   R1
   Msafe
   Mlossy
   ABP-safe
   ABP-lossy
   Spec2
   Spec2'
   Spec2''
   Two-link-netw
   Spec3
   Spec3'
   Spec3''
   Spec3'''
   Three-link-netw

Vi sono delle differenze tra la sintassi dei comandi di CWB-NC rispetto l'Edinburgh CWB,
quindi: leggere il relativo manuale disponibile in ``c:\bin\cwb-nc\doc``.

Inoltre raccomandiamo anche il seguente: *Reactive Systems: How to use the Concurrency Workbench*
([HENN2008]_) che è una buona introduzione all'uso di CWB-NC.

Tra le differenze osservate, spiccano le seguenti:

* in Edinburgh CWB è possibile definire un processo interattivamente, da linea di comando;
  in CWB-NC questo non è possibile: si deve scrivere la definizione in un file (con filename terminante in ``.ccs``)
  e poi caricarla in CWB-NC con il comando ``load``;
* la definizione di un agente non usa la parola chiave ``agent``, ma la parola chiave ``proc``.


---------------------

.. [#] Infatti originariamente la sigla *nc* indicava proprio il North Carolina.

.. [#] Ad esempio, non è necessario il punto e virgola per terminare il comando.

.. [#] Chi è interessato alle versioni Linux (x86) e Sparc-Solaris, può visitare 
   questo sito: http://sens.cse.msu.edu/local_only/Software/cwb-nc.html.

.. [#] Si noti la presenza dell'opzione che identifica il linguaggio di specifica
   da utilizzare, nell'esempio il *CCS*. Altri linguaggi disponibili sono: CSP,
   LOTOS, PCCS, SCCS, TCCS.