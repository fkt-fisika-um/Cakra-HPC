# 🚀 Tutorial: SLURM Pada HPC **Cakra**

Panduan ini ditunjukkan untuk membantu pengguna dalam mengoperasikan program melalui **SLURM** pada **HPC Cakra**.

## Program Python
Membuat file ``run.sh`` di directory file Python yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=qlstm    # ubah sesuai yang diinginkan
#SBATCH --qos=normal        # jangan diubah
#SBATCH --time=24:00:00     # jangan diubah
#SBATCH --nodes=1           # jangan diubah
#SBATCH --ntasks=1          # jangan diubah
#SBATCH --partition=part20A # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1   # jangan diubah
##SBATCH --gres=gpu:p40:1   # Jika program butuh GPU hapus 1 tanda pagar 
#SBATCH --mem=8G            # jangan diubah
#SBATCH --output=%x.out     # jangan diubah
#SBATCH --error=%x.err      # jangan diubah

# Aktifkan environment Python dengan memasukkan path virtual environment
source /media/mahasiswa/dirac/.namaenvironment/bin/activate # Contoh

echo "Start = $(date)"

# Jalankan program dengan nama file python milik anda
python3 train.py

echo "End = $(date)"
deactivate
```

## Program C
Membuat file ``run.sh`` di directory file C yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=c        # ubah sesuai yang diinginkan
#SBATCH --qos=normal        # jangan diubah
#SBATCH --time=24:00:00     # jangan diubah
#SBATCH --nodes=1           # jangan diubah
#SBATCH --ntasks=1          # jangan diubah
#SBATCH --partition=part20A # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1   # jangan diubah
##SBATCH --gres=gpu:p40:1   # Jika program butuh GPU hapus 1 tanda pagar
#SBATCH --mem=8G            # jangan diubah
#SBATCH --mem=8G            # jangan diubah
#SBATCH --output=%x.out     # jangan diubah
#SBATCH --error=%x.err      # jangan diubah

# load module compiler
module load gcc/11.4.0

echo "Start = $(date)"

# compile
gcc matmul.c -o matmul_c    # mamtul.c adalah nama program dengan output matmul_c

# binary file
./matmul_c

echo "End = $(date)"
```

## Program Fortran
Membuat file ``run.sh`` di directory file Fortran yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=fortran_test    # ubah sesuai yang diinginkan
#SBATCH --qos=normal               # jangan diubah
#SBATCH --time=24:00:00            # jangan diubah
#SBATCH --nodes=1                  # jangan diubah
#SBATCH --ntasks=1                 # jangan diubah
#SBATCH --partition=part20A        # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1          # jangan diubah
##SBATCH --gres=gpu:p40:1          # Jika program butuh GPU hapus 1 tanda pagar
#SBATCH --mem=8G                   # jangan diubah
#SBATCH --output=%x.out            # jangan diubah
#SBATCH --error=%x.err             # jangan diubah

# load module compiler
module load gfortran/11.4.0

echo "Start = $(date)"

# compile
gfortran matmul.f90 -o matmul      # mamtul.f90 adalah nama program dengan output matmul

# binary file
./matmul

echo "end = $(date)"
```

## Program Quantum Espresso
Struktur folder untuk Quantum Espresso paad directory masing-masing
```
project_qe/
 ├─ pseudo/              # semua pseudopotensial .UPF
 │   ├─ Si.pbe-n-kjpaw_psl.1.0.0.UPF
 │   ├─ Mo.pbe-spn-kjpaw_psl.1.0.0.UPF
 │   └─ Se.pbe-n-kjpaw_psl.1.0.0.UPF
 │
 ├─ scf/
 │   ├─ scf.in           # input SCF
 │   ├─ run.sh           # job script Slurm
 │   └─ scf.out          # output SCF
 │
 ├─ relax/
 │   ├─ relax.in         # input Relax
 │   ├─ run.sh           # job script Slurm
 │   └─ relax.out        # output Relax
 │
 └─ tmp/                 # folder kerja QE bersama
```


Membuat file ``run.sh`` di directory file Relax yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=MoSe2-relax     # ubah sesuai yang diinginkan 
#SBATCH --qos=normal               # jangan diubah
#SBATCH --time=24:00:00            # jangan diubah
#SBATCH --nodes=1                  # jangan diubah
#SBATCH --ntasks=10                # jangan diubah  
#SBATCH --partition=part20A        # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1          # jangan diubah
#SBATCH --mem=8G                   # jangan diubah
#SBATCH --output=%x.out            # jangan diubah
#SBATCH --error=%x.err             # jangan diubah

# Load module Quantum ESPRESSO
module load quantum-espresso

# Jangan diubah
export OMP_NUM_THREADS=1
export MKL_NUM_THREADS=1
export OPENBLAS_NUM_THREADS=1
export OMP_PLACES=cores
export OMP_PROC_BIND=close

echo "Running on $(hostname)"
echo "Start: $(date)"

mkdir -p ../tmp

# Jalankan Quantum ESPRESSO relax
mpirun -np $SLURM_NTASKS pw.x -in relax.in > relax.out

echo "End: $(date)"
```

**Script run.sh untuk SCF**
```bash
#!/bin/bash
#SBATCH --job-name=Si-scf           # ubah sesuai kebutuhan
#SBATCH --qos=normal                # jangan diubah
#SBATCH --time=24:00:00             # jangan diubah
#SBATCH --nodes=1                   # jangan diubah
#SBATCH --ntasks=10                 # jangan diubah
#SBATCH --partition=part20A         # bisa part20A atau part20B
#SBATCH --cpus-per-task=1           # jangan diubah
#SBATCH --mem=8G                    # jangan diubah
#SBATCH --output=%x.out             # jangan diubah
#SBATCH --error=%x.err              # jangan diubah

module load quantum-espresso

export OMP_NUM_THREADS=1
export MKL_NUM_THREADS=1
export OPENBLAS_NUM_THREADS=1
export OMP_PLACES=cores
export OMP_PROC_BIND=close

echo "Running on $(hostname)"
echo "Start: $(date)"

mkdir -p ./tmp

# Jalankan QE SCF
mpirun -np $SLURM_NTASKS pw.x -in scf.in > scf.out

echo "End: $(date)"

```