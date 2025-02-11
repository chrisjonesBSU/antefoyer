antefoyer
==============================
This repository provides support for the General Amber Force Field (GAFF) in foyer. Foyer is a part of the Molecular Simulation Design Framework (MoSDeF). A few relevant links are provided below:

- [MoSDeF](https://mosdef.org)
- [Foyer GitHub](https://github.com/mosdef-hub/foyer)
- [Foyer paper](https://doi.org/10.1016/j.commatsci.2019.05.026) 
- [GAFF paper](https://doi.org/10.1002/jcc.20035)
- [Antechamber paper](https://doi.org/10.1016/j.jmgm.2005.12.005)

There are two primary components to this repository: 

- Foyer-compatible XML file for GAFF
- Antechamber wrapper to integrate antechamber atomtyping and AM1-BCC charge assignment directly into MoSDeF workflows.

## Installation
The following instructions will create a new conda environment (antefoyer) and install the required packages and dependencies:

    git clone https://github.com/chrisjonesBSU/antefoyer
    cd antefoyer
    mamba env create -f environment.yml
    mamba activate antefoyer

To install a development environment where changes made to the source code are reflected in your environment:

    git clone https://github.com/chrisjonesBSU/antefoyer
    cd antefoyer
    mamba env create -f environment-dev.yml
    mamba activate antefoyer-dev
    pip install -e .
    
## Optional dependencies
To complete some of the examples below you will need mbuild and openbabel installed. These can be added via conda:

	mamba install -c conda-forge mbuild openbabel

## Usage
There are two primary workflows for atomtyping with `antefoyer`. The first uses the traditional `foyer` approach with SMARTS strings defined in `antefoyer/xml/gaff.xml`. 

    # Assuming we also have mbuild installed for this example
    import foyer
    import mbuild as mb
    
    # Build/import a molecule
    molecule = mb.load('CCC',smiles=True)
    
    # Load GAFF
    GAFF = foyer.forcefields.load_GAFF()
    
    # Use foyer to parameterize
    parameterized_molecule = GAFF.apply(molecule.to_parmed())
    
    # Save to whatever file format you desire
    
Note that `antefoyer` was _never_ imported. If `antefoyer` is properly installed, the GAFF forcefield will become available under `foyer.forcefields.load_GAFF()` via `entrypoints`. 

The second workflow uses the antechamber wrapper. This requires `antechamber` to be installed an accesible in your `PATH`. `Antechamber` can be installed from conda: `conda install -c conda-forge ambertools`.

    # Assuming we also have mbuild installed for this example
    import foyer
    import mbuild as mb
    
    # For this we need to import antefoyer
    import antefoyer
    
    # Build/import a molecule
    molecule = mb.load('CCC', smiles=True)
    
    # Load GAFF
    gaff = foyer.forcefields.load_GAFF()
    
    # Identify GAFF atomtypes with antechamber
    typed_molecule = antefoyer.ante_atomtyping(molecule, 'gaff')
    
    # Use foyer to parameterize
    parameterized_molecule = gaff.parametrize_system(typed_molecule)
    
    # We can also apply AM1-BCC charges from antechamber
    molecule_with_charges = antefoyer.ante_charges(parameterized_molecule,'bcc')
    
    # Save to whatever file format you desire

## Details of SMARTS strings development and testing
Further details of the SMARTS strings development and testing are provided in a separate repository, [GAFF-Foyer](https://github.com/rsdefever/GAFF-foyer).

## Acknowledgements
 
Project based on the
[Computational Molecular Science Python Cookiecutter](https://github.com/molssi/cookiecutter-cms) version 1.1.


This material is based upon work supported by the National Science Foundation under Grant #1835874
