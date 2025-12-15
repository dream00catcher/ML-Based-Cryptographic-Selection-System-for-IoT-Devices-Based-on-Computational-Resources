README.txt
==========

Title
-----
IoT Encryption/Decryption Simulation – Host/IoT Timing, RAM/ROM, CPU, and Battery Modeling (Research Code)

Manuscript Context
------------------
This archive contains the simulation code that generates per-algorithm performance traces for subsequent analysis.  
The script executes encryption and decryption repeatedly to overcome timer resolution, measures host CPU/RAM behavior,  
scales timings to IoT device profiles, estimates battery usage, and exports structured CSVs per data size.

Contents
--------
Code files
- Data_Simulation/Data_Generation_Final.py  (main simulation script)  
  • Calls Data_Simulation.consolidate (unified crypto API) to run AES-GCM, ChaCha20-Poly1305, Ascon-128/128a,  
    Speck-128, Simon-128, Present-128, TWINE-128, and their ECC+hybrid variants.  
  • Calibrates per-run iterations/repeats to target stable timing windows.  
  • Monitors peak RSS (RAM) and CPU frequency, computes host usage, and scales to IoT specs.  
  • Simulates battery usage from scaled time and active current draw.  
  • Exports CSV results grouped by DATA_SIZE.

Supporting utility scripts
- Data_Simulation/generate_env_info_auto.py  
  Automatically scans the project and generates a concise runtime environment report.  
  Produces `Data_Simulation/appendix_environment_concise.txt` for reproducibility documentation.

Upstream crypto utilities (required in project)
- Data_Simulation/consolidate.py               (unified encrypt/decrypt wrapper)  
- Data_Simulation/encry_decry_sym.py           (symmetric algorithm implementations)  
- Data_Simulation/encry_decry_hybrid.py        (hybrid ECC + symmetric implementations)  
- Cipher_GitHub/Simon_Speck_Ciphers/Python/simonspeckciphers/{simon.py,speck.py}  
- Cipher_GitHub/present/present_python/present.py  

Generated output (by this script)
- Data_Simulation/Simulation Results/IoT_Encryption_Decryption_Simulation_Results_{DATA_SIZE}.csv  

Environment descriptors
- Data_Simulation/requirements.txt                   (pinned dependencies for simulation stage)  
- Data_Simulation/appendix_environment_concise.txt   (auto-generated environment summary; see below)  

To regenerate the environment summary:
  python Data_Simulation/generate_env_info_auto.py --source Data_Simulation --out Data_Simulation/appendix_environment_concise.txt  

System Requirements
-------------------
Python version: 3.13.2  
Supported OS tested: Windows 11 (AMD64)  
OpenSSL (ssl module): runtime >= 3.0  (for cryptography backend)  
CPU: any x86_64/AMD64 processor  

Python Packages (pinning in requirements.txt)
---------------------------------------------
# Simulation & metrics  
psutil==6.0.0  
pandas==2.3.0  

# Crypto stack (same as ESM crypto environment)  
cryptography==45.0.4  
ascon==0.0.9  
xtwine==1.0.2  

Notes:
- cryptography provides AES-GCM, ChaCha20-Poly1305, ECC (SECP256R1), HKDF-SHA256.  
- ascon provides Ascon-128/128a AEAD.  
- xtwine provides TWINE-128 (block cipher).  
- Simon, Speck, and Present are local Python modules under Cipher_GitHub.

Installation
------------
Create and activate a virtual environment (recommended):

  python -m venv .venv
  .venv\Scripts\activate   (Windows)
  source .venv/bin/activate   (Linux/macOS)

Install packages:
  pip install -r Data_Simulation/requirements.txt

Keep the folder structure so local cipher modules and consolidate.py are importable:
  project_root/
    Data_Simulation/
      Data_Generation_Final.py               # <- main simulation script
      consolidate.py
      encry_decry_sym.py
      encry_decry_hybrid.py
      generate_env_info_auto.py
      appendix_environment_concise.txt
      Simulation Results/
    Cipher_GitHub/
      Simon_Speck_Ciphers/Python/simonspeckciphers/{simon.py,speck.py}
      present/present_python/present.py

File Path Configuration
-----------------------
This script uses a hardcoded absolute Windows output path for CSV export:

  C:/Users/willi/ML-Based IoT Device Encryption Algorithm Selection System/
    Data_Simulation/Simulation Results/

If you run this on a different system, update the string near the end of the script:
  output_filename = "C:/Users/willi/.../Data_Simulation/Simulation Results/IoT_Encryption_Decryption_Simulation_Results_{data_size_kb}.csv"

Examples:
- Change only the root to your location, keep the subfolders:
  D:/Research/IoT_Encryption_Project/Data_Simulation/Simulation Results/
- Windows paths may use forward slashes (/) or doubled backslashes (\\).
- On Linux/macOS, use:
  /home/username/IoT_Encryption_Project/Data_Simulation/Simulation Results/

Inputs (built-in sets)
----------------------
Data sizes (KB):  
  [0.0078125, 2, 3, 900, 1024, 5120]

IoT device parameter grids:  
  Battery initial (mAh):        [210, 1200, 5300, 999999999]  
  RAM total (MB):               [0.00390625, 0.0625, 0.125, 64, 1024, 4096]  
  ROM total (MB):               [0.046875, 0.375, 0.5, 8.008, 4096, 8192]  
  CPU frequency (MHz):          [8, 80, 100, 400, 580, 2000]  
  CPU cores:                    [1, 2, 8]  
  Active current draw (mA):     [3.36, 125, 135, 400, 1800, 5000]  
  Threat level:                 1..5  
  Sensitivity level:            1..5  

Algorithms simulated (examples):  
  "AES-GCM", "ECC+AES-GCM", "ChaCha20-Poly1305", "ECC+ChaCha20-Poly1305",  
  "Ascon128", "ECC+Ascon128", "Ascon128a", "ECC+Ascon128a",  
  "Speck128", "ECC+Speck128", "Simon128", "ECC+Simon128",  
  "Present128", "ECC+Present128", "Twine128", "ECC+Twine128"

What the Script Does
--------------------
Host-side measurement  
- Calibrates per-algorithm iterations to exceed a target timing window (≈1 s) and repeats via normal-approx CI.  
- Measures per-repeat wall time and CPU time; monitors peak RSS (RAM) and samples CPU frequency.  
- Computes host CPU usage % ~ (CPU time / wall time * 100) and flags “parallelism” if >100%.  

IoT scaling & battery model  
- Scales measured time by IoT CPU frequency and core count:  
    parallel: t_scaled = t_host * host_MHz / (iot_MHz * cores)  
    non-parallel: t_scaled = t_host * host_MHz / iot_MHz  
- Battery usage (mAh) = active_current_mA * time_seconds / 3600.  
- RAM/ROM feasibility checks vs device budgets; battery feasibility from remaining mAh.  

ROM & RAM estimators  
- ROM (MB) ≈ sizes of key, IV/nonce, ciphertext, tag, and relevant ECC keys (bytes → MB).  
- RAM (MB) ≈ (peak_rss - rss_before) / 1,048,576.  

Verification  
- Decryption correctness check (plaintext round-trip) recorded as a flag.  

Outputs (CSV columns)
---------------------
General  
  "No.", "Data Size", "Algorithm Used",  
  "Threat Level", "Sensitivity Level"

Host (Enc/Dec)  
  "{Enc|Dec} Host Duration (s)", "{Enc|Dec} Host CPU Time (s)",  
  "{Enc|Dec} Host Memory (Before) (KB)", "{Enc|Dec} Host Memory (Peak) (KB)",  
  "{Enc|Dec} Host RAM Usage (MB)", "{Enc|Dec} Host ROM Usage (MB)",  
  "Host CPU Frequency {Encrypt|Decrypt} (MHz)", "Host CPU Usage {Encrypt|Decrypt} (%)",  
  "Parallelism {Encrypt|Decrypt}", "Host CPU Core {Encrypt|Decrypt}"

IoT (Enc/Dec)  
  "{Enc|Dec} IoT Time (s)",  
  "{Enc|Dec} IoT RAM Usage (MB)", "{Enc|Dec} IoT RAM Feasibility",  
  "{Enc|Dec} IoT ROM Usage (MB)", "{Enc|Dec} IoT ROM Feasibility",  
  "{Enc|Dec} IoT Battery Usage (mAh)", "{Enc|Dec} IoT Remaining Battery (mAh)",  
  "{Enc|Dec} IoT Battery Feasibility",  
  "Decryption Verified" (for Dec)

Device parameters  
  "IoT CPU Frequency (MHz)", "IoT CPU Core",  
  "IoT RAM (MB)", "IoT ROM (MB)",  
  "IoT Battery Capacity (mAh)", "Active Current Draw (mA)"

Quick Start
-----------
From project_root:
  python Data_Simulation/Data_Generation_Final.py

The script will generate one CSV per data size in:
  Data_Simulation/Simulation Results/
(or the absolute path configured in the code).

Reproducibility Checklist
-------------------------
- Python: 3.13.2  
- Data_Simulation/requirements.txt installed in a fresh virtual environment  
- Folder structure preserved (Cipher_GitHub and Data_Simulation in project_root)  
- OpenSSL runtime >= 3.0 available to Python’s ssl/cryptography  
- Data_Simulation/appendix_environment_concise.txt included for machine/runtime provenance  
  (auto-generated by Data_Simulation/generate_env_info_auto.py)

Security and Scope
------------------
- Research/teaching reference only; not production-hardened.  
- Timing, memory, and frequency sampling are host-dependent and noisy by nature.  
- Peak RSS sampling uses very short intervals; may increase host load.  
- ECC curves fixed to SECP256R1; HKDF uses SHA-256 with info="session key" (as in consolidate.py demo).

Known Limitations
-----------------
- Absolute Windows paths are hardcoded for outputs; edit the string for other machines/OS.  
- RAM delta from RSS may be negative if GC runs asynchronously; script triggers gc.collect() before sampling.  
- CPU usage approximation (>100% implies inferred core-level parallelism) is heuristic.  
- ROM usage is a coarse estimator from buffer lengths (bytes).  
- Runtime can be long due to nested grids over devices, currents, and threat/sensitivity levels.

License and Third-Party Notices
-------------------------------
- This ESM code is provided for peer review and academic replication.  
- cryptography, ascon, xtwine under their respective licenses.  
- Simon/Speck/Present ports included under upstream licenses (see source headers).

Contact
-------
Corresponding author: NAME, AFFILIATION, EMAIL  
Version/date of this ESM: YYYY-MM-DD
