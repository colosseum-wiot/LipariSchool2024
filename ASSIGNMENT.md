# Assignments

You can use the 'sample xApp' available in [this repository](https://github.com/wineslab/colosseum-near-rt-ric) and this [OpenRAN Gym tutorial](https://openrangym.com/tutorials/scope-coloran-o-ran) as template for the xApps and guide for the assignments.

## Assignment 1: Create a Slicing Control xApp

- **Objective**: Develop an xApp with basic PRB increase/decrease policy to satisfy a certain minimum throughput.
- **Details**:
  - The xApp should dynamically adjust the PRBs to meet the minimum throughput requirements.
  - The PRB adjustments should be incremental, with increases or decreases of +3 or -3 (i.e. 1 RBG) at a time. The change should be made at regular intervals (e.g. every 5 s).

## Assignment 2: Create a Traffic Calssification xApp

- **Objective**: Develop an xApp that receives E2 data and classifies the type of traffic.
- **Details**:
  - The xApp should be capable of differentiating between two types of traffic: low throughput and high throughput.
  - The classification should be adaptive, considering the variable nature of `iperf` traffic.
