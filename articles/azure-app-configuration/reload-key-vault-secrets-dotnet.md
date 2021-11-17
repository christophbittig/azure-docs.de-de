---
title: Automatisches erneutes Laden von Geheimnissen und Zertifikaten
titleSuffix: Azure App Configuration
description: Informationen zum Einrichten Ihrer Anwendung, um Geheimnisse und Zertifikate aus Key Vault erneut zu laden
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: 133c788b3bbe787f87cea3e5c72699c8dd0c4f6f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316183"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>Erneutes Laden von Geheimnissen und Zertifikaten aus Key Vault

Die Dienste App Configuration und Key Vault ergänzen sich gegenseitig und werden bei vielen Anwendungen gemeinsam eingesetzt. App Configuration unterstützt Sie beim parallelen Verwenden der Dienste, indem Schlüssel in Ihrem App Configuration-Speicher erstellt werden, mit denen auf in Key Vault gespeicherte Geheimnisse oder Zertifikate verwiesen wird. Da Key Vault das öffentliche und private Schlüsselpaar eines Zertifikats als Geheimnis speichert, kann Ihre Anwendung jedes Zertifikat als Geheimnis von Key Vault abrufen.

In Sachen Sicherheit hat es sich bewährt, [Geheimnisse](../key-vault/secrets/tutorial-rotation.md) und [Zertifikate](../key-vault/certificates/tutorial-rotate-certificates.md) regelmäßig zu rotieren. Sobald diese in Key Vault rotiert wurden, soll Ihre Anwendung die aktuellsten Geheimnis- und Zertifikatswerte abrufen. Es gibt zwei Möglichkeiten, diesen Vorgang durchzuführen, ohne dass Sie Ihre Anwendung neu starten müssen:
- Aktualisieren Sie einen Sentinel-Schlüsselwert, um die Aktualisierung Ihrer gesamten Konfiguration auszulösen und so alle Key Vault-Geheimnisse und Zertifikate neu zu laden. Weitere Informationen finden Sie auf der Seite zur [dynamischen Konfiguration in einer ASP.NET Core-App](./enable-dynamic-configuration-aspnet-core.md).
- Laden Sie regelmäßig einige oder alle Geheimnisse und Zertifikate aus Key Vault neu.

Bei der ersten Option müssen Sie den Sentinel-Schlüsselwert in App Configuration immer dann aktualisieren, wenn Sie Geheimnisse und Zertifikate in Key Vault rotieren. Dieser Ansatz funktioniert gut, wenn Sie einen sofortigen erneuten Ladevorgang von Geheimnissen und Zertifikaten in Ihrer Anwendung erzwingen möchten. Wenn Geheimnisse und Zertifikate automatisch in Key Vault rotiert werden, können für Ihre Anwendung Fehler ausgegeben werden, wenn Sie den Sentinel-Schlüsselwert nicht rechtzeitig aktualisieren. Die zweite Option ermöglicht es Ihnen, diesen Prozess vollständig zu automatisieren. Sie können Ihre Anwendung so konfigurieren, dass sie Geheimnisse und Zertifikate innerhalb einer bestimmten Verzögerungsspanne zur Zeit der Rotation neu aus Key Vault lädt. Dieses Tutorial begleitet Sie durch den Prozess der zweiten Option.

## <a name="prerequisites"></a>Voraussetzungen

- In diesem Tutorial wird gezeigt, wie Sie Ihre Anwendung einrichten, um Geheimnisse und Zertifikate aus Key Vault erneut zu laden Es baut auf Grundlage des Tutorials zum Implementieren von Key Vault-Verweisen in Ihrem Code auf. Bevor Sie fortfahren, beenden Sie zunächst das [Tutorial: Verwenden von Key Vault-Verweisen in einer ASP.NET Core-App](./use-key-vault-references-dotnet-core.md).

- [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore)-Paket v4.4.0 oder höher.


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>Hinzufügen eines automatisch rotierenden Zertifikats zu Key Vault

 Befolgen Sie das [Tutorial zum Konfigurieren der automatischen Zertifikationsrotation in Key Vault](../key-vault/certificates/tutorial-rotate-certificates.md), um ein automatisch rotierendes Zertifikat mit dem Namen **ExampleCertificate** der im vorherigen Tutorial erstellten Key Vault-Instanz hinzuzufügen.


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>Hinzufügen eines Verweises zum Key Vault-Zertifikat in App Configuration

1. Wählen Sie im Azure-Portal die Option **Alle Ressourcen** und dann die Instanz des App Configuration-Speichers aus, die Sie im vorherigen Tutorial erstellt haben.

1. Wählen Sie **Konfigurations-Explorer** aus.

1. Wählen Sie **+ Erstellen** > **Schlüsseltresorverweis** aus, und geben Sie dann die folgenden Werte an:
    - **Schlüssel:** Wählen Sie **TestApp:Settings:KeyVaultCertificate** aus.
    - **Bezeichnung:** Lassen Sie diesen Wert leer.
    - **Abonnement**, **Ressourcengruppe** und **Schlüsselspeicher**: Geben Sie die Werte ein, die der Key Vault-Instanz aus dem vorherigen Tutorial entsprechen.
    - **Geheimnis:** Wählen Sie das Geheimnis mit dem Namen **ExampleCertificate** ein, das Sie im vorherigen Abschnitt erstellt haben.
    - **Version des Geheimnisses:** **Neueste Version**.

> [!Note]
> Wenn Sie auf eine bestimmte Version verweisen, wird durch das erneute Laden des Geheimnisses oder Zertifikats aus Key Vault immer derselbe Wert zurückgegeben.


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>Aktualisieren des Codes zum erneuten Laden von Key Vault-Geheimnissen und -Zertifikaten

Aktualisieren Sie in Ihrer *Program.cs*-Datei die `AddAzureAppConfiguration`-Methode, um ein Aktualisierungsintervall für Ihr Key Vault-Zertifikat mithilfe der `SetSecretRefreshInterval`-Methode einzurichten. Durch diese Änderung lädt Ihre Anwendung alle 12 Stunden das öffentliche- bzw. private Schlüsselpaar für **ExampleCertificate**.

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

Das erste Argument in der Methode `SetSecretRefreshInterval` ist der Schlüssel des Key Vault-Verweises in App Configuration. Dieses Argument ist optional. Wenn die Schlüsselparameter nicht angegeben werden, wird das Aktualisierungsintervall auf alle Geheimnisse und Zertifikat angewendet, für die keine individuellen Aktualisierungsintervalle festgelegt sind.

Ein Aktualisierungsintervall definiert die Häufigkeit, in der Ihre Geheimnisse und Zertifikate neu aus Key Vault geladen werden, unabhängig davon, ob Änderungen an ihren Werten in Key Vault oder App Configuration vorgenommen werden. Wenn Sie Geheimnisse und Zertifikate neu laden möchten, wenn sich deren Wert in App Configuration ändert, können Sie sie mithilfe der `ConfigureRefresh`-Methode überwachen. Weitere Informationen finden Sie auf der Seite zur [dynamischen Konfiguration in einer ASP.NET Core-App](./enable-dynamic-configuration-aspnet-core.md).

Wählen Sie das Aktualisierungsintervall im Hinblick auf die bestimmte Verzögerung aus, nachdem Ihre Geheimnisse und Zertifikate in Key Vault aktualisiert wurden. Es ist ebenso wichtig, die [Key Vault-Dienstbeschränkungen](../key-vault/general/service-limits.md) zu beachten, um eine Drosselung zu vermeiden.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie Ihre Anwendung einrichten, damit sie automatisch Geheimnisse und Zertifikate aus Key Vault lädt. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Managed Identity verwenden, um den Zugriff auf App Configuration und Key Vault zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
