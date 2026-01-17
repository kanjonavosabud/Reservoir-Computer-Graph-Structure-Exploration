# Reservoir Computer Graph Structure Exploration

## Overview

This project explores how different graph topologies affect the performance of reservoir computers in forecasting chaotic dynamical systems. The research systematically compares multiple graph structures—including random graphs, complete graphs, powerlaw trees, and echo state networks—to determine which topology provides the best forecasting accuracy for the Lorenz system.

## Table of Contents

- [Installation](#installation)
- [Dependencies](#dependencies)
- [Project Structure](#project-structure)
- [Research Methodology](#research-methodology)
- [Graph Topologies Tested](#graph-topologies-tested)
- [Key Components](#key-components)
- [Usage](#usage)
- [Results](#results)
- [Technical Details](#technical-details)

## Installation

### Prerequisites

- Python 3.7 or higher
- Jupyter Notebook or JupyterLab
- pip package manager

### Setup Instructions

1. Clone or navigate to this repository:
```bash
cd Reservoir-Computer-Graph-Structure-Exploration
```

2. Install required packages:
```bash
pip install reservoirpy numpy scipy matplotlib networkx tabulate
```

## Dependencies

### Core Libraries
- **reservoirpy**: Library for reservoir computing implementations (includes ESN support)
- **numpy**: Numerical computations and array operations
- **scipy**: Scientific computing utilities (eigenvalue computation)
- **matplotlib**: Visualization and plotting
- **networkx**: Graph generation and manipulation
- **tabulate**: Formatted table output for results

## Project Structure

```
Reservoir-Computer-Graph-Structure-Exploration/
├── ReservoirCode.ipynb          # Main implementation notebook
├── Exploration of Different Graph Reservoir Structure in Reservoir Computers for Dynamic Systems Forecasting.pdf  # Research paper
├── Results/                     # Generated results and visualizations
│   ├── Default/                # Standard random graph results
│   ├── ESN/                    # Echo State Network results
│   ├── Complete Reservoir/      # Fully connected graph results
│   ├── Powerlaw/               # Powerlaw tree results
│   └── Random/                 # Random edge probability results
└── README.md                   # This file
```

## Research Methodology

### Problem Statement

The research investigates how the underlying graph topology of a reservoir computer affects its forecasting performance. Traditional reservoir computers use random sparse graphs, but this work explores whether alternative topologies—such as complete graphs, powerlaw trees, or library-based ESN implementations—can improve prediction accuracy.

### Dataset

The **Lorenz system** is used as the benchmark:
- Parameters: σ = 10, ρ = 28, β = 8/3, h = 0.02 (time step)
- Features: 3-dimensional time series (X, Y, Z coordinates)
- Training/Testing split: 90% training, 10% testing
- Multiple timestep configurations: 5,000, 10,000, 15,000, and 20,000 total timesteps
- Multiple random seeds: 50, 51, 52 (for statistical robustness)

### Evaluation Metric

**Forecast Horizon Accuracy**: The fraction of predictions where the error is less than one standard deviation of the actual data:
```
Forecast Horizon = (count of |prediction - actual| < std(actual)) / total_predictions
```

This metric is calculated separately for each dimension (X, Y, Z) of the Lorenz system.

## Graph Topologies Tested

### 1. Default (Erdős–Rényi Random Graph)
- **Type**: `gnp_random_graph` from NetworkX
- **Parameters**: 
  - Edge creation probability: 0.1
  - Sparse random connectivity
- **Characteristics**: Traditional reservoir computing approach with random sparse connections

### 2. Echo State Network (ESN)
- **Implementation**: Using `reservoirpy` library
- **Parameters**:
  - Reservoir dimension: 300
  - Leak rate (lr): 0.5
  - Spectral radius (sr): 0.9
  - Ridge regression regularization: 1e-7
- **Characteristics**: Library-optimized ESN with built-in training pipeline

### 3. Complete Graph (Fully Connected)
- **Type**: `complete_graph` from NetworkX
- **Parameters**: All nodes connected to all other nodes
- **Characteristics**: Maximum connectivity, potentially better information flow but higher computational cost

### 4. Powerlaw Random Tree
- **Type**: `random_powerlaw_tree` from NetworkX
- **Parameters**: 
  - Seed for reproducibility
  - Tries: reservoir_dim × 1000 (for generation attempts)
- **Characteristics**: Hierarchical structure with powerlaw degree distribution, mimicking natural networks

### 5. Random Edge Probabilities
- **Type**: Custom implementation
- **Parameters**:
  - Number of edges: 50% of maximum possible edges
  - Random probability assignment for each edge
- **Characteristics**: Alternative random graph generation method with probabilistic edge weights

## Key Components

### Reservoir System Functions

#### `create_system()`
Creates the reservoir computing system with specified graph topology:
- Generates graph structure based on `graph_type` parameter
- Creates random weight matrix
- Normalizes adjacency matrix to desired spectral radius (1.2)
- Returns adjacency matrix, initial reservoir state, and input weights

#### `fit()`
Trains the reservoir on input data:
- Processes each timestep through the reservoir
- Stores reservoir states in state matrix
- Computes output weights using ridge regression (pseudoinverse with regularization)
- Returns trained reservoir state and output weights

#### `forecast()`
Generates predictions for future timesteps:
- Uses trained output weights to predict from reservoir state
- Feeds predictions back into reservoir (autoregressive)
- Returns predicted time series

#### `create_fit_forecast()`
Unified function that combines system creation, training, and forecasting:
- Flexible interface supporting different graph types
- Handles optional training and forecasting steps

### Evaluation Functions

#### `average_forecast_horizon()`
Calculates forecast horizon accuracy for each dimension:
- Compares predictions to actual values
- Uses standard deviation as threshold
- Returns accuracy for X, Y, and Z dimensions

#### `plot_graphs()`
Visualizes predictions vs. actual data:
- Creates 3-panel plot (one for each Lorenz dimension)
- Shows both predicted and actual trajectories
- Saves plots with descriptive titles

#### `makeAndSaveGraph()`
Extended plotting function for result saving:
- Similar to `plot_graphs()` but designed for batch processing
- Includes title formatting for organized result storage

## Usage

### Running Individual Models

1. Open `ReservoirCode.ipynb` in Jupyter
2. Run cells 1-6 to set up dependencies and define functions
3. Run individual model cells (8, 10-16) to test specific graph topologies
4. View results and visualizations

### Running Full Experiment Suite

The notebook includes a comprehensive experiment section (Cell 20) that:
- Tests all 5 graph topologies
- Runs across 4 different timestep configurations (5K, 10K, 15K, 20K)
- Tests with 3 different random seeds (50, 51, 52)
- Generates accuracy tables and timing information
- Saves all result visualizations

To run the full suite:
1. Ensure all dependencies are installed
2. Run all cells up to and including Cell 20
3. Review the formatted tables showing:
   - Forecast horizon accuracies for each model/seed/timestep combination
   - Execution times for each configuration
   - Averaged results across seeds

## Results

The research generates comprehensive results comparing:

1. **Forecast Horizon Accuracy**: For each graph topology across different timesteps and seeds
2. **Execution Time**: Computational cost of each approach
3. **Visual Comparisons**: Time series plots showing predicted vs. actual Lorenz trajectories

### Key Findings (from Results/plain.txt)

The results show:
- **ESN** (library implementation) consistently achieves high forecast horizons (often 1.0 for X and Y dimensions)
- **Complete Graph** shows competitive performance with forecast horizons around 0.94-0.96
- **Powerlaw Tree** demonstrates good performance (0.93-0.97 range)
- **Default Random Graph** shows variable performance depending on seed
- **Random Edge Probabilities** performs similarly to complete graph in some configurations

Results are saved in the `Results/` directory, organized by graph type, with files named by timestep and seed.

## Technical Details

### Reservoir Parameters

- **Reservoir Dimension**: 300 nodes
- **Spectral Radius**: 1.2 (normalized maximum eigenvalue)
- **Edge Creation Probability**: 0.1 (for random graphs)
- **Activation Function**: tanh
- **Regularization**: 0.0001 (ridge regression regularization parameter)

### Graph Generation Details

1. **Default Graph**: Uses NetworkX's `gnp_random_graph`, which creates an Erdős–Rényi random graph where each edge exists with probability `p`.

2. **Complete Graph**: All `n(n-1)/2` possible edges are present, creating maximum connectivity.

3. **Powerlaw Tree**: Uses NetworkX's `random_powerlaw_tree` which generates a tree structure with powerlaw degree distribution, requiring multiple attempts (tries parameter) to successfully generate.

4. **Random Edge Probabilities**: Custom implementation that:
   - Creates a graph with 50% of maximum possible edges
   - Assigns each edge with a random probability
   - Uses a probabilistic threshold for edge inclusion

### Spectral Radius Normalization

All graph topologies are normalized to have the same spectral radius (1.2):
```python
ev = np.linalg.eig(scale_mat)[0]
max_ev = np.max(ev)
rescale_mat = scale_mat / np.absolute(max_ev) * spectral_radius
```

This ensures fair comparison by controlling the reservoir's memory and computational properties.

### Training Process

1. **State Collection**: For each timestep in training data:
   - Compute: `x = adj @ reservoir + input_weights @ data[timestep]`
   - Update: `reservoir = tanh(x)`
   - Store reservoir state in state matrix

2. **Output Weight Calculation**: Using ridge regression:
   - `reservoir_output = data.T @ state_matrix @ (state_matrix @ state_matrix.T + λI)^(-1)`
   - Where λ = 0.0001 is the regularization parameter

3. **Forecasting**: Autoregressive prediction:
   - `prediction = reservoir_output @ reservoir`
   - Feed prediction back: `x = adj @ reservoir + input_weights @ prediction`
   - Update: `reservoir = tanh(x)`

## Nuances and Implementation Details

1. **Seed Reproducibility**: All experiments use fixed seeds (50, 51, 52) for graph generation and random weight initialization, ensuring reproducibility while testing robustness.

2. **Timestep Scaling**: The research tests scalability by running experiments with 5K, 10K, 15K, and 20K timesteps, showing how performance varies with dataset size.

3. **Statistical Robustness**: Testing with multiple seeds (50, 51, 52) provides statistical confidence in results, with final tables showing averaged performance across seeds.

4. **Library vs. Custom Implementation**: The ESN model uses the optimized `reservoirpy` library, while other models use custom implementations, allowing comparison of both topology effects and implementation quality.

5. **Graph-to-Matrix Conversion**: All NetworkX graphs are converted to NumPy adjacency matrices for efficient computation, using `nx.to_numpy_array()`.

6. **Regularization**: The ridge regression uses a small regularization term (0.0001) to prevent numerical instability in matrix inversion, especially important for complete graphs with high connectivity.

7. **Autoregressive Feedback**: During forecasting, predictions are fed back into the reservoir as inputs, creating an autoregressive loop that can accumulate errors over long prediction horizons.

8. **Warmup Period**: While not explicitly shown in the main code, the concept of allowing the reservoir to stabilize before extracting features is important in reservoir computing, though this implementation uses all training data.

## Future Work

Potential extensions include:
- Testing additional graph topologies (small-world, scale-free, etc.)
- Exploring adaptive graph structures
- Investigating optimal spectral radius for different topologies
- Comparing computational complexity more rigorously
- Testing on additional chaotic systems (Rössler, Mackey-Glass, etc.)
- Hybrid approaches combining multiple graph types

---

**Research conducted by Kanjonavo Sabud, guided by Dr. Selma Yilmaz.**
