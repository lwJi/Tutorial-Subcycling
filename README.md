# Tutorial-Subcycling

Papers:

* [GPU-accelerated Subcycling Time Integration with the Einstein Toolkit](https://arxiv.org/abs/2503.09629)

Design:

* [Design Doc](Design)

## Enabling Subcycling in CarpetX

* Use repo (branch `dev`)

    ```
    https://github.com/lwJi/CarpetX.git
    ```

* Add the following parameters to the parameter file

    ```
    CarpetX::use_subcycling_wip = yes
    CarpetX::restrict_during_sync = no
    ```


## Modifying Your Thorn for Subcycling Compatibility

### Step 1: Revisit `SYNC` for Non-Evolution Variables

When dealing with non-evolution variables, your synchronization strategy must change depending on what the variables rely on:

* **Variables depending only on evolution variables (no derivatives)**: Do not use `SYNC`. Using `SYNC` can mistakenly pull data from the coarse level that is not aligned with the current fine level at refinement boundary. Instead, compute these variables locally by using `loop_all_device`.

* **Variables depending on derivatives of evolution variables**: Using `SYNC` here can mistakenly pull data from the coarse level that is not aligned with the current fine level. To resolve this, you must average the old and new coarse levels before prolongating to the fine level. Enable this behavior by adding
    - `TIMELEVELS=2` in your `interface.ccl` for those variables,
    - `STORAGE: foo[timelevels]` in your `schedule.ccl`.
    - `foo::timelevels = 2` in your parfile.

### Step 2: Add `ODESolvers_Solve_Subcycling` to schedule.ccl

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

### Spacetime

Use repo (branch `dev`)

```
https://github.com/lwJi/SpacetimeX.git
```

Thorns

* [Z4c](https://github.com/lwJi/SpacetimeX/tree/dev/Z4c)
* [Z4cowGPU](https://github.com/lwJi/SpacetimeX/tree/dev/Z4cowGPU)

### GRMHD

Use repo (branch `devSC`)

```
https://github.com/EinsteinToolkit/AsterX.git
```

## More GPU-Friendly Thorns

* [WeylScalo](https://github.com/lwJi/SpacetimeX/tree/dev/WeylScalo)


## Example Parfiles

* `Parfile/q1-Z4cowGPU-N4.par`: Parameter file for a binary black hole (BBH) simulation with mass ratio q=1, using the Z4cowGPU thorn and WeylScalo thorn. The `max_grid_size` settings are tuned for efficient execution on 4 GPUs; adjust these values as needed for your own GPU configuration.
