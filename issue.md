Trying to get atomate2 / OpenMM / OpenFF working with openff-toolkit 16.7 and openff-interchange 0.4.0. However, 
the interchange schema might be different? Looked into the output of the interchange object and found that 
the new versions of openff report a 'charge_from_molecules' attribute which the older versions don't. The main 
place in the code that fails is the conditional statement where the Interchange or OpenMMInterchange object 
are created, either by openff or emmet, respectively. The bug is likely in both places, but the main error is 

```python
  File "/pscratch/sd/p/parmar/atomate2_feature_OPLS-AA/lib/python3.11/site-packages/jobflow/managers/local.py", line 117, in _run_job
    response = job.run(store=store)
               ^^^^^^^^^^^^^^^^^^^^
  File "/pscratch/sd/p/parmar/atomate2_feature_OPLS-AA/lib/python3.11/site-packages/jobflow/core/job.py", line 604, in run
    response = function(*self.function_args, **self.function_kwargs)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/global/u1/p/parmar/HTMD/PR/SMP/atomate2/src/atomate2/openmm/jobs/base.py", line 226, in make
    sim = self._create_simulation(interchange, prev_task)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/global/u1/p/parmar/HTMD/PR/SMP/atomate2/src/atomate2/openmm/jobs/base.py", line 515, in _create_simulation
    return interchange.to_openmm_simulation(
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/pscratch/sd/p/parmar/atomate2_feature_OPLS-AA/lib/python3.11/site-packages/emmet/core/openmm/tasks.py", line 271, in to_openmm_simulation
    system = XmlSerializer.deserialize(self.system)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/pscratch/sd/p/parmar/atomate2_feature_OPLS-AA/lib/python3.11/site-packages/openmm/openmm.py", line 11638, in deserialize
    match = re.search("<([^?]\S*)", inputString)
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/pscratch/sd/p/parmar/atomate2_feature_OPLS-AA/lib/python3.11/re/__init__.py", line 176, in search
    return _compile(pattern, flags).search(string)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TypeError: expected string or bytes-like object, got 'NoneType'
```

for which the actual error that force the OpenMMInterchange object to be used is:

```
1 validation error for Interchange
collections.key_map
  Value error, charge_from_molecules [type=value_error, input_value={'{"this_atom_index":0}':...smetic_attributes":{}}'}, input_type=dict]
    For further information visit https://errors.pydantic.dev/2.10/v/value_error
```
