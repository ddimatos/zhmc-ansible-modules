.. _zhmc_cpc_capacity_module:


zhmc_cpc_capacity -- Manages the processor capacity on demand of a Z system.
============================================================================

.. contents::
   :local:
   :depth: 1


Synopsis
--------

Gather facts about the processor capacity of a CPC (Z system).

Update the processor capacity of a CPC (Z system) via adding or removing temporary capacity (On/Off CoD).

For details on processor capacity on demand, see the :term:`Capacity on Demand User's Guide`.



Requirements
------------
The below requirements are needed on the host that executes this module.

- Access to the WS API of the HMC of the targeted Z system (see :term:`HMC API`). The targeted Z system can be in any operational mode (classic, DPM).
- Python package zhmcclient >=0.20.0



Parameters
----------

  hmc_host (True, str, None)
    The hostname or IP address of the HMC.


  hmc_auth (True, dict, None)
    The authentication credentials for the HMC, as a dictionary of ``userid``, ``password``.


    userid (True, str, None)
      The userid (username) for authenticating with the HMC.


    password (True, str, None)
      The password for authenticating with the HMC.



  name (True, str, None)
    The name of the target CPC.


  state (True, str, None)
    The desired state for the operation:

    * ``set``: Ensures that the CPC has the specified processor capacity and returns capacity related properties of the CPC..

    * ``facts``: Does not change anything on the CPC and returns capacity related properties of the CPC.


  test (False, bool, False)
    Indicates whether real or test resources should be activated. Test resources are automatically deactivated after 24h.


  record_id (True, str, None)
    The ID of the capacity record to be used for any updates of the processor capacity.


  software_model (False, str, None)
    The target software model to be active. This value must be one of the software models defined within the specified capacity record. The software model implies the number of general purpose processors that will be active. Target numbers of specialty processors can be specified with the ``specialty_processors`` parameter.


  specialty_processors (False, dict, {})
    The target number of specialty processors to be active. Processor types not specified will not be changed. Target numbers of general purpose processors can be set via the ``software_model`` parameter.

    Each item in the dictionary identifies the target number of processors of one type of specialty processor. The key identifies the type of specialty processor ('aap', 'cbp', 'icf', 'ifl', 'iip', 'sap'), and the value is the target number of processors of that type. Note that the target number is the number of permanently activated processors plus the number of temporarily activated processors.

    If the target number of processors is not installed in the system, or if the specified software model or number of a specialty processors exceeds the limits of the capacity record, the task will fail.


  log_file (False, str, None)
    File path of a log file to which the logic flow of this module as well as interactions with the HMC are logged. If null, logging will be propagated to the Python root logger.


  faked_session (False, raw, None)
    A ``zhmcclient_mock.FakedSession`` object that has a mocked HMC set up. If not null, this session will be used instead of connecting to the HMC specified in ``hmc_host``. This is used for testing purposes only.









Examples
--------

.. code-block:: yaml+jinja

    
    ---
    # Note: The following examples assume that some variables named 'my_*' are set.

    - name: Gather facts about the CPC processor capacity
      zhmc_cpc_capacity:
        hmc_host: "{{ my_hmc_host }}"
        hmc_auth: "{{ my_hmc_auth }}"
        name: "{{ my_cpc_name }}"
        state: facts
      register: cap1

    - name: Ensure the CPC has a certain general purpose processor capacity active
      zhmc_cpc_capacity:
        hmc_host: "{{ my_hmc_host }}"
        hmc_auth: "{{ my_hmc_auth }}"
        name: "{{ my_cpc_name }}"
        state: set
        record_id: 1234
        software_model: "710"
      register: cap1

    - name: Ensure the CPC has a certain IFL processor capacity active
      zhmc_cpc_capacity:
        hmc_host: "{{ my_hmc_host }}"
        hmc_auth: "{{ my_hmc_auth }}"
        name: "{{ my_cpc_name }}"
        state: set
        record_id: 1234
        specialty_processors:
          ifl: 20
      register: cap1



Return Values
-------------

cpc (success, dict, )
  A dictionary with capacity related properties of the CPC that indicate the currently active processor capacity.


  name (, str, )
    CPC name


  software_model (, str, )
    The current software model that is active. The software model implies the number of general purpose processors that are active.


  general_processors (, int, )
    The current number of general purpose processors that are active.


  specialty_processors (, dict, )
    The current number of specialty processors that are active.

    Each item in the dictionary identifies the number of processors of one type of specialty processor. The key identifies the type of specialty processor ('aap', 'cbp', 'icf', 'ifl', 'iip', 'sap'), and the value is the current number of processors of that type that are active. Note that this number is the number of permanently activated processors plus the number of temporarily activated processors.






Status
------




- This module is guaranteed to have backward compatible interface changes going forward. *[stableinterface]*


- This module is maintained by community.



Authors
~~~~~~~

- Andreas Maier (@andy-maier)

