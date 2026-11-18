# Zyxel Armor G5 için Türkçe OpenWRT Kurulum Rehberi
Bu rehberimizdeki yöntem ile cihazın güncel sürümlerdeki SCP-SSH erişim problemine karşın yalnızca telnet üzerinden  
Kurulumu gerçekleştirebilirsiniz. Yöntem, cihaz sürümü fark etmeksizin NBG7815 modeli tüm cihazlarda çalışmaktadır.  
*OpenWRT kurulumu cihazınızı garanti dışı bırakabilir, oluşabilecek tüm komplikasyonlar sizin sorumluluğunuzdadır.*  
*Konu ile ilgili hiçbir sorumluluk kabul etmiyoruz. Rehberimizi kaynak göstererek paylaşmanız önemle rica olunur.* 🙏

<p align="left">
  <a href="https://discord.gg/k6y5MBKCPW"><img src="https://img.shields.io/badge/Discord-Yardım İçin-blue?logo=discord&logoColor=white"/></a>
</p>
  
<details>
  <summary>İçindekiler</summary>
  <ol>
    <li>
      <a href="#%EF%B8%8F-cihaz-özellikleri">⚙️ Cihaz Özellikleri</a>
    </li>
    <li>
      <a href="#-başlarken">✨ Başlarken</a>
    </li>
    <li>
      <a href="#-kuruluma-hazırlık">🪄 Kuruluma Hazırlık</a>
    </li>
    <li>
      <a href="#-openwrt-kurulumu---i̇ndir">🚀 OpenWRT Kurulumu</a>
    </li>
    <li>
      <a href="#-merhaba-openwrt">😎 Merhaba OpenWRT!</a>
    </li>
    <li>
      <a href="#-kurulum-sonrası">💾 Kurulum Sonrası</a>
    </li>
    <li>
      <a href="#-katkıda-bulun--bağış">🤝 Katkıda Bulun / Bağış</a>
    </li>
    <li>
      <a href="#%EF%B8%8F-kaynaklar">🗃️ Kaynaklar</a>
    </li>
  </ol>
</details>



# ⚙️ Cihaz Özellikleri
- CPU: 2.2 Ghz Qualcomm IPQ8074A 
- RAM: 1 GB
- FLASH: 4 GB
- 2.4 Ghz: Qualcomm QCN5024 (4x4)
- 5 Ghz: Qualcomm QCN5054 (4x4) + Qualcomm QCN5054 (4x4)
- Bluetooth: CSR8811
- Ethernet: Qualcomm QCA8075 (4x1G) + Qualcomm QCA8081 (1x2.5G) + Aquantia AQR113C (1x10G)

# ✨ Başlarken
Öncelikle [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) gibi Telnet üzerinden erişim imkanı bulunan bir uçbirim ve [Python3](https://www.python.org/downloads/)'ü bilgisayarınıza indirin.  
Cihazın tüm ethernet bağlantılarını kesin. Reset butonuna led ışık turuncu renkte yanıp sönene kadar basılı tutun.  
Birkaç dakika sonra ışık sabit koyu mavi yandığında, LAN kablosunu sarı portlardan birine takarak PC'ye bağlayın.  
Putty uygulaması veya farklı bir uçbirim kullanarak **`Telnet port 23`** modunda **`192.168.123.1`** adresine bağlanın.  
Telnet için girmeniz gereken kullanıcı bilgileri: Kullanıcı adı: **`root`** - Şifre: **`nbg7815@2019`**  

# 🪄 Kuruluma Hazırlık
Telnet arayüzüne eriştikten sonra öncelikle cihazın bazı ayarlarını düzenlememiz gerekiyor.  
Uçbirim üzerinde düzenleyeceğimiz ilk ayarların kodları şu şekildedir:  
> **`uci set dropbear.setting.enable=1`**  
> **`uci commit dropbear`**  
> **`uci set network.general.auto_ip_change=0`**  
> **`uci commit network`**

Ardından **`vi /etc/init.d/preboot`** komutuyla **`preboot`** dosyasından **`dropbear`** bölümünü kapatacağız.  
Komutu girdikten sonra düzenleme moduna girmek için klavyenizden ℹ️ harfine basın.
Kodlarda **`dropbear`** satırından **`fi`** satırına kadar tüm satırların başına **`#`** ekleyiniz.
Düzenleme sonrası **`dropbear`** bölümü bu şekilde görünmelidir.  
> **`#dropbear`**  
> **`#ck_dropbear=$(uci get dropbear.setting.enable)`**  
> **`#if [ "$ck_dropbear" != "0" ]; then`**  
> **`#    uci set dropbear.setting.enable=0`**  
> **`#    uci commit dropbear`**  
> **`#fi`**

Dosyayı kaydedip kapatmak için **`ESC`** tuşuna bastıktan sonra **`:wq`** yazarak dosyadan çıkış yapın.  
Cihazın fişini söküp ardından geri takarak yeniden başlatın.  

# 🚀 OpenWRT Kurulumu - <a href="https://github.com/frudotz/openwrt-zyxel-nbg7815/releases/tag/NBG7815" target="_blank">İndir</a>
[Releases](https://github.com/frudotz/openwrt-zyxel-nbg7815/releases/tag/NBG7815) kısmında paylaştığımız dosyalar arasında yer alan **`ApplicationData`** klasörü içindeki **`flash_to_openwrt.sh`** ve  
OpenWRT imaj dosyasını bir dizine kopyalayın ve klasör içinde **`Shift + Sağ Tık`** yaparak bir uçbirim çalıştırın.  
Uçbirimde **`python3 -m http.server`** komutunu kullanarak dizinde bir Python yerel sunucusu başlatın.  

Ardından Telnet üzerinden cihaza tekrar erişerek aşağıdaki komutları girin.  
> **`cd /tmp/ApplicationData`**  
> **`wget http://<Bilgisayarın_Lokal_IPsi>:8000/flash_to_openwrt.sh`**  
> **`wget http://<Bilgisayarın_Lokal_IPsi>:8000/openwrt-ipq807x-generic-zyxel_nbg7815-squashfs-sysupgrade.bin`**  
> **`chmod -R 0777 flash_to_openwrt.sh`**  
> **`chmod -R 0777 openwrt-ipq807x-generic-zyxel_nbg7815-squashfs-sysupgrade.bin`**  

Son olarak **`./flash_to_openwrt.sh`** komutu ile kurulum scriptini çalıştırınız.

# 😎 Merhaba OpenWRT!
Kurulumda bir hata yapmadıysanız birkaç dakika içinde cihaz OpenWRT üzerinden başlar ve internete erişebilirsiniz.  
Tebrikler! Artık doğruca [192.168.1.1](http://192.168.1.1/) adresine giderek OpenWRT'ye merhaba diyebilirsiniz! \*alkış efekti\*  

# 💾 Kurulum Sonrası
Arayüzde gezinirken veya paketler kurarken cihaz hafızasının fazlasıyla küçük bir alana sahip olduğunu göreceksiniz.  
Bunun sebebi cihazda iki ayrı hafıza alanı bulunmasıdır, çözümüyse kullanılacak alanı büyük olan ile değiştirmektir.  
Bu işlemi güvenli şekilde ve sorun çıkması halinde otomatik geri dönüşle yapabilmeniz için ayrı bir rehber hazırladık:  
👉 **[Zyxel Armor G5 için Türkçe OpenWRT Extroot Rehberi](https://github.com/frudotz/openwrt-nbg7815-extroot)**

*Bu rehberin önceki sürümündeki komutlarla extroot yaptıysanız ve Wi-Fi'ye bağlanan cihazlar IP alamıyorsa, yeni rehberdeki Sorun Giderme bölümüne göz atın.*

# 🤝 Katkıda Bulun / Bağış
  - Yanlış gördüğünüz veya eklemek istediğiniz şeyleri PR/Issue açarak iletebilirsiniz.  
  - Rehberimizi faydalı bulduysanız [🍻 bir bira ısmarlayarak](https://coff.ee/frudotz) bana destek olabilirsiniz.

# 🗃️ Kaynaklar
  - [OpenWRT Wiki](https://openwrt.org/toh/zyxel/nbg7815_armor_g5)  
  - [Zyxel NBG7815 (Armor G5) Openwrt Kurma Rehberi @altuntepe2 - DH Forum](https://forum.donanimhaber.com/zyxel-nbg7815-armor-g5-openwrt-kurma-rehberi--155271460)  
  
-----------
🎀 Rehberimizi okuduğunuz için teşekkür ederiz!  
⭐ İçeriği faydalı bulduysanız desteklemek için **Star** verebilirsiniz.  
