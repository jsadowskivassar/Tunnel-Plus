# Tunnel+: Pitch Deception & Trajectory Modeling

**Full Write-Up:** [Read the full HTML report and methodology here.](https://jsadowskivassar.github.io/Tunnel-Plus/PitchTunnelFinal.html)

**Tunnel+** is a data-driven sports analytics framework that quantifies pitch tunneling. Unlike metrics that evaluate a pitcher's entire arsenal at once, Tunnel+ evaluates the full 3D flight of *individual pitch sequences* to measure how effectively a pitcher mirrors trajectories.


https://github.com/user-attachments/assets/445e7ec7-0927-41bf-a229-7d036ac470bc

Top Ranked Pitch Sequence


## Methodology:

The core metric was developed using MLB Statcast data from 2024 and 2025 and a multi-step modeling pipeline:

### 1. Spatial Tracking at 4 Checkpoints
Using kinematics and tracking data (velocity, acceleration, release coordinates), the model calculates pitch positions at four discrete flight milestones:
* Release Point
* Early Flight (40 feet from the plate)
* The Tunnel Point (~28.3 feet from the plate / 175ms before contact)
* Home Plate

### 2. Dimensionality Reduction (PCA)
Because pitch locations across these checkpoints have high covariance, I applied **Principal Component Analysis (PCA)** to the spatial differentials between paired pitches. The first two components captured 88.05% of the variance and perfectly matched real-world physics:
* **PC1 (Overall Separation):** Heavily weighted toward distance at the tunnel point.
* **PC2 (Late Break):** Heavily weighted toward distance at home plate compared to earlier trajectory.

### 3. Metric Creation
After experimenting with linear regression (which incorrectly favored pitches that ended as uncompetitive balls), **Tunnel+** was finalized by equally weighting and aggregating the z-scores of PC1 and PC2. This rewards traditional tunneling: pitches that look identical out of the hand but break aggressively apart at the plate. The final tunnel score was then scaled so 100 is average and 10 points represents a one standard deviation difference.

## Does Tunneling Matter? (Key Findings)

Applying the Tunnel+ metric to MLB data proved that deception directly correlates with run prevention. For an individual pitch, a 1-standard-deviation increase in Tunnel Score (a 10-point increase in Tunnel+) is associated with:
* **+10.8%** increase in swing-and-miss probability
* **+3.7%** increase in called strike probability
* **-0.005** decrease in xwOBAcon
* **~1.5 to 1.6 WAR** value over a full starting pitcher's season.

### Player Archetypes
The model successfully identifies different pitching strategies:
* **Elite Power Tunnelers:** Zack Wheeler, Jacob deGrom, Sandy Alcantara. 
* **Low-Velocity Deception:** Tyler Rogers (Submarine) and Bailey Ober, who use elite tunneling to generate immense value despite bottom-percentile fastball velocity.
* **The "Anti-Tunneler":** Max Fried, who grades out poorly in tunneling (Tunnel+ 85) due to reliance on distinct pitch shapes and eye-level disruption rather than trajectory mirroring.
* **Full Tunnel+ Leaderboard available in full blog post**

## Applying Tunnel+ to Rapsodo Data

As a college pitcher, I applied the Tunnel+ pipeline to my own bullpen data (118 pitch pairs). 

**Hardware Limitations:** The analysis revealed that portable Rapsodo tracking lacks the millimeter-level spatial calibration of MLB's Hawk-Eye system. Minor setup misalignments cause massive variance in horizontal release point measurements. I combated this by applying a linear shift to normalize mean release side to 2.6 feet, but concluded that portable radar/optical tracking requires significant error correction for precise trajectory modeling.

**Arsenal Insights:** Despite data noise, the model confirmed that my Curveball/Changeup and Fastball/Slider sequences tunnel the best, validating my primary in-game sequencing strategy against right-handed hitters.

## Video Processing Engine

To visually validate the model, this repository includes a custom video overlay generator:
* **Scrapes** MLB video clips directly via URLs.
* **Normalizes** Variable Frame Rate (VFR) web video to a strict 60 FPS Constant Frame Rate using FFmpeg.
* **Synchronizes & Blends** two pitch videos at the exact release frame using MoviePy, rendering 50% opacity overlays to visually demonstrate the tunnels.

## Repository Structure
All project files are housed in the main branch for direct GitHub Pages rendering:

```text
├── PitchTunnelFinal.html                  # The final rendered blog post (Live Site)
├── PitchTunnelFinal.qmd                   # Quarto source code for the data analysis and blog text
├── Savant_Pitch_Overlayer_(Final).ipynb   # Python code for MLB video scraping, FFmpeg, and MoviePy overlays
├── statcast_bat_tracking_2024_2025.csv    # Statcast Data for Every Pitch of 2024 and 2025 MLB seasons
├── savant_data.csv                        # Stacsta Data for first week of 2026 MLB Seasons
├── rapsodo_data.csv                       # College bullpen tracking dataset 
├── wheeler_overlay.mp4                    # (and other .mp4 files) Pitch tunneling overalys made through Savant_Pitch_Overlayer_(Final).ipynb
└── README.md                              # Project overview
