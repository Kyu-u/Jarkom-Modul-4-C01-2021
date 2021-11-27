# Jarkom-Modul-4-C01-2021

## Anggota Kelompok C01
| Nama | NRP |
| ------------- | ------------- |
| Muhammad Arifiansyah| 05111940000027  |
| Melanchthon Bonifacio Butarbutar  | 05111940000097  |
| Muhammad Valda Rizky Nur FIrdaus | 05111940000115  |

## VLSM pada GNS3

### Perhitungan Subnet

Pertama dibagikan subnet berdasarkan host yang dibutuhkan

| SUBNET | JUMLAH | NETMASK |
| ------ | ------ | ------- |
| A1     | 2021   | /21     |
| A2     | 1001   | /22     |
| A3     | 721    | /22     |
| A4     | 701    | /22     |
| A5     | 521    | /22     |
| A6     | 502    | /23     |
| A7     | 252    | /25     |
| A8     | 101    | /25     |
| A9     | 13     | /28     |
| A10    | 2      | /30     |
| A11    | 2      | /30     |
| A12    | 2      | /30     |
| A13    | 2      | /30     |
| TOTAL  | 5841   | /19     |  

Berdasarkan tabel tersebut dapat dibuat tree sebagai berikut untuk mendapatkan NID setiap subnet.

![image](https://user-images.githubusercontent.com/57700613/143678720-02de2def-279e-4c6c-a206-5c8a7b89b656.png)

Maka berdasarkan tree tersebut didapatkan pembagian NID sebagai berikut  

![image](https://user-images.githubusercontent.com/57700613/143678853-6e53ee03-8de4-4245-a94a-bd00cacad4c3.png)

### Routing  

![image](https://user-images.githubusercontent.com/57700613/143679308-d2f69ff6-ee11-4ac4-9198-617b5eb3acdc.png)

Pertama perlu dilakukan konfigurasi pada setiap node. Contohnya pada **FOOSHA** yang mengarah pada **BLUENO** 
#### FOOSHA

```
auto eth0
iface eth0 inet static
	address 192.184.8.1
	netmask 255.255.252.0
```

Karena yang mengarah ke **BLUENO** adalah `eth0` maka dilakukan konfigurasi pada `eth0` di mana address merupakan NID subnet A2 ditambah 1 sesuai pada tabel di atas.  
Netmask juga sesuai dengan netmask subnet A2.  

#### BLUENO

```
auto eth0
iface eth0 inet static
	address 192.184.8.2
	netmask 255.255.252.0
	gateway 192.184.8.1
```

Kemudian untuk address pada **BLUENO** merupakan address `eth0` **FOOSHA** ditambah 1 dan gateway `eth0` **FOOSHA**. Penambahan gateway hanya dilakukan di client dan server.  

Hal ini dilakukan pada semua router dan client.

Untuk routing yang pertama harus dilakukan adalah menambahkan default routing pada setiap router yang bukan **FOOSHA**.  
Contoh default routing pada **OIMO**:  

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.184.27.157
```
Di NID dan Netmask diisi dengan `0.0.0.0` dengan gateway merupakan IP dari router yang berada di atas **OIMO** yang mengarah ke **OIMO**.  
Dalam kasus ini hal tersebut adalah `eth2` dari **GUANHAO**.

Kemudian juga perlu ditambahkan routing ke setiap Subnet. Hal ini dapat dilakukan dengan menggunakan NID dan Netmask dari subnet yang dituju. Gateway merupakan IP sumber yang menuju subnet tersebut.

Contoh pada **FOOSHA** ke **JABRA**:
```
route add -net 192.184.20.0 netmask 255.255.252.0 gw 192.184.27.154
```
Di sini dapat dilihat bahwa NID dan Netmask sesuai subnet A5 dengan gateway merupakan `eth3` **FOOSHA**  

Perlu diingat ketika terdapat lebih dari satu router di antara router sumber dan tujuan, maka untuk setiap router yang tidak terhubung langsung dengan subnet tujuan, juga harus ditambahkan route ke subnet tersebut.  

Contohnya pada route dari **FOOSHA** menuju **ELENA**. Di sini dapat dilihat bahwa ada 2 router yang tidak terhubung langsung dengan **ELENA**, yaitu **GUANHAO** dan **OIMO**.  
Maka pada kedua router tersebut juga perlu ditambahkan routing ke subnet di mana **ELENA** berada.

Setelah mengikuti langkah langkah tersebut maka didapatkan routing di router sebagai berikut :  

#### FOOSHA

![image](https://user-images.githubusercontent.com/57700613/143680841-b77bf5ca-43c9-4c1b-9448-102af4d9698a.png)  

![image](https://user-images.githubusercontent.com/57700613/143680854-7dbca939-75b2-4688-9069-73be29700776.png)  

#### GUANHAO  
![image](https://user-images.githubusercontent.com/57700613/143680865-1540cbb2-d893-41ef-be25-cb95b75371cd.png)  

#### ALABASTA
![image](https://user-images.githubusercontent.com/57700613/143680875-2bc1a466-4d9d-42f5-a696-f0d66c8ad11e.png)  

#### OIMO
![image](https://user-images.githubusercontent.com/57700613/143680887-41418447-3b2f-49b4-9073-7a78ec88e7b8.png)  

#### SEASTONE
![image](https://user-images.githubusercontent.com/57700613/143680888-d8749124-f9a5-4ce5-afd7-68a0786655eb.png)  


#### WATER7

![image](https://user-images.githubusercontent.com/57700613/143680901-f89a7abd-7a7d-4a2c-891c-7eacd8af040c.png)


#### PUCCI

![image](https://user-images.githubusercontent.com/57700613/143680907-dea8e87d-4774-4d48-9458-91a4e45d97b9.png)

Agar dapat terhubung dengan internet maka perlu ditambahkan hal ini pada **FOOSHA**
```
iptables -t nat -A POSTROUTING -o eth4 -j MASQUERADE -s 192.184.0.0/19
```
Dan juga menambahkan ini pada setiap node :
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Testing

Testing dapat dilihat pada video berikut : 

https://user-images.githubusercontent.com/57700613/143681146-4f191133-b7c8-42d8-920d-4bb0fc959f36.mp4

