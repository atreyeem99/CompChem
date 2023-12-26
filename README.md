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
         or
```
!wB97X-D3 DEF2-SVPD OPT NUMFREQ
%CPCM SMD TRUE
      SMDSOLVENT "1-OCTANOL"
END
* XYZ 0 1 geom.xyz
```
using wB97X-D3 and basis set of DEF2-SVPD
 By the gibbs free energies from the output files of both, partition coefficient is calculated.

 ## Reaction
   1) Tightly optimizing the reactant

```      
      B3LYP cc-pVDZ OPT FREQ TightSCF

* XYZfile 0 1 prop_ox_opt.xyz

%pal nproc 16
end

%geom
 calc_hess true
 recalc_hess 5
 maxiter 50
 convergence tight
end
```
2) Tightly optimizing the product with proper conformer.
```
!B3LYP cc-pVDZ OPT FREQ TightSCF
*XYZfile 0 1 acetone_ss.xyz
%pal nproc 16
end
%geom
 calc_hess true
 recalc_hess 5
 maxiter 50
 convergence tight
end
```
3) Finding the NEB-TS that is the transition state energy
```
!wB97X-D3 def2-TZVP NEB-TS FREQ
%NEB NEB_END_XYZFILE "tight_opt_acetone_ss.xyz" END
* XYZfile 0 1 tight_opt_prop_ox.xyz
%pal nproc 16
end
```
4) DLPNO calculation of reactant using the optimized geometry
```
!DLPNO-CCSD(T) def2-TZVP AUTOAUX RIJCOSX
* XYZFILE 0 1 tight_opt_prop_ox.xyz
%pal nproc 16
end
```
5) DLPNO calculation of TS
```
!DLPNO-CCSD(T) def2-TZVP AUTOAUX RIJCOSX
* XYZFILE 0 1 tight_opt_ts_acetone.xyz
%pal nproc 16
end
```
6) DLPNO calculations of product
```
!DLPNO-CCSD(T) def2-TZVP AUTOAUX RIJCOSX
* XYZFILE 0 1 tight_opt_acetone_ss.xyz
%pal nproc 16
end
```
## 4.8 UV-VIS spectroscopy
1) optimization of E isomer
```
!B3LYP DEF2-TZVP D4 OPT FREQ TightSCF CPCM(HEXANE)

* XYZfile 0 1 E_final.xyz

%pal nproc 16
end 

%geom
 calc_hess true
 recalc_hess 5
 maxiter 50
 convergence tight
end
```

2) Excited state calculation
   
```
   !B3LYP DEF2-TZVP TightSCF CPCM(HEXANE)
%TDDFT
   NROOTS   10
   MAXITER 500
END
* XYZfile 0 1 E_azo_opt.xyz

%pal nproc 16
end
```
-From here we match the wavelength with experimental data.
  Using wB97X-D3
1)optimisation

```
 !wB97X-D3 DEF2-TZVP OPT FREQ TightSCF CPCM(HEXANE)

* XYZfile 0 1 E_final.xyz

%pal nproc 16
end

%geom
 calc_hess true
 recalc_hess 5
 maxiter 50
 convergence tight
end
```
- Using TDA false for accurate results
```
!wB97X-D3 DEF2-TZVP TightSCF CPCM(HEXANE)
%TDDFT
   TDA  FALSE
   NROOTS   10
   MAXITER 500
END
* XYZfile 0 1 E_wB97XD3_opt.xyz

%pal nproc 16
end
```





[Orca tutorials page](https://www.orcasoftware.de/tutorials_orca/)
