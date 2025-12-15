README.txt
==========

Title
-----
IoT Device Encryption/Decryption Algorithms – Symmetric, Hybrid (ECC+Symmetric), and Consolidation Routines (Research Code)

Manuscript Context
------------------
This archive accompanies the manuscript’s Electronic Supplementary Material (ESM).  
It contains Python reference implementations used to generate example ciphertexts and to compare multiple lightweight and AEAD ciphers, both standalone and in hybrid (ECC + symmetric) settings.

Contents
--------
Code files
- Data_Simulation/encry_decry_sym.py  
  Symmetric primitives: AES-GCM, ChaCha20-Poly1305, Ascon-128/128a, Speck-128/128, Simon-128/128, Present-128, TWINE-128.
- Data_Simulation/encry_decry_hybrid.py  
  Hybrid primitives (ECC key exchange + HKDF) combined with AES-GCM, ChaCha20-Poly1305, Ascon-128/128a, Speck-128, Simon-128, Present-128, TWINE-128.
- Data_Simulation/consolidate.py  
  Uniform interface to call encryption/decryption across all algorithms; includes ECC key generation and key exchange wiring for hybrid modes.
- Data_Simulation/generate_env_info_auto.py  
  Utility script to automatically scan the project, detect installed packages, and generate a concise environment record (`appendix_environment_concise.txt`).

External cipher submodules (bundled or expected in path)
- Cipher_GitHub/Simon_Speck_Ciphers/Python/simonspeckciphers/{simon.py,speck.py}
- Cipher_GitHub/present/present_python/present.py

Environment descriptors
- Data_Simulation/requirements.txt  (exact Python package versions used to run the code)
- Data_Simulation/appendix_environment_concise.txt  (auto-generated environment summary; see note below)

To regenerate the environment file:
  python Data_Simulation/generate_env_info_auto.py --source Data_Simulation --out Data_Simulation/appendix_environment_concise.txt

This command re-scans the project source and recreates the appendix report used for reproducibility documentation.

System Requirements
-------------------
Python version: 3.13.2  
Supported OS tested: Windows 11 (AMD64)  
OpenSSL (ssl module): runtime >= 3.0  
CPU: any x86_64/AMD64 capable processor

Python Packages (pinning in requirements.txt)
---------------------------------------------
cryptography==45.0.4  
ascon==0.0.9  
xtwine==1.0.2  

Notes:
- cryptography provides AES-GCM, ChaCha20-Poly1305, ECC (SECP256R1), HKDF-SHA256.  
- ascon provides Ascon-128/128a AEAD.  
- xtwine provides TWINE-128 (block cipher).  
- Simon, Speck, and Present are provided as local Python modules under Cipher_GitHub (no pip install needed if paths are kept).

Installation
------------
Create and activate a virtual environment (recommended):
  python -m venv .venv
  .venv\Scripts\activate   (Windows)
  source .venv/bin/activate   (Linux/macOS)

Install pinned packages:
  pip install -r Data_Simulation/requirements.txt

Ensure local cipher modules are importable by keeping the provided folder structure:
  project_root/
    Data_Simulation/
      encry_decry_sym.py
      encry_decry_hybrid.py
      consolidate.py
      generate_env_info_auto.py
      appendix_environment_concise.txt
      requirements.txt
    Cipher_GitHub/
      Simon_Speck_Ciphers/Python/simonspeckciphers/{simon.py,speck.py}
      present/present_python/present.py

If you relocate folders, add project_root to PYTHONPATH or run from project_root.

Quick Start (Interactive Tests)
-------------------------------
From project_root:

  python -c "from Data_Simulation.encry_decry_sym import test7; test7()"

Prompts for plaintext and runs TWINE-128 end-to-end (encryption + decryption).

Other quick tests (each prompts for plaintext):
  AES-GCM:           python -c "from Data_Simulation.encry_decry_sym import test;  test()"
  ChaCha20-Poly1305: python -c "from Data_Simulation.encry_decry_sym import test1; test1()"
  Ascon-128:         python -c "from Data_Simulation.encry_decry_sym import test2; test2()"
  Ascon-128a:        python -c "from Data_Simulation.encry_decry_sym import test3; test3()"
  Speck-128:         python -c "from Data_Simulation.encry_decry_sym import test4; test4()"
  Simon-128:         python -c "from Data_Simulation.encry_decry_sym import test5; test5()"
  Present-128:       python -c "from Data_Simulation.encry_decry_sym import test6; test6()"

Programmatic Usage (Consolidated API)
-------------------------------------
Example (symmetric):
  from Data_Simulation.consolidate import encryption, decryption
  algo = "AES-GCM"
  pt = "Hello IoT"
  ct, key, iv, tag, aad, r_priv, r_pub, s_priv, s_pub = encryption(algo, pt)
  dec = decryption(algo, ct, key, iv, tag, aad)

Example (hybrid ECC + AEAD):
  algo = "ECC+ChaCha20-Poly1305"
  ct, key, nonce, tag, aad, r_priv, r_pub, s_priv, s_pub = encryption(algo, "Hello IoT")
  dec = decryption(algo, ct, key, nonce, tag, aad)

Supported Algorithm Identifiers
-------------------------------
Symmetric (algo string):  
  "AES-GCM", "ChaCha20-Poly1305", "Ascon128", "Ascon128a",  
  "Speck128", "Simon128", "Present128", "Twine128"

Hybrid (ECC + symmetric) (algo string):  
  "ECC+AES-GCM", "ECC+ChaCha20-Poly1305", "ECC+Ascon128", "ECC+Ascon128a",  
  "ECC+Speck128", "ECC+Simon128", "ECC+Present128", "ECC+Twine128"

Inputs and Outputs
------------------
Encryption returns a tuple standardized as:  
  (ciphertext: bytes, key_or_derived_key: bytes, iv_or_nonce: bytes, tag: bytes, aad: bytes,  
   [receiver_private_key_bytes, receiver_public_key_bytes, sender_private_key_bytes, sender_public_key_bytes]  # only for hybrid modes)

Decryption returns:  
  decrypted_plaintext: str  
  or a diagnostic string such as "[AUTHENTICATION FAILED]" or "[INVALID PADDING]".

Security and Scope
------------------
- Research/teaching reference only. Not audited for production or safety-critical use.  
- Nonces/IVs are randomly generated per call; AEAD associated data in examples uses a dummy device id + timestamp for demonstration.  
- For Present, Simon, Speck, and Twine, implementations are pedagogical Python ports and not constant-time.  
- Keys are ephemeral in examples; secure key storage/management is out of scope.

Reproducibility Checklist
-------------------------
- Python: 3.13.2  
- requirements.txt installed in a fresh virtual environment  
- Folder structure preserved (Cipher_GitHub and Data_Simulation in project_root)  
- Data_Simulation/appendix_environment_concise.txt included for runtime provenance  
  (auto-generated by Data_Simulation/generate_env_info_auto.py)

Known Limitations
-----------------
- TWINE-128 requires an ASCII key; code converts derived bytes to printable ASCII when used in hybrid mode.  
- Present/Speck/Simon operate on block multiples; PKCS-style padding is used; errors return "[INVALID PADDING]".  
- ECC curves fixed to SECP256R1; HKDF uses SHA-256 with info="session key" and no salt (for clarity in demonstrations).

License and Third-Party Notices
-------------------------------
- This ESM code is provided for peer review and academic replication.  
- cryptography (Apache 2.0 / BSD-like components), ascon (per package license), xtwine (per package license).  
- Simon/Speck/Present Python ports are included under their respective upstream licenses; see source headers.

Contact
-------
Corresponding author: Ng Qi Ming, School of Technology, Asia Pacific University of Technology and Innovation, tp081131@mail.apu.edu.my  
Version/date of this ESM: YYYY-MM-DD
