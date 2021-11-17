---
title: 'Tutorial: Verwenden von OpenSSL zum Erstellen von X. 509-Test Zertifikaten für Azure IOT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Verwenden von OpenSSL zum Erstellen von Zertifizierungsstellen-und Geräte Zertifikaten für Azure IOT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5fd735420ad97934e45160f7d57fdc9029faee6a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547627"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Verwenden von OpenSSL zum Erstellen von Testzertifikaten

Obwohl Sie X. 509-Zertifikate von einer vertrauenswürdigen Zertifizierungsstelle erwerben können, ist das Erstellen einer eigenen Test Zertifikat Hierarchie oder die Verwendung selbst signierter Zertifikate ausreichend für das Testen der IOT Hub-Geräte Authentifizierung. Im folgenden Beispiel werden [OpenSSL](https://www.openssl.org/) und das [OpenSSL-Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) verwendet, um eine Zertifizierungsstelle (CA), eine untergeordnete Zertifizierungsstelle und ein Gerätezertifikat zu erstellen. Im Beispiel werden dann die untergeordnete Zertifizierungsstelle und das Gerätezertifikat in einer Zertifikatshierarchie signiert. Dies wird nur zu Beispiel Zwecken dargestellt.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Schritt 1: Erstellen der Verzeichnisstruktur der Stamm Zertifizierungsstelle

Erstellen Sie eine Verzeichnisstruktur für die Zertifizierungsstelle.

* Im **Zertifikate** -Verzeichnis werden neue Zertifikate gespeichert.
* Das Daten **Bankverzeichnis wird für die Zertifikat** Datenbank verwendet.
* Das **private** Verzeichnis speichert den privaten Schlüssel der Zertifizierungsstelle.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Schritt 2: Erstellen einer Konfigurationsdatei der Stamm Zertifizierungsstelle

Erstellen Sie vor dem Erstellen einer Zertifizierungsstelle eine Konfigurationsdatei, und speichern Sie sie als `rootca.conf` im rootca-Verzeichnis.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Schritt 3: Erstellen einer Stamm Zertifizierungsstelle

Generieren Sie zunächst den Schlüssel und die Zertifikat Signier Anforderung (Certificate Signing Request, CSR) im rootca-Verzeichnis.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Als nächstes erstellen Sie ein selbstsigniertes Zertifikat Selbst Signierung eignet sich für Testzwecke. Geben Sie die ca_ext Konfigurationsdatei Erweiterungen in der Befehlszeile an. Diese geben an, dass das Zertifikat für eine Stamm Zertifizierungsstelle gilt und zum Signieren von Zertifikaten und Zertifikat Sperr Listen (CRLs) verwendet werden kann. Signieren Sie das Zertifikat, und übergeben Sie es an die Datenbank.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Schritt 4: Erstellen der untergeordneten Zertifizierungsstellen-Verzeichnisstruktur

Erstellen Sie eine Verzeichnisstruktur für die untergeordnete Zertifizierungsstelle.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Schritt 5: Erstellen einer untergeordneten Zertifizierungsstellen-Konfigurationsdatei

Erstellen Sie eine Konfigurationsdatei, und speichern Sie sie als subca. conf im `subca` Verzeichnis.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days             = 365
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Schritt 6: Erstellen einer untergeordneten Zertifizierungsstelle

Erstellen Sie eine neue Seriennummer in der `rootca/db/serial` Datei für das untergeordnete Zertifizierungsstellen Zertifikat.

```bash
  openssl rand -hex 16 > ../rootca/db/serial
```

>[!IMPORTANT]
>Sie müssen für jedes untergeordnete Zertifizierungsstellen Zertifikat und jedes Gerätezertifikat, das Sie erstellen, eine neue Seriennummer erstellen. Verschiedene Zertifikate dürfen nicht die gleiche Seriennummer aufweisen.

In diesem Beispiel wird gezeigt, wie Sie eine untergeordnete oder Registrierungs Zertifizierungsstelle erstellen. Da Sie die Stamm Zertifizierungsstelle zum Signieren von Zertifikaten verwenden können, ist das Erstellen einer untergeordneten Zertifizierungsstelle nicht unbedingt erforderlich. Das vorhanden sein einer untergeordneten Zertifizierungsstelle imitiert echte Zertifikat Hierarchien, in denen die Stamm Zertifizierungsstelle offline gehalten wird und untergeordnete CAS Client Zertifikate ausstellen.

Verwenden Sie die Konfigurationsdatei zum Generieren eines Schlüssels und einer Zertifikat Signier Anforderung (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Senden Sie die CSR an die Stamm Zertifizierungsstelle, und verwenden Sie die Stamm Zertifizierungsstelle zum Ausstellen und Signieren des untergeordneten Zertifizierungsstellen Zertifikats. Geben Sie sub_ca_ext für den Schalter Erweiterungen in der Befehlszeile an. Diese geben an, dass das Zertifikat für eine Stamm Zertifizierungsstelle gilt und zum Signieren von Zertifikaten und Zertifikat Sperr Listen (CRLs) verwendet werden kann. Bei Aufforderung signieren Sie das Zertifikat, und übergeben Sie es an die Datenbank.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Schritt 7: Nachweis des Besitzes

Sie verfügen nun über ein Zertifikat der Stamm Zertifizierungsstelle und ein untergeordnetes Zertifizierungsstellen Zertifikat. Zum Signieren von Geräte Zertifikaten können Sie beide verwenden. Der von Ihnen gewählte muss in ihren IOT Hub hochgeladen werden. Bei den folgenden Schritten wird davon ausgegangen, dass Sie das untergeordnete ZS-Zertifikat verwenden. So laden Sie Ihr untergeordnetes ZS-Zertifikat hoch und registrieren Sie für Ihr IOT Hub:

1. Navigieren Sie im Azure-Portalzu IoTHub und wählen Sie **Einstellungen > Zertifikate**.

2. Wählen Sie **Hinzufügen**, um das neue untergeordnete Zertifizierungsstellen Zertifikat hinzuzufügen.

3. Geben Sie im Feld **Zertifikat Name** einen Anzeigenamen ein, und wählen Sie das hinzu zufügende PEM-Zertifikat aus.

> [!NOTE]
> Die oben erstellten CRT-Zertifikate sind identisch mit PEM-Zertifikaten. Sie können die Erweiterung einfach ändern, wenn Sie ein Zertifikat hochladen, um den Besitz nachzuweisen. Alternativ können den folgenden OpenSSL-Befehl verwenden:

```bash
openssl x509 -in mycert.crt -out mycert.pem -outform PEM
```

4. Wählen Sie **Speichern** aus. Ihr Zertifikat wird in der Zertifikat Liste mit dem Status nicht überprüft **angezeigt.** Der Überprüfungs Vorgang weist darauf hin, dass Sie das Zertifikat besitzen.

   
5. Wählen Sie das Zertifikat aus, um das Dialogfeld **Zertifikat Details** anzuzeigen.

6. Wählen Sie **Prüfcode generieren**. Weitere Informationen finden Sie unter nach [weisen des Besitzes](tutorial-x509-prove-possession.md)eines ZS-Zertifikats.

7. Kopieren Sie den Prüfcode in die Zwischenablage. Sie müssen den Überprüfungs Code als Zertifikat Antragsteller festlegen. Wenn der Überprüfungs Code z. b. BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A lautet, fügen Sie diesen als Betreff Ihres Zertifikats hinzu, wie in Schritt 9 gezeigt.

8. Erstellen eines privaten Schlüssels.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Generieren einer Zertifikatsignieranforderung (CSR) für einen vorhandenen privaten Schlüssel. Fügen Sie den Überprüfungs Code als Betreff des Zertifikats hinzu.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Erstellen Sie ein Zertifikat mithilfe der Konfigurationsdatei der untergeordneten Zertifizierungsstelle und der CSR-Datei für das Zertifikat zum Besitznachweis.

  ```bash
    openssl ca -config subca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Wählen Sie in der Ansicht **Zertifikat Details** das neue Zertifikat aus. Navigieren Sie zum Ordner "Zertifikate", um die PEM-Datei zu suchen.

12. Klicken Sie nach dem Hochladen des Zertifikats auf **überprüfen**. Der Status des Zertifizierungsstellen Zertifikats sollte in **überprüft** geändert werden.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Schritt 8: Erstellen eines Geräts in ihrer IOT Hub

Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub, und erstellen Sie eine neue IoT-Geräteidentität mit den folgenden Werten:

1. Geben Sie die **Geräte-ID** an, die mit dem Antragstellernamen Ihrer Gerätezertifikate übereinstimmt.

1. Wählen Sie den Authentifizierungstyp **X. 509-CA signiert** aus.

1. Wählen Sie **Speichern** aus.

## <a name="step-9---create-a-client-device-certificate"></a>Schritt 9: Erstellen eines Client Geräte Zertifikats

Zum Generieren eines Client Zertifikats müssen Sie zuerst einen privaten Schlüssel generieren. Der folgende Befehl zeigt, wie OpenSSL benutzt wird um einen privaten Schlüssel zu erstellen. Erstellen Sie den Schlüssel im subca-Verzeichnis.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Erstellen Sie eine Zertifikat Signier Anforderung (CSR) für den Schlüssel. Sie müssen kein Abfrage Kennwort oder einen optionalen Firmennamen eingeben. Sie müssen jedoch die Geräte-ID in das Feld Allgemeiner Name eingeben. Sie können auch eigene Werte für die anderen Parameter wie **Land**, **Organisationsname** usw. eingeben.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Überprüfen Sie, ob die CSR Ihren Erwartungen entspricht.

```bash
openssl req -text -in device.csr -noout
```

Senden Sie die CSR an die untergeordnete Zertifizierungsstelle, um sich bei der Zertifikatshierarchie anzumelden. Geben Sie `client_ext` im `-extensions` Schalter an. Beachten Sie, dass der `Basic Constraints` im ausgestellten Zertifikat anzeigt, dass dieses Zertifikat nicht für eine Zertifizierungsstelle gilt. Wenn Sie mehrere Zertifikate signieren, achten Sie darauf, die Seriennummer zu aktualisieren, bevor Sie jedes Zertifikat mit dem OpenSSL- `rand -hex 16 > db/serial` Befehl erstellen.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum [Testen der Zertifikat Authentifizierung](tutorial-x509-test-certificate.md), um festzustellen, ob Ihr Gerät Ihr Gerät für Ihre IOT Hub authentifizieren kann. Für den Code auf dieser Seite müssen Sie ein PFX-Zertifikat verwenden. Verwenden Sie den folgenden OpenSSL-Befehl, um das CRT-Zertifikat Ihres Geräts in das PFX-Format zu konvertieren.

```bash
openssl pkcs12 -export -in device.crt -inkey device.key -out device.pfx
```