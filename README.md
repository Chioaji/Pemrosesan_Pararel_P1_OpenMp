# Praktikum 1 – Pemrograman Shared Memory dengan OpenMP (A05)

Nama: Nama Lengkap Kamu · NIM: 123456789

## Struktur folder
```
p1-openmp/
├── src/
│   ├── matmul.c                    # perkalian matriks (serial + paralel)
│   ├── prima.c                     # hitung bilangan prima, reduction + schedule(runtime)
│   └── prima_b4_tanpa_reduction.c  # percobaan tanpa reduction (pertanyaan analisis 4)
├── hasil/
│   ├── matmul.txt                  # data mentah perkalian matriks
│   └── prima.txt                   # data mentah bilangan prima
└── README.md
```

## Persyaratan
- GCC dengan dukungan OpenMP (diuji: GCC 15.2.0, Ubuntu di WSL)
- Minimal 4 core (diuji: AMD Ryzen 5 5625U, 6 core fisik / 12 logis)

## Kompilasi
```bash
gcc -O2 -fopenmp src/matmul.c -o matmul -lm
gcc -O2 -fopenmp src/prima.c  -o prima
gcc -O2 -fopenmp src/prima_b4_tanpa_reduction.c -o prima_b4
OMP_NUM_THREADS=4 ./prima_b4
```

## Menjalankan

**Perkalian matriks** (argumen: ukuran n, default 1024):
```bash
OMP_NUM_THREADS=4 ./matmul 1024
```
Keluaran: waktu serial, waktu paralel, dan `benar=ya/TIDAK`.

**Bilangan prima** (kebijakan scheduling lewat `OMP_SCHEDULE`):
```bash
OMP_SCHEDULE="dynamic,1000" OMP_NUM_THREADS=4 ./prima
```
Keluaran: jumlah prima (harus 664579) dan waktu.

## Mengulang seluruh eksperimen
```bash
mkdir -p hasil
for t in 1 2 4 8; do
  for r in 1 2 3; do
    OMP_NUM_THREADS=$t ./matmul 1024 >> hasil/matmul.txt
  done
done

for s in "static" "dynamic,1000" "guided"; do
  for t in 1 2 4 8; do
    for r in 1 2 3; do
      echo -n "$s;$t;$r;" >> hasil/prima.txt
      OMP_SCHEDULE="$s" OMP_NUM_THREADS=$t ./prima >> hasil/prima.txt
    done
  done
done
```
Tabel pada laporan memakai median dari tiga pengulangan.

## Catatan
- Pengukuran di laptop dengan WSL, sehingga ada variasi antar-run (±10%).
- `prima_b4_tanpa_reduction.c` sengaja menghasilkan nilai salah, untuk menunjukkan race condition.
