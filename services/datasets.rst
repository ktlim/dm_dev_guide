######################################
Common Dataset Organization and Policy
######################################

This document covers the specific :ref:`format <format>` and :ref:`policies <policy>` governing the shared datasets in ``/datasets``, project GPFS space available on the login node ``lsst-dev01`` and on all of the compute nodes of the :doc:`Verification Cluster </services/verification>`.

.. _format:

Format
======

All data added to ``/datasets`` must adhere to the following format (caps are tokens):
``/datasets/<camera>/[REPO|RERUN|PREPROCESSED|SIM|RAW|CALIB] | /datasets/REFCATS`` where

- REPO = repo
  (:lmod:`butler` root)
- RERUN = REPO/rerun/PUBLIC | REPO/rerun/PRIVATE
  (processed results)
- PUBLIC = <ticket>/PRIVATE | <tag>/PRIVATE (ex. 'HSC-RC'; RFC needed for new tags)
- PRIVATE = private/<user> | ""
  (:ref:`see details below <CaveatForPrivate>`)
- PREPROCESSED = preprocessed/<label>/ | preprocessed/<label>/<date>/
  (ex. 'dr9')
- SIM = <ticket>_<date>/ | <user>/<ticket>/
- RAW = raw/<survey-name>/
  (where actual files live)
- CALIB = calib/default/ | calib/label/
  (ex. master20161025)
- REFCATS = refcats/<type>/<version>/<label>
  (ex. astrometry_net_data/sdss-dr8, htm/v1/gaia_DR1_v1)

Some data resides within ``/datasets`` which does not adhere to this format; they are provided for general consumption though not as verification data.
The following are currently exempted:

- ``/datasets/all-sky``
- ``/datasets/public_html``

.. _reference-catalogs:

Reference Catalogs
------------------

For the Gen2 Middleware, reference catalogs are contained in the repository itself, in a ``ref_cats/`` subdirectory.
For ``/datasets`` repositories, we handle this by symlinking from ``repo/ref_cats/NAME`` to the corresponding refcat directory in ``/datasets/refcats``.
The version subdirectory (e.g. ``v0/``, ``v1``) should match the ``REFCAT_FORMAT_VERSION`` that is set by the refcat ingestion task.
When adding a refcat, don't forget to add a note to the relevant ``README.txt``, as described in the `responsibilities`_ section.

Immutability/Sharing
--------------------

NCSA is working on a simple procedure for making the data both shared and safe.
The process to **Lock** or **Unlock** a set of data is to open a JIRA ticket (https://jira.lsstcorp.org) 
under the "IT Helpdesk Support" project.

Illustration:

Steps to add to datasets
^^^^^^^^^^^^^^^^^^^^^^^^

#. (you) RFC if necessary per :ref:`policy <policy>`
#. (you) Ask for write access to a new rerun|new camera|ref cat| directory
#. Directory created, write permissions given
#. (you) Populate and organize data (as per policy), ask to have it locked down
#. Sharing and immutability applied

Steps to modify/remove from datasets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. (you) RFC if necessary per :ref:`policy <policy>`
#. (you) Ask for write access to existing rerun|new camera|ref cat| directory
#. Write permissions given, immutability removed
#. (you) Reorganize, ask to have it locked down
#. Sharing and immutability reapplied (to parent directory, as applicable)

.. _policy:

Policy
======

Formatting exists to make data sets easier to consume for the DM project at large.
Policy exists to enforce the format and serves to inform whenever policy must change.
The following policies serve to both enforce and inform:

- **/datasets Format Changes**

  Future needs will certainly require format changes.
  We must go through the RFC process to change the format.

- **/datasets additions/changes/deletions**

  - Additions / modifications / deletions of any non-private data requires an RFC (strictly for input for naming convention, organization, etc)
  - Additions / modifications /deletions of private data can be performed without a RFC

The RFC allows a gate to confirm that things are compliant and necessary. The RFC should include:

- Description and reason for addition/change/deletion
- Target top-level-directory for location of addition/change/deletion
- Organization of data
- Other necessary domain knowledge as identified by project members relating to the contents of the data

**All local non-private data governed by this RFC must reside within /datasets proper; symbolic links to local non-private data residing on alternate file systems are prohibited.**
This does not prohibit the use of remote URI's, when supported through the butler, that point to external public repos although this does require the RFC process for addition/deletion of the URI-repo.
This is due to operational concerns including immutability, sharing permissions, developer change of positions / jobs, etc.

.. _responsibilities:

Responsibilities on ingest or maintenance
-----------------------------------------

- Ticket creator is responsible for butler-ization of dataset (or delegation of responsibility).
- Responsibility for maintaining usable datasets is a DM-wide effort.

Regardless of the reason for the RFC (implementation or maintenance), as part of implementing the RFC, any relevant information from the RFC should be transferred to a ``README.txt`` file at the root level of the dataset. There is no limit to how much information can be put in ``README.txt``, however at the minimum, it should contain:

- A description of the instrument and observatory that produced the data
- The intended purpose of the dataset
- At least a high level summary of the selection criteria for the dataset
- The primary point of contact for questions about the dataset. Name is sufficient, but email would be appreciated.
- If preprocessed, a description of the preprocessed data products available
- If a subset is preprocessed, a description of how the subset was created (and why)

For butler repository datasets, the root level is the directory just above the butler repository: e.g. ``/datasets/hsc/README.txt``. For reference catalogs, there should be one ``README.txt`` for all reference catalogs of a particular type: e.g. ``/datasets/refcats/htm/README.txt``. Each reference catalog should have an entry in that file with the above information.


.. _CaveatForPrivate:

Caveats / Implementation Details for PRIVATE
--------------------------------------------

- ``private/`` is created with the sticky bit to allow user managed contents
- ``private/`` only contains symbolic links pointing out of datasets or contains sub directories containing symbolic links (for organization)
- No data resides in ``private/`` or subdirectories
- No access or recovery is offered from ``private/`` other than that provided by the target file system
- It is a user responsibility to make the private rerun repo shared, or not, and allow, or disallow, sub rerun directories from other users
- Data retention in ``private/`` is not guaranteed (points to scratch, points to home and user leaves, user erroneously deletes repo, etc)
- Data in ``private/`` is not immutable
- ``private/`` entries do not require Jira tickets for creation/deletion/modification

In other words, if:

- you need to do some private work that you don't want to disappear, symlink into ``~/``.
- you need to so some private work that does not fit into your home quota (to be 1TB), symlink to ``/scratch/`` (180 days purge).
- you need something to be maintained/shared/immutable/managed, create a ticket and move to PUBLIC.
- you place actual data in ``private/``, you will be asked to move/delete/clean it in some way.

Examples on Running Tasks with the Common Dataset
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For each camera, there is one single URI as the main :lmod:`butler` repo root (``/datasets/<camera>/repo``).

Currently our task framework tools support putting outputs in a new repo by specifying a path (``--output``) or specifying a symbolic name for outputs to go to a common place (``--rerun``).

To use ``--rerun`` for private runs, you can create a link without a ticket:
``/datasets/hsc/repo/rerun/private/jalt/first_attempt -> /scratch/jalt/rerun_output_location``
and then you can run tasks:

.. prompt:: bash

   processXXX.py /datasets/hsc/repo/ --rerun private/jalt/first_attempt ...
