---
title: Zertifikate in der App Service-Umgebung
description: Hier werden Themen rund um Zertifikate in einer App Service-Umgebung erläutert. Informieren Sie sich über die Funktionsweise von Zertifikatbindungen auf Apps mit einem einzelnen Mandanten in einer App Service-Umgebung.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: a86a5e67faafc88c837f7401db473b0164125a82
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526746"
---
# <a name="certificates-and-the-app-service-environment"></a>Zertifikate und die App Service-Umgebung 
> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
>

Die App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service, die in Ihrem virtuellen Azure-Netzwerk ausgeführt wird. Sie kann mit einem über das Internet zugänglichen Anwendungsendpunkt oder einem Anwendungsendpunkt in Ihrem virtuellen Netzwerk bereitgestellt werden. Wenn Sie die ASE mit einem über das Internet zugänglichen Endpunkt bereitstellen, wird diese Bereitstellung als externe ASE bezeichnet. Wenn Sie die ASE mit einem Endpunkt in Ihrem virtuellen Netzwerk bereitstellen, wird diese Bereitstellung als IBL-ASE bezeichnet. Weitere Informationen zur ILB-ASE finden Sie im Dokument [Erstellen und Verwenden einer ILB-ASE](./creation.md).

## <a name="application-certificates"></a>Anwendungszertifikate

In einer ASE gehostete Apps können die anwendungsorientierten Zertifikatsfeatures nutzen, die im mehrinstanzenfähigen App Service verfügbar sind. Zu den Features zählen:

- SNI-Zertifikate
- KeyVault-gehostete Zertifikate

Voraussetzungen und Anweisungen zum Hochladen und Verwalten dieser Zertifikate finden Sie unter [Hinzufügen eines TLS/SSL-Zertifikats in Azure App Service](../configure-ssl-certificate.md).

Nach dem Hinzufügen eines Zertifikats zu Ihrer App Service- oder Funktions-App können Sie damit einen [benutzerdefinierten Domänennamen schützen](../configure-ssl-bindings.md) oder es [in Ihrem Anwendungscode verwenden](../configure-ssl-certificate-in-code.md).

## <a name="tls-settings"></a>TLS-Einstellungen

Sie können die [TLS-Einstellung auf App-Ebene konfigurieren](../configure-ssl-bindings.md#enforce-tls-versions).

## <a name="private-client-certificate"></a>Privates Clientzertifikat

Ein häufiger Anwendungsfall ist die Konfiguration Ihrer App als Client in einem Client/Server-Modell. Wenn Sie Ihren Server mit einem privaten CA-Zertifikat schützen, müssen Sie das Clientzertifikat in Ihre App hochladen. Die folgenden Anweisungen laden Zertifikate in den Truststore der Worker, in dem Ihre App ausgeführt wird. Wenn Sie das Zertifikat in eine App laden, können Sie es mit Ihren anderen Apps im gleichen App Service-Plan verwenden, ohne das Zertifikat erneut hochzuladen.

>[!NOTE]
> Private Clientzertifikate werden außerhalb der App nicht unterstützt. Dadurch wird die Verwendung in Szenarien wie dem Pullen des App-Containerimages aus einer Registrierung mithilfe eines privaten Zertifikats und der TLS-Überprüfung über die Front-End-Server mithilfe eines privaten Zertifikats eingeschränkt.

Führen Sie die folgenden Schritte aus, um das Zertifikat (Datei vom Typ *.cer*) in Ihre App in der ASE hochzuladen. Die Datei vom Typ *.cer* kann aus Ihrem Zertifikat exportiert werden. Zu Testzwecken gibt es am Ende ein PowerShell-Beispiel, um ein temporäres selbstsigniertes Zertifikat zu generieren:

1. Wechseln Sie zu der App im Azure-Portal, die das Zertifikat benötigt.
1. Navigieren Sie in der App zu **TLS-/SSL-Einstellungen**. Klicken Sie auf **Öffentliches Schlüsselzertifikat (CER)** . Wählen Sie **Öffentliches Schlüsselzertifikat hochladen** aus. Geben Sie einen Namen ein. Navigieren Sie zu Ihrer *CER*-Datei und wählen Sie sie aus. Wählen Sie „Hochladen“ aus. 
1. Kopieren Sie den Fingerabdruck.
1. Navigieren Sie zu **Anwendungseinstellungen**. Erstellen Sie die Anwendungseinstellung WEBSITE_LOAD_ROOT_CERTIFICATES mit dem Fingerabdruck als Wert. Wenn Sie über mehrere Zertifikate verfügen, können Sie diese in derselben Einstellung einfügen, getrennt durch Kommas und ohne Leerzeichen wie 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Das Zertifikat steht allen Apps im gleichen App Service-Plan zur Verfügung wie die App, die diese Einstellung konfiguriert hat. Wenn es für Apps in einem anderen App Service-Plan verfügbar sein soll, müssen Sie den Vorgang der Anwendungseinstellung in einer App in diesem App Service-Plan wiederholen. Um zu überprüfen, ob das Zertifikat festgelegt ist, wechseln Sie zur Kudu-Konsole, und geben Sie den folgenden Befehl in die PowerShell-Debugkonsole ein:

```azurepowershell-interactive
dir Cert:\LocalMachine\Root
```

Sie können zum Testen ein selbstsigniertes Zertifikat erstellen und eine *CER*-Datei mit der folgenden PowerShell generieren: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -CertStoreLocation "Cert:\LocalMachine\My" -DnsName "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "Cert:\LocalMachine\My\" + $certificate.Thumbprint
$fileName = "exportedcert.cer"
Export-Certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Verwendung von Zertifikaten im Anwendungscode](../configure-ssl-certificate-in-code.md).