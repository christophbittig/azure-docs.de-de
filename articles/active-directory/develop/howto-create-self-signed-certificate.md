---
title: Erstellen eines selbstsignierten öffentlichen Zertifikats zum Authentifizieren Ihrer Anwendung | Azure
titleSuffix: Microsoft identity platform
description: Erstellen Sie ein selbstsigniertes öffentliches Zertifikat zum Authentifizieren Ihrer Anwendung.
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 3cc084fe1b9df8a4ab4db5b926bb7b44646f17c2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030323"
---
# <a name="create-a-self-signed-public-certificate-to-authenticate-your-application"></a>Erstellen Sie ein selbstsigniertes öffentliches Zertifikat zum Authentifizieren Ihrer Anwendung.

Azure Active Directory (Azure AD) unterstützt zwei Authentifizierungsarten für Dienstprinzipale: **kennwortbasierte Authentifizierung** (App-Geheimnis) und **zertifikatbasierte Authentifizierung**. Obwohl App-Geheimnisse ohne großen Aufwand im Azure-Portal erstellt werden können, wird jedoch empfohlen, für Ihre Anwendung ein Zertifikat zu verwenden.

Zu Testzwecken können Sie anstelle eines von einer Zertifizierungsstelle signierten Zertifikats ein selbstsigniertes öffentliches Zertifikat verwenden. In diesem Artikel erfahren Sie, wie Sie Windows PowerShell zum Erstellen und Exportieren eines selbstsignierten Zertifikats verwenden.

> [!CAUTION]
> Die Verwendung eines selbstsignierten Zertifikats wird nur für die Entwicklung und nicht für die Produktion empfohlen.

Sie konfigurieren verschiedene Parameter für das Zertifikat konfigurieren, z. B. die Kryptografie- und Hashalgorithmen, die Gültigkeitsdauer des Zertifikats und den Domänennamen. Exportieren Sie dann das Zertifikat je nach Anwendungsanforderungen mit oder ohne den jeweiligen privaten Schlüssel. 

Die Anwendung, die die Authentifizierungssitzung initiiert, benötigt den privaten Schlüssel, während die Anwendung, die die Authentifizierung bestätigt, den öffentlichen Schlüssel benötigt. Bei einer Authentifizierung über Ihre PowerShell-Desktop-App bei Azure AD wird also nur der öffentliche Schlüssel (`.cer`-Datei) exportiert und in das Azure-Portal hochgeladen. Ihre PowerShell-App verwendet den privaten Schlüssel aus Ihrem lokalen Zertifikatspeicher, um die Authentifizierung zu initiieren und Zugriffstoken für Microsoft Graph abzurufen.

Ihre Anwendung kann auch von einem anderen Computer ausgeführt werden, z. B. Azure Automation. In diesem Szenario exportieren Sie das öffentliche/private Schlüsselpaar aus Ihrem lokalen Zertifikatspeicher. Laden Sie außerdem den öffentlichen Schlüssel in das Azure-Portal und den privaten Schlüssel (eine `.pfx`-Datei) in Azure Automation hoch. Ihre Anwendung, die in Azure Automation ausgeführt wird, verwendet den privaten Schlüssel, um die Authentifizierung zu initiieren und Zugriffstoken für Microsoft Graph abzurufen.

In diesem Artikel wird das PowerShell-Cmdlet `New-SelfSignedCertificate` zum Erstellen des selbstsignierten Zertifikats und das Cmdlet `Export-Certificate` verwendet, um das Zertifikat an einen Speicherort zu exportieren, der leicht zugänglich ist. Diese Cmdlets sind in moderne Versionen von Windows integriert (Windows 8.1 und höher sowie Windows Server 2012R2 und höher). Das selbstsignierte Zertifikat ist wie folgt konfiguriert:

+ Das Zertifikat weist eine Schlüssellänge von 2048 Bit auf. Obwohl längere Werte unterstützt werden, wird die Größe von 2048 Bit als optimale Kombination von Sicherheit und Leistung empfohlen.
+ Das Zertifikat verwendet den RSA-Kryptografiealgorithmus. Azure AD unterstützt derzeit nur RSA.
+ Das Zertifikat ist mit dem SHA256-Hashalgorithmus signiert. Azure AD unterstützt auch Zertifikate, die mit SHA384- und SHA512-Hashalgorithmen signiert sind.
+ Das Zertifikat ist nur ein Jahr lang gültig.
+ Die Verwendung des Zertifikats wird sowohl für die Client- als auch für die Serverauthentifizierung unterstützt.

> [!NOTE]
> Informationen zum Anpassen des Start- und Ablaufdatums sowie anderer Eigenschaften des Zertifikats finden Sie in der [`New-SelfSignedCertificate`-Referenz](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true).


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>Option 1: Erstellen und Exportieren Ihres öffentlichen Zertifikats ohne privaten Schlüssel

Verwenden Sie das Zertifikat, das Sie mit dieser Methode erstellen, für die Authentifizierung über eine Anwendung, die auf Ihrem Computer ausgeführt wird, z. B. für die Authentifizierung über Windows PowerShell.

Führen Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus, und lassen Sie die PowerShell-Konsolensitzung geöffnet. Ersetzen Sie `{certificateName}` durch den Namen, den Sie Ihrem Zertifikat geben möchten.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

Die **$cert**-Variable im obigen Befehl speichert Ihr Zertifikat in der aktuellen Sitzung und ermöglicht es, das Zertifikat zu exportieren. Mit dem folgenden Befehl wird das Zertifikat im `.cer`-Format exportiert. Sie können es auch in anderen Formaten exportieren, die im Azure-Portal unterstützt werden, z. B. `.pem` und `.crt`.

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

Ihr Zertifikat kann jetzt in das Azure-Portal hochgeladen werden. Rufen Sie nach dem Hochladen den Zertifikatfingerabdruck ab, der für die Authentifizierung Ihrer Anwendung verwendet werden soll.


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>Option 2: Erstellen und Exportieren Ihres öffentlichen Zertifikats mit dem privaten Schlüssel

Verwenden Sie diese Option, um ein Zertifikat und den jeweiligen privaten Schlüssel zu erstellen, wenn Ihre Anwendung von einem anderen Computer oder einer cloudbasierten Anwendung ausgeführt wird, z. B. Azure Automation.

Führen Sie an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten den folgenden Befehl aus, und lassen Sie die PowerShell-Konsolensitzung geöffnet. Ersetzen Sie `{certificateName}` durch den Namen, den Sie Ihrem Zertifikat geben möchten.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

Die **$cert**-Variable im obigen Befehl speichert Ihr Zertifikat in der aktuellen Sitzung und ermöglicht es, das Zertifikat zu exportieren. Mit dem folgenden Befehl wird das Zertifikat im `.cer`-Format exportiert. Sie können es auch in anderen Formaten exportieren, die im Azure-Portal unterstützt werden, z. B. `.pem` und `.crt`.


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

Erstellen Sie in derselben Sitzung ein Kennwort für den privaten Schlüssel des Zertifikats, und speichern Sie es in einer Variablen. Ersetzen Sie im folgenden Befehl `{myPassword}` durch das Kennwort, das Sie zum Schützen des privaten Schlüssels Ihres Zertifikats verwenden möchten.

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

Verwenden Sie nun das Kennwort, das Sie in der Variablen `$mypwd` gespeichert haben, um den privaten Schlüssel zu sichern und zu exportieren.

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

Ihr Zertifikat (`.cer`-Datei) kann jetzt in das Azure-Portal hochgeladen werden. Sie verfügen außerdem über einen privaten Schlüssel (`.pfx`-Datei), der verschlüsselt ist und nicht von anderen Parteien gelesen werden kann. Rufen Sie nach dem Hochladen den Zertifikatfingerabdruck ab, der für die Authentifizierung Ihrer Anwendung verwendet werden soll.


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>Optionale Aufgabe: Löschen Sie das Zertifikat aus dem Keystore.

Wenn Sie das Zertifikat mit Option 2 erstellt haben, können Sie das Schlüsselpaar aus Ihrem persönlichen Speicher löschen. Führen Sie zunächst den folgenden Befehl aus, um den Zertifikatfingerabdruck abzurufen.

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

Kopieren Sie dann den angezeigten Fingerabdruck, und verwenden Sie ihn, um das Zertifikat und den zugehörigen privaten Schlüssel zu löschen.

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>Ermitteln des Ablaufdatums Ihres Zertifikats

Das selbstsignierte Zertifikat, das Sie mit den obigen Schritten erstellt haben, weist eine begrenzte Lebensdauer auf, bevor es abläuft. Im Abschnitt **App-Registrierungen** des Azure-Portals wird das Ablaufdatum des Zertifikats auf dem Bildschirm **Zertifikate & Geheimnisse** angezeigt. Wenn Sie Azure Automation verwenden, wird das Ablaufdatum des Zertifikats auf dem Bildschirm **Zertifikate** des Automation-Kontos angezeigt. Führen Sie die oben beschriebenen Schritte aus, um ein neues selbstsigniertes Zertifikat zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
