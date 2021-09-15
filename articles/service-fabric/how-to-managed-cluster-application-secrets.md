---
title: Bereitstellen von Anwendungsgeheimnissen in verwalteten Service Fabric-Clustern
description: Erfahren Sie mehr über Azure Service Fabric-Anwendungsgeheimnisse und deren Bereitstellung in verwalteten Clustern.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 81fbd254f6aee216661e720a73c97e89351a9fad
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867345"
---
# <a name="deploy-application-secrets-to-a-service-fabric-managed-cluster"></a>Bereitstellen von Anwendungsgeheimnissen in verwalteten Service Fabric-Clustern

Geheimnisse beinhalten jegliche Art von vertraulichen Informationen (z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen). Wir empfehlen, Azure Key Vault zu verwenden, um Schlüssel und Geheimnisse für Service Fabric verwaltete Cluster zu verwalten und für diesen Artikel zu nutzen. Die *Verwendung* von Geheimnissen in einer Anwendung ist jedoch cloudplattformunabhängig, sodass Anwendungen auf einem Cluster bereitgestellt werden können, der an einem beliebigen Standort gehostet wird.

Es wird empfohlen, Dienstkonfigurationseinstellungen über [Dienstkonfigurationspakete][config-package] zu verwalten. Konfigurationspakete verfügen über eine Versionsangabe und können über parallele Upgrades aktualisiert werden. Außerdem kann die Integrität überprüft und ein automatischer Rollback durchgeführt werden. Dies wird der globalen Konfiguration vorgezogen, da die Wahrscheinlichkeit eines globalen Dienstausfalls verringert wird. Verschlüsselte Geheimnisse stellen keine Ausnahme dar. Service Fabric verfügt über integrierte Features zum Verschlüsseln und Entschlüsseln von Werten in der Konfigurationspaketdatei „Settings.xml“ mithilfe der Zertifikatverschlüsselung.

Das Diagramm unten zeigt den grundlegenden Ablauf bei der Verwaltung von Geheimnissen in einer Service Fabric-Anwendung:

![Übersicht über die Verwaltung von Geheimnissen][overview]

Dieser Vorgang besteht im Wesentlichen aus vier Schritten:

1. Abrufen eines Datenverschlüsselungszertifikats
2. Installieren des Zertifikats in Ihrem Cluster
3. Verschlüsseln von Geheimnissen bei der Bereitstellung einer Anwendung mit dem Zertifikat und Einfügen dieser Geheimnisse in die Konfigurationsdatei „Settings.xml“ des Diensts
4. Lesen der verschlüsselten Werte aus der Datei „Settings.xml“, indem diese mit demselben Verschlüsselungszertifikat entschlüsselt werden 

[Azure Key Vault][key-vault-get-started] wird hier als sicherer Speicherort für Zertifikate sowie zum Installieren von Zertifikaten auf den verwalteten Service Fabric-Clusterknoten in Azure verwendet.

Ein Beispiel zum Implementieren von Anwendungsgeheimnissen finden Sie unter [Verwalten von Anwendungsgeheimnissen](service-fabric-application-secret-management.md).

Alternativ wird auch [KeyVaultReference](service-fabric-keyvault-references.md) unterstützt. Die KeyVaultReference-Unterstützung von Service Fabric vereinfacht das Bereitstellen von Geheimnissen für Ihre Anwendungen, indem auf die URL des Geheimnisses verwiesen wird, die in Key Vault gespeichert ist.

## <a name="create-a-data-encipherment-certificate"></a>Erstellen eines Datenverschlüsselungszertifikats
Um Ihren eigenen Schlüsseltresor zu erstellen und Zertifikate einzurichten, befolgen Sie die Anweisungen aus Azure Key Vault, indem Sie die [Azure CLI, PowerShell, das Portal und mehr][key-vault-certs] verwenden.

>[!NOTE]
> Der Schlüsseltresor [muss für Vorlagenbereitstellung aktiviert sein](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI), damit der Computeressourcenanbieter Zertifikate daraus abrufen und auf Clusterknoten installieren kann.

## <a name="install-the-certificate-in-your-cluster"></a>Installieren des Zertifikats in Ihrem Cluster
Dieses Zertifikat muss auf jedem Knoten im Cluster installiert sein. Verwaltete Service Fabric-Cluster erleichtern dies. Der verwaltete Clusterdienst kann versionsspezifische Geheimnisse an die Knoten pushen, um Geheimnisse zu installieren, die sich nicht häufig ändern, z. B. die Installation einer privaten Stammzertifizierungsstelle auf den Knoten. Für die meisten Produktionsworkloads wird die Verwendung der [KeyVault-Erweiterung][key-vault-windows] empfohlen. Die Key Vault-VM-Erweiterung ermöglicht die automatische Aktualisierung von Zertifikaten, die in einem Azure-Schlüsseltresor gespeichert sind, im Vergleich zu einer statischen Version.

Für verwaltete Cluster benötigen Sie drei Werte: zwei aus Azure Key Vault und einen Wert, den Sie als Namen des lokalen Speichers auf den Knoten festlegen.

Parameter: 
* `Source Vault`: Dies ist der/die/das 
    * beispielsweise: /subscriptions/{abonnementid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1
* `Certificate URL`: Dies ist der vollständige Objektbezeichner, bei dem Groß-/Kleinschreibung nicht beachtet wird und der unveränderlich ist
    * https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}
* `Certificate Store`: Dies ist der lokale Zertifikatspeicher auf den Knoten, auf denen das Zertifikat platziert wird
    * Name des Zertifikatspeichers auf den Knoten, z. B. „MY“

Verwaltete Service Fabric-Cluster unterstützen zwei Methoden zum Hinzufügen versionsspezifischer Geheimnisse zu Ihren Knoten.

1. Portal während der anfänglichen Clustererstellung, nur Werte aus den oben aufgeführten in diesen Bereich einfügen:

![Eingabe von Geheimnissen im Portal][sfmc-secrets]

2. Azure Resource Manager während der Erstellung oder jederzeit

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "properties": {
        "vmSecrets": [
          {
            "sourceVault": {
              "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
            },
            "vaultCertificates": [
              {
                "certificateStore": "MY",
                "certificateUrl": "https://mykeyvault1.vault.azure.net/certificates/{certificatename}/{secret-version}"
              }
            ]
          }
        ]
    }    
}
```


<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[key-vault-certs]: ../key-vault/certificates/quick-create-cli.md
[config-package]: service-fabric-application-and-service-manifests.md
[key-vault-windows]: ../virtual-machines/extensions/key-vault-windows.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
[sfmc-secrets]:./media/how-to-managed-cluster-application-secrets/sfmc-secrets.png