# Two-stage cooperative lane changes for connected vehicles

[Download ZIP](https://github.com/libai1943/Cooperative_Lane_Changes_of_CAVs/archive/refs/heads/main.zip) · [Getting started](#run-one-traffic-scenario) · [Guide](#from-lane-requests-to-trajectories) · [Citation](#citation)

Ten vehicles share three lanes. Each receives a starting lane and a destination lane, and a maneuver must give every vehicle room to cross. This MATLAB implementation of the 2018 IEEE T-IV method separates that task into formation adjustment and lane changing. It computes the lane-change stage first, so the formation planner knows the spacing it must deliver at the handover. [Journal article](https://doi.org/10.1109/TIV.2018.2843159)

## Run one traffic scenario

Use MATLAB on Windows in a writable copy of the repository. Keep the protected `.p` files and the AMPL/IPOPT runtime together. The solver needs a license that supports the problem size and the linear solver configured in `ipopt.opt`. The renderer uses MATLAB video support.

```matlab
rng(1)                    % repeat the randomly drawn initial traffic task
RunMe
[tf1, tf2, gross_time]    % planned stage durations and their sum
```

The driver clears the workspace and figures. It writes task and solver exchange files in the current directory, combines both stages in `coop_traj_final` and passes that trajectory to the supplied video renderer. Look at `coop_traj_final(1)` to see the first vehicle's record. `gross_time` measures the duration of the planned maneuver.

## From lane requests to trajectories

| Step | Entry points | Data passed onward |
| --- | --- | --- |
| Define the task | [InitParams.m](InitParams.m), [GenerateTask.m](GenerateTask.m) | Geometry, vehicle limits and a random set of initial positions and destination lane centers. |
| Plan the lane changes (stage 2) | [GenerateInitialGuessForStage2.m](GenerateInitialGuessForStage2.m), `SolveNlpAtStage2.p`, `LoadOptimumForStage2.p` | Lane-change trajectories in `coop_traj` and duration `tf2`. |
| Resolve conflicts | [ProduceDelayList.m](ProduceDelayList.m), `IsCurTrajValidWithFixedPart.p` | Vehicles are considered from front to rear. When a trajectory conflicts with the accepted part of the formation, the relevant same-lane trajectories shift rearward in 0.5 m steps. |
| Plan formation adjustment (stage 1) | [WriteRelativeFormationInfoForStage1.m](WriteRelativeFormationInfoForStage1.m), [GenerateInitialGuessForStage1.m](GenerateInitialGuessForStage1.m), `SolveNlpAtStage1.p` | The approach trajectories in `coop_traj0` bring the convoy into the spacing needed for the lane changes. |
| Join and display | `StitchTwoStages.p`, `dsa.p`, [CreateVehiclePolygon.m](CreateVehiclePolygon.m) | The chronological maneuver, from initial formation to final lanes. The original renderer exports `SIM.mp4`. |

In [InitParams.m](InitParams.m), the default task uses 10 cars, 3 lanes, 300 stage-1 elements and 100 stage-2 elements. [GenerateTask.m](GenerateTask.m) places the lane centers 3.75 m apart and samples the additional initial longitudinal gap up to 5 m. Fix the same random seed when comparing settings so the initial traffic is comparable.

## Citation

If you use the implementation, cite Bai Li, Youmin Zhang, Yiheng Feng, Yue Zhang, Yuming Ge and Zhijiang Shao, “Balancing Computation Speed and Quality: A Decentralized Motion Planning Method for Cooperative Lane Changes of Connected and Automated Vehicles,” *IEEE Transactions on Intelligent Vehicles* **3**(3), 340–350 (2018). [DOI](https://doi.org/10.1109/TIV.2018.2843159)

```bibtex
@article{Li2018MVMP,
  author = {Bai Li and Youmin Zhang and Yiheng Feng and Yue Zhang and Yuming Ge and Zhijiang Shao},
  title = {Balancing Computation Speed and Quality: A Decentralized Motion Planning Method for Cooperative Lane Changes of Connected and Automated Vehicles},
  journal = {IEEE Transactions on Intelligent Vehicles},
  volume = {3}, number = {3}, pages = {340--350}, year = {2018},
  doi = {10.1109/TIV.2018.2843159}
}
```

© 2018–2025 Bai Li and collaborators. See the repository [license](LICENSE) and notices accompanying individual files. AMPL, IPOPT and other third-party components have their own terms. Contact the maintainer for commercial licensing or collaboration inquiries.
