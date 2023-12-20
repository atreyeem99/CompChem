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
  

[Orca tutorials page](https://www.orcasoftware.de/tutorials_orca/)
