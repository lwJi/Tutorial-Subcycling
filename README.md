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
