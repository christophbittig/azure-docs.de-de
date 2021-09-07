---
title: Erstellen von Zertifikaten für eine Azure Stack Edge Pro-GPU mit Azure PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit Azure PowerShell Zertifikate für ein Azure Stack Edge Pro-GPU-Gerät erstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364034"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Verwenden von Zertifikaten mit einem Azure Stack Edge Pro-GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird das Verfahren zum Erstellen eigener Zertifikate mithilfe der Azure PowerShell-Cmdlets beschrieben. Der Artikel enthält die Richtlinien, die Sie befolgen müssen, wenn Sie eigene Zertifikate auf einem Azure Stack Edge-Gerät bereitstellen möchten.

Zertifikate stellen sicher, dass die Kommunikation zwischen Ihrem Gerät und Clients, die darauf zugreifen, vertrauenswürdig ist und Sie verschlüsselte Informationen an den richtigen Server senden. Wenn Ihr Azure Stack Edge-Gerät anfänglich konfiguriert ist, werden automatisch selbstsignierte Zertifikate generiert. Optional können Sie Ihre eigenen Zertifikate vorlegen. 

Sie können eine der folgenden Methoden verwenden, um eigene Zertifikate für das Gerät zu erstellen:

 - Verwenden Sie die Azure PowerShell-Cmdlets.
 - Verwenden Sie das Azure Stack Hub Readiness Checker-Tool, um Zertifikatsignieranforderungen (Certificate Signing Request, CSR) zu erstellen, die Ihrer Zertifizierungsstelle das Ausstellen der Zertifikate ermöglichen. 

In diesem Artikel wird nur das Verfahren zum Erstellen eigener Zertifikate mithilfe der Azure PowerShell-Cmdlets beschrieben. 

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie eigene Zertifikate bereitstellen:

- Sie kennen die [Zertifikattypen, die mit Ihrem Azure Stack Edge-Gerät verwendet werden können](azure-stack-edge-gpu-certificates-overview.md).
- Sie haben die [Zertifikatanforderungen für jeden Zertifikattyp](azure-stack-edge-gpu-certificate-requirements.md) überprüft.


## <a name="create-certificates"></a>Erstellen von Zertifikaten

Der folgende Abschnitt beschreibt das Verfahren zum Erstellen von Signaturkettenzertifikaten und Endpunktzertifikaten.


### <a name="certificate-workflow"></a>Zertifikatworkflow

Sie haben eine definierte Möglichkeit, die Zertifikate für die in Ihrer Umgebung betriebenen Geräte zu erstellen. Sie können die Zertifikate verwenden, die Ihnen von Ihrem IT-Administrator zur Verfügung gestellt werden. 

**Nur zu Entwicklungs- oder Testzwecken können Sie Windows PowerShell auch zum Erstellen von Zertifikaten auf Ihrem lokalen System verwenden.** Beachten Sie beim Erstellen der Zertifikate für den Client die folgenden Vorgaben:

1. Sie können eine der folgenden Arten von Zertifikaten erstellen:

    - Erstellen Sie ein einzelnes Zertifikat, das für die Verwendung mit einem einzelnen vollqualifizierten Domänennamen (FQDN) gültig ist. Beispiel: *mydomain.com*.
    - Erstellen Sie ein Platzhalterzeichenzertifikat, um den Hauptdomänennamen und auch mehrere Unterdomänen zu sichern. Beispiel: * *.mydomain.com*.
    - Erstellen Sie ein SAN-Zertifikat (alternativer Antragstellername), das mehrere Domänennamen in einem einzelnen Zertifikat abdeckt. 

2. Wenn Sie Ihr eigenes Zertifikat bereitstellen, benötigen Sie ein Stammzertifikat für die Signaturkette. Weitere Informationen finden Sie unter den Schritten zum [Erstellen von Signaturkettenzertifikaten](#create-signing-chain-certificate).

3. Dann können Sie die Endpunktzertifikate für die lokale Benutzeroberfläche, für das Blob und für Azure Resource Manager erstellen. Sie können drei separate Zertifikate für die Appliance, das Blob und Azure Resource Manager erstellen, oder Sie können ein Zertifikat für alle drei Endpunktzertifikate erstellen. Detaillierte Schritte finden Sie unter [Erstellen von Signatur- und Endpunktzertifikaten](#create-signed-endpoint-certificates).

4. Unabhängig davon, ob Sie drei separate Zertifikate oder ein Zertifikat erstellen, geben Sie die Antragstellernamen (SN) und die alternativen Antragstellernamen (SAN) gemäß den Richtlinien an, die für jeden Zertifikattyp bereitgestellt werden. 

### <a name="create-signing-chain-certificate"></a>Erstellen von Signaturkettenzertifikaten

Erstellen Sie diese Zertifikate über Windows PowerShell, das im Administratormodus ausgeführt wird. **Die auf diese Weise erstellten Zertifikate sollten nur für Entwicklungs- oder Testzwecke verwendet werden.**

Das Signaturkettenzertifikat muss nur einmal erstellt werden. Die anderen Endpunktzertifikate verweisen für die Signatur auf dieses Zertifikat.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Erstellen signierter Endpunktzertifikate

Erstellen Sie diese Zertifikate über Windows PowerShell, das im Administratormodus ausgeführt wird.

In diesen Beispielen werden Endpunktzertifikate für ein Gerät mit Folgendem erstellt:
    - Gerätename: `DBE-HWDC1T2`
    - DNS-Domäne: `microsoftdatabox.com`

Ersetzen Sie sie durch den Namen und die DNS-Domäne für Ihr Gerät, um Zertifikate für Ihr Gerät zu erstellen.
 
**Blobendpunktzertifikat**

Erstellen Sie ein Zertifikat für den Blobendpunkt in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager-Endpunktzertifikat**

Erstellen Sie ein Zertifikat für die Azure Resource Manager-Endpunkte in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Lokales Webbenutzeroberflächen-Gerätezertifikat**

Erstellen Sie in Ihrem persönlichen Speicher ein Zertifikat für die lokale Webbenutzeroberfläche des Geräts.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Ein einzelnes Zertifikat für mehrere alternative Antragstellernamen für alle Endpunkte**

Erstellen Sie ein einzelnes Zertifikat für alle Endpunktzertifikate in Ihrem persönlichen Speicher.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Nachdem die Zertifikate erstellt wurden, besteht der nächste Schritt darin, die Zertifikate auf Ihr Azure Stack Edge Pro-GPU-Gerät hochzuladen.

## <a name="next-steps"></a>Nächste Schritte

[Hochladen von Zertifikaten auf das Gerät](azure-stack-edge-gpu-manage-certificates.md)
