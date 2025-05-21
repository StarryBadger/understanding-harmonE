---
layout: project_page
permalink: /

title: "HarmonE: A Self-Adaptive Approach to Architecting Sustainable MLOps"
authors: |
    Hiya Bhatt<sup>1⋆</sup>,
    Shaunak Biswas<sup>1⋆</sup>,
    Srinivasan Rakhunathan<sup>2</sup>,
    Karthik Vaidhyanathan<sup>1</sup>
affiliations: |
    <sup>1</sup>Software Engineering Research Centre, IIIT Hyderabad, India<br>
    <sup>2</sup>Microsoft, India<br>
    <span style="font-size: 0.8em;"><sup>⋆</sup>Equal contribution</span>

paper: https://arxiv.org/abs/2505.13693
code: https://github.com/sa4s-serc/HarmonE 
data: https://pems.dot.ca.gov
---

<!-- Using HTML to center the abstract -->
<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Abstract</h2>
        <div class="content has-text-justified">
Machine Learning Enabled Systems (MLS) are becoming integral to real-world applications, but ensuring their sustainable performance over time remains a significant challenge. These systems operate in dynamic environments and face runtime uncertainties like data drift and model degradation, which affect the sustainability of MLS across multiple dimensions: technical, economical, environmental, and social. While Machine Learning Operations (MLOps) addresses the technical dimension by streamlining the ML model lifecycle, it overlooks other dimensions. Furthermore, some traditional practices, such as frequent retraining, incur substantial energy and computational overhead, thus amplifying sustainability concerns. To address them, we introduce HarmonE, an architectural approach that enables self-adaptive capabilities in MLOps pipelines using the MAPE-K loop. HarmonE allows system architects to define explicit sustainability goals and adaptation thresholds at design time, and performs runtime monitoring of key metrics, such as prediction accuracy, energy consumption, and data distribution shifts, to trigger appropriate adaptation strategies. We validate our approach using a Digital Twin (DT) of an Intelligent Transportation System (ITS), focusing on traffic flow prediction as our primary use case. The DT employs time series ML models to simulate real-time traffic and assess various flow scenarios. Our results show that HarmonE adapts effectively to evolving conditions while maintaining accuracy and meeting sustainability goals. 
        </div>
    </div>
</div>

---

## Motivation
Machine Learning Enabled Systems (MLS) are increasingly vital in diverse applications, yet their long-term sustainability across technical, environmental, economic, and social dimensions is a growing concern. Current MLOps practices often focus on technical aspects like model accuracy, sometimes overlooking other sustainability dimensions. Practices like frequent model retraining, while ensuring accuracy, can lead to significant energy consumption and computational costs, exacerbating environmental and economic concerns. The Green AI movement aims to address these, but a comprehensive, adaptive approach integrated into MLOps is needed.

## Our Approach: HarmonE
We introduce **HarmonE**, an architectural approach designed to integrate self-adaptive capabilities into MLOps pipelines. HarmonE aims to manage runtime uncertainties and align system behavior with predefined sustainability goals using the MAPE-K (Monitor-Analyze-Plan-Execute over a shared Knowledge base) control loop.
At its core, HarmonE allows system architects to:
1.  Define explicit sustainability goals (e.g., energy consumption limits, minimum accuracy) and adaptation thresholds at design time using a **Decision Map**.
2.  Perform runtime **Monitoring** of key metrics like prediction accuracy, energy usage, and data distribution shifts.
3.  **Analyze** these metrics against the defined goals and thresholds to detect deviations or potential sustainability violations.
4.  **Plan** and **Execute** appropriate adaptation strategies, such as switching to a more energy-efficient model, selectively retraining a model, or reusing a previously versioned model.
HarmonE employs a control-theoretic mechanism that allows dynamic adaptation boundaries to evolve based on the cumulative effect of deviations from a reference level, enabling more deliberate and context-aware responses.

## Key Features and Components
HarmonE is structured around three main components: the Managed System, the Managing System, and the Decision Map.

1.  **Decision Map (DM):** A design-time artifact where architects visualize sustainability concerns across various dimensions (Social, Environmental, Technical, Economic) and define relationships between system functionalities and these concerns. Adaptation boundaries are set based on these goals.
2.  **Managed System:** The MLOps pipeline under adaptation, consisting of:
    *   *Training Subsystem:* For training/retraining models when triggered.
    *   *Inference Subsystem:* For real-time predictions using the deployed model.
3.  **Managing System (MAPE-K Loop):**
    *   *Monitor:* Collects runtime metrics (e.g., R² score, normalized energy, KL divergence for data drift).
    *   *Analyzer:* Detects uncertainties by comparing monitored metrics against thresholds (e.g., `EMA(Score) < S_min`, `Energy > Dynamic_Threshold_TE`, `Drift > T_drift`). It uses dynamic thresholds for aspects like energy.
    *   *Planner:* Selects adaptation tactics (e.g., model switching, model retraining, data/model versioning) based on the detected uncertainty and historical performance. Uses strategies like ε-greedy for exploration.
    *   *Executor:* Enacts the chosen adaptation strategy (e.g., triggers retraining, updates the inference model).
    *   *Knowledge Base:* Central repository storing:
        *   Data Repository (sensor data, predictions)
        *   Sustainability Goals Repository
        *   Tactics Repository
        *   Current Model Repository (available models like LR, SVM, LSTM)
        *   Versioned Model Repository (stores retrained models and their training data distributions)

![HarmonE Architecture](/static/image/HarmonE_Architecture.png)

## Experimental Validation & Key Results
We validated HarmonE using a Digital Twin of an Intelligent Transportation System (ITS) for traffic flow prediction, using the PeMS dataset. We compared HarmonE against several baselines, including individual models (LR, SVM, LSTM) with and without periodic retraining (PRT), and a simpler switching mechanism.

**Key Findings (RQ answers):**
*   **RQ1 (Environmental Sustainability):** HarmonE maintains energy consumption below predefined threshold constraints. It consumed **54.5% less energy** than the high-performing LSTM+PRT baseline.
*   **RQ2 (Accuracy-Energy Trade-off):** HarmonE achieves **95.0% of LSTM+PRT's R² accuracy** while consuming only **45.5% of its energy** and improving inference time by **53.4%**.
*   **RQ3 (Adaptation Efficiency):** The HarmonE decision-making process (MAPE-K loop) is highly efficient, consuming only **1.3660% of the total system energy**, demonstrating negligible runtime overhead.

**Performance Comparison of Different Approaches**

| Approach      | Energy (mJ) | R² Score | Inference Time (ms) | # Adaptations |
|---------------|-------------|--------|---------------------|---------------|
| LR            | 12.1157     | 0.7597 | 0.8306              | -             |
| LR + PRT      | 12.6987     | 0.7624 | 0.8306              | 4             |
| SVM           | 14.7649     | 0.7897 | 1.1134              | -             |
| SVM + PRT     | 16.3304     | 0.8167 | 1.1134              | 4             |
| LSTM          | 32.9147     | 0.9005 | 3.8475              | -             |
| LSTM + PRT    | 45.3517     | 0.9085 | 3.8475              | 4             |
| Switch        | 24.3218     | 0.8445 | 2.2656              | 13            |
| Switch + PRT  | 25.3176     | 0.8435 | 2.2656              | 17            |
| **HarmonE**   | **20.6203** | **0.8628** | **1.8887**          | **12**        |

## Significance
HarmonE presents a novel, self-adaptive architectural approach for building sustainable MLOps pipelines. By explicitly defining and managing sustainability goals across multiple dimensions, HarmonE dynamically balances predictive performance with resource consumption (e.g., energy). It outperforms traditional periodic retraining methods by triggering adaptations only when necessary, significantly reducing resource expenditure and operational overhead while maintaining high accuracy. This work contributes to the growing field of Green AI and sustainable software engineering by providing a practical framework for operationalizing sustainability in complex MLS deployments.

## Citation

```
@misc{bhatt2025harmoneselfadaptiveapproacharchitecting,
      title={HarmonE: A Self-Adaptive Approach to Architecting Sustainable MLOps}, 
      author={Hiya Bhatt and Shaunak Biswas and Srinivasan Rakhunathan and Karthik Vaidhyanathan},
      year={2025},
      eprint={2505.13693},
      archivePrefix={arXiv},
      primaryClass={cs.SE},
      url={https://arxiv.org/abs/2505.13693}, 
}
```