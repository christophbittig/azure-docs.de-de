---
title: Cloud Services (klassisch) und Verwaltungszertifikate | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Erstellen und Bereitstellen von Zertifikaten für Clouddienste und die Authentifizierung mit der Verwaltungs-API in Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e96d3219668475760556c209b3d7a4d59da1b275
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423354"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Übersicht über Zertifikate für Azure Cloud Services (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Zertifikate werden in Azure für Clouddienste verwendet ([Dienstzertifikate](#what-are-service-certificates)) und für die Authentifizierung mit der Verwaltungs-API genutzt ([Verwaltungszertifikate](#what-are-management-certificates)). Dieses Thema bietet eine allgemeine Übersicht über beide Zertifikattypen sowie über deren [Erstellung](#create) und Bereitstellung in Azure.

Die in Azure verwendeten Zertifikate sind X.509 v3-Zertifikate und können von einem anderen vertrauenswürdigen Zertifikat signiert werden oder selbstsigniert sein. Ein selbstsigniertes Zertifikat wird vom eigenen Ersteller signiert und ist daher standardmäßig nicht vertrauenswürdig. Die meisten Browser können dieses Problem ignorieren. Selbstsignierte Zertifikate sollten Sie nur beim Entwickeln und Testen Ihrer Clouddienste verwenden. 

Die in Azure verwendeten Zertifikate können einen öffentlichen Schlüssel enthalten. Zertifikate verfügen über einen Fingerabdruck, durch den sie eindeutig identifiziert werden. Mithilfe dieses Fingerabdrucks wird in der Azure- [Konfigurationsdatei](cloud-services-configure-ssl-certificate-portal.md) ermittelt, welches Zertifikat ein Clouddienst verwenden soll. 

>[!Note]
>Azure Cloud Services akzeptiert keine mit AES256-SHA256 verschlüsselten Zertifikate.

## <a name="what-are-service-certificates"></a>Was sind Dienstzertifikate?
Dienstzertifikate werden an Clouddienste angefügt und ermöglichen die sichere Kommunikation zu und von den Diensten. Wenn Sie beispielsweise eine Webrolle bereitgestellt haben, sollten Sie ein Zertifikat angeben, das einen verfügbar gemachten HTTPS-Endpunkt authentifizieren kann. Dienstzertifikate, die in der Dienstdefinition definiert sind, werden automatisch auf dem virtuellen Computer bereitgestellt, auf dem eine Instanz der Rolle ausgeführt wird. 

Sie können Dienstzertifikate entweder über das Azure-Portal oder mithilfe des klassischen Bereitstellungsmodells in Azure hochladen. Dienstzertifikate sind einem bestimmten Clouddienst zugeordnet. Sie sind einer Bereitstellung in der Dienstdefinitionsdatei zugewiesen.

Dienstzertifikate können gesondert von Ihren Diensten sowie von verschiedenen Personen verwaltet werden. Beispielsweise kann ein Entwickler ein Dienstpaket hochladen, das auf ein Zertifikat verweist, das ein IT-Manager zuvor in Azure hochgeladen hat. Ein IT-Manager kann dieses Zertifikat verwalten und erneuern (die Konfiguration des Diensts ändern), ohne ein neues Dienstpaket hochladen zu müssen. Das Aktualisieren ohne ein neues Dienstpaket ist möglich, da der logische Name, der Speichername und der Speicherort des Zertifikats in der Dienstdefinitionsdatei angegeben sind, während der Zertifikatfingerabdruck in der Dienstkonfigurationsdatei angegeben ist. Um das Zertifikat zu aktualisieren, muss lediglich ein neues Zertifikat hochgeladen und der Fingerabdruckwert in der Dienstkonfigurationsdatei geändert werden.

>[!Note]
>Der Artikel [Häufig gestellte Fragen zu Cloud Services: Konfiguration und Verwaltung](cloud-services-configuration-and-management-faq.yml) enthält einige nützliche Informationen zu Zertifikaten.

## <a name="what-are-management-certificates"></a>Was sind Verwaltungszertifikate?
Verwaltungszertifikate ermöglichen Ihnen die Authentifizierung mit dem klassischen Bereitstellungsmodell. Diese Zertifikate werden in vielen Programmen und Tools (z.B. Visual Studio oder Azure SDK) zum Automatisieren der Konfiguration und Bereitstellung verschiedener Azure-Dienste verwendet. Diese stehen eigentlich nicht in Zusammenhang mit Clouddiensten. 

> [!WARNING]
> Vorsicht ist geboten! Alle Personen, die die Authentifizierung mit diesen Zertifikaten durchführen, können das zugeordnete Abonnement verwalten. 
> 
> 

### <a name="limitations"></a>Einschränkungen
Pro Abonnement sind maximal 100 Verwaltungszertifikate zulässig. Ebenso sind maximal 100 Verwaltungszertifikate für alle Abonnements unter der Benutzer-ID eines bestimmten Dienstadministrators zulässig. Wenn über die Benutzer-ID für den Kontoadministrator bereits 100 Verwaltungszertifikate hinzugefügt wurden und weitere Zertifikate benötigt werden, können Sie einen Co-Administrator festlegen, um die zusätzlichen Zertifikate hinzuzufügen. 

Darüber hinaus können Verwaltungszertifikate nicht mit CSP-Abonnements verwendet werden, da CSP-Abonnements nur das Azure Resource Manager-Bereitstellungsmodell unterstützen und Verwaltungszertifikate das klassische Bereitstellungsmodell verwenden. Weitere Informationen zu Ihren Optionen für CSP-Abonnements finden Sie unter [Azure Resource Manager vs. klassisches Bereitstellungsmodell](/azure/azure-resource-manager/management/deployment-models) und [Authentifizierung mit dem Azure SDK für .NET verstehen](/dotnet/azure/sdk/authentication).

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Erstellen eines neuen selbstsignierten Zertifikats
Ein selbstsigniertes Zertifikat können Sie mit allen verfügbaren Tools erstellen, sofern die folgenden Einstellungen beachtet werden:

* Es muss sich um ein X.509-Zertifikat handeln.
* Es enthält einen öffentlichen Schlüssel.
* Es ist für den Schlüsselaustausch erstellt (PFX-Datei).
* Der Name des Antragstellers muss der Domäne entsprechen, über die auf den Clouddienst zugegriffen wird.

    > Sie können kein TLS/SSL-Zertifikat für die Domäne „cloudapp.net“ (oder für eine andere Domäne in Zusammenhang mit Azure) beziehen; der Name des Antragstellers für das Zertifikat muss mit dem benutzerdefinierten Domänennamen übereinstimmen, mit dem auf Ihre Anwendung zugegriffen wird. Beispielsweise **contoso.net**, nicht **contoso.cloudapp.net**.

* Mindestens 2048-Bit-Verschlüsselung.
* **Nur Dienstzertifikat**: Das clientseitige Zertifikat muss sich im *persönlichen* Zertifikatspeicher befinden.

Es gibt zwei einfache Möglichkeiten zum Erstellen eines Zertifikats unter Windows: mithilfe des Dienstprogramms `makecert.exe` oder mit IIS.

### <a name="makecertexe"></a>makecert.exe
Dieses Hilfsprogramm ist veraltet und wird hier nicht länger beschrieben. Weitere Informationen dazu finden Sie in [diesem MSDN-Artikel](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Wenn Sie das Zertifikat mit einer IP-Adresse anstelle einer Domäne verwenden möchten, verwenden Sie die IP-Adresse im Parameter -DnsName.


Wenn Sie dieses [Zertifikat mit dem Verwaltungsportal](/previous-versions/azure/azure-api-management-certs)verwenden möchten, exportieren Sie es in eine **CER** -Datei:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internetinformationsdienste (IIS)
Im Internet wird auf vielen Seiten erläutert, wie mit IIS Zertifikate erstellt werden können. [Hier](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) eine Seite, auf der dies nach meinem Empfinden anschaulich erklärt wird. 

### <a name="linux"></a>Linux
[diesem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Artikel wird beschrieben, wie Zertifikate mit SSH erstellt werden.

## <a name="next-steps"></a>Nächste Schritte
[Hochladen des Dienstzertifikats in das Azure-Portal](cloud-services-configure-ssl-certificate-portal.md).

Hochladen des [Verwaltungs-API-Zertifikats](/previous-versions/azure/azure-api-management-certs) in das Azure-Portal.
