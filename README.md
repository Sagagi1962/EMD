# EMD
Energetic Materials Discovery 
# Explosophore-Based Molecular Library Generator

A Python/RDKit workflow for generating virtual libraries of energetic molecules by decorating a user-supplied scaffold with explosophore fragments. The generator uses stochastic site selection, fragment attachment, duplicate filtering, and a Monte Carlo complexity acceptance rule to produce a diverse set of candidate structures.

## Overview

This project builds energetic-molecule libraries from a core scaffold represented as a SMILES string. The script identifies substitutable hydrogen-bearing sites on the scaffold and randomly attaches explosophore fragments such as nitro, azide, nitramine, nitrate ester, difluoroamine, halogens, and related energetic functionalities.

The workflow is intended for:

- virtual library generation for energetic materials discovery
- scaffold decoration with explosophoric groups
- creation of candidate sets for downstream screening
- rapid exploration of substituent combinations before descriptor, ML, or quantum-chemical evaluation

## Features

- Accepts a user-defined scaffold SMILES
- Automatically detects substitutable heavy-atom sites with attached hydrogens
- Attaches explosophore fragments using fragment-based molecular editing
- Uses stochastic substitution across multiple scaffold sites
- Applies complexity-based acceptance to avoid over-substituted molecules
- Removes duplicate molecules using canonical SMILES
- Exports the final library to CSV
- Tracks substitution count and attached fragment identities

## Built-In Explosophore Fragment Library

The current implementation includes a built-in fragment pool with entries such as:

- `NO2`
- `ONO2`
- `NNO2`
- `C(NO2)3`
- `C(NO2)2F`
- `C(NO2)2NF2`
- `C(NO2)2N3`
- `NF2`
- `N3`
- `OClO3`
- `NClO3`
- `N(O)=NNO2`
- `F`
- `Cl`
- `CN`
- `CH3`
- `CF3`
- `NH2`
- `OH`
- `NHNO2`

These fragments span multiple energetic substituent classes, including nitro, nitrate ester, nitramine, azide, fluorinated, perchlorate-type, and simple functional substituents.

## How It Works

### 1. Scaffold Input
The user provides a scaffold SMILES string. The script loads and sanitizes the scaffold with RDKit.

### 2. Site Detection
The generator identifies substitutable sites as scaffold atoms that carry at least one hydrogen.

### 3. Fragment Selection
For each candidate molecule, the script randomly selects:
- the number of substitution sites
- the specific scaffold sites
- the explosophore fragments to attach

### 4. Fragment Attachment
Each fragment is attached by:
- converting scaffold hydrogens to explicit hydrogens
- locating a hydrogen on the target site
- combining the scaffold and fragment molecules
- creating a new bond
- removing the replaced hydrogen
- sanitizing the product molecule

### 5. Complexity Scoring
Each successful non-hydrogen substitution contributes +1 to a simple complexity score.

### 6. Complexity Acceptance
Generated molecules are filtered by a Monte Carlo acceptance rule:
- all molecules with complexity `<= x0` are accepted
- molecules with complexity `> complexity_max` are rejected
- intermediate cases are accepted probabilistically using an exponential penalty controlled by `beta`

### 7. Duplicate Removal
Canonical SMILES are used to detect and skip duplicate molecules.

### 8. CSV Export
Accepted molecules are written to a CSV file for downstream analysis.

## Requirements

Install the required Python packages:

```bash
pip install numpy tqdm rdkit
