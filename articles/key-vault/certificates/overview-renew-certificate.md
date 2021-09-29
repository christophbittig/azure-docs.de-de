---
title: Informationen zur Azure Key Vault-Zertifikaterneuerung
description: In diesem Artikel wird beschrieben, wie Sie Azure Key Vault-Zertifikate verlängern.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: b2eeca4bb6f5d8af01aa283446961b56d27918d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761667"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Verlängern Ihrer Azure Key Vault-Zertifikate

Azure Key Vault ermöglicht Ihnen das einfache Bereitstellen und Verwalten von digitalen Zertifikaten für Ihr Netzwerk und das Aktivieren der sicheren Kommunikation für Ihre Anwendungen. Weitere Informationen zu Zertifikaten finden Sie unter [Informationen zu Azure Key Vault-Zertifikaten](./about-certificates.md).

Indem Sie Zertifikate mit kurzer Lebensdauer verwenden oder die Häufigkeit der Zertifikatrotation erhöhen, können Sie verhindern, dass nicht autorisierte Benutzer auf Ihre Anwendungen zugreifen.

In diesem Artikel wird beschrieben, wie Sie Ihre Azure Key Vault-Zertifikate verlängern.

## <a name="get-notified-about-certificate-expiration"></a>Erhalten von Benachrichtigungen zum Zertifikatablauf
Sie müssten einen Zertifikatkontakt hinzufügen, um über Ereignisse zur Zertifikatlebensdauer benachrichtigt zu werden. Zertifikatkontakte enthalten Kontaktinformationen zum Senden von Benachrichtigungen, die durch Zertifikatlebensdauer-Ereignisse ausgelöst werden. Die Kontaktinformationen werden von allen Zertifikaten im Schlüsseltresor gemeinsam genutzt. Eine Benachrichtigung zu einem Ereignis eines beliebigen Zertifikats im Schlüsseltresor wird an alle angegebenen Kontakte gesendet.

### <a name="steps-to-set-certificate-notifications"></a>Schritte zum Festlegen von Zertifikatbenachrichtigungen:
Fügen Sie zunächst einen Zertifikatkontakt zu Ihrem Schlüsseltresor hinzu. Sie können zum Hinzufügen das Azure-Portal oder das PowerShell-Cmdlet [Add-AzKeyVaultCertificateContact](/powershell/module/az.keyvault/add-azkeyvaultcertificatecontact) nutzen.

Konfigurieren Sie als Nächstes, wann Sie über den Ablauf des Zertifikats benachrichtigt werden möchten. Informationen zum Konfigurieren der Lebenszyklusattribute des Zertifikats finden Sie unter [Konfigurieren der automatischen Zertifikatrotation in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Wenn für die Richtlinie eines Zertifikats eine automatische Verlängerung festgelegt ist, wird bei den folgenden Ereignissen eine Benachrichtigung gesendet.

- Vor der Zertifikatverlängerung
- Nach der Zertifikatverlängerung, um mitzuteilen, ob das Zertifikat erfolgreich verlängert wurde, oder ob ein Fehler aufgetreten ist, sodass die manuelle Zertifikatverlängerung erforderlich ist.  

  Wenn in der Richtlinie für ein Zertifikat die manuelle Verlängerung festgelegt ist (nur E-Mail), wird eine Benachrichtigung gesendet, sobald das Zertifikat verlängert werden muss.  

In Key Vault gibt es drei Kategorien von Zertifikaten:
-    Zertifikate, die mit einer integrierten Zertifizierungsstelle (ZS) erstellt werden, z. B. DigiCert oder GlobalSign
-    Zertifikate, die mit einer nicht integrierten Zertifizierungsstelle erstellt werden
-    Selbstsignierte Zertifikate

## <a name="renew-an-integrated-ca-certificate"></a>Verlängern eines Zertifikats mit einer integrierten Zertifizierungsstelle 
Azure Key Vault führt die End-to-End-Wartung von Zertifikaten durch, die von den vertrauenswürdigen Microsoft-Zertifizierungsstellen DigiCert und GlobalSign ausgestellt werden. Erfahren Sie, wie Sie eine [vertrauenswürdige Zertifizierungsstelle in Key Vault integrieren](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Verlängern eines Zertifikats mit einer nicht integrierten Zertifizierungsstelle 
Mit Azure Key Vault können Sie Zertifikate von einer beliebigen Zertifizierungsstelle importieren. Dieser Vorteil ermöglicht Ihnen die Integration in mehrere Azure-Ressourcen und eine einfache Bereitstellung. Falls Sie Bedenken haben, dass Sie den Überblick über Ihre Ablaufdaten der Zertifikate verlieren, oder ein Zertifikat bereits abgelaufen ist, können Sie die Schlüsseltresorfunktionen nutzen, um auf dem Laufenden gehalten zu werden. Bei Zertifikaten mit einer nicht integrierten Zertifizierungsstelle können Sie über den Schlüsseltresor E-Mail-Benachrichtigungen einrichten, die kurz vor dem Ablauf gesendet werden. Diese Benachrichtigungen können auch für mehrere Benutzer festgelegt werden.

> [!IMPORTANT]
> Ein Zertifikat ist ein Objekt mit Versionsangabe. Wenn die aktuelle Version abläuft, müssen Sie eine neue Version erstellen. Vom Konzept her handelt es sich bei jeder neuen Version um ein neues Zertifikat, das aus einem Schlüssel und einem Blob besteht, mit dem der Schlüssel mit einer Identität verknüpft wird. Bei Verwendung einer Zertifizierungsstelle, mit der keine Partnerschaft besteht, wird vom Schlüsseltresor ein Schlüssel-Wert-Paar generiert und eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) zurückgegeben.

Gehen Sie wie folgt vor, um ein Zertifikat mit einer nicht integrierten Zertifizierungsstelle zu verlängern:

1. Melden Sie sich beim Azure-Portal an, und öffnen Sie dann das Zertifikat, das Sie verlängern möchten.
1. Wählen Sie im Zertifikatbereich die Option **Neue Version** aus.
1. Wählen Sie **Zertifikatvorgang** aus.
1. Wählen Sie die Option **CSR herunterladen** aus, um eine CSR-Datei auf Ihr lokales Laufwerk herunterzuladen.
1. Senden Sie die CSR an die Zertifizierungsstelle Ihrer Wahl, um die Anforderung zu signieren.
1. Verwenden Sie die signierte Anforderung, und wählen Sie auf demselben Bildschirm „Zertifikatvorgang“ die Option **CSR mergen** aus.

> [!NOTE]
> Es ist wichtig, dass Sie die signierte CSR mit derselben von Ihnen erstellten CSR-Anforderung mergen. Andernfalls ergibt sich für den Schlüssel keine Übereinstimmung.

Weitere Informationen zum Erstellen einer neuen CSR finden Sie unter [Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Key Vault](create-certificate-signing-request.md).

## <a name="renew-a-self-signed-certificate"></a>Verlängern eines selbstsignierten Zertifikats

Von Azure Key Vault wird auch die automatische Verlängerung von selbstsignierten Zertifikaten durchgeführt. Weitere Informationen zur Änderung der Ausstellungsrichtlinie und Aktualisierung der Lebenszyklusattribute eines Zertifikats finden Sie unter [Konfigurieren der automatischen Zertifikatrotation in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Problembehandlung
* Falls sich das ausgestellte Zertifikat im Azure-Portal im Status *Deaktiviert* befindet, sollten Sie unter **Zertifikatvorgang** die Fehlermeldung für das Zertifikat anzeigen.
* Fehlertyp „The CSR used to get your certificate has already been used. Please try to generate a new certificate with a new CSR.“ (Die zum Abrufen Ihres Zertifikats verwendete CSR wurde bereits verwendet. Versuchen Sie, ein neues Zertifikat mit einer neuen CSR zu generieren.)
  Navigieren Sie zum Abschnitt „Erweiterte Richtlinie“ des Zertifikats, und überprüfen Sie, ob die Option **Schlüssel beim Erneuern wiederverwenden?** deaktiviert ist.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Wie kann ich das Feature für die automatische Rotation des Zertifikats testen?**

Erstellen Sie ein selbstsigniertes Zertifikat mit einer Gültigkeitsdauer von **1 Monat**, und legen Sie die Lebensdaueraktion für die Rotation dann auf **1 %** fest. Sie sollten den Verlauf der Zertifikatversion anzeigen können, der in den nächsten Tagen erstellt wird.
  
**Werden die Tags nach der automatischen Verlängerung des Zertifikats repliziert?**

Ja. Die Tags werden nach der automatischen Verlängerung repliziert.

## <a name="next-steps"></a>Nächste Schritte
*    [Integrieren von Key Vault in die DigiCert-Zertifizierungsstelle](how-to-integrate-certificate-authority.md)
*    [Tutorial: Konfigurieren der automatischen Zertifikatrotation in Key Vault](tutorial-rotate-certificates.md)
