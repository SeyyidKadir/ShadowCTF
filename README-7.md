# 🐉 ShadowCTF

**Tarayıcı tabanlı Kali Linux CTF simülasyon laboratuvarı.**  
Kurulum yok. Sunucu yok. Tek HTML dosyası.

🔗 **[Oyna → seyyidkadir.github.io/ShadowCTF](https://seyyidkadir.github.io/ShadowCTF/)**

---

## Nedir?

Gerçek bir Kali Linux masaüstü deneyimi sunan, offline çalışan CTF lab'ı.  
Boot ekranı → GDM login → XFCE masaüstü → Terminal, Firefox, Burp Suite.

Her flag için **2–4 adım** gerekli. Önceki makineden loot toplamadan ilerleyemezsin.  
Şifre, hash, token bulmadan bir sonraki kapı açılmaz.

---

## Özellikler

- **33 hedef makine** — 10 tier, progressive unlock
- **Kali Linux hissi** — XFCE panel, pencereler, sürükle-bırak masaüstü ikonları
- **Loot sistemi** — Her makine bir öncekinin credential'ına bağlı
- **RDP simülasyonu** — Windows makinelere bağlan, mimikatz çalıştır
- **Gerçek araçlar** — sqlmap, nmap, aircrack-ng, impacket, john, netcat
- **Offline** — İnternet bağlantısı gerekmez, ~130KB

---

## Attack Path

```
192.168.1.0/24  (DMZ)
  target1 → SQLi dump → JWT secret
  ftp.shadowlab → anonymous login → config.bak
  mail.shadowlab → open relay → inbox dump → RDP creds
  wp-blog → LFI → vb-forum → Blind SQLi → MongoDB
  WIN-WS01 → RDP + Mimikatz → NTLM → SMB PtH

10.10.0.0/24  (Internal — pivot gerekli)
  Jenkins RCE → prod privesc → mainframe z/OS
  GitLab RCE → Kubernetes escape → Domain Controller DCSync

172.16.0.0/24  (OT/SCADA — VPN gerekli)
  SCADA HMI (EternalBlue) → Siemens PLC S7-300

AWS Cloud
  S3 misconfigured → EC2 IMDSv1 → IAM abuse
  Cobalt Strike C2 panel

Final
  Splunk default creds → HSM key extract → Shadow Root
```

---

## Makine Listesi

| # | IP | Host | Vuln |
|---|-----|------|------|
| 1 | 192.168.1.10 | target1.local | UNION SQLi |
| 2 | 192.168.1.20 | ftp.shadowlab | Anonymous FTP |
| 3 | 192.168.1.22 | mail.shadowlab | SMTP Open Relay |
| 4 | 192.168.1.11 | target2.local | JWT alg:none |
| 5 | 192.168.1.21 | cms.shadowlab | Drupalgeddon2 RCE |
| 6 | 192.168.1.50 | WIN-WS01 | RDP + Mimikatz |
| 7 | 192.168.1.13 | wp-blog.local | LFI + File Upload |
| 8 | 10.10.0.5 | internal-app | SSRF → AWS metadata |
| 9 | 192.168.1.51 | FILE-SRV01 | SMB Pass-the-Hash |
| 10 | 192.168.1.14 | vb-forum.local | Blind SQLi + XSS |
| 11 | 10.10.0.12 | devops-jenkins | Jenkins Groovy RCE |
| 12 | 10.10.1.1 | vpn.shadow.corp | pfSense default creds |
| 13 | 192.168.1.15 | db-server.local | NoSQL injection |
| 14 | 10.10.0.20 | prod-server | Sudo baron samedit |
| 15 | 172.16.0.10 | scada-hmi | EternalBlue + SCADA |
| 16 | 10.10.2.5 | core-sw01 | SNMP + CVE-2023-20198 |
| 17 | ShadowCorp_5G | WiFi | WPA2 handshake crack |
| 18 | 192.168.1.16 | shadow-vault | Buffer overflow |
| 19 | 192.168.100.1 | plc-s7-300 | S7comm no-auth |
| 20 | 10.99.0.1 | fw-paloalto | CVE-2024-3400 |
| 21 | 10.10.0.30 | mainframe | z/OS backdoor |
| 22 | 192.168.1.16:1338 | shadow-vault:1338 | Race condition |
| 23 | 10.10.0.40 | WIN-STAGING | RDP + DCSync |
| 24 | s3.amazonaws.com | shadowcorp-bucket | S3 misconfiguration |
| 25 | 10.10.0.50 | shadow-dc | DCSync Domain Admin |
| 26 | 10.10.0.60 | backup-srv | CVE-2023-27532 Veeam |
| 27 | 54.210.x.x | ec2.shadowcorp.io | IMDSv1 IAM abuse |
| 28 | 10.10.0.70 | siem.shadow.corp | Splunk default creds |
| 29 | 10.10.0.80 | hsm.shadow.corp | HSM default PIN |
| 30 | 185.220.x.x | c2.shadow.io | Cobalt Strike panel |
| 31 | 10.10.0.90 | git.shadow.corp | GitLab CVE-2021-22205 |
| 32 | 10.10.0.95 | k8s.shadow.corp | Kubernetes escape |
| 33 | 10.10.0.100 | shadow-root | Custom backdoor — FINAL |

---

## Nasıl Oynanır

**Online:**
```
https://seyyidkadir.github.io/ShadowCTF/
```

**Offline:**
```bash
# Dosyayı indir, tarayıcıda aç
open index.html

# veya
python3 -m http.server 8080
```

**Giriş:**
```
operator / sh4d0w_0ps
root     / toor
```

**Başlangıç:**
```bash
nmap 192.168.1.0/24        # Ağı tara
missions                   # Görevleri gör
hint 1                     # İlk ipucunu al
loot                       # Bulunan credential'ları gör
```

---

## Öğrenilen Konular

| Kategori | Teknikler |
|----------|-----------|
| Web | SQLi, XSS, LFI, File Upload, IDOR, JWT, SSRF, NoSQL |
| Network | SMB PtH, RDP, SMTP relay, SNMP, VPN |
| Privilege Esc. | Sudo misconfig, EternalBlue, baron samedit |
| Active Directory | DCSync, Pass-the-Hash, Golden Ticket |
| Wireless | WPA2 crack, aircrack-ng |
| Binary | Buffer overflow, ret2libc, race condition |
| ICS/OT | SCADA HMI, Siemens S7-300 |
| Cloud | AWS IMDSv1, S3 misconfig, IAM abuse |
| DevOps | Jenkins, GitLab, Kubernetes |
| Crypto | HSM key extraction, JWT manipulation |

---

## Teknik

- Vanilla HTML/CSS/JavaScript — framework yok
- ~130KB tek dosya
- Offline çalışır
- Chrome, Firefox, Safari, Edge — mobil dahil

---

## ⚠️ Yasal Uyarı

Tamamen simülasyon. Gerçek ağ trafiği oluşturmaz.  
Yalnızca eğitim amaçlıdır.

---

## Lisans

MIT

---

<div align="center">
  <b>ShadowCTF</b> — Learn by hacking. Hack to learn.
</div>
