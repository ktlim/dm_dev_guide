################
JIRA Agile Usage
################

Refer to `DMTN-020 <https://dmtn-020.lsst.io/>`_ for detailed information on
the JIRA-based planning process used by DM.

Key Concepts
============

- Cycles (aka Releases) occur every 6 months.
- Sprints are monthly, fixed timeframes.
- Epics are higher-level features or capabilities that are to be delivered in a
  cycle.  Epic names should be of the form "Area Short Description" (e.g.
  Database qserv refactor)
- Stories describe concrete, demonstrable features to be delivered in a single
  sprint.
- Story points are to be assigned at 2 story points per full-time day of an
  appropriate developer.
- Stories and story points should not be changed once work on a story has
  started.

.. _jira-teams:

Teams
=====

The JIRA “team” field is used to define financial and managerial responsibility for getting the work done.
We recognize the following teams:

================================== ==============================
Team Name                          Responsible Manager
================================== ==============================
System Management                  Wil O'Mullane
DM Science                         Leanne Guy
Architecture                       K-T Lim
Alert Production                   John Swinbank
Data Release Production            Yusra AlSayyad & John Swinbank
Science User Interface             Xiuqin Wu
Data Access and Database           Fritz Mueller
Data Facility                      Margaret Gelman
International Comms and Base Site  Jeff Kantor
SQuaRE                             Frossie Economou
Telescope and Site                 Andy Clements
External                           None (see below)
================================== ==============================

The “External” team is used to label work done by individuals who are not funded by LSST.

.. _jira-labels:

Labels
======

We support and encourage the use of labels to group related tickets.
Their use is not formally restricted or regulated.
However, there are a few labels which are of general interest:

=================== =============================================================================
Label               Meaning
=================== =============================================================================
``dm-sst``          This work is of interest to the DM System Science Team.
``dm-set``          This work is of interest to the DM Systems Engineering Team.
``DMLT``            This work is of interest to the DM Leadership Team.
``gen3-middleware`` Work on the “generation 3” Butler and associated middleware (e.g. SuperTask).
=================== =============================================================================

In addition, please label any tickets describing work performed on documents with an assigned document handle with that handle (``DMTN-123``, ``LDM-456``, ``LSE-789``, etc).

.. _jira-components:

Components
==========

You can assign one or more components to tickets to describe which part or parts of the system they affect.
Components are selected from a pre-defined list; only JIRA administrators have the ability to add new components.
The following guidelines may be helpful when choosing components:

- If the ticket involves working in one or more software repositories, add the corresponding components (for example, ``afw``, ``daf_butler``, ``ap_association``).
  If no component exists for the repository you are working in, ask your T/CAM for help.
- If the ticket involves work on a document, choose a component that best describes the type of the document you are working on (for example, ``Design Documents``, ``Review Documents``, ``Requirements Documents``).
  Even if the document is hosted in a Git repository, don't add that repository as a component; use a label :ref:`label <jira-labels>` instead.
