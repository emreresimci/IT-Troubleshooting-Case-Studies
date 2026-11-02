# Windows Update KB5127648 – Network Authentication Issue
# Windows KB5127648 Güncellemesi Sonrası Ağ Kimlik Doğrulama Sorunu

## 1. Problem / Sorun

**English:**

After installing Windows update KB5127648, wired Ethernet and wireless Wi-Fi connections using WPA2-Enterprise and 802.1X authentication may experience connectivity problems.

**Türkçe:**

Windows KB5127648 güncellemesinin ardından WPA2-Enterprise ve 802.1X kimlik doğrulaması kullanan kablolu Ethernet ve kablosuz Wi-Fi bağlantılarında erişim sorunları yaşanabilir.

## 2. Cause / Sorunun Nedeni

The issue is related to NTLMv1-derived Single Sign-On credentials used by certain MS-CHAPv2 authentication configurations.

Sorun, bazı MS-CHAPv2 kimlik doğrulama yapılandırmalarında kullanılan NTLMv1 türevi Tek Oturum Açma (SSO) kimlik bilgileriyle ilişkilidir.

## 3. Solution – Registry Modification / Çözüm – Kayıt Defteri Düzenlemesi

### Step 1 – Open Registry Editor
### Adım 1 – Kayıt Defteri Düzenleyicisi'ni açın

Press `Win + R`, type `regedit` and press Enter.

`Win + R` tuşlarına basın, `regedit` yazın ve Enter'a basın.

### Step 2 – Navigate to the Registry Path
### Adım 2 – Kayıt Defteri Yoluna Gidin

Navigate to:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0`

### Step 3 – Create or Modify the Registry Value
### Adım 3 – Kayıt Defteri Değerini Oluşturun veya Düzenleyin

Create a new DWORD (32-bit) Value:

- Value name: `BlockNtlmv1SSO`
- Type: `REG_DWORD`
- Value data: `0`

Yeni bir DWORD (32-bit) değeri oluşturun:

- Değer adı: `BlockNtlmv1SSO`
- Tür: `REG_DWORD`
- Değer verisi: `0`

**Value 0 – Audit Mode / Denetim Modu**

Allows the NTLMv1-derived SSO request while generating warning events.

NTLMv1 türevi SSO isteğine izin verir ve uyarı olaylarının kaydedilmesini sağlar.

### Step 4 – Apply Using Command Prompt
### Adım 4 – Komut İstemi ile Uygulama

Open Command Prompt as Administrator and run:

Komut İstemi'ni yönetici olarak açın ve şu komutu çalıştırın:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0" /v BlockNtlmv1SSO /t REG_DWORD /d 0 /f
```

### Step 5 – Verify the Registry Value
### Adım 5 – Kayıt Defteri Değerini Doğrulayın

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0" /v BlockNtlmv1SSO
```

The expected value is `0x0`.

Beklenen değer `0x0` olmalıdır.

## 4. Test the Connection / Bağlantıyı Test Edin

- Disconnect and reconnect the affected Ethernet or Wi-Fi connection.
- Etkilenen Ethernet veya Wi-Fi bağlantısını kesin ve yeniden bağlanın.
- Verify that network authentication succeeds.
- Ağ kimlik doğrulamasının başarılı olduğunu kontrol edin.
- Check the NTLM Operational event log for Event ID 4024.
- NTLM Operational olay günlüğünde 4024 olay kimliğini kontrol edin.

**Note / Not:** A restart is not required after changing this registry value according to Microsoft documentation.

Microsoft dokümantasyonuna göre bu kayıt defteri değişikliğinden sonra bilgisayarı yeniden başlatmak gerekmez.

## 5. Security Warning / Güvenlik Uyarısı

**English:**

Setting `BlockNtlmv1SSO` to `0` is a temporary compatibility workaround. It reduces the protection provided by Enforce Mode. The long-term recommendation is to migrate away from MS-CHAPv2-based authentication toward certificate-based authentication such as EAP-TLS.

**Türkçe:**

`BlockNtlmv1SSO` değerini `0` yapmak geçici bir uyumluluk çözümüdür. Zorlama Modu'nun sağladığı güvenlik korumasını azaltır. Uzun vadede MS-CHAPv2 tabanlı kimlik doğrulamadan EAP-TLS gibi sertifika tabanlı kimlik doğrulamaya geçilmesi önerilir.

## References / Kaynaklar

- [Microsoft – Manage NTLMv1 Changes](https://support.microsoft.com/en-us/servicing/os/windows/docs/2025/08/upcoming-changes-to-ntlmv1-in-windows-11-version-24h2-and-windows-server-2025)
- Microsoft – NTLMv1 Değişikliklerini Yönetme

