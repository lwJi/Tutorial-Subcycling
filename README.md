# Tutorial-Subcycling


## Enabling Subcycling in CarpetX

* Use repo (branch `development`)

    ```
    https://github.com/lwJi/CarpetX.git
    ```

* Add the following parameters to the parameter file

    ```
    CarpetX::use_subcycling_wip = yes
    CarpetX::restrict_during_sync = no
    ```

## Modifying Your Thorn for Subcycling Compatibility

* Step 1: Modify the `interface.ccl` to include `foo_old, foo_k1, foo_k2, foo_k3, foo_k4`,
in addition to `foo_rhs`. For example,
[in Z4c](https://github.com/lwJi/SpacetimeX/blob/baa0b78ae5643a82b7dc31b3e23282538ae157f1/Z4c/interface.ccl#L56).

* Step 2: Replace `SYNC` of non-evolved variables with `loop_all_device`.
Otherwise, at the refinement boundaries, ghost zones may be filled using coarser-level data
from a different time step, resulting in low-order accuracy.

* Step 3 (optional): Schedule all the subroutines from `ODESolvers_PostStep` in `ODESolvers_PostSubStep` as well,
excluding the `SYNC` calls for evolved variables.
The `SYNC` of evolved variables is automatically handled by `ODESovlers`.
Add parameter `use_odesolvers_poststep_during_rksubsteps = no` to your parfile.

## List of Thorns Compatible with Subcycling

* Spacetime

    * Use repo (branch `development`)

        ```
        https://github.com/lwJi/SpacetimeX.git
        ```

    * Thorns

        * [Z4c](https://github.com/lwJi/SpacetimeX/tree/development/Z4c)
        * [Z4cow](https://github.com/lwJi/SpacetimeX/tree/development/Z4cow)
        * [Z4cowGPU](https://github.com/lwJi/SpacetimeX/tree/development/Z4cowGPU) (optimized for GPU)

* GRMHD

    * Use repo (branch `development`)

        ```
        https://github.com/lwJi/AsterX.git
        ```

## List of GPU-Friendly Thorns

* Thorns

    * [WeylScalo](https://github.com/lwJi/SpacetimeX/tree/development/WeylScalo)
