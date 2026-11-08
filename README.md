# Jarkom-Modul-4-2026-K-29
**Kelompok:** K29

**Prefix IP Dasar:** 10.78.0.0
## Ahmad Nayottama Juliansyah - 5027251045
## Muhammad Nasrulhaq - 5027251117

Link SPS: https://docs.google.com/spreadsheets/d/1bG8NAdUlTcEw9hmiICyJPxpX_T9eDBv7/edit?usp=sharing&ouid=101692903481131073762&rtpof=true&sd=true

**Metode:** Classless Inter-Domain Routing (CIDR)

## 1. Pendahuluan

Praktikum ini bertujuan untuk merancang dan membangun arsitektur jaringan *The Wired* dalam skala besar menggunakan metode perhitungan pengalamatan *Classless Inter-Domain Routing* (CIDR). Seluruh topologi diimplementasikan pada environment GNS3 menggunakan *image docker* berbasis Debian/Alpine. Topologi ini terdiri dari 13 Router dan 25 Subnet yang saling terhubung, dengan *Router Cyberia* bertindak sebagai gateway utama yang terhubung ke jaringan luar (NAT).

Aturan utama dalam desain ini adalah penggabungan rute (*route summarization*) harus mengikuti struktur fisik topologi dari ujung jaringan (*leaf*) ditarik ke pusat (*core router*), sehingga menghasilkan tabel *routing* yang sangat efisien.

## 2. Perhitungan Subnetting (CIDR)

### 2.1. Kebutuhan Rute dan IP (Host + Gateway)

Perhitungan jumlah IP untuk setiap subnet didasarkan pada jumlah host akhir ditambah dengan jumlah *interface* router yang terhubung ke subnet tersebut.

| Nama Subnet | Rute Topologi | IP Riil | Kapasitas Subnet | Netmask | 
| ----- | ----- | ----- | ----- | ----- | 
| **A1** | Cyberia > JJ, MyuMyu | 126 | 126 | /25 | 
| **A2** | Cyberia > Wired | 2 | 2 | /30 | 
| **A3** | Wired > Switch2 > Psyche, Copland, Schumann | 4 | 6 | /29 | 
| **A4** | Psyche > Karl, Lin | 881 | 1.022 | /22 | 
| **A5** | Copland > Juri | 32 | 62 | /26 | 
| **A6** | Copland > Masayuki | 301 | 510 | /23 | 
| **A7** | Schumann > Akashic | 4 | 6 | /29 | 
| **A8** | Schumann > Accela | 2 | 2 | /30 | 
| **A9** | Accela > Omnipresence, Denpa | 514 | 1.022 | /22 | 
| **A10** | Wired > Tachibana | 2 | 2 | /30 | 
| **A11** | Tachibana > Collective, Ghost | 267 | 510 | /23 | 
| **A12** | Tachibana > Deus | 2 | 2 | /30 | 
| **A13** | Deus > Lilith, Specter | 99 | 126 | /25 | 
| **A14** | Deus > Protocol7 | 2 | 2 | /30 | 
| **A15** | Protocol7 > Chisa, Hodgeson | 33 | 62 | /26 | 
| **A16** | Protocol7 > Knights | 2 | 2 | /30 | 
| **A17** | Knights > Eiri | 3 | 2 | /30\* | 
| **A18** | Knights > Phantoma | 2 | 2 | /30 | 
| **A19** | Phantoma > Semi | 15 | 14 | /28\* | 
| **A20** | Phantoma > Shibuya | 2 | 2 | /30 | 
| **A21** | Shibuya > Lain, Mika | 62 | 62 | /26 | 
| **A22** | Wired > Navi | 2 | 2 | /30 | 
| **A23** | Navi > Alice, Reika | 254 | 254 | /24 | 
| **A24** | Navi > Taro | 58 | 62 | /26 | 
| **A25** | Navi > Yasuo, Miho, Kuma | 143 | 254 | /24 | 

*(Catatan: Kapasitas subnet A17 dan A19 dipaksa sesuai dengan limit modul praktikum).*

### 2.2. Proses Penggabungan (Supernetting)

Penggabungan dilakukan menggunakan metode *Bottom-Up* murni sesuai jalur fisik kabel antar-router. Subnet diujung digabung dengan *link* luarnya secara berpasangan hingga mencapai *Core Router* (Wired).

| Tahap | Penggabungan | Gabungan dari 1 | Gabungan dari 2 | Netmask Akhir | Keterangan Router/Jalur |
| :---: | :---: | :--- | :--- | :---: | :--- |
| **I** | **B1** | A21 (/26) | A20 (/30) | **/25** | (Shibuya Lokal) |
| | **B2** | A19 (/28) | A18 (/30) | **/27** | (Phantoma Lokal) |
| | **B3** | A17 (/30) | A16 (/30) | **/29** | (Knights Lokal) |
| | **B4** | A15 (/26) | A14 (/30) | **/25** | (Protocol7 Lokal) |
| | **B5** | A13 (/25) | A12 (/30) | **/24** | (Deus Lokal) |
| | **B6** | A11 (/23) | A10 (/30) | **/22** | (Tachibana Lokal) |
| | **B7** | A1 (/25) | A2 (/30) | **/24** | (Cyberia Lokal) |
| | **B8** | A23 (/24) | A25 (/24) | **/23** | (Navi Kanan) |
| | **B9** | A24 (/26) | A22 (/30) | **/25** | (Navi Kiri) |
| | **B10** | A5 (/26) | A6 (/23) | **/22** | (Copland Lokal) |
| | **B11** | A9 (/22) | A8 (/30) | **/21** | (Accela Lokal) |
| **II** | **C1** | B1 (/25) | B2 (/27) | **/24** | (Shibuya + Phantoma) |
| | **C2** | B8 (/23) | B9 (/25) | **/22** | (Navi Utuh) |
| | **C3** | B11 (/21) | A7 (/29) | **/20** | (Accela + Schumann) |
| | **C4** | A4 (/22) | B10 (/22) | **/21** | (Psyche + Copland) |
| **III** | **D1** | C1 (/24) | B3 (/29) | **/23** | (Cabang Kanan Bawah - Knights) |
| | **D2** | C3 (/20) | C4 (/21) | **/19** | (Seluruh Cabang Kiri - Switch2) |
| **IV** | **E1** | D1 (/23) | B4 (/25) | **/22** | (Cabang Kanan Bawah - Protocol7) |
| | **E2** | D2 (/19) | A3 (/29) | **/18** | (Seluruh Cabang Kiri + Kabel Wired) |
| **V** | **F1** | E1 (/22) | B5 (/24) | **/21** | (Cabang Kanan Bawah - Deus) |
| **VI** | **G1** | F1 (/21) | B6 (/22) | **/20** | (Cabang Kanan Bawah - Tachibana) |
| **VII** | **H1** | G1 (/20) | C2 (/22) | **/19** | (Wired: Kanan Bawah + Kanan Atas) |
| | **H2** | H1 (/19) | B7 (/24) | **/18** | (Wired: Seluruh Kanan + Atas Cyberia) |
| **VIII**| **I1** | E2 (/18) | H2 (/18) | **/17** | (**Global Network: 10.78.0.0/17**) |

### 2.3. Pembagian Alokasi IP

Berdasarkan pohon biner dari blok global `10.78.0.0/17`, alokasi setiap subnet disusun tanpa ada batas biner yang bertabrakan (*Strict Binary Alignment*).

| Subnet | Network ID | Netmask | Broadcast | Range IP | 
| ----- | ----- | ----- | ----- | ----- | 
| **A1** | 10.78.96.0 | 255.255.255.128 | 10.78.96.127 | 10.78.96.1 - 10.78.96.126 | 
| **A2** | 10.78.96.128 | 255.255.255.252 | 10.78.96.131 | 10.78.96.129 - 10.78.96.130 | 
| **...** | ... | ... | ... | ... | 
| **A21** | 10.78.64.0 | 255.255.255.192 | 10.78.64.63 | 10.78.64.1 - 10.78.64.62 | 
| **A23** | 10.78.80.0 | 255.255.255.0 | 10.78.80.255 | 10.78.80.1 - 10.78.80.254 | 
| *(Detail alokasi IP lengkap merujuk pada Spreadsheet Laporan).* |  |  |  |  | 

## 3. Konfigurasi GNS3

Konfigurasi antarmuka jaringan dan *static routing* diimplementasikan menggunakan *shell script* (`/root/node_config.sh`) pada setiap node berbasis image `ardhptr21/debinet:latest`.

### 3.1. Konfigurasi Gateway Router (Cyberia)

Router Cyberia dikonfigurasi untuk menghubungkan *The Wired* dengan dunia luar menggunakan NAT (MASQUERADE).

```bash
#!/bin/bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 10.78.96.1
    netmask 255.255.255.128

auto eth2
iface eth2 inet static
    address 10.78.96.129
    netmask 255.255.255.252
EOF
        
ip addr flush dev eth1 2>/dev/null
ip addr flush dev eth2 2>/dev/null
ip addr add 10.78.96.1/25 dev eth1 && ip link set eth1 up
ip addr add 10.78.96.129/30 dev eth2 && ip link set eth2 up

echo 1 > /proc/sys/net/ipv4/ip_forward

# Route menuju Core Router (Wired)
ip route replace 10.78.0.0/17 via 10.78.96.130
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE 2>/dev/null || true
```

### 3.2. Konfigurasi Core Router (Wired)

Router Wired bertindak sebagai pusat pertemuan (Core) antar cabang distribusi utama di topologi. Dengan CIDR, *routing table* disederhanakan dengan rute agregasi gabungan.

```bash
#!/bin/bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 10.78.96.130
    netmask 255.255.255.252

auto eth1
iface eth1 inet static
    address 10.78.82.65
    netmask 255.255.255.252

auto eth2
iface eth2 inet static
    address 10.78.32.1
    netmask 255.255.255.248

auto eth3
iface eth3 inet static
    address 10.78.74.1
    netmask 255.255.255.252
EOF
        
for dev in eth0 eth1 eth2 eth3; do ip addr flush dev $dev 2>/dev/null; done
ip addr add 10.78.96.130/30 dev eth0 && ip link set eth0 up
ip addr add 10.78.82.65/30 dev eth1 && ip link set eth1 up
ip addr add 10.78.32.1/29 dev eth2 && ip link set eth2 up
ip addr add 10.78.74.1/30 dev eth3 && ip link set eth3 up

echo 1 > /proc/sys/net/ipv4/ip_forward

# Default route ke Cyberia
ip route replace default via 10.78.96.129

# Summary Rute Cabang Kiri (Switch2)
ip route replace 10.78.0.0/20 via 10.78.32.4
ip route replace 10.78.16.0/22 via 10.78.32.2
ip route replace 10.78.20.0/22 via 10.78.32.3

# Summary Rute Cabang Bawah (Tachibana dkk)
ip route replace 10.78.64.0/20 via 10.78.74.2

# Summary Rute Cabang Kanan (Navi dkk)
ip route replace 10.78.80.0/22 via 10.78.82.66
```

### 3.3. Konfigurasi Distribution Router (Phantoma)

Phantoma adalah router perantara yang melayani jaringan lokal sekaligus merutekan kembali blok CIDR gabungan ke arah Shibuya.

```bash
#!/bin/bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 10.78.64.146
    netmask 255.255.255.252

auto eth1
iface eth1 inet static
    address 10.78.64.65
    netmask 255.255.255.252

auto eth2
iface eth2 inet static
    address 10.78.64.129
    netmask 255.255.255.240
EOF
        
for dev in eth0 eth1 eth2; do ip addr flush dev $dev 2>/dev/null; done
ip addr add 10.78.64.146/30 dev eth0 && ip link set eth0 up
ip addr add 10.78.64.65/30 dev eth1 && ip link set eth1 up
ip addr add 10.78.64.129/28 dev eth2 && ip link set eth2 up

echo 1 > /proc/sys/net/ipv4/ip_forward

# Route menuju pusat (Core)
ip route replace default via 10.78.64.145

# Route blok gabungan menuju cabang Shibuya
ip route replace 10.78.64.0/25 via 10.78.64.66
```

### 3.4. Konfigurasi End-Node (Contoh: PC Lain)

Konfigurasi pada sisi klien/host ditambahkan *default gateway* yang mengarah ke *interface* router di jaringan lokalnya.

```bash
#!/bin/bash
cat << 'EOF' > /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 10.78.64.2
    netmask 255.255.255.192
    gateway 10.78.64.1
EOF
        
ip addr flush dev eth0 2>/dev/null
ip addr add 10.78.64.2/26 dev eth0 && ip link set eth0 up
ip route replace default via 10.78.64.1
```

## 4. Pengujian Konektivitas

Untuk memastikan jaringan telah terhubung dari ujung ke ujung, dibuat sebuah *script* automasi PING (`/root/test_ping.sh`) yang dijalankan dari sisi host.

```bash
#!/bin/bash
echo "======================================================"
echo "    PENGUJIAN KONEKTIVITAS THE WIRED (MODUL 4)        "
echo "======================================================"

uji_ping() {
    echo -n "Ping ke $1 ($2) ... "
    if ping -c 2 -W 2 $2 &> /dev/null; then
        echo -e "\e[32m[ BERHASIL ]\e[0m"
    else
        echo -e "\e[31m[ GAGAL ]\e[0m"
    fi
}

uji_ping "Router Cyberia (Gateway A1)" "10.78.96.1"
uji_ping "Karl (Subnet A4 - Psyche)" "10.78.16.2"
uji_ping "Ghost (Subnet A11 - Tachibana)" "10.78.72.3"
uji_ping "Lain (Subnet A21 - Shibuya / Terdalam)" "10.78.64.2"
uji_ping "Alice (Subnet A23 - Navi)" "10.78.80.2"
```

## 5. Kesimpulan

Implementasi teknik **CIDR** dengan skema agregasi rute fisik (*Bottom-Up Aggregation*) terbukti mampu meringkas tabel rute (*routing table*) di *core router* secara signifikan. Seluruh jaringan di belakang *Router Wired* berhasil direpresentasikan dengan efisien tanpa mengakibatkan isolasi node. Skema instalasi dengan memanfaatkan *Bash Scripting* pada sistem `/root/` sesuai arahan modul juga mempermudah *deployment* jaringan The Wired tanpa harus melakukan modifikasi manual berulang pada setiap perangkat.

**Metode:** Variable Length Subnet Mask (VLSM) — Cisco Packet Tracer

## 6. Pendahuluan (VLSM & Cisco Packet Tracer)

Selain implementasi pada GNS3, arsitektur jaringan *The Wired* juga dirancang dan disimulasikan pada **Cisco Packet Tracer** menggunakan metode perhitungan pengalamatan *classless* **Variable Length Subnet Mask (VLSM)** dengan blok IP dasar **`10.78.0.0/19`** (total kapasitas blok sebesar 4.120 alamat IP untuk 25 subnet A1 hingga A25).

Secara perangkat keras pada simulasi Cisco Packet Tracer, topologi ini dibangun menggunakan **13 Router Cisco 2811** (dengan penambahan modul ekspansi `NM-4E` pada router yang membutuhkan lebih dari 2 port bawaan), **13 Switch-PT**, dan **25 End-Device** (PC-PT, Laptop-PT, dan Server-PT). **Router Cyberia** berfungsi sebagai satu-satunya *border/gateway router* yang terhubung ke jaringan luar (`Cloud0`) menggunakan mekanisme **NAT Overload (PAT)** pada antarmuka `FastEthernet0/0` (`200.100.50.2/30`), sementara **Router Wired** bertindak sebagai *core distribution router* yang membagi jalur ke empat percabangan utama.

## 7. Perhitungan Subnetting (VLSM)

### 7.1. Rute Topologi dan Kebutuhan IP per Subnet

Berikut adalah rincian rute setiap subnet (A1–A25) beserta jumlah alokasi IP *usable* dan *netmask* berdasarkan perhitungan VLSM:

| Nama Subnet | Rute Topologi | Kapasitas IP Usable | Netmask | 
| ----- | ----- | ----- | ----- | 
| **A1** | Cyberia > JJ, MyuMyu | 126 | `/25` | 
| **A2** | Cyberia > Wired | 2 | `/30` | 
| **A3** | Wired > Switch2 > Psyche, Copland, Schumann | 6 | `/29` | 
| **A4** | Psyche > Karl, Lin | 1.022 | `/22` | 
| **A5** | Copland > Juri | 62 | `/26` | 
| **A6** | Copland > Masayuki | 510 | `/23` | 
| **A7** | Schumann > Akashic | 6 | `/29` | 
| **A8** | Schumann > Accela | 2 | `/30` | 
| **A9** | Accela > Omnipresence, Denpa | 1.022 | `/22` | 
| **A10** | Wired > Tachibana | 2 | `/30` | 
| **A11** | Tachibana > Collective, Ghost | 510 | `/23` | 
| **A12** | Tachibana > Deus | 2 | `/30` | 
| **A13** | Deus > Lilith, Specter | 126 | `/25` | 
| **A14** | Deus > Protocol7 | 2 | `/30` | 
| **A15** | Protocol7 > Chisa, Hodgeson | 62 | `/26` | 
| **A16** | Protocol7 > Knights | 2 | `/30` | 
| **A17** | Knights > Eiri | 2 | `/30` | 
| **A18** | Knights > Phantoma | 2 | `/30` | 
| **A19** | Phantoma > Semi | 14 | `/28` | 
| **A20** | Phantoma > Shibuya | 2 | `/30` | 
| **A21** | Shibuya > Lain, Mika | 62 | `/26` | 
| **A22** | Wired > Navi | 2 | `/30` | 
| **A23** | Navi > Alice, Reika | 254 | `/24` | 
| **A24** | Navi > Taro | 62 | `/26` | 
| **A25** | Navi > Yasuo, Miho, Kuma | 254 | `/24` | 
| **Total** | **Seluruh Topologi The Wired** | **4.120** | **`/19`** | 

### 7.2. Pengurutan dan Pembagian Blok VLSM (Terbesar ke Terkecil)

Untuk mencegah terjadinya *overlapping* maupun ruang alamat IP yang terbuang (*fragmentation*), alokasi blok VLSM diurutkan dari subnet dengan kebutuhan *host* terbesar (`/22`) hingga terkecil (`/30`):

1. **Blok Terbesar (`/22` — $2^{10}-2 = 1.022$ IP usable):** Dialokasikan terlebih dahulu untuk **Subnet A4** di Router Psyche (Server Karl 700 + Lin 180 = 880 host) pada Network ID `10.78.0.0/22`, disusul oleh **Subnet A9** di Router Accela (Omnipresence 510 + Denpa 3 = 513 host) pada `10.78.4.0/22`.
2. **Blok Menengah (`/23` hingga `/26`):**
   * Kebutuhan ratusan host pada **A6** (Masayuki 300 host) dan **A11** (Collective 260 + Ghost 6 = 266 host) menggunakan blok `/23` ($2^9-2 = 510$ IP usable) pada `10.78.8.0/23` dan `10.78.10.0/23`.
   * Cabang Router Navi yaitu **A23** (Alice 208 + Reika 45 = 253 host) serta **A25** (Yasuo 120 + Miho 18 + Kuma 4 = 142 host) menggunakan blok `/24` ($2^8-2 = 254$ IP usable) pada `10.78.12.0/24` dan `10.78.13.0/24`.
   * Oktet `.14` dipecah menjadi dua blok `/25` ($2^7-2 = 126$ IP usable) untuk **A1** (`10.78.14.0/25` — JJ & MyuMyu 125 host) dan **A13** (`10.78.14.128/25` — Lilith & Specter 98 host).
   * Oktet `.15` dipecah menjadi empat blok `/26` ($2^6-2 = 62$ IP usable) secara presisi tanpa sisa untuk **A21** (`10.78.15.0/26` — Lain & Mika 61 host), **A24** (`10.78.15.64/26` — Taro 57 host), **A15** (`10.78.15.128/26` — Chisa & Hodgeson 32 host), dan **A5** (`10.78.15.192/26` — Juri 31 host).
3. **Blok Kecil & Point-to-Point (`/28` hingga `/30`):**
   * Dimulai pada oktet `.16`, **Subnet A19** (PC Semi) dialokasikan pada `10.78.16.0/28` ($2^4-2 = 14$ IP usable).
   * **Subnet A7** (Server Akashic) dan **Subnet A3** (Switch2 Core yang menghubungkan 4 interface router sekaligus: Wired, Psyche, Copland, Schumann) masing-masing menggunakan `/29` ($2^3-2 = 6$ IP usable) pada `10.78.16.16/29` dan `10.78.16.24/29`.
   * Sepuluh subnet terakhir (**A2, A8, A10, A12, A14, A16, A17, A18, A20, A22**) ditempatkan berurutan menggunakan `/30` ($2^2-2 = 2$ IP usable) mulai dari `10.78.16.32/30` hingga `10.78.16.68/30`.

| Subnet | Network ID | Netmask | Broadcast | Range IP Usable | 
| ----- | ----- | ----- | ----- | ----- | 
| **A4** | `10.78.0.0` | `255.255.252.0` (`/22`) | `10.78.3.255` | `10.78.0.1` - `10.78.3.254` | 
| **A9** | `10.78.4.0` | `255.255.252.0` (`/22`) | `10.78.7.255` | `10.78.4.1` - `10.78.7.254` | 
| **A6** | `10.78.8.0` | `255.255.254.0` (`/23`) | `10.78.9.255` | `10.78.8.1` - `10.78.9.254` | 
| **A11** | `10.78.10.0` | `255.255.254.0` (`/23`) | `10.78.11.255` | `10.78.10.1` - `10.78.11.254` | 
| **A23** | `10.78.12.0` | `255.255.255.0` (`/24`) | `10.78.12.255` | `10.78.12.1` - `10.78.12.254` | 
| **A25** | `10.78.13.0` | `255.255.255.0` (`/24`) | `10.78.13.255` | `10.78.13.1` - `10.78.13.254` | 
| **A1** | `10.78.14.0` | `255.255.255.128` (`/25`) | `10.78.14.127` | `10.78.14.1` - `10.78.14.126` | 
| **A13** | `10.78.14.128` | `255.255.255.128` (`/25`) | `10.78.14.255` | `10.78.14.129` - `10.78.14.254` | 
| **A21** | `10.78.15.0` | `255.255.255.192` (`/26`) | `10.78.15.63` | `10.78.15.1` - `10.78.15.62` | 
| **A24** | `10.78.15.64` | `255.255.255.192` (`/26`) | `10.78.15.127` | `10.78.15.65` - `10.78.15.126` | 
| **A15** | `10.78.15.128` | `255.255.255.192` (`/26`) | `10.78.15.191` | `10.78.15.129` - `10.78.15.190` | 
| **A5** | `10.78.15.192` | `255.255.255.192` (`/26`) | `10.78.15.255` | `10.78.15.193` - `10.78.15.254` | 
| **A19** | `10.78.16.0` | `255.255.255.240` (`/28`) | `10.78.16.15` | `10.78.16.1` - `10.78.16.14` | 
| **A7** | `10.78.16.16` | `255.255.255.248` (`/29`) | `10.78.16.23` | `10.78.16.17` - `10.78.16.22` | 
| **A3** | `10.78.16.24` | `255.255.255.248` (`/29`) | `10.78.16.31` | `10.78.16.25` - `10.78.16.30` | 
| **A2** | `10.78.16.32` | `255.255.255.252` (`/30`) | `10.78.16.35` | `10.78.16.33` - `10.78.16.34` | 
| **A8** | `10.78.16.36` | `255.255.255.252` (`/30`) | `10.78.16.39` | `10.78.16.37` - `10.78.16.38` | 
| **A10** | `10.78.16.40` | `255.255.255.252` (`/30`) | `10.78.16.43` | `10.78.16.41` - `10.78.16.42` | 
| **A12** | `10.78.16.44` | `255.255.255.252` (`/30`) | `10.78.16.47` | `10.78.16.45` - `10.78.16.46` | 
| **A14** | `10.78.16.48` | `255.255.255.252` (`/30`) | `10.78.16.51` | `10.78.16.49` - `10.78.16.50` | 
| **A16** | `10.78.16.52` | `255.255.255.252` (`/30`) | `10.78.16.55` | `10.78.16.53` - `10.78.16.54` | 
| **A17** | `10.78.16.56` | `255.255.255.252` (`/30`) | `10.78.16.59` | `10.78.16.57` - `10.78.16.58` | 
| **A18** | `10.78.16.60` | `255.255.255.252` (`/30`) | `10.78.16.63` | `10.78.16.61` - `10.78.16.62` | 
| **A20** | `10.78.16.64` | `255.255.255.252` (`/30`) | `10.78.16.67` | `10.78.16.65` - `10.78.16.66` | 
| **A22** | `10.78.16.68` | `255.255.255.252` (`/30`) | `10.78.16.71` | `10.78.16.69` - `10.78.16.70` | 

### 7.3. Pembagian Alokasi IP pada 25 End-Device

Pada setiap subnet LAN, alamat IP *usable* pertama selalu dialokasikan sebagai **Default Gateway** pada antarmuka router lokal, sedangkan alamat berikutnya diberikan secara berurutan kepada setiap PC, Laptop, dan Server:

| No | Nama Perangkat | Router / Subnet | IPv4 Address | Subnet Mask | Default Gateway | 
| :--- | :--- | :--- | :--- | :--- | :--- | 
| 1 | **PC-PT JJ** | Cyberia (`A1`) | `10.78.14.2` | `255.255.255.128` | `10.78.14.1` | 
| 2 | **Laptop-PT MyuMyu** | Cyberia (`A1`) | `10.78.14.3` | `255.255.255.128` | `10.78.14.1` | 
| 3 | **Server-PT Karl** | Psyche (`A4`) | `10.78.0.2` | `255.255.252.0` | `10.78.0.1` | 
| 4 | **Server-PT Lin** | Psyche (`A4`) | `10.78.0.3` | `255.255.252.0` | `10.78.0.1` | 
| 5 | **Laptop-PT Juri** | Copland (`A5`) | `10.78.15.194` | `255.255.255.192` | `10.78.15.193` | 
| 6 | **Laptop-PT Masayuki** | Copland (`A6`) | `10.78.8.2` | `255.255.254.0` | `10.78.8.1` | 
| 7 | **Server-PT Akashic** | Schumann (`A7`) | `10.78.16.18` | `255.255.255.248` | `10.78.16.17` | 
| 8 | **PC-PT Omnipresence** | Accela (`A9`) | `10.78.4.2` | `255.255.252.0` | `10.78.4.1` | 
| 9 | **Laptop-PT Denpa** | Accela (`A9`) | `10.78.4.3` | `255.255.252.0` | `10.78.4.1` | 
| 10 | **Server-PT Collective** | Tachibana (`A11`) | `10.78.10.2` | `255.255.254.0` | `10.78.10.1` | 
| 11 | **PC-PT Ghost** | Tachibana (`A11`) | `10.78.10.3` | `255.255.254.0` | `10.78.10.1` | 
| 12 | **Laptop-PT Lilith** | Deus (`A13`) | `10.78.14.130` | `255.255.255.128` | `10.78.14.129` | 
| 13 | **PC-PT Specter** | Deus (`A13`) | `10.78.14.131` | `255.255.255.128` | `10.78.14.129` | 
| 14 | **Laptop-PT Chisa** | Protocol7 (`A15`) | `10.78.15.130` | `255.255.255.192` | `10.78.15.129` | 
| 15 | **PC-PT Hodgeson** | Protocol7 (`A15`) | `10.78.15.131` | `255.255.255.192` | `10.78.15.129` | 
| 16 | **Server-PT Eiri** | Knights (`A17`) | `10.78.16.58` | `255.255.255.252` | `10.78.16.57` | 
| 17 | **PC-PT Semi** | Phantoma (`A19`) | `10.78.16.2` | `255.255.255.240` | `10.78.16.1` | 
| 18 | **Laptop-PT Lain** | Shibuya (`A21`) | `10.78.15.2` | `255.255.255.192` | `10.78.15.1` | 
| 19 | **PC-PT Mika** | Shibuya (`A21`) | `10.78.15.3` | `255.255.255.192` | `10.78.15.1` | 
| 20 | **Laptop-PT Alice** | Navi (`A23`) | `10.78.12.2` | `255.255.255.0` | `10.78.12.1` | 
| 21 | **PC-PT Reika** | Navi (`A23`) | `10.78.12.3` | `255.255.255.0` | `10.78.12.1` | 
| 22 | **PC-PT Taro** | Navi (`A24`) | `10.78.15.66` | `255.255.255.192` | `10.78.15.65` | 
| 23 | **PC-PT Yasuo** | Navi (`A25`) | `10.78.13.2` | `255.255.255.0` | `10.78.13.1` | 
| 24 | **Laptop-PT Miho** | Navi (`A25`) | `10.78.13.3` | `255.255.255.0` | `10.78.13.1` | 
| 25 | **Server-PT Kuma** | Navi (`A25`) | `10.78.13.4` | `255.255.255.0` | `10.78.13.1` | 

## 8. Konfigurasi Cisco Packet Tracer (Penjelasan & Konfigurasi Final)

Strategi *routing* pada Cisco Packet Tracer dirancang untuk mengoptimalkan ukuran tabel *routing* menggunakan kombinasi **Default Route (`0.0.0.0/0`)**, **Route Summarization (Supernetting)**, dan **Static Route Spesifik**:

1. **Efisiensi Supernetting `/19` & NAT Overload di Router Cyberia:**
   * Sebagai *gateway* utama menuju WAN (`Cloud0`), antarmuka `FastEthernet0/0` (`200.100.50.2/30`) disetel sebagai `ip nat outside`, sedangkan antarmuka ke LAN A1 (`Ethernet1/0`) dan ke Router Wired (`FastEthernet0/1`) disetel sebagai `ip nat inside`. Seluruh blok `10.78.0.0/19` diizinkan melalui `access-list 1 permit 10.78.0.0 0.0.31.255` untuk ditranslasikan dengan fitur PAT (`overload`).
   * Daripada mendaftarkan 24 rute statis ke bawah, Cyberia cukup menggunakan **1 baris rute ringkasan** `ip route 10.78.0.0 255.255.224.0 10.78.16.34` menuju Router Wired. Trafik ke LAN A1 (`10.78.14.0/25`) tidak akan salah terlempar ke Wired karena prinsip **Longest Prefix Match** di Cisco IOS memprioritaskan rute *Directly Connected* `/25` dibanding rute statis `/19`.
2. **Distribusi Terpusat di Core Router (`Wired`):**
   * Berdasarkan verifikasi fisik menggunakan `show cdp neighbors`, keempat port pada Router Wired terhubung ke: `FastEthernet0/0` (Cyberia - A2), `Ethernet1/0` (Switch2 Core - A3), `FastEthernet0/1` (Tachibana - A10), dan `Ethernet1/1` (Navi - A22).
   * Router Wired mengarahkan *default route* (`0.0.0.0/0`) ke Cyberia (`10.78.16.33`), mendistribusikan rute cabang kiri ke Psyche (`10.78.16.26`), Copland (`10.78.16.27`), dan Schumann (`10.78.16.28`), merangkum rute cabang kanan menuju Navi (`10.78.16.70`) dengan *summary route* `10.78.12.0/23`, serta meneruskan trafik rantai bawah menuju Tachibana (`10.78.16.42`).
3. **Efisiensi Satu Baris pada Stub Routers (`Psyche`, `Copland`, `Accela`, `Navi`, `Shibuya`):**
   * Kelima router yang berada di ujung percabangan hanya memiliki satu jalur keluar menuju *upstream*. Oleh karena itu, masing-masing cukup dikonfigurasi dengan **1 baris Default Route (`ip route 0.0.0.0 0.0.0.0 <next-hop>`)** tanpa membebani memori dengan puluhan rute statis.
4. **Rute Hierarkis pada Router Rantai Perantara (`Schumann`, `Tachibana`, `Deus`, `Protocol7`, `Knights`, `Phantoma`):**
   * Menggunakan 1 baris *Default Route* (`0.0.0.0/0`) ke arah atas (menuju Wired/Cyberia) dan rute statis spesifik ke arah router di bawahnya.

### 8.1. Konfigurasi Final Gateway & Core Router (`Cyberia` & `Wired`)

#### 1. Router Cyberia (Border NAT, Subnet A1, A2)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Cyberia

interface FastEthernet0/0
 description WAN-to-Cloud0
 ip address 200.100.50.2 255.255.255.252
 ip nat outside
 no shutdown

interface Ethernet1/0
 description A1-LAN-Switch1
 ip address 10.78.14.1 255.255.255.128
 ip nat inside
 no shutdown

interface FastEthernet0/1
 description A2-Link-to-Wired
 ip address 10.78.16.33 255.255.255.252
 ip nat inside
 no shutdown

access-list 1 permit 10.78.0.0 0.0.31.255
ip nat inside source list 1 interface FastEthernet0/0 overload
ip route 0.0.0.0 0.0.0.0 200.100.50.1
ip route 10.78.0.0 255.255.224.0 10.78.16.34
end
write memory
```

#### 2. Router Wired (Core Distribution Router — Subnet A2, A3, A10, A22)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Wired

interface FastEthernet0/0
 description A2-Link-to-Cyberia
 ip address 10.78.16.34 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A3-Switch2-Core
 ip address 10.78.16.25 255.255.255.248
 no shutdown

interface FastEthernet0/1
 description A10-Link-to-Tachibana
 ip address 10.78.16.41 255.255.255.252
 no shutdown

interface Ethernet1/1
 description A22-Link-to-Navi
 ip address 10.78.16.69 255.255.255.252
 no shutdown

! Default route ke Cyberia
ip route 0.0.0.0 0.0.0.0 10.78.16.33

! Rute ke Cabang Kiri (Switch2 -> Psyche, Copland, Schumann, Accela)
ip route 10.78.0.0 255.255.252.0 10.78.16.26
ip route 10.78.8.0 255.255.254.0 10.78.16.27
ip route 10.78.15.192 255.255.255.192 10.78.16.27
ip route 10.78.4.0 255.255.252.0 10.78.16.28
ip route 10.78.16.16 255.255.255.248 10.78.16.28
ip route 10.78.16.36 255.255.255.252 10.78.16.28

! Rute ke Cabang Kanan (Navi)
ip route 10.78.12.0 255.255.254.0 10.78.16.70
ip route 10.78.13.0 255.255.255.0 10.78.16.70
ip route 10.78.15.64 255.255.255.192 10.78.16.70

! Rute ke Cabang Bawah (Tachibana -> Deus -> Protocol7 -> Knights -> Phantoma -> Shibuya)
ip route 10.78.10.0 255.255.254.0 10.78.16.42
ip route 10.78.14.128 255.255.255.128 10.78.16.42
ip route 10.78.15.0 255.255.255.192 10.78.16.42
ip route 10.78.15.128 255.255.255.192 10.78.16.42
ip route 10.78.16.0 255.255.255.240 10.78.16.42
ip route 10.78.16.44 255.255.255.252 10.78.16.42
ip route 10.78.16.48 255.255.255.248 10.78.16.42
ip route 10.78.16.56 255.255.255.248 10.78.16.42
ip route 10.78.16.64 255.255.255.252 10.78.16.42
end
write memory
```

### 8.2. Konfigurasi Final Cabang Kiri (`Psyche`, `Copland`, `Schumann`, `Accela`)

#### 3. Router Psyche (Subnet A3, A4)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Psyche

interface FastEthernet0/0
 description A3-Switch2-Core
 ip address 10.78.16.26 255.255.255.248
 no shutdown

interface FastEthernet0/1
 description A4-LAN-Switch3
 ip address 10.78.0.1 255.255.252.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.25
end
write memory
```

#### 4. Router Copland (Subnet A3, A5, A6)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Copland

interface FastEthernet0/0
 description A3-Switch2-Core
 ip address 10.78.16.27 255.255.255.248
 no shutdown

interface FastEthernet0/1
 description A5-LAN-Juri
 ip address 10.78.15.193 255.255.255.192
 no shutdown

interface Ethernet1/0
 description A6-LAN-Switch4
 ip address 10.78.8.1 255.255.254.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.25
end
write memory
```

#### 5. Router Schumann (Subnet A3, A7, A8)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Schumann

interface FastEthernet0/0
 description A3-Switch2-Core
 ip address 10.78.16.28 255.255.255.248
 no shutdown

interface FastEthernet0/1
 description A7-LAN-Akashic
 ip address 10.78.16.17 255.255.255.248
 no shutdown

interface Ethernet1/0
 description A8-Link-to-Accela
 ip address 10.78.16.37 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.25
ip route 10.78.4.0 255.255.252.0 10.78.16.38
end
write memory
```

#### 6. Router Accela (Subnet A8, A9)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Accela

interface FastEthernet0/0
 description A8-Link-to-Schumann
 ip address 10.78.16.38 255.255.255.252
 no shutdown

interface FastEthernet0/1
 description A9-LAN-Switch5
 ip address 10.78.4.1 255.255.252.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.37
end
write memory
```

### 8.3. Konfigurasi Final Rantai Bawah & Kanan (`Tachibana`, `Deus`, `Protocol7`, `Knights`, `Phantoma`, `Shibuya`, `Navi`)

#### 7. Router Tachibana (Subnet A10, A11, A12)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Tachibana

interface FastEthernet0/0
 description A10-Link-to-Wired
 ip address 10.78.16.42 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A11-LAN-Switch9
 ip address 10.78.10.1 255.255.254.0
 no shutdown

interface FastEthernet0/1
 description A12-Link-to-Deus
 ip address 10.78.16.45 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.41
ip route 10.78.14.128 255.255.255.128 10.78.16.46
ip route 10.78.15.0 255.255.255.192 10.78.16.46
ip route 10.78.15.128 255.255.255.192 10.78.16.46
ip route 10.78.16.0 255.255.255.240 10.78.16.46
ip route 10.78.16.48 255.255.255.248 10.78.16.46
ip route 10.78.16.56 255.255.255.248 10.78.16.46
ip route 10.78.16.64 255.255.255.252 10.78.16.46
end
write memory
```

#### 8. Router Deus (Subnet A12, A13, A14)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Deus

interface FastEthernet0/0
 description A12-Link-to-Tachibana
 ip address 10.78.16.46 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A13-LAN-Switch10
 ip address 10.78.14.129 255.255.255.128
 no shutdown

interface FastEthernet0/1
 description A14-Link-to-Protocol7
 ip address 10.78.16.49 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.45
ip route 10.78.15.0 255.255.255.192 10.78.16.50
ip route 10.78.15.128 255.255.255.192 10.78.16.50
ip route 10.78.16.0 255.255.255.240 10.78.16.50
ip route 10.78.16.52 255.255.255.252 10.78.16.50
ip route 10.78.16.56 255.255.255.248 10.78.16.50
ip route 10.78.16.64 255.255.255.252 10.78.16.50
end
write memory
```

#### 9. Router Protocol7 (Subnet A14, A15, A16)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Protocol7

interface FastEthernet0/0
 description A14-Link-to-Deus
 ip address 10.78.16.50 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A15-LAN-Switch11
 ip address 10.78.15.129 255.255.255.192
 no shutdown

interface FastEthernet0/1
 description A16-Link-to-Knights
 ip address 10.78.16.53 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.49
ip route 10.78.15.0 255.255.255.192 10.78.16.54
ip route 10.78.16.0 255.255.255.240 10.78.16.54
ip route 10.78.16.56 255.255.255.248 10.78.16.54
ip route 10.78.16.64 255.255.255.252 10.78.16.54
end
write memory
```

#### 10. Router Knights (Subnet A16, A17, A18)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Knights

interface FastEthernet0/0
 description A16-Link-to-Protocol7
 ip address 10.78.16.54 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A17-LAN-Eiri
 ip address 10.78.16.57 255.255.255.252
 no shutdown

interface FastEthernet0/1
 description A18-Link-to-Phantoma
 ip address 10.78.16.61 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.53
ip route 10.78.15.0 255.255.255.192 10.78.16.62
ip route 10.78.16.0 255.255.255.240 10.78.16.62
ip route 10.78.16.64 255.255.255.252 10.78.16.62
end
write memory
```

#### 11. Router Phantoma (Subnet A18, A19, A20)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Phantoma

interface FastEthernet0/0
 description A18-Link-to-Knights
 ip address 10.78.16.62 255.255.255.252
 no shutdown

interface Ethernet1/0
 description A19-LAN-Switch12
 ip address 10.78.16.1 255.255.255.240
 no shutdown

interface FastEthernet0/1
 description A20-Link-to-Shibuya
 ip address 10.78.16.65 255.255.255.252
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.61
ip route 10.78.15.0 255.255.255.192 10.78.16.66
end
write memory
```

#### 12. Router Shibuya (Subnet A20, A21)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Shibuya

interface FastEthernet0/0
 description A20-Link-to-Phantoma
 ip address 10.78.16.66 255.255.255.252
 no shutdown

interface FastEthernet0/1
 description A21-LAN-Switch13
 ip address 10.78.15.1 255.255.255.192
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.65
end
write memory
```

#### 13. Router Navi (Subnet A22, A23, A24, A25)
```cisco
enable
configure terminal
no ip domain-lookup
hostname Navi

interface FastEthernet0/0
 description A22-Link-to-Wired
 ip address 10.78.16.70 255.255.255.252
 no shutdown

interface Ethernet1/1
 description A23-LAN-Switch6
 ip address 10.78.12.1 255.255.255.0
 no shutdown

interface Ethernet1/0
 description A24-LAN-Switch7
 ip address 10.78.15.65 255.255.255.192
 no shutdown

interface FastEthernet0/1
 description A25-LAN-Switch8
 ip address 10.78.13.1 255.255.255.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.78.16.69
end
write memory
```

### 8.4. Verifikasi Tabel Routing (`show ip route`)

Pemeriksaan tabel *routing* dilakukan menggunakan perintah `show ip route` pada CLI router. Kode **`C`** menandakan subnet yang terhubung langsung (*Directly Connected*), **`S`** menandakan *Static Route* dengan nilai *Administrative Distance* `[1/0]`, dan **`S*`** menandakan *Candidate Default Route* (`0.0.0.0/0`).

#### Output `show ip route` pada Router Cyberia:
```text
Gateway of last resort is 200.100.50.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 200.100.50.1
      10.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
S        10.78.0.0/19 [1/0] via 10.78.16.34
C        10.78.14.0/25 is directly connected, Ethernet1/0
C        10.78.16.32/30 is directly connected, FastEthernet0/1
      200.100.50.0/30 is subnetted, 1 subnets
C        200.100.50.0 is directly connected, FastEthernet0/0
```

#### Output `show ip route` pada Router Wired:
```text
Gateway of last resort is 10.78.16.33 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.78.16.33
      10.0.0.0/8 is variably subnetted, 26 subnets, 9 masks
S        10.78.0.0/22 [1/0] via 10.78.16.26
S        10.78.4.0/22 [1/0] via 10.78.16.28
S        10.78.8.0/23 [1/0] via 10.78.16.27
S        10.78.10.0/23 [1/0] via 10.78.16.42
S        10.78.12.0/23 [1/0] via 10.78.16.70
S        10.78.13.0/24 [1/0] via 10.78.16.70
S        10.78.14.128/25 [1/0] via 10.78.16.42
S        10.78.15.0/26 [1/0] via 10.78.16.42
S        10.78.15.64/26 [1/0] via 10.78.16.70
S        10.78.15.128/26 [1/0] via 10.78.16.42
S        10.78.15.192/26 [1/0] via 10.78.16.27
S        10.78.16.0/28 [1/0] via 10.78.16.42
S        10.78.16.16/29 [1/0] via 10.78.16.28
C        10.78.16.24/29 is directly connected, Ethernet1/0
C        10.78.16.32/30 is directly connected, FastEthernet0/0
S        10.78.16.36/30 [1/0] via 10.78.16.28
C        10.78.16.40/30 is directly connected, FastEthernet0/1
S        10.78.16.44/30 [1/0] via 10.78.16.42
S        10.78.16.48/30 [1/0] via 10.78.16.42
S        10.78.16.56/30 [1/0] via 10.78.16.42
S        10.78.16.64/30 [1/0] via 10.78.16.42
C        10.78.16.68/30 is directly connected, Ethernet1/1
```

## 9. Pengujian Konektivitas (Cisco Packet Tracer)

Pengujian dilakukan melalui Command Prompt pada *end-device* untuk membuktikan seluruh node lintas cabang dapat saling terhubung tanpa *routing loop* serta mampu mencapai antarmuka luar (*NAT Outside*) di Router Cyberia:

### 9.1. Uji Rute Rantai Terjauh (`tracert` dari Laptop Lain di Shibuya ke Server Karl di Psyche)
Membuktikan paket dari titik terdalam di kanan bawah (`10.78.15.2`) berhasil melintasi 9 *hop* (Shibuya $\rightarrow$ Phantoma $\rightarrow$ Knights $\rightarrow$ Protocol7 $\rightarrow$ Deus $\rightarrow$ Tachibana $\rightarrow$ Wired $\rightarrow$ Psyche $\rightarrow$ Server Karl `10.78.0.2`):

```text
C:\>tracert 10.78.0.2

Tracing route to 10.78.0.2 over a maximum of 30 hops:

  1   0 ms      0 ms      0 ms      10.78.15.1
  2   0 ms      0 ms      0 ms      10.78.16.65
  3   0 ms      0 ms      0 ms      10.78.16.61
  4   0 ms      0 ms      0 ms      10.78.16.53
  5   0 ms      0 ms      0 ms      10.78.16.49
  6   0 ms      1 ms      9 ms      10.78.16.45
  7   0 ms      0 ms      0 ms      10.78.16.41
  8   *         0 ms      0 ms      10.78.16.26
  9   *         0 ms      0 ms      10.78.0.2

Trace complete.
```

### 9.2. Uji Ping Lintas Cabang (Laptop Lain di Shibuya ke Server Kuma di Navi)
```text
C:\>ping 10.78.13.4

Pinging 10.78.13.4 with 32 bytes of data:

Request timed out.
Reply from 10.78.13.4: bytes=32 time<1ms TTL=120
Reply from 10.78.13.4: bytes=32 time=1ms TTL=120
Reply from 10.78.13.4: bytes=32 time=2ms TTL=120

Ping statistics for 10.78.13.4:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 2ms, Average = 1ms
```

### 9.3. Uji Ping Menuju Gateway Luar NAT (PC Taro di Navi ke Interface WAN Cyberia)
```text
C:\>ping 200.100.50.2

Pinging 200.100.50.2 with 32 bytes of data:

Request timed out.
Request timed out.
Reply from 200.100.50.2: bytes=32 time<1ms TTL=253
Reply from 200.100.50.2: bytes=32 time=20ms TTL=253

Ping statistics for 200.100.50.2:
    Packets: Sent = 4, Received = 2, Lost = 2 (50% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 20ms, Average = 10ms
```
*(Catatan: Request timed out pada paket awal disebabkan oleh proses resolusi MAC address melalui broadcast ARP saat paket pertama kali melewati antarmuka router; pengujian ping kedua dan seterusnya menghasilkan `0% loss`).*

## 10. Kesimpulan (VLSM & Cisco Packet Tracer)

Perancangan pengalamatan **VLSM** pada blok `10.78.0.0/19` yang diurutkan dari kebutuhan host terbesar (`/22`) hingga terkecil (`/30`) memungkinkan 25 subnet pada topologi *The Wired* dialokasikan secara rapat dan efisien tanpa terjadi tumpang tindih (*overlapping*). Kombinasi *Default Route* (`0.0.0.0/0`) pada seluruh *stub router*, *Route Summarization* (`10.78.0.0/19` di Cyberia dan `10.78.12.0/23` di Wired), serta konfigurasi *NAT Overload* pada Router Cyberia berhasil memastikan seluruh 25 *end-device* dan 13 router dapat saling berkomunikasi secara optimal di Cisco Packet Tracer.