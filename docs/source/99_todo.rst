
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

* concetti di base: context-sensitive grammars
* concetti di base: context-free grammars
* che significa: (reg.exp. sono) finite, complete, decidibili
  (https://en.wikipedia.org/wiki/Decidability_(logic)).
* structural operational semantics
* denotational semantics
* formal logic
* propositional logic
* first order logic
* equivalence relation
* BPMN, un capitolo a parte (2018-12-14, prof.lorenzo rossi, non ho partecipato, non ho appunti)

Done
---------

* *2019-01-06* concetti di base: regular grammars
* *2019-01-05* concetti di automa: finito, deterministico e non.
* *2019-02-09* + appendice per l'installazione di CWB in CentOS 6

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




