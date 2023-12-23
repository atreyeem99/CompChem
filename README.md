# Notes on ...

# 1. Smiles
```
    `filename.smi` is for saving smiles input
```
# 2. Obabel
```
     obabel -osvg filename.smi > filename.svg
        - for converting smiles file to svg file
```
```
      obabel -oxyz filename.smi > filename.xyz --gen3d
       -  for generating x,y,z coordinates using obabel
```

# 3. Avogadro
```
wine avogadro
```
- used to open a file in avogadro

# 4. Orca

## 4.1 Input file

Some details `TIGHTSCF`

```
!B3LYP DEF2-TZVP D4 OPT FREQ TightSCF

* XYZfile 0 1 geom.xyz

%pal nproc 16
end

%geom
 calc_hess true
 recalc_hess 5
 maxiter 50
 convergence tight
end

```
- `TightSCF` is to tightly optimize the geometry

## 4.2 To run a parallel calculation
```
%pal nproc 16
end
```
- `nproc 16` is to mention how many processors to be used
## 4.3 To generate orbitals and SCF calculations
```
!HF DEF2-SVP LARGEPRINT
%SCF
   MAXITER 500
END
* xyz 0 1
O   0.0000   0.0000   0.0626
H  -0.7920   0.0000  -0.4973
H   0.7920   0.0000  -0.4973
*
%pal nproc 4
end
```
- Here `LARGEPRINT` is used to generate the orbitals
- SCF ititrations goes upto 500
- The x,y,z coordinates of water are written inside the input file

## Single point Energy calculation
` Lowest energy solution for the Schrodinger equation`
1) Hartree Fock
   ```
   !HF DEF2-SVP
   ```
2) Unrestricted Hartree fock
   `UHF` is used for ny multiplicity other than 1
3)Acceleration of the SCF
  ```
!HF DEF2-SVP DEF2/J RIJDX
```
If it has no auxilliary basis, use `AUTOAUX` flag for automatic genre
```
!HF 6-31G(d,p) AUTOAUX RIJDX
```
4)MP2 Purturbation theory
```
!RI-MP2 cc-pVTZ cc pVTZ/C
```
5) Coupled Cluster (cc)
```
!DLPNO-CCSD(T) cc-pVTZ cc-pVTZ/c
```
- Check the T1 diagnostic valuein the output file.
- If it is greater than 0.02, the result is not good and HF reference is poor
   

## 4.4 DLPNO 
```
!DLPNO-CCSD(T) aug-cc-pVTZ AUTOAUX RIJCOSX
* XYZFILE 0 1 geom_opt.xyz
%pal nproc 16
end
```
  - For calculationg the final single point energy
  - coordinates of the optimized geometry is used
## 4.5 Thermodynamics at different temperatures
```
%FREQ TEMP 77, 298, 330, 450 END
```
## 4.6 Solvation models
  1) CPCM
     ```
     !BP86 DEF2-SVP CPCM(WATER)
     * XYZFILE 0 1 aspirin.xyz
     ```
     - The FINAL SINGLE POINT ENERGY now already includes all computed solvation terms.

  2) SMD
  ```
     %CPCM SMD TRUE
      SMDSOLVENT "SOLVENT"
    END
         OR
         
!BP86 DEF2-SVP
%CPCM SMD TRUE
      SMDSOLVENT "WATER"
END
* XYZFILE 0 1 aspirin.xyz
```
- it uses the full solute electron density to compute the cavity-dispersion contribution instead of the area only.
- less flexible

## 4.7 Calculation of partition coeffecients

 1) water as solvent
  ```
!B3LYP DEF2-SVP OPT NUMFREQ D4
%CPCM SMD TRUE
      SMDSOLVENT "WATER"
END
* XYZ 0 1  geom.xyz
%pal nproc 4
end
```
From this the gibbs free energy of the compound with water as a solvent is calculated.
2) Octanol as solvent
```
!B3LYP DEF2-SVP OPT NUMFREQ D4
%CPCM SMD TRUE
      SMDSOLVENT "1-OCTANOL"
END
* XYZFILE 0 1 geom.xyz
%pal nproc 4
end
```
From this the gibbs free energy of the compound with octanol as a solvent is calculated.




[Orca tutorials page](https://www.orcasoftware.de/tutorials_orca/)
