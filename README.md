# Dijital-Kapi-Gorevlisi-Projesi

sanal makinemiz kali  kurban makinemiz windows üzerine 
windows iso dosyası : https://www.microsoft.com/tr-tr/software-download/windows10

<img width="1087" height="620" alt="image" src="https://github.com/user-attachments/assets/45a3e71e-c01b-4600-8b75-baa6f4f28f37" />
<img width="1078" height="621" alt="image" src="https://github.com/user-attachments/assets/36d9b655-5f95-4eda-9639-84495102a6c8" />
<img width="1074" height="601" alt="image" src="https://github.com/user-attachments/assets/08239657-6c6d-4370-ae9e-53735fde2fa7" />



"Arkadaşlar, 11. hafta kurulumlarını tamamladım. Kali makinemizi bir Gateway (Ağ Geçidi) yaptık ve tcpdump ile trafiği dinliyoruz. Sizin kendi bilgisayarlarınızda 12. haftaya geçmeden önce sırasıyla şu komutları çalıştırıp altyapıyı kurmanız gerekiyor..."




sudo apt update

sudo apt install netplan.io snort tcpdump -y


sudo systemctl stop NetworkManager

sudo systemctl disable NetworkManager

sudo systemctl enable systemd-networkd

sudo systemctl start systemd-networkd


sudo nano /etc/netplan/01-netcfg.yaml


--
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true 
    eth1:
      addresses: [192.168.10.1/24]
      

--
sudo netplan apply


sudo nano /etc/sysctl.conf

sudo sysctl -p


sudo tcpdump -i eth1 -n
---------

ip a  ->  Amacı: Sistemdeki ağ kartlarının (eth0, eth1 vb.) isimlerini öğrenmek ve onlara doğru IP adreslerinin atanıp atanmadığını kontrol etmek için kullandık.

sudo nano /etc/netplan/01-netcfg.yaml   -> Amacı: Kali makinemize "İç Ağ" (Internal Network) üzerinden kurban makineyle konuşabilmesi için sabit bir IP adresi (192.168.10.1) tanımlamak amacıyla bu dosyayı açıp düzenledik.

network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
    eth1:
      addresses:
        - 192.168.10.1/24

sudo netplan apply  -> Amacı: Nano ile dosyaya yazdığımız o yeni IP ayarlarının bilgisayar tarafından anında işleme alınmasını (aktif olmasını) sağlamak için çalıştırdık.


sudo nano /etc/sysctl.conf  -> Amacı: Normalde güvenlik gereği kapalı olan "trafiği başka yere yönlendirme" özelliğini açmak için girdik. İçerisindeki #net.ipv4.ip_forward=1 satırının başındaki # işaretini kaldırarak Kali'yi bir "Yönlendirici" (Router) yaptık.

net.ipv4.ip_forward=1

sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  -> Amacı: İç ağdaki Windows makinesinin internete çıkabilmesi için, Windows'un trafiğini Kali'nin kendi dış IP'sinin arkasına saklayarak (maskeleyerek) dışarıya aktarmasını sağladık.


sudo tcpdump -i eth1 -n  -> Amacı: Kapı görevlisinin (Kali) iç ağ kapısında (eth1) beklemesini ve oradan geçen tüm veri paketlerini (trafiği) canlı olarak ekrana dökmesini sağladık. Projenin 11.2 kriterini bu komutla kanıtladık.


WİNDOWS KOMUTLARIMIZ

ping 192.168.10.1  -> Amacı: Windows makinesinin, bizim kurduğumuz Kapı Görevlisi'ni (Kali) aynı ağın içinde görüp göremediğini doğrulamak için yaptık.


ping 8.8.8.8  -> Amacı: Windows'un Kali üzerinden geçerek dış dünyadaki (internetteki) Google DNS sunucularına ulaşıp ulaşamadığını test ettik. Bu komut çalıştığında Kali'deki tcpdump ekranına loglar akmaya başladı.


