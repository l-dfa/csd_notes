
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Buffer PIPE
   :description language=en: Notes on Complex Systems Design - Buffer PIPE
   :keywords: Complex Systems Design, buffer, PIPE
   :author: Luciano De Falco Alfano

.. index:: PIPE buffer

.. _ref_pipe_buffer:
   
PIPE Buffer
=============

.. contents:: 
   :local:

..

  Lezione del *2019-01-10*.

Mantenendo le specifiche illustrate nel :ref:`fifo buffer <ref_fifo_buffer>`,
consideriamo una implementazione a PIPE, con la seguente architettura.

.. index:: PIPE SW architecture

.. _ref_pipe_sw_architecture:
   
PIPE buffer: software architecture
----------------------------------------

Un buffer PIPE è formato da una serie di celle, ognuna con la capacità
di memorizzare una informazione, concatenate tra loro.

L'informazione fluisce, usando i canali :math:`\sigma_i` da una cella all'altra, 
come illustrato qui di seguito:

.. image:: images/pipe.*
   :align: center

Quando una cella è vuota, utilizzamo il simbolo :math:`\bot` (*bottom*) per rimarcare
che non contiene alcun dato.

Quindi la stringa *s* é: :math:`s \in (V \cup \{ \bot \})^+`. E la sua
lunghezza è :math:`\lvert s \rvert = N+2`.

Specifichiamo una singola generica cellula *c*, considerandola come un buffer di capacità 1.

Se la cella è vuota abbiamo solo la possibilità di inserire un dato :math:`e \in V = \{ 0, 1 \}`:

.. math::
   c(\bot) = \sum_{e \in V} in(e) \cdot c(e)
   
Se la cella contiene un dato, è piena. Quindi possiamo solo ricevere:

.. math::
   c(e) = out(e) \cdot c(\bot)
   
Ora, nel considerare il buffer con :math:`N+2` celle, abbiamo i seguenti casi.

Con l'indice della cella che varia tra 0 e N+1: :math:`0  \leqslant i  \leqslant N+1`,
l'estremo sinistro sarà per :math:`i = 0`, come segue:

.. image:: images/pipe_c0.*
   :align: center

Ora definiamo una funzione di relabeling come segue:

.. math::
   \phi_0(a) = \begin{cases}
                 \sigma_0 \quad & if \, a = in \\ 
                 out      \quad & if \, a = out
               \end{cases}
   
ovvero, se l'azione è un input, diviene :math:`\sigma_0`, se invece è un 
output, l'azione rimane inalterata.

In questo modo possiamo ridefinire :math:`c_0 = c[\phi_0]`, utilizzando la definizione
di *c(e)*.

Consideriamo ora l'estremo destro: :math:`i = N+1`, avremo:

.. image:: images/pipe_cn+1.*
   :align: center

Usando la funzione di relabeling:   
   
.. math::
   \phi_{N+1}(a) = \begin{cases}
                     in        \quad & if \, a = in \\ 
                     \sigma_N  \quad & if \, a = out
                   \end{cases}
   
potremo definire :math:`c_{N+1} = c[\phi_{N+1}]`.

Per l'indice generico :math:`0 < i < N+1` avremo:

.. image:: images/pipe_ci.*
   :align: center

Usando la precednte funzione di relabeling:   
   
.. math::
   \phi_i(a) = \begin{cases}
                     \sigma_i      \quad & if \, a = in \\ 
                     \sigma_{i-1}  \quad & if \, a = out
                   \end{cases}
   
potremo definire :math:`c_i = c[\phi_i]`.

Ora componiamo in parallelo le diverse celle:

.. math::
   pipe(s) = [(c_0[\phi_0] \parallel_{\sigma_0} \cdots \parallel_{\sigma_{i-1}} c_i[\phi_i] \parallel_{\sigma_i} \cdots \parallel_{\sigma_N} c_{N+1}[\phi_{N+1}])] \setminus \{ \sigma_0 \cdots \sigma_{N} \}
 
facendo attenzione che vi sia il vincolo della sincronizzazione rispetto 
i canali :math:`\sigma_i, 0  \leqslant i  \leqslant N`. Questa condizione
permette il flusso ordinato dei dati dalla prima all'ultima cella.
   
..

  Lezione del *2019-01-17*
  
Osserviamo che con questa architettura un dato in input deve attraversare tutte le 
celle prima di essere ricevuto in output. Con l'architettura parallela esposta,
questo avviene subito. Ricordando che tutte le celle sono vuote, quando la prima
cella (:math:`c_{N+1}`) riceve un dato, visto che a sx :math:`c_N` è vuota, 
si sincronizza con lei (azione :math:`\sigma_N`), passando il dato e svuotandosi.
Ciò capita a catena sino ad arrivare a :math:`c_0`, dove il dato sarà a 
disposizione del destinatario con l'azione *out(e)*.

Se un secondo dato :math:`e_2` viene immesso nel buffer prima che il precedente 
sia stato ricevuto dal destinatario, allora si fermerà nell'ultima cella vuota disponibile:
:math:`c_1`; e così via; eventualmente sino al riempimento del buffer.

Quindi per una generica stringa *s* il buffer avrà lo stato:

.. math::
   pipe(s) = (c_0(s_0) \parallel_{\sigma_0} c_1(s_1) \parallel_{\sigma_1} \cdots \parallel_{\sigma_N} c_{N+1}(s_{N+1})
   
ottenendo :math:`s = s_0 \, s_1 \cdots s_{N+1}, \; s_i \in \{ \bot, 0, 1 \}`.
 
Osserviamo inoltre, che l'architettura indicata può nascondere i dettagli di comportamento della
PIPE rispetto l'osservatore esterno: le azioni :math:`\sigma_i, 0  \leqslant i  \leqslant N`
non saranno visibili all'esterno.

Per formalizzare questa osservazione, definiamo la funzione di relabeling:

.. math::
   \phi(a) = \begin{cases}
                 in     \quad & if \, a = in \\ 
                 out    \quad & if \, a = in \\ 
                 \tau   \quad & if \, a = \sigma_0, \sigma_1, \cdots \sigma_N
             \end{cases}

con :math:`\tau` azione nascosta, invisibile all'osseratore esterno.

Applicando questa funzione di relabeling alla pipe, le azioni :math:`\sigma_i`
saranno trasformate in :math:`\tau`. Solo *input* e *output* non sono
rietichettate. Quindi un osservatore esterno che guarda le azioni, vedrà
solo le sequenze di input e output intervallate con una serie di :math:`\tau`.

Nello stato iniziale avremo *N+2* volte :math:`\bot` (prima inizializzazione del sistema)

.. math::
   pipe(\bot \bot \cdots \bot)[\phi]




   
   