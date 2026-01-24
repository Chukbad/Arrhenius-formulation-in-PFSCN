# 🥬 Arrhenius Kinetics Integration in Perishable Food Supply Chain Network (PFSCN) Optimization

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Pyomo](https://img.shields.io/badge/Pyomo-6.0%2B-green.svg)](http://www.pyomo.org/)
[![Gurobi](https://img.shields.io/badge/Solver-Gurobi%2011.0-red.svg)](https://www.gurobi.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **A Mixed-Integer Linear Programming (MILP) model for optimizing perishable food supply chains with temperature-dependent quality degradation based on Arrhenius kinetics.**

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [Mathematical Model](#-mathematical-model)
- [Installation](#-installation)
- [Usage](#-usage)
- [Results](#-results)
- [Project Structure](#-project-structure)
- [Interactive Dashboard](#-interactive-dashboard)
- [Publications](#-publications)
- [Author](#-author)
- [Citation](#-citation)

---

## 🎯 Overview

This repository contains the implementation of a **Perishable Food Supply Chain Network (PFSCN)** optimization model that integrates **Arrhenius kinetics** to capture temperature-dependent quality degradation of perishable products.

### The Problem

Perishable food products (fruits, vegetables, dairy, meat) lose quality over time due to biochemical reactions. The rate of degradation is highly temperature-sensitive — a few degrees difference can mean the difference between premium-quality produce and unsellable waste.

### The Solution

This model:
1. **Captures quality degradation** using the scientifically-validated Arrhenius equation
2. **Optimizes logistics decisions** (sourcing, routing, storage) to maximize profit
3. **Implements 3-zone pricing** based on quality at delivery (Premium, Standard, Discount)
4. **Uses McCormick linearization** to handle bilinear terms, enabling use of efficient MILP solvers

---

## ✨ Key Features

| Feature | Description |
|---------|-------------|
| 🌡️ **Arrhenius Kinetics** | Temperature-dependent quality decay: `Q(t) = Q₀ × exp(-k×t)` where `k = A × exp(-Eₐ/RT)` |
| 💰 **3-Zone Pricing** | Premium (≥95%), Standard (80-95%), Discount (<80%) with differentiated pricing |
| 🚛 **Multi-Modal Transport** | Refrigerated vs. ambient vehicle selection with cost-quality tradeoffs |
| 🏭 **Warehouse Decisions** | Binary facility location with temperature-controlled storage |
| 📊 **McCormick Linearization** | Converts bilinear `Q × y` terms to linear constraints for MILP |
| ⚡ **Gurobi Optimization** | Solves to optimality in <1 second for realistic instances |

---

## 📐 Mathematical Model

### Objective Function

```
Maximize: Total Profit = Revenue - Costs

Revenue = Σ (Premium_Sales × P_premium + Standard_Sales × P_standard + Discount_Sales × P_discount)

Costs = Purchase_Cost + Transport_Cost + Storage_Cost + Waste_Penalty + Unmet_Demand_Penalty
```

### Arrhenius Equation

The core innovation is integrating the Arrhenius equation for quality degradation:

```
Q(t) = Q₀ × exp(-k × t)

where:
  k = A × exp(-Eₐ / (R × T))
  
  Q₀  = Initial quality (100%)
  t   = Time (days)
  A   = Pre-exponential factor (s⁻¹)
  Eₐ  = Activation energy (J/mol)
  R   = Gas constant (8.314 J/mol·K)
  T   = Temperature (Kelvin)
```

### Key Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Eₐ | 50,000 J/mol | Activation energy for produce degradation |
| A | 1×10¹⁰ s⁻¹ | Pre-exponential factor |
| T_refrigerated | 4°C (277K) | Refrigerated transport/storage |
| T_ambient | 20°C (293K) | Ambient conditions |
| Premium threshold | ≥95% | Quality for premium pricing |
| Standard threshold | 80-95% | Quality for standard pricing |

### Quality Zones & Pricing

| Zone | Quality Range | Price ($/kg) |
|------|---------------|--------------|
| 🟢 Premium | ≥ 95% | $10 - $12 |
| 🟡 Standard | 80% - 95% | $7 - $8.50 |
| 🔴 Discount | < 80% | $4 - $5 |

---

## 🛠️ Installation

### Prerequisites

- Python 3.8+
- Gurobi Optimizer (academic license available free)
- Jupyter Notebook

### Setup

```bash
# Clone the repository
git clone https://github.com/Chukbad/Arrhenius-formulation-in-PFSCN.git
cd Arrhenius-formulation-in-PFSCN

# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install pyomo numpy pandas matplotlib gurobipy jupyter
```

### Gurobi License

1. Register at [Gurobi Academic License](https://www.gurobi.com/academia/academic-program-and-licenses/)
2. Download and install Gurobi
3. Activate license: `grbgetkey YOUR-LICENSE-KEY`

---

## 🚀 Usage

### Quick Start

```python
# Open Jupyter Notebook
jupyter notebook "Full Code implementation for Arrhenius Kinetics Integration in PFSCN.ipynb"
```

### Running the Model

The notebook is organized in phases:

1. **Phase 1**: Import libraries and define Arrhenius parameters
2. **Phase 2**: Define sets, parameters, and network structure
3. **Phase 3**: Create decision variables
4. **Phase 4**: Add constraints (flow balance, capacity, quality tracking)
5. **Phase 5**: Define objective function
6. **Phase 6**: Configure Gurobi solver and optimize
7. **Phase 7**: Extract and visualize results

### Example Code

```python
import pyomo.environ as pyo

# Create model
model = pyo.ConcreteModel()

# Arrhenius quality calculation
def arrhenius_quality(T_celsius, time_days, Ea=50000, A=1e10):
    R = 8.314
    T_kelvin = T_celsius + 273.15
    k = A * math.exp(-Ea / (R * T_kelvin))
    return math.exp(-k * time_days)

# At 4°C for 1.5 days
quality = arrhenius_quality(4, 1.5)  # Returns ~0.97 (97%)
```

---

## 📊 Results

### Optimization Performance

| Metric | Value |
|--------|-------|
| **Optimal Profit** | $40,976 |
| **Solver** | Gurobi 11.0.3 |
| **Model Type** | Linear MIP |
| **Solve Time** | 0.63 seconds |
| **MIP Gap** | 0.00% (optimal) |
| **Simplex Iterations** | 131 |

### Solution Insights

- ✅ **100% refrigerated transport** selected (quality preservation > cost savings)
- ✅ **96.2% average quality** at delivery (Premium zone)
- ✅ **Zero waste** due to optimal routing and timing
- ✅ **11-day planning horizon** with variable demand

### Quality Impact Analysis

| Storage Temp | Quality at Delivery | Zone | Profit Impact |
|--------------|---------------------|------|---------------|
| 4°C | 97.2% | Premium | Baseline |
| 10°C | 89.5% | Standard | -23% |
| 20°C | 72.1% | Discount | -48% |
| 30°C | 51.3% | Waste | -85% |

---

## 📁 Project Structure

```
Arrhenius-formulation-in-PFSCN/
│
├── Full Code implementation for Arrhenius Kinetics Integration in PFSCN.ipynb
│   └── Complete MILP model with all phases
│
├── Arrhenius integration Cor (1).ipynb
│   └── Core Arrhenius integration and testing
│
├── README.md
│   └── This documentation
│
└── (Coming Soon)
    ├── data/
    │   └── sample_network.json
    ├── src/
    │   ├── arrhenius.py
    │   ├── model.py
    │   └── solver.py
    └── results/
        └── optimization_output.csv
```

---

## 🖥️ Interactive Dashboard

An interactive React-based dashboard is available for visualizing model results and performing what-if analysis.

**Features:**
- Real-time profit calculation based on parameter changes
- Arrhenius degradation curves at different temperatures
- Animated supply chain network visualization
- Sensitivity analysis tools

> 🔗 **Live Demo**: [Coming Soon]

---

## 📚 Publications

This research has been published/presented at:

1. **IEOM Bali 2025** - 6th Asia Pacific Conference on Industrial Engineering and Operations Management
   - Paper: "Arrhenius Kinetics Integration in Perishable Food Supply Chain Optimization"
   - [ResearchGate](https://www.researchgate.net/publication/396865079_Arrhenius_Kinetics_Integration_in_Perishable_Food_Supply_Chain_Optimization)
   - [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5545258)

2. **IEOM Milwaukee 2026** - Upcoming presentation (Extended version with interactive dashboard)

3. **Computers & Industrial Engineering** (Elsevier) - Manuscript in preparation

### Related Literature

**Quality Degradation & Arrhenius Kinetics:**
- Eze et al. (2024) - Machine learning-based optimal temperature management model for safety and quality control of perishable food supply chain. *Scientific Reports*, 14, 27228.
- Lejarza & Baldea (2022) - An efficient optimization framework for tracking multiple quality attributes in supply chains of perishable products. *European Journal of Operational Research*, 297(3), 890-903.
- Zhang & Mohammad (2024) - Sustainability of perishable food cold chain logistics: A systematic literature review. *SAGE Open*, 14(3).

**Cold Chain Optimization Models:**
- Kumar & Singh (2024) - Warehouse location optimization in perishable supply chains under deterministic demand. *International Journal of Logistics Management*, 35(2), 234-251.
- Wu et al. (2025) - Multi-objective optimization for green cold chain logistics of perishable products with heterogeneous vehicle fleet. *Transportation Research Part E*, 145, 102543.
- Claassen et al. (2024) - Integrating time-temperature dependent deterioration in the economic order quantity model for perishable products in multi-echelon supply chains. *Omega*, 125, 103041.

**MILP & Supply Chain Network Design:**
- Chen et al. (2023) - Green vehicle routing using mixed fleets for cold chain distribution. *Expert Systems with Applications*, 233, 120979.
- Arabsheybani et al. (2024) - Sustainable cold supply chain design for livestock and perishable products using data-driven robust optimization. *Int. J. Management Science and Engineering Management*.
- Gholian-Jouybari et al. (2024) - An in-depth metaheuristic approach to design a sustainable closed-loop agri-food supply chain network. *Applied Soft Computing*, 150, 111017.

**IoT & Real-Time Monitoring:**
- Waldhans et al. (2024) - Temperature control and data exchange in food supply chains: Current situation and applicability of digitalized time–temperature-indicators. *J. Packaging Technology and Research*, 8(1), 79-93.
- Hassoun et al. (2024) - Food traceability 4.0 as part of the fourth industrial revolution: key enabling technologies. *Critical Reviews in Food Science and Nutrition*, 64(3), 873-889.

---

## 👤 Author

**Damilola Badejo**

- 🎓 M.Sc. Industrial Management, Texas A&M University-Kingsville (Expected May 2026)
- 🏆 GPA: 4.0/4.0
- 📧 [LinkedIn](https://linkedin.com/in/damilola-badejo)
- 🔬 Research Focus: Supply Chain Optimization, Operations Research, Quality Management

### Experience Highlights
- **Tesla** - Global Supply Manager Intern, Supplier Industrialization Engineer Intern
- **Six Sigma Green Belt** Certified
- **AWS Cloud Practitioner** Certified

---

## 📖 Citation

If you use this code in your research, please cite:

```bibtex
@inproceedings{badejo2025arrhenius,
  author = {Badejo, Damilola Chukwudy},
  title = {Arrhenius Kinetics Integration in Perishable Food Supply Chain Optimization},
  booktitle = {Proceedings of the 6th Asia Pacific Conference on Industrial Engineering and Operations Management},
  year = {2025},
  location = {Bali, Indonesia},
  publisher = {IEOM Society International},
  note = {Available at SSRN: https://ssrn.com/abstract=5545258}
}
```

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Texas A&M University-Kingsville - Industrial Management Program
- Gurobi Optimization - Academic License Program
- IEOM Society International

---

<p align="center">
  <strong>⭐ If you find this useful, please star the repository! ⭐</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/github/stars/Chukbad/Arrhenius-formulation-in-PFSCN?style=social" alt="GitHub Stars">
</p>
