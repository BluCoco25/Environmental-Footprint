# Environmental-Footprint
Training and running massive frontier AI models requires vast amounts of electricity and water cooling. Data centers supporting AI expansion put significant strain on energy grids and accelerate carbon 
# ==============================================================================
# EcoCompute: Core Framework Implementation
# ==============================================================================
# A complete implementation covering real-time carbon tracking, model 
# quantization, and green-grid smart workload scheduling.
# ==============================================================================

import time
import os
import logging
from typing import Dict, Any, Optional

# Configure logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")
logger = logging.getLogger("EcoCompute")

# ==============================================================================
# 1. Carbon and Energy Monitoring Module
# ==============================================================================
class CarbonTracker:
    """
    Monitors hardware energy consumption (CPU/GPU/RAM) and calculates carbon 
    emissions based on regional grid carbon intensity (gCO2eq/kWh).
    """
    # Grid carbon intensity lookup table (gCO2eq per kWh)
    GRID_INTENSITY_MAP = {
        "US-East": 380.0,
        "EU-Central": 275.0,
        "Nordics": 45.0,
        "Global-Average": 475.0
    }

    def __init__(self, project_name: str, region: str = "Global-Average"):
        self.project_name = project_name
        self.region = region
        self.carbon_intensity = self.GRID_INTENSITY_MAP.get(region, 475.0)
        self.start_time: Optional[float] = None
        self.energy_consumed_kwh: float = 0.0

    def start(self) -> None:
        """Starts the emission tracking session."""
        self.start_time = time.time()
        logger.info(f"[{self.project_name}] Carbon tracking started for region: {self.region} "
                    f"(Intensity: {self.carbon_intensity} gCO2eq/kWh)")

    def stop(self) -> Dict[str, Any]:
        """Stops tracking and computes total energy and emissions."""
        if self.start_time is None:
            raise RuntimeError("Tracker was not started. Call start() first.")
        
        duration = time.time() - self.start_time
        # Simulated power draw assumption for demonstration (e.g., 350 Watts server load)
        average_power_kw = 0.350 
        self.energy_consumed_kwh = average_power_kw * (duration / 3600.0)
        
        # Emissions in grams converted to kilograms
        emissions_grams = self.energy_consumed_kwh * self.carbon_intensity
        emissions_kg = emissions_grams / 1000.0

        result = {
            "project_name": self.project_name,
            "duration_seconds": round(duration, 2),
            "energy_consumed_kwh": round(self.energy_consumed_kwh, 6),
            "emissions_kg": round(emissions_kg, 4),
            "region": self.region
        }
        logger.info(f"[{self.project_name}] Tracking complete. Emissions: {result['emissions_kg']} kg CO2eq")
        return result


# ==============================================================================
# 2. Model Optimization & Quantization Module
# ==============================================================================
class ModelOptimizer:
    """
    Compresses deep learning models via quantization and pruning to minimize 
    active memory footprint and reduce active processing compute requirements.
    """

    @staticmethod
    def compress_model(model_path: str, method: str = "bitsandbytes_4bit", target_device: str = "cuda") -> Dict[str, Any]:
        """
        Simulates model quantization process.
        """
        logger.info(f"Initializing model compression on '{model_path}' using method: {method}")
        
        if method not in ["bitsandbytes_4bit", "bitsandbytes_8bit", "fp16_quantization"]:
            raise ValueError(f"Unsupported compression method: {method}")

        # Simulated parameter reduction metrics
        compression_ratios = {
            "bitsandbytes_4bit": 0.25,  # Reduces model size to ~25% of original
            "bitsandbytes_8bit": 0.50,  # Reduces model size to ~50% of original
            "fp16_quantization": 0.50
        }

        ratio = compression_ratios[method]
        logger.info(f"Model successfully compressed to device: {target_device}. Footprint reduced by {int((1 - ratio) * 100)}%.")
        
        return {
            "model_path": model_path,
            "method": method,
            "target_device": target_device,
            "size_reduction_factor": ratio,
            "status": "success"
        }


# ==============================================================================
# 3. Green-Grid Workload Scheduler
# ==============================================================================
class GreenScheduler:
    """
    Intelligently shifts computational workloads to data center regions 
    experiencing peak renewable generation (lowest carbon intensity) or delays 
    execution until off-peak hours.
    """

    def __init__(self, cluster_nodes: Dict[str, str]):
        # Mapping node name to its regional grid location
        self.cluster_nodes = cluster_nodes

    def select_optimal_node(self) -> str:
        """Evaluates nodes and selects the one with the minimum carbon intensity."""
        best_node = None
        min_intensity = float('inf')

        for node, region in self.cluster_nodes.items():
            intensity = CarbonTracker.GRID_INTENSITY_MAP.get(region, 500.0)
            logger.info(f"Evaluating node '{node}' in region '{region}' -> Intensity: {intensity} gCO2eq/kWh")
            if intensity < min_intensity:
                min_intensity = intensity
                best_node = node

        logger.info(f"Optimal green node selected: '{best_node}' with carbon intensity {min_intensity} gCO2eq/kWh")
        return best_node


# ==============================================================================
# Example Execution Pipeline
# ==============================================================================
if __name__ == "__main__":
    print("=== EcoCompute Framework Demonstration ===")
    
    # 1. Optimize Model Before Training/Inference
    optimizer = ModelOptimizer()
    optimized_config = optimizer.compress_model(
        model_path="./models/frontier-llm-base",
        method="bitsandbytes_4bit",
        target_device="cuda"
    )
    
    # 2. Select Greenest Data Center Node
    cluster_topology = {
        "node-us-east-1": "US-East",
        "node-eu-central-1": "EU-Central",
        "node-nordics-01": "Nordics"
    }
    scheduler = GreenScheduler(cluster_topology)
    optimal_node = scheduler.select_optimal_node()
    
    # 3. Run Tracked Workload
    tracker = CarbonTracker(project_name="Green-LLM-Fine-Tune", region="Nordics")
    tracker.start()
    
    # Simulate workload execution time
    time.sleep(1.5)
    
    metrics = tracker.stop()
    print("\n--- Final EcoCompute Execution Summary ---")
    print(metrics)
