# Tutorial-Subcycling

Papers:

* [GPU-accelerated Subcycling Time Integration with the Einstein Toolkit](https://arxiv.org/abs/2503.09629)

## Enabling Subcycling in CarpetX

* Use repo (branch `development`)

    ```
    https://github.com/lwJi/CarpetX.git
    ```

* Add the following parameters to the parameter file

    ```
    CarpetX::use_subcycling_wip = yes
    CarpetX::restrict_during_sync = no
    ODESolvers::use_odesolvers_poststep_during_rksubsteps = no
    ```

## Modifying Your Thorn for Subcycling Compatibility

* Step 1: Modify the `interface.ccl` to include `foo_old, foo_k1, foo_k2, foo_k3, foo_k4`,
in addition to `foo_rhs`. For example,
[in Z4c](https://github.com/lwJi/SpacetimeX/blob/baa0b78ae5643a82b7dc31b3e23282538ae157f1/Z4c/interface.ccl#L56).

* Step 2: Replace `SYNC` of non-evolved variables with `loop_all_device`.
Otherwise, at the refinement boundaries, ghost zones may be filled using coarser-level data
from a different time step, resulting in low-order accuracy.

* Step 3: Schedule all the subroutines from `ODESolvers_PostStep` in `ODESolvers_PostSubStep` as well,
excluding the `SYNC` calls for evolved variables.
The `SYNC` of evolved variables is automatically handled by `ODESovlers`.
Add parameter `use_odesolvers_poststep_during_rksubsteps = no` to your parfile.

* Step 4: Add `ODESolvers_Solve_Subcycling` to `schedule.ccl` which contains `ODESolvers_Solve`.
For example,
[in WeyleScalo](https://github.com/lwJi/SpacetimeX/blob/91e36e0aaf11b50a00f341c7d3427381f1788663/WeylScalo/schedule.ccl#L10)

## Running on GPUs

* Use 1 OMP only per GPU device. Multiple OMP per GPU is not compatible with some thorns for now (e.g. PunctureTracker).

* Setting the following to disable tiling
    ```
    CarpetX::max_tile_size_x = 10240000
    CarpetX::max_tile_size_y = 10240000
    CarpetX::max_tile_size_z = 10240000
    ```

## List of Thorns Compatible with Subcycling

* Spacetime

    * Use repo (branch `development`)

        ```
        https://github.com/lwJi/SpacetimeX.git
        ```

    * Thorns

        * [Z4c](https://github.com/lwJi/SpacetimeX/tree/development/Z4c)
        * [Z4cowGPU](https://github.com/lwJi/SpacetimeX/tree/development/Z4cowGPU)

* GRMHD

    * Use repo (branch `developmentSC`)

        ```
        https://github.com/EinsteinToolkit/AsterX.git
        ```

## More GPU-Friendly Thorns

* [WeylScalo](https://github.com/lwJi/SpacetimeX/tree/development/WeylScalo)


## Example Parfiles

* `Parfile/q1-Z4cowGPU-N4.par`: Parameter file for a binary black hole (BBH) simulation with mass ratio q=1, using the Z4cowGPU thorn and WeylScalo thorn. The `max_grid_size` settings are tuned for efficient execution on 4 GPUs; adjust these values as needed for your own GPU configuration.
