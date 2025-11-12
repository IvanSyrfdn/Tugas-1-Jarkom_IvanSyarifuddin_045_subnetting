# Tugas-1-Jarkom_IvanSyarifuddin_045_subnetting

# Tugas 1 Jarkom - VLSM, CIDR, & Static Routing


* **Nama:** Ivan Syarifuddin
* **NRP:** 5027241045
* **Kelas:** C

---
## Deskripsi Tugas

Tugas ini mencakup perancangan dan implementasi jaringan untuk Yayasan Pendidikan ARA yang memiliki kantor pusat dan kantor cabang. Pengerjaan meliputi:
1.  Perhitungan **Base Network** unik berdasarkan NRP.
2.  Perhitungan alokasi IP menggunakan **VLSM** untuk 7 subnet (5 di Pusat, 1 di Cabang, 1 WAN).
3.  Perhitungan agregasi rute **CIDR (Supernetting)** untuk Kantor Pusat.
4.  Implementasi dan konfigurasi topologi jaringan di **Cisco Packet Tracer**, termasuk **Static Routing**.

---

##  Langkah Pengerjaan

Berikut adalah tahapan pengerjaan tugas, dari perhitungan hingga implementasi.

### 1. Perhitungan Base Network

Langkah pertama adalah menentukan alamat IP dasar (base network) yang akan digunakan untuk alokasi.

* **Aturan:** `10.(NRP mod 256).0.0`
* **Perhitungan:** `5027241045 mod 256 = 85`
* **Base Network:** **`10.85.0.0/16`**

### 2. Perhitungan VLSM (Variable Length Subnet Mask)

Perhitungan VLSM dilakukan dengan mengurutkan kebutuhan host dari yang terbesar ke terkecil untuk mendapatkan alokasi IP yang paling efisien.

**Kebutuhan Subnet (Diurutkan):**
1.  Sekretariat: 380 host
2.  Bidang Kurikulum: 220 host
3.  Bidang Guru & Tendik: 95 host
4.  Bidang Sarana Prasarana: 45 host
5.  Bidang Pengawas Sekolah (Cabang): 18 host
6.  Server & Admin: 6 host
7.  Link WAN (Antar-Router): 2 host

Hasil perhitungan VLSM lengkap (mencakup Network, Mask, Prefix, Range Host, Broadcast, Gateway) untuk 7 subnet di atas dapat dilihat pada file `Tugas 1 Jarkom_[Nama]_[NRP].xlsx` di repositori ini.

### 3. Perhitungan CIDR (Route Aggregation)

CIDR (Supernetting) digunakan untuk meringkas rute dari Kantor Pusat agar tabel routing di `R-Cabang` menjadi lebih efisien.

1.  **Jaringan yang Diagregasi:** 5 subnet di Kantor Pusat (Sekretariat, Kurikulum, Guru, Sarpras, Server).
2.  **Rentang Alamat:** Dari `10.85.0.0` (Network terendah) hingga `10.85.3.231` (Broadcast tertinggi).
3.  **Analisis Biner:** Dengan membandingkan bit terendah dan tertinggi, didapatkan 22 bit yang sama (common prefix).
4.  **Hasil Supernet:** **`10.85.0.0/22`**
    * **Network:** `10.85.0.0`
    * **Mask:** `255.255.252.0`
    * **Range:** `10.85.0.1` - `10.85.3.254`
    * **Broadcast:** `10.85.3.255`

### 4. Desain & Konfigurasi Topologi CPT

Topologi dirancang menggunakan 2 router (Pusat & Cabang) yang terhubung via Serial, dengan 1 switch untuk setiap LAN.

* **R-Pusat (`4331`):** Diberikan modul tambahan (`HWIC-1GE-SFP` dengan transceiver `GLC-T`, atau modul `NIM-2GE-CU-SFP`) untuk menambah jumlah port fisik. Konfigurasi 5 gateway LAN dipasang langsung pada interface fisik router (bukan RoaS).
* **R-Cabang (`2911`):** Dikonfigurasi dengan 1 gateway LAN pada interface `G0/0/0`.
* **Static Routing:**
    * **Di `R-Pusat`:** Ditambahkan static route spesifik untuk menjangkau LAN Cabang:
        ```bash
        ip route 10.85.3.192 255.255.255.224 10.85.3.234
        ```
    * **Di `R-Cabang`:** Ditambahkan static route **supernet (CIDR)** untuk menjangkau *semua* LAN di Pusat:
        ```bash
        ip route 10.85.0.0 255.255.252.0 10.85.3.233
        ```

---
