---
title: TLS-Terminierung mit Azure Key Vault-Zertifikaten
description: Erfahren Sie, wie Sie Azure Application Gateway mit Key Vault für Serverzertifikate integrieren können, die einem HTTPS-fähigen Listener zugeordnet sind.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: cf0afce3c5495199b23059df226115154dade7f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339507"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-Terminierung mit Key Vault-Zertifikaten

[Azure Key Vault](../key-vault/general/overview.md) ist ein als Plattform verwalteter Geheimnisspeicher, mit dem Sie Geheimnisse, Schlüssel und TLS/SSL-Zertifikate schützen können. Azure Application Gateway unterstützt die Integration mit Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. Diese Unterstützung ist auf die v2-SKU von Application Gateway beschränkt.

Die Key Vault-Integration bietet zwei Modelle für die TLS-Terminierung:

- Sie können explizit TLS/SSL-Zertifikate angeben, die an den Listener angefügt sind. Dies ist das traditionelle Modell für die Übergabe von TLS/SSL-Zertifikaten an Application Gateway für die TLS-Terminierung.
- Sie können bei der Erstellung des HTTPS-fähigen Listeners optional einen Verweis auf ein vorhandenes Key Vault-Zertifikat oder -Geheimnis angeben.

Die Application Gateway-Integration mit Key Vault bietet viele Vorteile. Zu diesen gehören:

- Höhere Sicherheit, da TLS/SSL-Zertifikate nicht direkt vom Anwendungsentwicklungsteam gehandhabt werden. Die Integration ermöglicht einem getrennten Sicherheitsteam Folgendes:
  * Einrichten von Anwendungsgateways
  * Steuern von Application Gateway-Lebenszyklen
  * Erteilen von Berichtigungen für ausgewählte Gateways, damit diese auf Zertifikate zugreifen können, die in Ihrem Schlüsseltresor gespeichert sind
- Unterstützung des Imports vorhandener Zertifikate in Ihren Schlüsseltresor Verwenden von Key Vault-APIs zum Erstellen und Verwalten neuer Zertifikate bei den vertrauenswürdigen Key Vault-Partnern
- Unterstützung für die automatische Verlängerung von Zertifikaten, die in Ihrem Schlüsseltresor gespeichert sind

Application Gateway unterstützt derzeit nur per Software überprüfte Zertifikate. Durch das Hardwaresicherheitsmodul (HSM) überprüfte Zertifikate werden nicht unterstützt. Nachdem Application Gateway für die Verwendung von Key Vault-Zertifikaten konfiguriert wurde, rufen die Instanzen die Zertifikate von Key Vault ab und installieren sie lokal für die TLS-Terminierung. Die Instanzen rufen außerdem Key Vault in 4-Stunden-Intervallen ab, um ggf. eine erneuerte Version des Zertifikats zu erhalten. Wenn ein aktualisiertes Zertifikat gefunden wird, wird das derzeit dem HTTPS-Listener zugeordnete TLS/SSL-Zertifikat automatisch rotiert.

> [!NOTE]
> Das Azure-Portal unterstützt nur KeyVault-Zertifikate und keine Geheimnisse. Application Gateway unterstützt weiterhin die Referenzierung von Geheimnissen aus KeyVault, aber nur über Nicht-Portalressourcen wie PowerShell, CLI, API, ARM-Vorlagen usw. 

> [!WARNING]
> Azure Application Gateway unterstützt derzeit nur Key Vault-Konten im selben Abonnement, in dem sich auch die Application Gateway-Ressource befindet. Das Auswählen eines Schlüsseltresors in einem anderen Abonnement als dem Ihres Application Gateways führt zu einem Fehler.

## <a name="how-integration-works"></a>Funktionsweise der Integration

Die Application Gateway-Integration in Key Vault erfolgt über eine Konfiguration in drei Schritten:

1. **Erstellen einer benutzerseitig zugewiesenen verwalteten Identität**

   Sie erstellen eine benutzerseitig zugewiesene verwaltete Identität oder verwenden eine bereits vorhandenen, über die Application Gateway in Ihrem Namen Zertifikate von Key Vault abruft. Weitere Informationen finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). In diesem Schritt wird eine neue Identität im Azure Active Directory-Mandanten erstellt. Die Identität wird vom Abonnement, das zum Erstellen der Identität verwendet wird, als vertrauenswürdig eingestuft.

1. **Konfigurieren Ihres Schlüsseltresors**

   Sie importieren dann entweder ein vorhandenes Zertifikat oder erstellen ein neues in Ihrem Schlüsseltresor. Das Zertifikat wird von Anwendungen verwendet, die über das Anwendungsgateway ausgeführt werden. In diesem Schritt kann auch ein Key Vault-Geheimnis verwendet werden, das auch die Speicherung einer base-64-codierten PFX-Datei ohne Kennwort ermöglicht. Die Verwendung eines „Zertifikat“-Typs wird empfohlen, da für Objekte dieses Typs in Key Vault Funktionen für die automatische Verlängerung zur Verfügung stehen. Nach dem Erstellen eines Zertifikats oder Geheimnisses müssen Sie Zugriffsrichtlinien in der Key Vault-Instanz definieren, damit der Identität der GET-Zugriff auf das Geheimnis gewährt werden kann.
   
   > [!IMPORTANT]
   > Ab dem 15. März 2021 wird Azure Application Gateway von Key Vault als einer der vertrauenswürdigen Dienste anerkannt, sodass Sie eine sichere Netzwerkgrenze in Azure erstellen können. Dadurch haben Sie die Möglichkeit, den Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) auf die Key Vault-Instanz zu verweigern, aber weiterhin der Application Gateway-Ressource in Ihrem Abonnement den Zugriff zu ermöglichen. 

   > Sie können Ihre Application Gateway-Instanz in einem eingeschränkten Key Vault-Netzwerk wie folgt konfigurieren. <br />
   > a) Unter dem Blatt „Netzwerk“ von Key Vault <br />
   > b) Wählen Sie in der Registerkarte „Firewall und virtuelle Netzwerke“ die Option „Privater Endpunkt und ausgewählte Netzwerke“ aus. <br/>
   > c) Verwenden Sie dann „Virtuelle Netzwerke“, und fügen Sie das virtuelle Netzwerk und Subnetz Ihrer Application Gateway-Instanz hinzu. Konfigurieren Sie während des Prozesses auch den Dienstendpunkt „Microsoft.KeyVault“, indem Sie das zugehörige Kontrollkästchen aktivieren. <br/>
   > d) Wählen Sie abschließend „Ja“ aus, damit vertrauenswürdige Dienste die Key Vault-Firewall umgehen können. <br/>
   > 
   > ![Key Vault-Firewall](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Wenn Sie das Anwendungsgateway über eine ARM-Vorlage, entweder mithilfe der Azure CLI oder mit PowerShell, oder über eine im Azure-Portal bereitgestellte Azure-Anwendung bereitstellen, wird das SSL-Zertifikat im Schlüsseltresor als Base64-codierte PFX-Datei gespeichert. Sie müssen die unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/templates/key-vault-parameter.md) aufgeführten Schritte durchführen. 
   >
   > Es ist besonders wichtig, `enabledForTemplateDeployment` auf `true` festzulegen. Das Zertifikat kann ein Kennwort aufweisen oder Kennwortlos sein. Im folgenden Beispiel wird eine mögliche Konfiguration für den `sslCertificates`-Eintrag in den `properties`-Eigenschaften für die Konfiguration der ARM-Vorlage für ein App-Gateway veranschaulicht, wenn ein Zertifikat mit einem Kennwort vorliegt. Die Werte `appGatewaySSLCertificateData` und `appGatewaySSLCertificatePassword` werden wie im Abschnitt [Referenzieren von Geheimnissen mit einer dynamischen ID](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id) beschrieben im Schlüsseltresor gesucht. Sie können die Funktionsweise der Suche nachvollziehen, indem Sie die Verweise von `parameters('secretName')` zurückverfolgen. Wenn das Zertifikat Kennwortlos ist, lassen Sie den `password`-Eintrag aus.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Konfigurieren der Application Gateway-Instanz**

   Nachdem Sie die beiden vorherigen Schritte abgeschlossen haben, können Sie ein vorhandenes Anwendungsgateway einrichten oder ändern, um die vom Benutzer zugewiesene verwaltete Identität zu verwenden. Weitere Informationen finden Sie unter [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Sie können auch das TLS/SSL-Zertifikat des HTTP-Listeners so konfigurieren, dass es auf den vollständigen URI des Key Vault-Zertifikats oder der Geheimnis-ID zeigt.

   ![Key Vault-Zertifikate](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von TLS-Terminierung mit Key Vault-Zertifikaten mithilfe von Azure PowerShell](configure-keyvault-ps.md)
