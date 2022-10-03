# Chặn IP quốc tế với iptables + ipset
## 1. Tìm hiểu 
#### Cài đặt
`sudo apt-get install ipset`
#### Tạo ipset list vietnam với type nethash
`sudo ipset create vietnam nethash`
#### Thêm dải IP Việt Nam vào list vietnam

```
#! /bin/bash 
for i in $(cat ~/vietnam.list); do sudo ipset add vietnam $i; done
```
#### Thêm rule cho iptables
*Drop tất cả các gói tin tới HA với source IP không phải dải của Việt Nam và dest IP là VIP trên server HA*

`sudo iptables -I INPUT -d $VIP -m set ! --match-set vietnam src -j DROP`

#### Một số lệnh với ipset
* Xem list vietnam : `ipset list vietnam`
* Xóa toàn bộ dải IP trong list vietnam : `sudo ipset flush vietnam`
* Xóa list vietnam : `sudo ipset destroy vietnam`
* Muốn ipset hoạt động khi reboot, tạo file config : `sudo sh -c "ipset save > /etc/ipset.conf"` và đưa vào /etc/rc.local : `ipset restore < /etc/ipset.conf`

## 2. Phương án
#### Chạy ansible 
Link : https://git.k8s.vn/sysadmin/haproxy_svr/-/tree/master/roles/block_quocte
- Khai báo dải IP Việt Nam nằm trong /files/vietnam.list
- Khai báo VIP cần chặn trong /defaults/main.yml
- Deploy : 
```
ansible-playbook -i host_block site_block.yml
```
