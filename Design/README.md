# Design

## 0. Redefine `Sync` for subcycling

**Current**

* inter-process + prolongation + restriction for all groups

**Desire**

* inter-process only for evolution groups (prolongation and restriction handled by driver)
* inter-process + prolongation + restriction for the rest

## 1. Prolongate non-evolve groups

### Algebraic

* Use `loop_device_all`

### Derivative

**Current**

* Direct copy

**Desire**

* [ ] Save `old` slice `[ntls=2]` &rarr; linear intep in time

## 2. Support `SSP-RK3` and `Linear`

## 3. Efficient Storage for `ks`

## 4. Slow `SetKs`

## 5. Only checkpoint `current` and `old` state, and always restart from coarsest `old`

* [ ] we don't need to checkpoint `ks`
