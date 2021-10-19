---
title: TLS-Terminierung mit Azure Key Vault-Zertifikaten
description: Erfahren Sie, wie Sie Azure Application Gateway mit Key Vault für Serverzertifikate integrieren können, die einem HTTPS-fähigen Listener zugeordnet sind.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 15119b3196735e2358b76c1a804ca25f67b05efa
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809936"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-Terminierung mit Key Vault-Zertifikaten

[Azure Key Vault](../key-vault/general/overview.md) ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und TLS/SSL-Zertifikate schützen können. Azure Application Gateway unterstützt die Integration mit Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. Diese Unterstützung ist auf die v2-SKU von Application Gateway beschränkt.

Application Gateway bietet zwei Modelle für den TLS-Abschluss:

- Bereitstellung von TLS/SSL-Zertifikaten, die an den Listener angefügt sind. Dies ist das traditionelle Modell für die Übergabe von TLS/SSL-Zertifikaten an Application Gateway für die TLS-Terminierung.
- Bereitstellung eines Verweises auf ein vorhandenes Key Vault-Zertifikat oder -Geheimnis, wenn Sie einen HTTPS-fähigen Listener erstellen.

Die Application Gateway-Integration mit Key Vault bietet viele Vorteile. Zu diesen gehören:

- Höhere Sicherheit, da TLS/SSL-Zertifikate nicht direkt vom Anwendungsentwicklungsteam gehandhabt werden. Die Integration ermöglicht einem getrennten Sicherheitsteam Folgendes:
  * Einrichten von Anwendungsgateways
  * Steuern von Application Gateway-Lebenszyklen
  * Erteilen von Berichtigungen für ausgewählte Gateways, damit diese auf Zertifikate zugreifen können, die in Ihrem Schlüsseltresor gespeichert sind
- Unterstützung des Imports vorhandener Zertifikate in Ihren Schlüsseltresor Verwenden von Key Vault-APIs zum Erstellen und Verwalten neuer Zertifikate bei den vertrauenswürdigen Key Vault-Partnern
- Unterstützung für die automatische Verlängerung von Zertifikaten, die in Ihrem Schlüsseltresor gespeichert sind

## <a name="supported-certificates"></a>Unterstützte Zertifikate

Application Gateway unterstützt derzeit nur per Software überprüfte Zertifikate. Durch das Hardwaresicherheitsmodul (HSM) überprüfte Zertifikate werden nicht unterstützt. 

Nachdem Application Gateway für die Verwendung von Key Vault-Zertifikaten konfiguriert wurde, rufen die Instanzen die Zertifikate von Key Vault ab und installieren sie lokal für die TLS-Terminierung. Die Instanzen rufen Key Vault in 4-Stunden-Intervallen ab, um ggf. eine erneuerte Version des Zertifikats zu erhalten. Wenn ein aktualisiertes Zertifikat gefunden wird, wird das derzeit dem HTTPS-Listener zugeordnete TLS/SSL-Zertifikat automatisch rotiert. 

Application Gateway verwendet einen Geheimnisbezeichner in Key Vault, um auf die Zertifikate zu verweisen. Für Azure PowerShell, die Azure CLI oder Azure Resource Manager wird dringend empfohlen, einen Geheimbezeichner zu verwenden, der keine Version angibt. Auf diese Weise rotiert Application Gateway das Zertifikat automatisch, wenn eine neuere Version in Ihrem Schlüsseltresor verfügbar ist. Ein Beispiel für einen Geheimnis-URI ohne Version ist `https://myvault.vault.azure.net/secrets/mysecret/`.

Das Azure-Portal unterstützt nur KeyVault-Zertifikate, keine Geheimnisse. Application Gateway unterstützt weiterhin das Verweisen auf Geheimnisse aus Key Vault, aber nur über Nicht-Portalressourcen wie PowerShell, Azure CLI, APIs und Azure Resource Manager-Vorlagen (ARM-Vorlagen).

> [!WARNING]
> Azure Application Gateway unterstützt derzeit nur Key Vault-Konten im selben Abonnement, in dem sich auch die Application Gateway-Ressource befindet. Das Auswählen eines Schlüsseltresors in einem anderen Abonnement als dem Ihres Application Gateways führt zu einem Fehler.

## <a name="certificate-settings-in-key-vault"></a>Zertifikateinstellungen in Key Vault

Für den TLS-Abschluss unterstützt Application Gateway Zertifikate im PFX-Format (Personal Information Exchange). Sie können dann entweder ein vorhandenes Zertifikat importieren oder ein neues in Ihrem Schlüsseltresor erstellen. Um Ausfälle zu vermeiden, stellen Sie sicher, dass der Status des Zertifikats in Key Vault auf **Aktiviert** festgelegt ist.

## <a name="how-integration-works"></a>Funktionsweise der Integration

Die Application Gateway-Integration in Key Vault erfolgt über einen Konfigurationsprozess in drei Schritten:

![Diagramm, das drei Schritte zur Integration von Application Gateway in Key Vault zeigt.](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Sie erstellen entweder eine benutzerseitig zugewiesene verwaltete Identität oder verwenden eine vorhandene wieder. Application Gateway verwendet die verwaltete Identität, um in Ihrem Auftrag Zertifikate von Key Vault abzurufen. Weitere Informationen finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

In diesem Schritt wird eine neue Identität im Azure Active Directory-Mandanten erstellt. Die Identität wird vom Abonnement, das zum Erstellen der Identität verwendet wird, als vertrauenswürdig eingestuft.

### <a name="configure-your-key-vault"></a>Konfigurieren Ihres Schlüsseltresors

Definieren Sie Zugriffsrichtlinien, um die benutzerseitig zugewiesene verwaltete Identität mit Ihrem Schlüsseltresor zu verwenden:
    
1. Wechseln Sie im Azure-Portal zu **Key Vault**.
1. Öffnen Sie den Bereich **Zugriffsrichtlinien**.
1. Wenn Sie das Berechtigungsmodell **Tresorzugriffsrichtlinie** verwenden: Wählen Sie **+ Zugriffsrichtlinie hinzufügen** aus, wählen Sie für **Geheimnisberechtigungen** die Option **Abrufen** aus, und wählen Sie für **Prinzipal auswählen** Ihre benutzerseitig zugewiesene verwaltete Identität aus. Klicken Sie dann auf **Speichern**.
   
   Bei Verwendung des Berechtigungsmodells **rollenbasierte Zugriffssteuerung in Azure**: Fügen Sie dem Azure-Schlüsseltresor eine Rollenzuweisung für die benutzerseitig zugewiesene verwaltete Identität für die Rolle **Key Vault-Geheimnisbenutzer** hinzu.

Ab dem 15. März 2021 erkennt Key Vault Application Gateway als vertrauenswürdigen Dienst an, indem benutzerseitig verwaltete Identitäten für die Authentifizierung bei Azure Key Vault genutzt werden.  Mit der Verwendung von Dienstendpunkten und der Aktivierung der Option für vertrauenswürdige Dienste für die Firewall des Schlüsseltresors können Sie eine sichere Netzwerkgrenze in Azure erstellen. Sie können den Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) auf Key Vault verweigern, aber weiterhin der Application Gateway-Ressource in Ihrem Abonnement den Zugriff auf Key Vault ermöglichen.

Wenn Sie einen eingeschränkten Schlüsseltresor verwenden, führen Sie die folgenden Schritte aus, um Application Gateway für die Verwendung von Firewalls und virtuellen Netzwerken zu konfigurieren: 

1. Wählen Sie im Azure-Portal in Ihrem Schlüsseltresor **Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** die Option **Privater Endpunkt und ausgewählte Netzwerke** aus.
1. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Vorhandene virtuelle Netzwerke hinzufügen** aus, und fügen Sie dann das virtuelle Netzwerk und Subnetz für Ihre Application Gateway-Instanz hinzu. Konfigurieren Sie während des Prozesses auch den Dienstendpunkt `Microsoft.KeyVault`, indem Sie das zugehörige Kontrollkästchen aktivieren.
1. Wählen Sie **Ja** aus, um vertrauenswürdigen Diensten die Umgehung der Key Vault-Firewall zu gestatten.
  
![Screenshot, der die ausgewählten Optionen für die Konfiguration von Application Gateway für die Verwendung von Firewalls und virtuellen Netzwerken zeigt.](media/key-vault-certs/key-vault-firewall.png)

Wenn Sie die Application Gateway-Instanz über eine ARM-Vorlage, entweder mithilfe der Azure CLI oder mit PowerShell, oder über eine im Azure-Portal bereitgestellte Azure-Anwendung bereitstellen, wird das SSL-Zertifikat im Schlüsseltresor als Base64-codierte PFX-Datei gespeichert. Sie müssen die unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/templates/key-vault-parameter.md) aufgeführten Schritte durchführen. 

Es ist besonders wichtig, `enabledForTemplateDeployment` auf `true` festzulegen. Das Zertifikat hat eventuell ein Kennwort. Im folgenden Beispiel wird eine mögliche Konfiguration für den `sslCertificates`-Eintrag in den `properties` für die Konfiguration der ARM-Vorlage für ein App-Gateway veranschaulicht, wenn ein Zertifikat mit einem Kennwort vorliegt. 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

Die Werte `appGatewaySSLCertificateData` und `appGatewaySSLCertificatePassword` werden, wie unter [Referenzieren von Geheimnissen mit einer dynamischen ID](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id) beschrieben, im Schlüsseltresor gesucht. Sie können die Funktionsweise der Suche nachvollziehen, indem Sie die Verweise von `parameters('secretName')` zurückverfolgen. Wenn das Zertifikat Kennwortlos ist, lassen Sie den `password`-Eintrag aus.

### <a name="configure-application-gateway"></a>Konfigurieren von Application Gateway

Nachdem Sie eine benutzerseitig zugewiesene verwaltete Identität erstellt und Ihren Schlüsseltresor konfiguriert haben, können Sie die verwaltete Identität für Ihre Application Gateway-Instanz über die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM) zuweisen. Informationen zu PowerShell finden Sie unter [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

## <a name="investigating-and-resolving-key-vault-errors"></a>Untersuchen und Beheben von Key Vault-Fehlern

Azure Application Gateway ruft nicht nur alle vier Stunden die erneuerte Zertifikatversion von Key Vault ab. Es protokolliert auch alle Fehler und ist in Azure Advisor integriert, um jegliche Fehlkonfiguration als Empfehlung anzuzeigen. Die Empfehlung enthält Details zum Problem und der zugehörigen Key Vault-Ressource. Sie können diese Informationen zusammen mit dem [Leitfaden zur Problembehandlung](../application-gateway/application-gateway-key-vault-common-errors.md) verwenden, um einen solchen Konfigurationsfehler schnell zu beheben. 

Es wird dringend empfohlen, [Advisor-Warnungen so zu konfigurieren](../advisor/advisor-alerts-portal.md), dass Sie auf dem neuesten Stand bleiben, wenn ein Problem erkannt wird. Um eine Warnung für diesen speziellen Fall einzurichten, verwenden Sie **Beheben eines Azure Key Vault-Problems für Ihre Application Gateway-Instanz** als Empfehlungstyp. 

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von TLS-Terminierung mit Key Vault-Zertifikaten mithilfe von Azure PowerShell](configure-keyvault-ps.md)
