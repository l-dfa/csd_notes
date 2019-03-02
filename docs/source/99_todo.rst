
.. meta::
   :language: it
   :description language=it: Appunti di Complex Systems Design - Da fare e note
   :description language=en: Notes on Complex Systems Design - To do & remarks
   :keywords: Complex Systems Design, to do, remarks
   :author: Luciano De Falco Alfano

.. index:: to do & remarks

.. _ref_to_do_&_remarks:

To do & remarks
=================

.. contents:: 
   :local:
   
To do
-------

* che significa: (reg.exp. sono) finite, complete, decidibili
  (https://en.wikipedia.org/wiki/Decidability_(logic)).
* formal logic
* propositional logic
* first order logic
* equivalence relation
* BPMN, un capitolo a parte (2018-12-14, prof.lorenzo rossi, non ho partecipato, non ho appunti)

Done
---------

* *2019-02-21* chg producer consumer: + bisimilar equivalence di PC e Buffer; correcting
  typoes here and there
* *2019-02-14* chg concetti di base: + context-sensitive grammars, + context-free grammars
* *2019-02-14* chg concetti di base: + semantics
* *2019-02-14* chg concetti di base: + structural operational semantics, denotational semantics
* *2019-02-13* + appendice per l'installazione di CWB_NC in MS Windows
* *2019-02-09* + appendice per l'installazione di CWB in CentOS 6
* *2019-01-06* chg concetti di base: regular grammars
* *2019-01-05* chg concetti di base: automa finito, deterministico e non.

Remarks about latex math notations
-------------------------------------

conditional equations with one-sided curly brackets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

si scrive::

  .. math::
  
     L^i = \begin{cases}
             \{\lambda\}          & \text{se } i = 0 \\ 
             L \cdot L^{i-1}  & \text{se } i > 0
           \end{cases}

risultato:

.. math::

   L^i = \begin{cases}
           \{\lambda\}          & \text{se } i = 0 \\ 
           L \cdot L^{i-1}  & \text{se } i > 0
         \end{cases}

         
two symbols, one over the other
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

si scrive::

         :math:`\overset{def}{=}`
         
si ottiene: :math:`\overset{def}{=}`


module
^^^^^^^

si scrive::

         :math:`\lvert x \rvert`
         
si ottiene: :math:`\lvert x \rvert`




