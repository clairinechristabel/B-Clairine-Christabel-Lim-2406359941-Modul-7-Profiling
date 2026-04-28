# Modul 7 - Profiling

**Nama:** Clairine Christabel Lim  
**NPM:** 2406359941  
**Kelas:** B

---

## Hasil JMeter - Sebelum Optimasi

### Test Plan 1 (`/all-student`)
![view_tree_test_1](assets/image-2.png)
![view_table_test_1](assets/image-3.png)
![summary_report_test_1](assets/image-4.png)
![graph_result_test_1](assets/image-5.png)

### Test Plan 2 (`/all-student-name`)
![view_tree_test_2](assets/image-6.png)
![view_table_test_2](assets/image-7.png)
![summary_report_test_2](assets/image-8.png)
![graph_result_test_2](assets/image-9.png)

### Test Plan 3 (`/highest-gpa`)
![view_tree_test_3](assets/image-10.png)
![view_table_test_3](assets/image-11.png)
![summary_report_test_3](assets/image-12.png)
![graph_result_test_3](assets/image-13.png)

### JTL Reports - Sebelum Optimasi

#### Test Plan 1
![test_plan_1_make_jtl](assets/image-16.png)
![test_plan_1_jtl](assets/image-17.png)

#### Test Plan 2
![test_plan_2_make_jtl](assets/image-1.png)
![test_plan_2_jtl](assets/image-14.png)

#### Test Plan 3
![test_plan_3_make_jtl](assets/image.png)
![test_plan_3_jtl](assets/image-15.png)

---

## Hasil JMeter - Setelah Optimasi

### Test Plan 1 (`/all-student`) - Optimized
![view_tree_test_1_optimize](assets/image-18.png)
![view_table_test_1_optimize](assets/image-19.png)
![summary_report_test_1_optimize](assets/image-20.png)
![graph_result_test_1_optimize](assets/image-21.png)

### Test Plan 2 (`/all-student-name`) - Optimized
![view_tree_test_2_optimize](assets/image-22.png)
![view_table_test_2_optimize](assets/image-23.png)
![summary_report_test_2_optimize](assets/image-24.png)
![graph_result_test_2_optimize](assets/image-25.png)

### Test Plan 3 (`/highest-gpa`) - Optimized
![view_tree_test_3_optimize](assets/image-26.png)
![view_table_test_3_optimize](assets/image-27.png)
![summary_report_test_3_optimize](assets/image-28.png)
![graph_result_test_3_optimize](assets/image-29.png)

### JTL Reports - Setelah Optimasi

#### Test Plan 1
![test_1_make_jtl_optimize](assets/image-30.png)
![test_1_optimize_jtl](assets/image-31.png)

#### Test Plan 2
![test_2_make_jtl_optimize](assets/image-32.png)
![test_2_optimize_jtl](assets/image-33.png)

#### Test Plan 3
![test_3_make_jtl_optimize](assets/image-34.png)
![test_3_optimize_jtl](assets/image-35.png)

---

## Profiling Results - Perbandingan Sebelum & Sesudah Optimasi

Berikut adalah hasil profiling menggunakan IntelliJ Profiler setelah dilakukan optimasi pada ketiga endpoint, sebagai perbandingan terhadap performa sebelum optimasi.

### `/all-student` (getAllStudentsWithCourses)
![optimization_all_student](assets/optimization_all_student.jpeg)

### `/all-student-name` (joinStudentNames)
![optimization_student_names](assets/optimization_student_names.jpeg)

### `/highest-gpa` (findStudentWithHighestGpa)
![optimization_highest_gpa](assets/optimization_highest_gpa.jpeg)

---


## Kesimpulan (Conclusion)

Setelah melakukan profiling dan optimasi pada ketiga endpoint, terdapat peningkatan performa yang signifikan (lebih dari 20%) pada masing-masing endpoint:

1. **`/all-student` (getAllStudentsWithCourses):** Sebelum optimasi, method ini menggunakan `findAll()` yang menyebabkan **N+1 query problem** — setiap `StudentCourse` memicu query tambahan untuk mengambil data `Student` dan `Course`. Setelah dioptimasi dengan menggunakan **JOIN FETCH** pada JPQL query, semua data diambil dalam satu query saja, sehingga waktu pemrosesan berkurang drastis.

2. **`/highest-gpa` (findStudentWithHighestGpa):** Sebelum optimasi, method ini memuat **seluruh data mahasiswa** dari database ke dalam memori, lalu melakukan iterasi manual di Java untuk mencari GPA tertinggi. Setelah dioptimasi, pencarian GPA tertinggi didelegasikan langsung ke **database query** (`findTopByOrderByGpaDesc()`), sehingga hanya 1 row yang dikembalikan dari database.

3. **`/all-student-name` (joinStudentNames):** Sebelum optimasi, method ini menggunakan **String concatenation (`+=`)** di dalam loop, yang menyebabkan kompleksitas O(n²) karena setiap operasi `+=` membuat objek String baru. Setelah dioptimasi menggunakan **StringBuilder**, kompleksitasnya menjadi O(n) dan penggunaan memori jauh lebih efisien.

Berdasarkan hasil pengujian JMeter sebelum dan sesudah optimasi, terlihat penurunan response time yang konsisten pada ketiga endpoint. Hal ini membuktikan bahwa optimasi pada level kode (refactoring query database dan penggunaan struktur data yang tepat) dapat memberikan dampak yang signifikan terhadap performa aplikasi secara keseluruhan.

---

## Refleksi (Reflection)

### 1. Apa perbedaan pendekatan performance testing dengan JMeter dan profiling dengan IntelliJ Profiler dalam konteks optimasi performa aplikasi?

JMeter dan IntelliJ Profiler memiliki pendekatan yang berbeda namun saling melengkapi. **JMeter** berfokus pada pengujian performa dari **perspektif pengguna (black-box)** — mengukur response time, throughput, dan error rate dari endpoint HTTP. JMeter mensimulasikan beban pengguna nyata dan memberikan gambaran tentang bagaimana aplikasi berperforma di bawah tekanan. Sementara itu, **IntelliJ Profiler** berfokus pada analisis performa dari **perspektif internal kode (white-box)** — mengukur CPU time, memory allocation, dan call tree dari setiap method. Profiler membantu kita mengidentifikasi *exact method* mana yang menjadi bottleneck. Singkatnya, JMeter menjawab "seberapa lambat?", sedangkan IntelliJ Profiler menjawab "kenapa lambat?".

### 2. Bagaimana proses profiling membantu dalam mengidentifikasi dan memahami titik lemah (weak points) dalam aplikasi?

Proses profiling membantu dengan cara menampilkan **CPU time** dan **execution time** dari setiap method yang dipanggil selama aplikasi berjalan. Melalui **Call Tree** dan **Method List** pada IntelliJ Profiler, kita bisa melihat method mana yang memakan waktu paling lama dan berapa kali method tersebut dipanggil. Misalnya, dalam kasus ini, profiler menunjukkan bahwa `getAllStudentsWithCourses()` memiliki CPU time yang sangat tinggi karena N+1 query problem. Tanpa profiler, kita mungkin hanya tahu bahwa endpoint lambat, tapi tidak tahu bagian kode mana yang menjadi penyebabnya.

### 3. Apakah IntelliJ Profiler efektif dalam membantu menganalisis dan mengidentifikasi bottleneck dalam kode aplikasi?

Ya, IntelliJ Profiler sangat efektif. Fitur **Flame Graph** memvisualisasikan call stack secara hierarkis sehingga kita bisa dengan cepat melihat method mana yang paling banyak mengonsumsi CPU. Fitur **Method List** memungkinkan kita mengurutkan berdasarkan CPU time untuk langsung menemukan method yang paling lambat. Selain itu, fitur **Timeline** menunjukkan aktivitas thread secara real-time. Semua fitur ini terintegrasi langsung di IDE, sehingga kita bisa langsung navigasi ke source code yang bermasalah dan melakukan perbaikan.

### 4. Apa tantangan utama yang dihadapi saat melakukan performance testing dan profiling, dan bagaimana cara mengatasinya?

Tantangan utama yang saya hadapi antara lain:
- **JIT Compiler warm-up:** Pada run pertama, JVM belum optimal karena JIT compiler belum melakukan kompilasi. Solusinya, saya melakukan beberapa kali run terlebih dahulu sebelum mengambil data pengukuran yang valid.
- **Variabilitas hasil:** Hasil pengujian bisa bervariasi tergantung beban sistem saat itu. Solusinya, saya menjalankan test beberapa kali dan membandingkan hasilnya.
- **Membedakan bottleneck kode vs database:** Kadang sulit membedakan apakah lambatnya disebabkan oleh kode Java atau query database. Solusinya, saya menggunakan `spring.jpa.show-sql=true` untuk melihat query yang dihasilkan dan menganalisis apakah ada N+1 problem.

### 5. Apa manfaat utama yang diperoleh dari penggunaan IntelliJ Profiler untuk profiling kode aplikasi?

Manfaat utama dari IntelliJ Profiler meliputi:
- **Identifikasi bottleneck secara presisi:** Kita bisa melihat secara persis method mana yang menjadi penyebab lambatnya aplikasi.
- **Integrasi dengan IDE:** Tidak perlu tools eksternal, cukup klik kanan dan profile langsung dari IntelliJ IDEA.
- **Visualisasi yang informatif:** Flame Graph, Call Tree, dan Timeline memberikan perspektif berbeda untuk memahami performa aplikasi.
- **Perbandingan sebelum dan sesudah:** Fitur comparison view memungkinkan kita membandingkan dua sesi profiling untuk memvalidasi bahwa optimasi berhasil.

### 6. Bagaimana menangani situasi di mana hasil profiling dengan IntelliJ Profiler tidak sepenuhnya konsisten dengan temuan dari performance testing menggunakan JMeter?

Ketidakkonsistenan antara hasil profiler dan JMeter bisa terjadi karena keduanya mengukur aspek yang berbeda. JMeter mengukur **end-to-end response time** yang mencakup network latency, serialization, dan overhead HTTP, sedangkan profiler mengukur **CPU time** murni dari eksekusi kode. Jika terjadi ketidakkonsistenan, saya akan:
- Menganalisis apakah bottleneck berada di level kode (terdeteksi profiler) atau di level infrastruktur/network (terdeteksi JMeter).
- Memastikan kondisi testing konsisten (jumlah data, beban sistem, dll).
- Menggunakan kedua tools secara komplementer — JMeter untuk mengukur dampak keseluruhan, dan profiler untuk mendalami penyebab spesifik.

### 7. Strategi apa yang diterapkan dalam mengoptimasi kode aplikasi setelah menganalisis hasil dari performance testing dan profiling? Bagaimana memastikan perubahan yang dilakukan tidak memengaruhi fungsionalitas aplikasi?

Strategi optimasi yang saya terapkan:
1. **Optimasi query database:** Mengganti `findAll()` dengan `JOIN FETCH` untuk menghindari N+1 query problem.
2. **Delegasi ke database:** Mengganti iterasi manual di Java dengan query database yang lebih efisien (seperti `findTopByOrderByGpaDesc()`).
3. **Penggunaan struktur data yang tepat:** Mengganti `String +=` dengan `StringBuilder` untuk menghindari pembuatan objek berulang.

Untuk memastikan perubahan tidak memengaruhi fungsionalitas:
- Saya memastikan **output/response** dari setiap endpoint tetap sama sebelum dan sesudah optimasi.
- Saya melakukan pengujian manual pada setiap endpoint setelah perubahan.
- Perubahan dilakukan secara **incremental** (satu method per commit), sehingga jika ada masalah bisa di-revert dengan mudah.