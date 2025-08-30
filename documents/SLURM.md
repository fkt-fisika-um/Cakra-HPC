# 🚀 Tutorial: SLURM Pada **Cakra**

Dokumen ini membantu cara pemakaian **SLURM** untuk menjalankan sebuah program pada **HPC Cakra**.

## Program Python
Membuat file ``run.sh`` di directory file python yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=qlstm    # ubah sesuai yang diinginkan
#SBATCH --qos=normal        # jangan diubah
#SBATCH --time=24:00:00     # jangan diubah
#SBATCH --nodes=1           # jangan diubah
#SBATCH --ntasks=1          # jangan diubah
#SBATCH --partition=part20A # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1   # jangan diubah
##SBATCH --gres=gpu:p40:1   # Jika program butuh GPU hapus 1 tanda pagar dan bisa memilih gpu:p40:1 atau gpu:m40:1
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
Membuat file ``run.sh`` di directory file python yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=c        # ubah sesuai yang diinginkan
#SBATCH --qos=normal        # jangan diubah
#SBATCH --time=24:00:00     # jangan diubah
#SBATCH --nodes=1           # jangan diubah
#SBATCH --ntasks=1          # jangan diubah
#SBATCH --partition=part20A # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1   # jangan diubah
##SBATCH --gres=gpu:p40:1   # Jika program butuh GPU hapus 1 tanda pagar dan bisa memilih gpu:p40:1 atau gpu:m40:1
#SBATCH --mem=8G            # jangan diubah
#SBATCH --output=%x.out     # jangan diubah
#SBATCH --error=%x.err      # jangan diubah

# load module compiler
module load gcc/11.4.0

# compile
gcc matmul.c -o matmul_c    # mamtul.c adalah nama program dengan output matmul_c

# binary file
./matmul_c
```

## Program Fortran
Membuat file ``run.sh`` di directory file python yang akan dijalankan dengan isi:
```bash
#!/bin/bash
#SBATCH --job-name=fortran_test    # ubah sesuai yang diinginkan
#SBATCH --qos=normal               # jangan diubah
#SBATCH --time=24:00:00            # jangan diubah
#SBATCH --nodes=1                  # jangan diubah
#SBATCH --ntasks=1                 # jangan diubah
#SBATCH --partition=part20A        # bisa memakai part20A atau part20B
#SBATCH --cpus-per-task=1          # jangan diubah
##SBATCH --gres=gpu:p40:1          # Jika program butuh GPU hapus 1 tanda pagar dan bisa memilih gpu:p40:1 atau gpu:m40:1
#SBATCH --mem=8G                   # jangan diubah
#SBATCH --output=%x.out            # jangan diubah
#SBATCH --error=%x.err             # jangan diubah

# load module compiler
module load gfortran/11.4.0

# compile
gfortran matmul.f90 -o matmul      # mamtul.f90 adalah nama program dengan output matmul

# jalankan program
./matmul
```
