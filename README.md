# Tutorial-Subcycling

Papers:

* [GPU-accelerated Subcycling Time Integration with the Einstein Toolkit](https://arxiv.org/abs/2503.09629)

## Enabling Subcycling in CarpetX

* Use repo (branch `dev`)

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

### Step 1: Update `interface.ccl` with RK storage variables

In addition to `foo_rhs` add the following variables: `foo_old`, `foo_k1`, `foo_k2`, `foo_k3`, `foo_k4`.

See [Z4c](https://github.com/lwJi/SpacetimeX/blob/80a0383f4cbeb211254bcf88c42b037baf971cde/Z4c/interface.ccl#L56) for a working example.

Specifically:

* Append `old="foo_old"` and `ks="foo_k1 foo_k2 foo_k3 foo_k4"` after `rhs="foo_rhs"` in the declaration of [`foo`](https://github.com/lwJi/SpacetimeX/blob/80a0383f4cbeb211254bcf88c42b037baf971cde/Z4c/interface.ccl#L27).
* Set the tags `checkpoint="yes" evolve="no" restrict="no"` for `foo_old`, `foo_k1`, `foo_k2`, `foo_k3`, `foo_k4`.

### Step 2: Replace `SYNC` of non-evolved variables with `loop_all_device`

At refinement boundaries, ghost zones may be filled using coarser-level data from a different time step, resulting in low-order accuracy. Using `loop_all_device` instead of `SYNC` for non-evolved variables avoids this issue.

### Step 3: Schedule subroutines for sub-step execution

Schedule all subroutines from `ODESolvers_PostStep` in `ODESolvers_PostSubStep` as well, excluding the `SYNC` calls for evolved variables — `ODESolvers` handles those automatically.

Add the following parameter to your parfile:

```
use_odesolvers_poststep_during_rksubsteps = no
```

### Step 4: Add `ODESolvers_Solve_Subcycling` to schedule.ccl

In the `schedule.ccl` file that contains `ODESolvers_Solve`, add a corresponding `ODESolvers_Solve_Subcycling` schedule block.

See [WeylScalo](https://github.com/lwJi/SpacetimeX/blob/80a0383f4cbeb211254bcf88c42b037baf971cde/WeylScalo/schedule.ccl#L10) for a working example.


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

    * Use repo (branch `dev`)

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
