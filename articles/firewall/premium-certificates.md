---
title: Azure Firewall Premium-Zertifikate
description: Zum ordnungsgemäßen Konfigurieren der TLS-Inspektion für Azure Firewall Premium müssen Zertifikate von Zwischenzertifizierungsstellen konfiguriert und installiert werden.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: victorh
ms.openlocfilehash: 0826e58f4b61e0a99064226a8488f1c3fb499cd7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346587"
---
# <a name="azure-firewall-premium-certificates"></a>Azure Firewall Premium-Zertifikate 

Um die TLS-Inspektion für Azure Firewall Premium ordnungsgemäß zu konfigurieren, muss ein gültiges Zertifikat der Zwischenzertifizierungsstelle bereitgestellt und in Azure Key Vault gespeichert werden.

## <a name="certificates-used-by-azure-firewall-premium"></a>Von Azure Firewall Premium verwendete Zertifikate

Es gibt drei Arten von Zertifikaten, die in einer typischen Bereitstellung verwendet werden:

- **Zertifikat der Zwischenzertifizierungsstelle (CA-Zertifikat)**

   Eine Zertifizierungsstelle (Certificate Authority, CA) ist eine vertrauenswürdige Organisation zum Signieren von digitalen Zertifikaten. Eine Zertifizierungsstelle überprüft die Identität und Legitimität eines Unternehmens oder einer Person, von denen ein Zertifikat angefordert wird. Ist die Überprüfung erfolgreich, stellt die Zertifizierungsstelle ein signiertes Zertifikat aus. Wenn der Server dem Client (z. B. Ihrem Webbrowser) das Zertifikat während eines SSL/TLS-Handshakes vorlegt, versucht der Client, die Signatur anhand einer Liste von *bekannten geeigneten* Signaturgebern zu überprüfen. Webbrowser verfügen normalerweise über Listen von Zertifizierungsstellen, denen sie implizit vertrauen, um Hosts zu identifizieren. Wenn die Zertifizierungsstelle nicht in der Liste enthalten ist, wie bei einigen Websites, die ihre eigenen Zertifikate signieren, warnt der Browser den Benutzer, dass das Zertifikat nicht von einer anerkannten Zertifizierungsstelle signiert ist, und fragt den Benutzer, ob er die Kommunikation mit der nicht verifizierten Website fortsetzen möchte.

- **Serverzertifikat (Websitezertifikat)**

   Ein Zertifikat, das mit einem bestimmten Domänennamen verknüpft ist. Wenn eine Website ein gültiges Zertifikat aufweist, bedeutet dies, dass eine Zertifizierungsstelle Schritte unternommen hat, um zu überprüfen, dass die Webadresse tatsächlich zu dieser Organisation gehört. Wenn Sie eine URL eingeben oder einem Link zu einer sicheren Website folgen, überprüft Ihr Browser das Zertifikat auf die folgenden Merkmale:
   - Die Adresse der Website stimmt mit der Adresse auf dem Zertifikat überein.
   - Das Zertifikat ist von einer Zertifizierungsstelle signiert, die der Browser als *vertrauenswürdig* erkennt.
   
   Gelegentlich können Benutzer eine Verbindung mit einem Server mit einem nicht vertrauenswürdigen Zertifikat herstellen. Azure Firewall trennt die Verbindung, als ob der Server die Verbindung beendet hätte.

- **Zertifikat der Stammzertifizierungsstelle (Stammzertifikat)**

   Eine Zertifizierungsstelle kann mehrere Zertifikate in Form einer Baumstruktur ausstellen. Ein Stammzertifikat ist das oberste Zertifikat der Baumstruktur.

Azure Firewall Premium kann ausgehenden HTTP/S-Datenverkehr abfangen und automatisch ein Serverzertifikat für `www.website.com` generieren. Dieses Zertifikat wird mit dem von Ihnen bereitgestellten Zertifikat der Zwischenzertifizierungsstelle generiert. Browser- und Clientanwendungen der Endbenutzer müssen dem Zertifikat der Stammzertifizierungsstelle oder dem Zertifikat der Zwischenzertifizierungsstelle Ihrer Organisation vertrauen, damit dieses Verfahren funktioniert. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Zertifikatprozess":::

## <a name="intermediate-ca-certificate-requirements"></a>Anforderungen an das Zertifikat der Zwischenzertifizierungsstelle

Stellen Sie sicher, dass Ihr Zertifikat der Zertifizierungsstelle die folgenden Anforderungen erfüllt:

- Bei der Bereitstellung als Key Vault-Geheimnis müssen Sie PFX (Pkcs12) ohne Kennwort mit einem Zertifikat und einem privaten Schlüssel verwenden.

- Es muss ein einzelnes Zertifikat sein und sollte nicht die gesamte Zertifikatskette umfassen.  

- Es muss ein Jahr lang gültig sein.  

- Es muss ein privater RSA-Schlüssel mit einer Mindestgröße von 4096 Bytes sein.  

- Er muss die `KeyUsage`-Erweiterung aufweisen, die mit dem `KeyCertSign`-Flag als „Kritisch“ gekennzeichnet ist (RFC 5280; 4.2.1.3 Schlüsselverwendung).

- Es muss mit der `BasicContraints`-Erweiterung als „Kritisch“ gekennzeichnet sein (RFC 5280; 4.2.1.9 Basiseinschränkungen).  

- Das Flag `CA` muss auf TRUE festgelegt werden.

- Die Pfadlänge muss größer als oder gleich 1 sein.

## <a name="azure-key-vault"></a>Azure-Schlüsseltresor

[Azure Key Vault](../key-vault/general/overview.md) ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und TLS/SSL-Zertifikate schützen können. Azure Firewall Premium unterstützt die Integration mit Key Vault für Serverzertifikate, die an eine Firewallrichtlinie angefügt sind.
 
So konfigurieren Sie Ihren Schlüsseltresor

- Sie müssen ein vorhandenes Zertifikat mit seinem Schlüsselpaar in Ihren Schlüsseltresor importieren. 
- Alternativ kann auch ein Key Vault-Geheimnis verwendet werden, das als Base64-codierte PFX-Datei ohne Kennwort gespeichert ist.  Eine PFX-Datei ist ein digitales Zertifikat, das sowohl den privaten als auch den öffentlichen Schlüssel enthält.
- Es wird empfohlen, einen Zertifikatsimport einer Zertifizierungsstelle zu verwenden, da Sie damit eine Warnung basierend auf dem Ablaufdatum des Zertifikats konfigurieren können.
- Nachdem Sie ein Zertifikat oder ein Geheimnis importiert haben, müssen Sie im Schlüsseltresor Zugriffsrichtlinien definieren, damit die zugewiesene Identität Zugriff auf das Zertifikat/Geheimnis erhält.
- Das bereitgestellte Zertifikat der Zertifizierungsstelle muss von Ihrer Azure-Workload als vertrauenswürdig eingestuft werden. Stellen Sie sicher, dass sie ordnungsgemäß bereitgestellt werden.

Sie können entweder eine benutzerseitig zugewiesene verwaltete Identität erstellen oder eine bereits vorhandene erneut verwenden, über die Azure Firewall in Ihrem Namen Zertifikate von Key Vault abruft. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md). 

## <a name="configure-a-certificate-in-your-policy"></a>Konfigurieren eines Zertifikats in der Richtlinie

Um ein Zertifikat der Zertifizierungsstelle in Ihrer Firewall Premium-Richtlinie zu konfigurieren, wählen Sie Ihre Richtlinie und dann **TLS-Inspektion** aus. Wählen Sie **Aktiviert** auf der Seite **TLS-Inspektion** aus. Wählen Sie dann Ihr Zertifizierungsstellenzertifikat in Azure Key Vault aus, wie in der folgenden Abbildung gezeigt:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure Firewall Premium: Übersichtsdiagramm":::
 
> [!IMPORTANT]
> Wenn Sie ein Zertifikat aus dem Azure-Portal anzeigen und konfigurieren möchten, müssen Sie Ihr Azure-Benutzerkonto zur Key Vault-Zugriffsrichtlinie hinzufügen. Weisen Sie Ihrem Benutzerkonto die Rechte zum **Abrufen** und **Auflisten** unter **Geheimnisberechtigungen** zu.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault-Zugriffsrichtlinie":::

## <a name="create-your-own-self-signed-ca-certificate"></a>Erstellen Ihres eigenen selbstsignierten Zertifizierungsstellenzertifikats

Wenn Sie zum Testen und Überprüfen der TLS-Inspektion Ihre eigenen Zertifikate erstellen möchten, können Sie mithilfe der folgenden Skripts Ihre eigene selbstsignierte Stammzertifizierungsstelle und Zwischenzertifizierungsstelle erstellen.

> [!IMPORTANT]
> Für die Produktion sollten Sie Ihre Unternehmens-PKI zum Erstellen eines Zertifikats der Zwischenzertifizierungsstelle verwenden. Eine Unternehmens-PKI nutzt die vorhandene Infrastruktur und erledigt die Stammzertifizierungsstellenverteilung an alle Endpunktcomputer. Weitere Informationen finden Sie unter [Bereitstellen und Konfigurieren von Zertifikaten der Unternehmenszertifizierungsstelle für Azure Firewall](premium-deploy-certificates-enterprise-ca.md).

Es gibt zwei Versionen dieses Skripts:
- ein Bash-Skript `cert.sh` 
- ein PowerShell-Skript `cert.ps1` 

 Außerdem verwenden beide Skripts die Konfigurationsdatei `openssl.cnf`. Zur Verwendung dieser Skripts kopieren Sie den Inhalt von `openssl.cnf` und `cert.sh` oder `cert.ps1` auf Ihren lokalen Computer.

Die Skripts generieren die folgenden Dateien:
- rootCA.crt/rootCA.key – Öffentliches Zertifikat der Stammzertifizierungsstelle und privater Schlüssel.
- interCA.crt/interCA.key – Öffentliches Zertifikat der Zwischenzertifizierungsstelle und privater Schlüssel
- interCA.pfx –-pkcs12-Paket der Zwischenzertifizierungsstelle, das von der Firewall verwendet wird

> [!IMPORTANT]
> rootCA.key – Sollte an einem sicheren Offlinespeicherort gespeichert werden. Die Skripts generieren ein Zertifikat mit einer Gültigkeit von 1.024 Tagen.
> Für die Skripts müssen OpenSSL-Binärdateien auf dem lokalen Computer installiert werden. Weitere Informationen finden Sie unter https://www.openssl.org/.
> 
Nachdem die Zertifikate erstellt wurden, stellen Sie sie an den folgenden Speicherorten bereit:
- rootCA.crt – Stellen Sie auf Endpunktcomputern bereit (nur öffentliches Zertifikat).
- interCA.pfx – Importieren Sie als Zertifikat auf einer Key Vault und weisen Sie der Firewallrichtlinie zu.

### <a name="opensslcnf"></a>**openssl.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash-Skript – cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell – cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="certificate-auto-generation-preview"></a>Automatische Zertifikatgenerierung (Vorschau)

Für Bereitstellungen außerhalb der Produktion können Sie die automatische Zertifikatgenerierung von Azure Firewall Premium verwenden. Durch diesen Mechanismus werden die drei folgenden Ressourcen automatisch für Sie erstellt:

- Verwaltete Identität
- Key Vault
- Selbstsigniertes Stammzertifikat

Wählen Sie einfach die neue verwaltete Vorschauidentität aus. Daraufhin werden die drei Ressourcen in Ihrer Premium-Richtlinie miteinander verknüpft, und die TLS-Inspektion wird eingerichtet. 

:::image type="content" source="media/premium-certificates/auto-gen-certs.png" alt-text="Automatisch generierte Zertifikate":::

## <a name="troubleshooting"></a>Problembehandlung

Wenn Ihr Zertifizierungsstellenzertifikat gültig ist, aber bei der TLS-Überprüfung der Zugriff auf FQDNs oder URLs nicht möglich ist, sollten Sie folgende Punkte überprüfen:

- Stellen Sie sicher, dass das Webserverzertifikat gültig ist.  

- Stellen Sie sicher, dass das Zertifikat der Stammzertifizierungsstelle auf dem Clientbetriebssystem installiert ist.  

- Stellen Sie sicher, dass der Browser oder HTTPS-Client ein gültiges Stammzertifikat enthält. Firefox und einige andere Browser weisen möglicherweise spezielle Zertifizierungsrichtlinien auf.  

- Stellen Sie sicher, dass der URL-Zieltyp in Ihrer Anwendungsregel den richtigen Pfad und alle anderen auf der HTML-Zielseite eingebetteten Links abdeckt. Sie können Platzhalter verwenden, um den vollständigen erforderlichen URL-Pfad auf einfache Weise abzudecken.  


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über die Features von Azure Firewall Premium](premium-features.md)
