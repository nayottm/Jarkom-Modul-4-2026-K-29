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