# CarpetX Dev Branch

* **Repo:** [https://github.com/lwJi/CarpetX.git](https://github.com/lwJi/CarpetX.git)
* **Branch:** `dev`

## New Features Compare to Main

### Subcycling

* Supports `RK4` and `SSP-RK3` time integration methods

### Timelevels Working

### 2D output in OpenPMD and Silo

```
CarpetX::out_openpmd_2d_every = 16
CarpetX::out_openpmd_2d_vars = "foo"
CarpetX::out_silo_2d_every = 16
CarpetX::out_silo_2d_vars = "foo"

IO::out_xyplane_z = 0
IO::out_xzplane_y = 0
IO::out_yzplane_x = 0
```
