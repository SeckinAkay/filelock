# 🔐 Dinamik Klasör Kilitleyici (v5.0)

Windows üzerinde üçüncü parti hiçbir yazılım (SecretFolder vb.) kurmaya gerek kalmadan, tamamen yerleşik kod yapısıyla çalışan **renkli, dinamik ve güvenli bir klasör gizleme/kilitleme aracıdır.**

Bu betik, klasörlerinizi sadece gizlemekle kalmaz; onları Windows'un gözünde bir **Sistem Dosyası (+s +h)** haline getirerek *"Gizli dosyaları göster"* seçeneği açık olsa bile tamamen görünmez kılar.

---

## ✨ Özellikler

* **🎨 Gelişmiş Arayüz:** Tamamen siyah arka plan üzerinde mavi çerçeveler, kırmızı başlıklar ve yeşil komutlarla siber görünüm.
* **🌐 Türkçe Karakter Desteği:** UTF-8 kodlama desteği sayesinde Türkçe karakter hatası barındırmaz.
* **⚙️ Dinamik İlk Kurulum:** Kod satırlarıyla uğraşmadan, programı ilk açışta şifrenizi ve kilitlenecek klasör adını arayüzden belirleyebilirsiniz.
* **🔄 Güvenli Şifre Sıfırlama:** Klasör açık durumdayken mevcut şifrenizi doğrulayarak şifre veya klasör adını anında değiştirebilirsiniz.
* **🕵️‍♂️ Görünmez Ayar Dosyası:** Belirlediğiniz şifre ve ayarlar, sistem tarafından tamamen gizlenen özel bir dosyada saklanır.

---

## 🚀 Kurulum ve Kullanım

1. Bu depoda (repository) bulunan **`filelock.txt`** dosyasını bilgisayarınıza indirin. Ve .bat uzantılı olarak kaydedin. Türkçe karakter için UTF-8 seçili olsun.
2. Dosyayı kilitleme yapmak istediğiniz konuma (Örn: Masaüstü, D:\ vb.) taşıyın.
3. `filelock.bat` dosyasına çift tıklayarak çalıştırın.
4. **İlk Kurulumda:** Ekrandaki talimatları izleyerek kasanız için bir **Şifre** ve **Klasör Adı** belirleyin.
5. Oluşan klasörün içine gizlemek istediğiniz dosyaları yükleyin ve programı tekrar açarak **`E`** tuşu ile kilitleyin.

6. Bat dosyam bozulur yada kayıp olursa. Ayar dosyası sistem tarafından görünmez yapıldığı için normal şartlarda klasör seçeneklerinden "Gizli dosyaları göster" deseniz bile klasör içinde belirmeyecektir.
Bu gizli ayar dosyasına (.kasa_ayarlari.dat) erişmek, içindeki şifreyi manuel olarak görmek veya düzenlemek için kullanabileceğiniz en pratik 2 yöntemi aşağıda bulabilirsiniz:

1. Yöntem: Doğrudan Not Defteri ile Açmak (En Hızlısı)

Dosya gezgininde görünmesini beklemeden, yerleşik Windows komutuyla dosyayı direkt Not Defteri'nde açabilirsiniz:
1. .bat dosyanızın bulunduğu klasörün içindeyken klavyenizden Windows Tuşu + R kombinasyonuna basarak Çalıştır penceresini açın.
2. Açılan kutucuğun içine notepad .kasa_ayarlari.dat yazın ve Enter'a basın.
3. Dosya anında Not Defteri ile açılacaktır. İçerisinde Sifre=... ve Klasor=... şeklinde ayarlarınızı görebilir ve düzenleyebilirsiniz.

2. Yöntem: Komut İstemi (CMD) ile Görünür Yapmak

Eğer dosyayı klasörün içinde normal bir dosya gibi fiziksel olarak görmek ve üzerinde işlem yapmak istiyorsanız:
1. .bat dosyanızın olduğu klasörün boş bir yerine tıklayın.
2. Üstteki adres çubuğuna (klasör yolunun yazdığı yere) cmd yazıp Enter'a basın. O klasör dizininde siyah komut ekranı açılacaktır.
3. Şu komutu yapıştırıp Enter'a basın:
4. attrib -h -s .kasa_ayarlari.dat


---

## 🛠️ Kod İçeriği

Projenin temelini oluşturan Toplu İşlem (Batch) kodları aşağıdadır:

```batch
@ECHO OFF
chcp 65001 > nul
title Dinamik Klasör Kilitleyici v5.1
mode con: cols=65 lines=18

:: =================================================================
:: PROJE: Dinamik Klasör Kilitleyici
:: GELİŞTİRİCİ: (htts://seckinakay.com)
:: KATKIDA BULUNAN: Gemini Yapay Zeka Sohbet Ajanı
:: LİSANS: MIT (Açık Kaynak)
:: =================================================================

set "CONFIG_FILE=.kasa_ayarlari.dat"

:: İlk Kurulum Kontrolü
if NOT EXIST "%CONFIG_FILE%" goto ILK_KURULUM

:AYARLARI_YUKLE
for /f "tokens=1,2 delims==" %%A in (%CONFIG_FILE%) do (
    if "%%A"=="Sifre" set "SIFRE=%%B"
    if "%%A"=="Klasor" set "KLASOR_ADI=%%B"
)

if EXIST "Kilitli_%KLASOR_ADI%" goto KILIT_AC_KONTROL
if NOT EXIST "%KLASOR_ADI%" goto KASA_YOK

:ONAY
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                     KLASÖR KİLİTLEME SİSTEMİ                    ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [E] ''%KLASOR_ADI%'' klasörünü KİLİTLE                        ' -ForegroundColor Green"
powershell -Command "Write-Host '  [S] Şifre veya Klasör Adını SIFIRLA / DEĞİŞTİR                ' -ForegroundColor Yellow"
powershell -Command "Write-Host '  [H] Programdan ÇIK                                            ' -ForegroundColor Red"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
set /p "secim= > Seçiminiz (E/S/H): "
if %secim%==E goto KILITLE
if %secim%==e goto KILITLE
if %secim%==S goto AYAR_SIFIRLA
if %secim%==s goto AYAR_SIFIRLA
if %secim%==H goto END_NO_PAUSE
if %secim%==h goto END_NO_PAUSE
echo.
powershell -Command "Write-Host '  [!] Geçersiz seçim yaptınız. Lütfen E, S veya H girin.' -ForegroundColor Red"
timeout /t 2 > nul
goto ONAY

:KILITLE
ren "%KLASOR_ADI%" "Kilitli_%KLASOR_ADI%"
attrib +h +s +r +i "Kilitli_%KLASOR_ADI%"
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                          İŞLEM BAŞARILI                         ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [+] Klasör başarıyla gizlendi ve kilitlendi.                  ' -ForegroundColor Green"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
timeout /t 3 > nul
goto END_NO_PAUSE

:KILIT_AC_KONTROL
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                     KİLİTLİ KASA ERİŞİMİ                        ' -ForegroundColor Red"
powershell -Command "Write-Host '                      www.seckinakay.com                         ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [-] Klasörün kilidini açmak için şifrenizi girmeniz gerekiyor. ' -ForegroundColor Green"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
set /p "girilen_sifre= > Şifrenizi Girin: "
if NOT "%girilen_sifre%"=="%SIFRE%" goto HATALI_SIFRE
attrib -h -s -r -i "Kilitli_%KLASOR_ADI%"
ren "Kilitli_%KLASOR_ADI%" "%KLASOR_ADI%"
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                     İŞLEM BAŞARILI                              ' -ForegroundColor Red"
powershell -Command "Write-Host '                   www.seckinakay.com                            ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [+] Şifre doğru. Klasörünüz erişime açıldı!                    ' -ForegroundColor Green"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
timeout /t 3 > nul
goto AYARLARI_YUKLE

:HATALI_SIFRE
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                           !!! HATA !!!                          ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [X] Hatalı şifre girdiniz! Erişim reddedildi.                  ' -ForegroundColor Red"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
timeout /t 3 > nul
goto END_NO_PAUSE

:AYAR_SIFIRLA
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                       GÜVENLİK DOĞRULAMASI                      ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [-] Ayarları değiştirmek için mevcut şifreyi doğrulayın.       ' -ForegroundColor Yellow"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
set /p "onay_sifre= > Mevcut Şifre: "
if NOT "%onay_sifre%"=="%SIFRE%" goto HATALI_SIFRE

attrib -h -s "%CONFIG_FILE%" > nul 2>&1
del "%CONFIG_FILE%" > nul 2>&1
goto ILK_KURULUM

:KASA_YOK
md "%KLASOR_ADI%"
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                          BİLGİLENDİRME                          ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [*] ''%KLASOR_ADI%'' adında klasörünüz yeniden oluşturuldu.    ' -ForegroundColor Green"
powershell -Command "Write-Host '  [*] İçindeki dosyalar güvende. Tekrar kilitleyebilirsiniz.      ' -ForegroundColor Green"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
pause
goto ONAY

:ILK_KURULUM
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                       KASA AYARLARI MENÜSÜ                      ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [+] Kasanız için yeni şifre ve klasör adı belirleyin.' -ForegroundColor Green"
echo.
set /p "yeni_sifre= > 1. Adım - İstediğiniz Şifreyi Yazın: "
set /p "yeni_klasor= > 2. Adım - Klasörün Adı Ne Olsun?: "

echo Sifre=%yeni_sifre%>%CONFIG_FILE%
echo Klasor=%yeni_klasor%>>%CONFIG_FILE%
attrib +h +s "%CONFIG_FILE%"

if NOT EXIST "%yeni_klasor%" md "%yeni_klasor%"
cls
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
powershell -Command "Write-Host '                         İŞLEM TAMAMLANDI                        ' -ForegroundColor Red"
powershell -Command "Write-Host '                        www.seckinakay.com                       ' -ForegroundColor Red"
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
echo.
powershell -Command "Write-Host '  [*] Ayarlar güncellendi. ''%yeni_klasor%'' klasörü aktif.      ' -ForegroundColor Green"
powershell -Command "Write-Host '  [🤖] Seçkin Akay ve Gemini Yapay Zeka İle Tamamlandı.         ' -ForegroundColor Cyan"
echo.
powershell -Command "Write-Host '=================================================================' -ForegroundColor Blue"
timeout /t 4 > nul
goto AYARLARI_YUKLE

:END_NO_PAUSE

## 📄 Lisans

Bu proje **MIT Lisansı** altında lisanslanmıştır. Dilediğiniz gibi geliştirebilir, kopyalayabilir ve paylaşabilirsiniz.
