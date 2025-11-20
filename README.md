# TurtleBot4-Autonomous-Pick-and-Place-Navigation

# Deskripsi Proyek
Proyek ini merupakan implementasi sistem robot pengantar barang otonom menggunakan TurtleBot4 dan ROS. Sistem ini mengintegrasikan seluruh tahapan dari Practical Work 1–3, yaitu:
- Setup & Configuration: konfigurasi awal TurtleBot4, sensor, jaringan, dan paket ROS.
- Map Generation: proses pembuatan peta lingkungan menggunakan SLAM.
- Autonomous Navigation: navigasi otonom menggunakan paket Nav2.

# Studi Kasus
Robot bertugas mengantarkan barang dari Ruang 203 ke Lobby BRAIL (Lantai 2, Gedung Utama Polibatam). Sistem pick-and-place disimulasikan menggunakan buzzer/speaker dengan aturan: 1x beep ketika robot mencapai lokasi pick (Ruang 203) dan 2x beep ketika robot mencapai lokasi place (Lobby BRAIL).
Robot melakukan lokalisasi, pemetaan, perencanaan jalur, eksekusi navigasi otonom. 

# Cara Menjalankan Proyek
1. Membuat Folder Workspace
Pada langkah ini, membuat workspace ROS2 dengan struktur standar. Semua package akan ditempatkan di dalam folder src, sehingga ROS2 dapat mengenali dan membangunnya menggunakan colcon build.
```mkdir kelompok4a_uts/src```
```cd kelompok4a_uts/src```

3. Membuat Package dan Dependencies
```source /opt/ros/jazzy/setup.bash```
```ros2 pkg create --build-type ament_cmake --node-name navkel4a navkel4a --dependencies rclcpp nav2_msgs rclcpp_action tf2```

- Perintah source digunakan agar terminal mengenali semua perintah dan library ROS2.
- Perintah ros2 pkg create membuat sebuah package baru bernama navkel4a.
- Tipe build yang digunakan adalah ament_cmake karena umum dipakai untuk ROS2 C++.
- Dependencies seperti rclcpp, nav2_msgs, rclcpp_action, dan tf2 diperlukan untuk menulis node C++, mengirim goal ke Nav2, dan mengakses transformasi robot.
File node utama akan bernama navkel4a.cpp dan otomatis dibuat saat package dibuat.

3. Build Package
```colcon build```
Perintah ini melakukan kompilasi seluruh package dalam workspace, lalu menghasilkan folder install/ yang berisi executable (node), konfigurasi, dan library. Setelah proses build selesai, environment workspace dapat diaktifkan menggunakan:
```source install/setup.bash```

# Menghubungkan dari PC ke TurtleBot4
### Koneksi Menggunakan Kabel LAN
Atur IP pada komputer secara manual: (IP Address: 192.168.185.6; Netmask: 255.255.255.0; Gateway / Robot IP: 192.168.185.3)
Setelah itu buka terminal dan lakukan SSH:
```ssh ubuntu@192.168.185.3```
Pada tahap ini komputer akan terhubung langsung ke TurtleBot4 melalui jaringan kabel. Robot tersebut menggunakan sistem operasi Ubuntu untuk menjalankan ROS2.

# Mapping (SLAM)
### Menjalankan SLAM
```ros2 launch turtlebot4_navigation slam.launch.py```
Perintah ini menjalankan proses SLAM (Simultaneous Localization and Mapping), mengaktifkan sensor LIDAR, serta memungkinkan robot bergerak untuk membangun peta lingkungan.

### Menjalankan RViz
Karena menggunakan Ubuntu 24, jalankan versi Jazzy dengan mengetik perintah berikut:
```ros2 launch turtlebot4_viz view_navigation.launch.py```
RViz digunakan untuk memvisualisasikan peta, posisi robot, data LIDAR, serta frame transformasi yang digunakan oleh Nav2.

# Menjalankan Nav2 (Navigasi)
1. Menjalankan Navigation Node
```ros2 launch nav_kel4a run_navigation.launch.py```
Launch file ini berisi konfigurasi navigasi yang dibuat dalam package nav_kel4a, mencakup parameter navigasi seperti planner, controller, dan behavior tree.

2. Menjalankan Localization
```ros2 launch turtlebot4_navigation localization.launch.py map:=kelompok4A_uts/src/nav_kel4a/maps/kel4a.yaml```
Tahap ini menjalankan AMCL (Adaptive Monte Carlo Localization) untuk menentukan posisi robot pada peta yang telah dibuat saat mapping. ROS2 akan memuat file map .yaml tersebut dan mengaktifkan Nav2.

3. Menampilkan RViz
```ros2 launch turtlebot4_viz view_navigation.launch.py```
RViz kemudian digunakan kembali untuk melihat posisi robot pada peta, termasuk orientasi, transformasi frame, serta jalur navigasi yang direncanakan Nav2.

# Menjalankan Node Program
### Menjalankan Node C++ yang Mengirim Goal
```ros2 run nav_kel4a nav_kel4a```
Ini merupakan langkah terakhir. Node C++ yang telah dibuat akan:
- Mengirim robot menuju Point A
- Mengaktifkan buzzer satu kali
- Mengirim robot menuju Point B
- Mengaktifkan buzzer dua kali
Node ini mengontrol alur navigasi menggunakan action client Nav2 yang mengirim goal pose ke robot.
