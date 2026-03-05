# SEEMS: Smart Efficient Energy Management System for Charging Stations Infrastructure

## Overview

This repository presents a Stackelberg game-theoretic framework for optimizing Mobile Charging Vehicle (MCV) deployment alongside Fixed Charging Stations (FCSs) to dynamically manage electric vehicle (EV) charging demand in urban areas. The model captures the hierarchical interaction between charging operators (leaders) and EV drivers (followers) to optimize MCV placement, pricing strategies, and service efficiency.

The simulator underlying SEEMS is used in two complementary lines of work:

- a hybrid FCS–MCV optimization study based on Stackelberg game theory and multi-objective Pareto analysis (Transportation Research Part C submission), and  
- an agentic AI / LLM-based coordination study for dynamic EV charging (IEEE Intelligent Transportation Systems Magazine submission).   

This repository is intended to give reviewers and readers a precise view of the modeling assumptions, parameters, and experimental setup used in those manuscripts.

---

## Key Features

🚗 **Game-theoretic optimization**  
Stackelberg game formulation between the charging operator (leader) and EV drivers (followers), capturing pricing and deployment decisions on the operator side and generalized cost minimization on the user side. 

🔄 **Dynamic deployment strategy**  
MCV deployment reacts to real-time congestion at FCSs using queue-based triggers, utilization thresholds, and a rank-based selection of preferred meeting points. 

📊 **Multi-objective optimization**  
Jointly considers operator revenue, combined FCS+MCV utilization, the number of unserved EVs, and average waiting time. Pareto fronts over different FCS–MCV configurations are used to identify balanced operating points. 

⚡ **Scalable and flexible**  
Designed for city-scale grids, with separate layers for long-term planning (FCS expansion), tactical deployment (MCV allocation, pricing), and real-time control, and with parameters that can be reconfigured by a higher-level planner or LLM-based agent. 

📈 **Performance validation**  
Simulation results show reduced unserved EVs, improved utilization of both FCSs and MCVs, and shorter waiting times compared to an FCS-only baseline, across a range of demand and infrastructure scenarios. 

---

## Model and Simulation Summary

This section summarizes the main modeling choices and simulation settings so that readers can easily align the manuscripts with the artifact.

- **Network and infrastructure**  
  - Urban area: 20 km × 20 km Manhattan-style grid.  
  - FCSs: baseline with 3 FCSs (each with 10 ports and a defined energy budget), extended to 4–6 FCS configurations in the Pareto analysis.  
  - MCVs: fleet of up to 10 MCVs, each with finite onboard battery capacity and 4 concurrent charging ports.  
  - Meeting points: 10 candidate locations used for temporary MCV deployment. 

- **Decision hierarchy (Stackelberg game)**  
  - **Leader (operator):** chooses FCS tariffs, MCV deployment decisions (x_im), MCV prices (p_m), and return-to-depot policies, subject to capacity, fleet-size, and meeting-point constraints.  
  - **Followers (EV drivers):** select a single charging option (FCS or MCV) that minimizes a generalized cost combining charging cost, travel distance, and waiting time.

- **Core constraints (examples)**  
  - MCV and FCS energy capacity limits (Eqs. (1)–(2)).  
  - Meeting-point capacity and MCV fleet-size limits (Eqs. (3)–(4)).  
  - Single-source charging per EV (Eq. (5)).  
  - MCV idle-time and state-of-charge rules for return and recharge. 

- **Demand and time structure**  
  - Time horizon: 8-hour window with peak, normal, and off-peak intervals.  
  - EV arrivals: Poisson processes with different rates for each interval.  
  - EV energy demand: Gaussian distribution (typically 30–70 kWh) to reflect heterogeneous charging needs.

- **Key parameters and their roles**  

  These parameters are central both for the optimization study and for the agentic AI policy-orchestration experiments:

  - **θ (theta)** – target profit margin for MCV operations. Larger θ increases MCV prices to achieve higher profit per unit of energy; smaller θ prioritizes affordability and service quality over revenue.  
  - **α, β, γ** – cost-weighting factors in the MCV pricing rule:
    - **α** multiplies travel-related costs (how strongly long-distance deployment is penalized),  
    - **β** multiplies idle-time costs at the curb (how sensitive pricing is to scarce curb space and congestion),  
    - **γ** multiplies other operational costs (maintenance, staffing, etc.).  
    Together, (α, β, γ) control how local conditions translate into MCV price premiums over FCS prices.  
  - **ζ, δ, ε** – driver cost weights in the generalized cost minimized by EVs:
    - **ζ** weights monetary charging cost,  
    - **δ** weights travel distance cost,  
    - **ε** weights waiting-time cost.  
    Changing (ζ, δ, ε) lets the simulator emulate different user populations, from price-sensitive to time-sensitive drivers.  
  - **BatteryCap_i** – MCV i’s onboard energy capacity, limiting the total energy it can deliver before returning to a depot.  
  - **Q_j** – maximum energy capacity that FCS j can dispense over the planning horizon.  
  - **MCV_Limit** – maximum number of MCVs available in the fleet, capping the total mobile capacity.  
  - **MCVcap_m** – maximum number of MCVs that can be parked and operated simultaneously at meeting point m (curb-space / regulation constraint).  
  - **C_im** – fixed deployment cost of sending MCV i to meeting point m (e.g., setup, staffing).  
  - **T_im and F** – T_im is the travel distance from the depot to meeting point m for MCV i; F is the per‑kilometer travel cost for MCVs.  
  - **CostPerKm and CostPerHour** – coefficients used in the EV generalized cost to convert distance and waiting time into monetary terms.  
  - **T_idle** – maximum allowed idle time for an MCV at a location before it must return to the depot for redeployment or recharge.  

  The technical PDF in this repository contains the complete set of parameters and decision variables, including any that are not explicitly listed above, together with their default values and units.

- **Performance metrics**  
  - Total revenue and revenue per FCS/MCV.  
  - Combined and per-unit utilization.  
  - Number of unserved EVs and unmet energy.  
  - Average waiting time at FCSs and MCVs.  
  - Fairness index across districts (used in the agentic AI proof-of-concept experiments).   

Section numbering, equations, and parameter symbols in the technical PDF follow the same notation as in the journal manuscripts, so that every quantity reported there can be traced back to its exact definition here.

---

## Technology Stack

🧠 **Game theory and optimization**  
Stackelberg game formulation, mixed-integer decision variables for deployment and assignment, and Pareto-based multi-objective analysis. 

📊 **Data processing and simulation**  
Python-based simulation using libraries such as NumPy, Pandas, and SimPy for demand generation, queue tracking, and event-driven logic.

🎥 **Visualization**  
Matplotlib-based visualization and exported videos illustrating EV arrivals, cumulative assignments, and real-time system dynamics.

---

## Use Cases

✔ **Smart EV charging infrastructure planning** for dense urban areas with heterogeneous demand.  

✔ **Dynamic pricing and resource allocation** for MCV fleets operating alongside fixed infrastructure.  

✔ **Demand-responsive, risk-aware deployment strategies** that can defer or target permanent FCS investments.  

✔ **Agentic AI research** where higher-level planners (e.g., LLM-based agents) reconfigure charging policies via a compact set of interpretable parameters.

---

## Project Update

✅ **Simulation completed**  
Extensive simulations have been carried out to validate the Stackelberg-based hybrid FCS–MCV framework under different demand patterns and infrastructure configurations. Results indicate substantial improvements in charging efficiency, reduced waiting times, and higher utilization compared to FCS-only baselines. 

📝 **Technical documentation uploaded**  
A detailed technical PDF (included in this repository) documents:
- the full model formulation (sets, parameters, decision variables, and constraints),
- the Stackelberg leader–follower structure and objective functions,
- the dynamic MCV deployment and ranking algorithms, and
- the simulation setup and evaluation metrics used in the experiments. 

🎥 **Simulation videos uploaded**  
To illustrate system behavior, the repository includes videos showing:
1. EV arrivals over time,  
2. cumulative FCS–MCV assignments, and  
3. real-time assignment and deployment dynamics.

📄 **Associated manuscripts**  
Two journal manuscripts based on this framework are being prepared / submitted:
- a methodological paper on the Stackelberg-based hybrid FCS–MCV optimization (target: *Transportation Research Part C: Emerging Technologies*), and  
- a system-level, agentic AI perspective article on LLM-based coordination for dynamic EV charging (target: *IEEE Intelligent Transportation Systems Magazine*).   

📌 **Code release plan**  
To protect the integrity of the peer-review process and allow for double-blind submissions where required, the full source code will be released after the associated manuscripts complete peer review and are accepted. Until then, the technical documentation and parameter tables provide sufficient detail for independent reimplementation of the main experiments.

Stay tuned for further updates! 🚀🔋⚡

---

## Role in Agentic AI for Hybrid EV Charging

This simulator also serves as the tactical and operational layer in our ongoing work on agentic AI for EV charging. In that line of work, a higher-level planner (for example, an LLM-based coordinator agent) does not compute schedules directly, but configures a small set of parameters in the Stackelberg model, such as: 

- the profit margin parameter θ and the cost weights (α, β, γ) in the MCV pricing rule,  
- the driver cost weights (ζ, δ, ε) that trade off price, distance, and waiting time,  
- the deployment triggers and Pareto tie-breaking rules used to select operating points, and  
- fairness-related metrics and thresholds (e.g., district-level service quality indices).

By changing only these policy-level parameters, the same SEEMS simulator can realize different operator intents such as revenue maximization, equitable service across districts, or peak-resilient operation. The proof-of-concept policy orchestration experiments reported in our *IEEE Intelligent Transportation Systems Magazine* submission on agentic AI for dynamic EV charging are built directly on this simulator. 

---

## Reproducibility and Current Contents

At the moment, this repository provides:

- a detailed technical PDF that explains the model formulation, optimization approach, deployment logic, and simulation setup used in the manuscripts,  
- three short videos that visualize EV arrivals, cumulative FCS–MCV assignments, and real-time assignment dynamics.

The Python implementation will be released in a later update once the associated journal manuscripts have completed peer review. The technical document includes all modeling assumptions, parameter values, and algorithmic details (including pseudo-code) needed to reimplement the framework independently. 

---

## How to Cite

If you use SEEMS or refer to the simulator in your own work, please cite it as:

> J. Dutta, Z. Husain, R. Mizouni, S. Singh, T. H. M. El-Fouly, and H. Otrok,  
> "SEEMS: Smart Efficient Energy Management System for Charging Stations Infrastructure,"  
> GitHub repository, 2026. [Online]. Available: https://github.com/joy-dutta/SEEMS

BibTeX:

```bibtex
@misc{SEEMS_repo,
  author       = {Dutta, Joy and Husain, Zainab and Mizouni, Rabeb and Singh, Shakti and El-Fouly, Tarek H. M. and Otrok, Hadi},
  title        = {SEEMS: Smart Efficient Energy Management System for Charging Stations Infrastructure},
  howpublished = {\url{https://github.com/joy-dutta/SEEMS}},
  year         = {2026},
  note         = {Accessed: <month> <year>}
}
