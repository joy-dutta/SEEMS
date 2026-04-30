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
 
### Note: 
Fairness is measured using **Jain's fairness index**, a standard metric in resource allocation. For each district *i* we compute a service level `s_i`, defined as the fraction of EV charging
requests starting in that district that are successfully served during the simulation horizon. If there are n districts and s_i denotes the service level in district i, the fairness index is:

J = ( (sum of s_i over all districts)^2 ) / ( n × (sum of s_i^2 over all districts) )

where `J` ranges from `1/n` (highly unequal service) to `1` (perfectly equal service). Higher values therefore indicate more equitable service quality across districts. In the IEEE ITS Magazine proof‑of‑concept experiments, we report this Jain fairness index alongside revenue, average waiting time, and the number of unserved EVs.  

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
- a methodological paper on the Stackelberg-based hybrid FCS–MCV optimization (target: **Energy**), and  
- a system-level research paper on bounded agentic AI orchestration for dynamic EV charging with fixed charging stations and mobile charging vehicles (target: **IEEE Network Magazine**).

📌 **Code release plan**  
To protect the integrity of the peer-review process and allow for double-blind submissions where required, the full source code will be released after the associated manuscripts complete peer review and are accepted. Until then, the technical documentation and parameter tables provide sufficient detail for independent reimplementation of the main experiments.

Stay tuned for further updates! 🚀🔋⚡

---

## Role in Agentic AI for Hybrid EV Charging

This repository also supports a second line of work on **agentic AI for dynamic EV charging**, currently under review as an IEEE Network Magazine article:

> *Agentic AI for Dynamic EV Charging: Explainable Coordination of Fixed Charging Stations and Mobile Charging Vehicles*

The central idea of that article is not that a language model should directly control chargers, power electronics, or vehicles. Instead, the proposed architecture uses an LLM-based agentic layer as a **bounded tactical coordinator** above an existing EV charging simulator. The simulator remains responsible for the physical and operational calculations: travel time, queueing delay, service time, charger availability, MCV battery feasibility, pricing, reservation feasibility, and resource-state updates.

In other words:

- the simulator decides what actions are physically feasible;
- the LLM-based agent reads operator and EV context;
- the LLM can choose only from simulator-generated feasible options;
- every LLM decision can be checked, logged, and audited;
- low-level real-time charger control remains outside the LLM.

This distinction is important for reviewers. The agentic AI layer is not an unconstrained autonomous controller. It is an **intent-to-action and explanation layer** that helps translate EV preferences, operator policy, and system state into bounded decisions over a hybrid FCS-MCV charging system.

### Proof-of-Concept Experiment Used in the IEEE Network Article

For the IEEE Network proof-of-concept, we implemented a lightweight hybrid EV charging simulator with:

- a 20 km by 20 km Manhattan-style service region;
- 3 fixed charging stations (FCSs);
- 6 mobile charging vehicles (MCVs);
- heterogeneous EV models from Tesla, BYD, Geely, and Volkswagen;
- EV-side intents: `time_optimized`, `cost_optimized`, and `greedy`;
- an operator-side revenue-oriented policy;
- per-resource queue updates after each EV assignment;
- MCV battery-reserve feasibility, so an MCV cannot accept EVs indefinitely;
- per-EV flow logs and aggregate comparison files.

The proof-of-concept uses realistic abstraction rather than field deployment. FCSs are modeled as DC fast-charging sites with multiple ports. MCVs are modeled as battery-backed mobile chargers that can be dispatched from a central depot to FCS locations as temporary capacity boosters. EVs arrive during a peak-period setting, and each EV sees the current state of queues, travel time, charging time, cost, and feasible FCS/MCV options.

### Controller Modes Compared

The IEEE Network experiment compares three main controller modes:

1. **`deterministic_fixed`**

   This is the original fixed-rule non-LLM baseline. It uses deterministic scoring logic and does not make any LLM calls.

2. **`deterministic_satisfaction`**

   This is a stronger non-LLM comparator. It receives the same feasible option list available to the EV-side LLM and chooses the option with the highest estimated EV satisfaction. Ties are broken by lower waiting time, lower cost, and lower total time.

3. **`llm_full`**

   This is the bounded LLM-assisted controller. For each arriving EV, the operator-side LLM can reason about whether mobile support should be deployed, and the EV-side LLM can choose one option from the feasible simulator-generated option list. The LLM cannot invent new stations, prices, meeting points, queues, or physical resources.

The second controller is especially important. It prevents the proof-of-concept from being a weak comparison of "LLM versus simple heuristic." Instead, the LLM-assisted controller is compared against both an original fixed rule and a strong satisfaction-maximizing non-LLM controller.

### What EV Satisfaction Means

The key user-centered metric is **EV satisfaction**, not operator revenue. For each EV, the simulator evaluates the chosen option using a weighted utility over:

- total time,
- charging cost,
- waiting time,
- reservation reliability.

The weights depend on the EV intent. A `time_optimized` EV gives more importance to time and waiting. A `cost_optimized` EV gives more importance to monetary cost. A `greedy` EV chooses the currently best immediate tradeoff among time, price, distance, and MCV suitability, without planning for future system outcomes.

This satisfaction score is used consistently across deterministic and LLM-assisted runs.


### Extended Evaluation for the Paper Revision

To make the proof-of-concept stronger for peer review, an extended evaluation was added. It includes:

- a demand sweep over EV counts from 10 to 30;
- the three controller modes listed above;
- representative reruns using three random seeds;
- role ablations with `llm_operator_only` and `llm_ev_only`;
- LLM call-budget logging;
- feasibility and compliance auditing for every LLM EV decision;
- per-EV decision logs and aggregate CSV/TeX summary tables.

The extended evaluation found that hybrid FCS-MCV activity appeared already at the 10-EV case. Therefore, the routine and hybrid-threshold cases coincide in this particular experiment. A stronger stress scenario with 30 EVs was also evaluated. These results should be interpreted carefully. The satisfaction-maximizing deterministic controller is very strong and often reduces waiting time by aggressively selecting MCV options. The LLM-assisted controller does not simply minimize waiting time. Instead, it provides a bounded, policy-aware coordination layer that balances EV satisfaction, operator intent, and scarce mobile-charger use.

### Repository Documents for Reviewers

To support the IEEE Network review process, this repository includes two companion PDFs for the agentic AI proof-of-concept:

1. **Plain-language experiment guide**

   Explains the real-world analogy, EV/FCS/MCV setup, controller modes, EV and operator intents, result columns, and interpretation of the normal and extended evaluations. File name: "AGENTIC_EXPERIMENT_GUIDE_EASY_READ"

2. **Technical recreation guide**

   Provides the mathematical formulas, queueing logic, satisfaction calculation, MCV deployment rules, bounded LLM contract, controller definitions, output files, and parameter values needed to recreate the experiment. File name: "AGENTIC_EXPERIMENT_RECREATION_TECHNICAL_GUIDE"

Together, these documents expand the compact proof-of-concept section in the IEEE Network article. The article itself is space-limited, while the repository documents provide the full practical and technical detail for reviewers and future researchers.

### What This Repository Does Not Claim

This repository does **not** claim that LLMs replace optimization, reinforcement learning, queueing models, or verified local controllers. It also does **not** implement autonomous real-time control by a language model.

The intended role of the LLM-based agentic layer is limited to:

- tactical EV-arrival and dispatch-decision events;
- bounded selection among feasible simulator-generated options;
- operator-policy interpretation;
- EV-preference interpretation;
- explanation and audit support.

This matches the central argument of the IEEE Network article: agentic AI is most useful for dynamic EV charging when it acts as a transparent, auditable orchestration layer above existing charging models and controllers, rather than bypassing them.

---

## Reproducibility and Current Contents

For the Stackelberg-based SEEMS optimization study, the repository provides the existing technical documentation and visualization artifacts describing the hybrid FCS-MCV model, deployment logic, and simulation setup.

For the IEEE Network agentic-AI proof-of-concept, the repository provides two additional PDF companions:

- a plain-language guide to the normal and extended evaluations;
- a technical recreation guide with all formulas, parameters, controller modes, and output-file descriptions.

These documents are intended to help reviewers trace the paper's proof-of-concept claims back to the simulator assumptions, calculations, and result files. If the source code is not yet public during peer review, the technical recreation guide provides the necessary details to independently reproduce the experiment structure and metrics.





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
