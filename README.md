# Praktikum RTOS - SEMESTER 5 (3 Teknik Komputer A)

- **Diva Sakananda (3222600013)**
- **Billy Lukito Danuharja (3222600027)**

# Task 5 Exercise 6 - Demonstrate a simple way to eliminate resource contention - suspending the scheduler.

## Deskripsi Projek
Proyek ini bertujuan untuk mendemonstrasikan cara mengeliminasi **resource contention** dalam sistem multitasking berbasis RTOS (Real-Time Operating System) pada mikrokontroler STM32. Teknik yang digunakan adalah **critical section**, yang memastikan hanya satu task dapat mengakses resource bersama pada satu waktu dengan menonaktifkan interrupt.

Proyek ini menggunakan STM32 untuk:
- **FlashGreenLedTask**: Mengendalikan LED Hijau dengan waktu periodik tertentu sambil mengakses resource bersama.
- **FlashRedLedTask**: Mengendalikan LED Merah dengan prioritas lebih tinggi dan waktu periodik yang lebih singkat, juga mengakses resource bersama.

Fungsi utama:
- **taskENTER_CRITICAL()**: Menonaktifkan interrupt untuk melindungi critical section.
  - Fungsi ini memastikan tidak ada interrupt atau task lain yang dapat mengganggu eksekusi bagian kode kritis, sehingga resource bersama dapat diakses dengan aman.
  - Secara teknis, RTOS menonaktifkan semua interrupt kecuali Non-Maskable Interrupt (NMI) untuk menjaga stabilitas sistem.
- **taskEXIT_CRITICAL()**: Mengaktifkan kembali interrupt setelah selesai mengakses resource bersama.
  - Fungsi ini memastikan multitasking dan interrupt dapat berjalan kembali setelah critical section selesai, memungkinkan task lain atau interrupt untuk dijadwalkan ulang.
- **SimulateReadWriteOperation()**: Mensimulasikan akses resource bersama dengan delay berbasis loop untuk menghindari masalah pada fungsi delay berbasis interrupt.
  - Fungsi ini menggunakan loop sederhana dengan instruksi no-operation (`__asm("nop")`) untuk membuat jeda waktu yang aman di dalam critical section tanpa bergantung pada interrupt atau timer.


1. **FlashGreenLedTask (Prioritas: Normal)**
   - Task ini menyalakan LED Hijau selama 0.5 detik setiap periodik waktu yang ditentukan.
   - Menggunakan critical section untuk mengakses resource bersama.
   - Karena memiliki prioritas lebih rendah, task ini harus menunggu task dengan prioritas lebih tinggi (FlashRedLedTask) selesai sebelum dieksekusi.

2. **FlashRedLedTask (Prioritas: Above Normal)**
   - Task ini menyalakan LED Merah selama 0.1 detik dengan waktu periodik yang lebih singkat.
   - Memiliki prioritas lebih tinggi, sehingga lebih sering mendapatkan akses ke resource bersama dibandingkan task LED Hijau.
   - Menggunakan critical section untuk melindungi resource bersama saat diakses.

## Pola Kedipan LED
- **LED Merah**: Berkedip sebanyak 5 kali sebelum **LED Hijau** berkedip sekali.
- Kedipan LED lebih lama dari delay yang ditentukan karena penambahan waktu dari fungsi `SimulateReadWriteOperation()`.
  - Delay tambahan ini berasal dari loop dummy yang berjalan selama akses critical section.
- Siklus berulang: LED Merah kembali berkedip setelah LED Hijau selesai, dengan waktu periodik masing-masing task tergantung pada prioritas dan delay tambahan.
  - **FlashRedLedTask** memiliki waktu eksekusi lebih sering karena prioritasnya lebih tinggi, sehingga FlashGreenLedTask hanya dieksekusi setelah FlashRedLedTask selesai.

## Hubungan Antar Task
- **FlashGreenLedTask**: Bergantung pada akses resource bersama. Dieksekusi setelah FlashRedLedTask selesai karena prioritas lebih rendah.
- **FlashRedLedTask**: Mengakses resource bersama lebih sering karena prioritas lebih tinggi.
- Kedua task berbagi resource bersama melalui critical section untuk menghindari **resource contention**.
  - Penggunaan critical section memastikan bahwa resource bersama tidak diakses secara bersamaan, menghindari kesalahan seperti kondisi balapan (*race condition*).

## Foto Hardware
![foto hardware exercise 6](https://github.com/user-attachments/assets/0f3f461e-80f2-455d-bc05-d8ad68fc95de)


## Short Video Hardware
https://github.com/user-attachments/assets/6042b776-4f29-4994-93fb-6dab5e20d747



