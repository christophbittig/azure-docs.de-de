---
title: Entwicklerhandbuch zu Azure-Schlüsseltresor
description: Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 275013b82866b7cb49488edfc0e63e71a04e364a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670346"
---
# <a name="azure-key-vault-developers-guide"></a>Entwicklerhandbuch zu Azure-Schlüsseltresor

Mithilfe des Schlüsseltresors können Sie in Ihren Anwendungen sicher auf vertrauliche Daten zugreifen:

- Schlüssel, Geheimnisse und Zertifikate werden geschützt, ohne dass Sie den Code selbst schreiben müssen, und können auf einfache Weise in Ihren Anwendungen verwendet werden.
- Sie gestatten Kunden, dass sie ihre eigenen Schlüssel, Geheimnisse und Zertifikate verwalten, sodass Sie sich auf die Bereitstellung der Hauptfunktionen der Software konzentrieren können. Auf diese Weise müssen Ihre Anwendungen weder die Verantwortung noch die Haftung für die Mandantenschlüssel, Geheimnisse und Zertifikate Ihrer Kunden übernehmen.
- Ihre Anwendung kann Signatur- und Verschlüsselungsschlüssel verwenden, wobei die Schlüssel außerhalb der Anwendung verwaltet werden. Weitere Informationen zu Schlüsseln finden Sie unter [Informationen zu Schlüsseln](../keys/about-keys.md).
- Sie können Anmeldeinformationen wie Kennwörter, Zugriffsschlüssel und SAS-Token verwalten, indem Sie die Informationen in Key Vault als Geheimnisse speichern. Näheres dazu erfahren Sie unter [Informationen zu Schlüsseln](../secrets/about-secrets.md).
- Verwalten Sie Zertifikate. Weitere Informationen finden Sie unter [Informationen zu Zertifikaten](../certificates/about-certificates.md).

Allgemeine Informationen zu Azure Key Vault finden Sie unter [Was ist der Schlüsseltresor?](overview.md).

## <a name="public-previews"></a>Öffentliche Vorschauversionen

Wir veröffentlichen in regelmäßigen Abständen eine öffentliche Vorschau des neuen Key Vault-Features. Testen Sie die Funktionen der öffentlichen Vorschau, und teilen Sie uns Ihre Meinung über die folgende E-Mail-Adresse für Feedback mit: azurekeyvault@microsoft.com.

## <a name="creating-and-managing-key-vaults"></a>Erstellen und Verwalten von Schlüsseltresoren

Key Vault-Verwaltung erfolgt ähnlich wie bei anderen Azure-Diensten über den Azure Resource Manager-Dienst. Azure Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto ermöglicht. Weitere Informationen finden Sie unter [Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Der Zugriff auf die Verwaltungsschicht wird durch die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/overview.md) gesteuert. In Key Vault können Sie mit der Verwaltungsschicht, auch als Verwaltungs- oder Steuerungsebene bezeichnet, Schlüsseltresore und zugehörige Attribute erstellen und verwalten, einschließlich Zugriffsrichtlinien, aber keine Schlüssel, Geheimnisse und Zertifikate, die auf der Datenebene verwaltet werden. Sie können eine vordefinierte `Key Vault Contributor`-Rolle verwenden, um Verwaltungszugriff auf Key Vault zu gewähren.     

**APIs und SDKs für die Key Vault-Verwaltung:**

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault)<br>[Schnellstart](quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault)<br>[Schnellstart](quick-create-powershell.md)|[Referenz](/rest/api/keyvault/)|[Referenz](/azure/templates/microsoft.keyvault/vaults)<br>[Schnellstart](./vault-create-template.md)|[Referenz](/dotnet/api/microsoft.azure.management.keyvault)|[Referenz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referenz](/java/api/com.microsoft.azure.management.keyvault)|[Referenz](/javascript/api/@azure/arm-keyvault)|

Weitere Informationen zu Installationspaketen und Quellcode finden Sie unter [Clientbibliotheken](client-libraries.md).

Weitere Informationen zur Key Vault-Verwaltungsebene finden Sie unter [Azure Key Vault-Sicherheitsfunktionen](security-features.md).

## <a name="authenticate-to-key-vault-in-code"></a>Authentifizieren bei Key Vault mit Code

Key Vault verwendet Azure AD-Authentifizierung, die den Azure AD-Sicherheitsprinzipal erfordert, um Zugriff zu gewähren. Ein Azure AD-Sicherheitsprinzipal kann ein Benutzer, ein Anwendungsdienstprinzipal, eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) oder eine Gruppe eines beliebigen Typs von Sicherheitsprinzipalen sein.

### <a name="authentication-best-practices"></a>Bewährte Methoden für die Authentifizierung

Es wird empfohlen, die verwaltete Identität für in Azure bereitgestellte Anwendungen zu verwenden. Wenn Sie Azure-Dienste verwenden, die keine verwaltete Identität unterstützen, oder wenn Anwendungen lokal bereitgestellt werden, ist ein [Dienstprinzipal mit einem Zertifikat](../../active-directory/develop/howto-create-service-principal-portal.md) eine mögliche Alternative. In diesem Szenario sollte das Zertifikat in Key Vault gespeichert und regelmäßig rotiert werden. Ein Dienstprinzipal mit Geheimnis kann für Entwicklungs- und Testumgebungen verwendet werden. Lokal oder in Cloud Shell wird die Verwendung eines Benutzerprinzipals empfohlen.

Empfohlene Sicherheitsprinzipale pro Umgebung:
- **Produktionsumgebung**:
  - Verwaltete Identität oder Dienstprinzipal mit einem Zertifikat
- **Test- und Entwicklungsumgebungen**:
  - Verwaltete Identität, Dienstprinzipal mit Zertifikat oder Dienstprinzipal mit Geheimnis
- **Lokale Entwicklung**:
  - Benutzerprinzipal oder Dienstprinzipal mit Geheimnis

Die oben beschriebenen Authentifizierungsszenarien werden von der **Azure Identity-Clientbibliothek** unterstützt und in Key Vault SDKs integriert. Die Azure Identity-Bibliothek kann in verschiedenen Umgebungen und auf verschiedenen Plattformen verwendet werden, ohne den Code zu ändern. Azure Identity ruft auch automatisch das Authentifizierungstoken von bei Azure angemeldeten Benutzern mit der Azure CLI, Visual Studio, Visual Studio Code und anderen Anwendungen ab. 

Weitere Informationen zur Azure Identity-Clientbibliothek finden Sie unter:

**Azure Identity-Clientbibliotheken**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> Die [App-Authentifizierungsbibliothek](/dotnet/api/overview/azure/service-to-service-authentication), die für Key Vault .NET SDK, Version 3, empfohlen wurde, ist mittlerweile veraltet. Befolgen Sie die Anweisungen im [AppAuthentication to Azure.Identity Migration-Leitfaden](/dotnet/api/overview/azure/app-auth-migration), um zu Key Vault .NET SDK, Version 4, zu migrieren.

Tutorials zum Authentifizieren bei Key Vault in Anwendungen finden Sie unter:
- [Authentifizieren bei Key Vault in einer in der VM gehosteten Anwendung in .NET](./tutorial-net-virtual-machine.md)
- [Authentifizieren bei Key Vault in einer in der VM gehosteten Anwendung in Python](./tutorial-python-virtual-machine.md)
- [Authentifizieren bei Key Vault mit App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Verwalten von Schlüsseln, Zertifikaten und Geheimnissen

Der Zugriff auf Schlüssel, Geheimnisse und Zertifikate wird durch die Datenebene gesteuert. Die Zugriffssteuerung auf Datenebene kann mithilfe von Zugriffsrichtlinien für lokale Tresore oder per Azure RBAC erfolgen.

### <a name="keys-apis-and-sdks"></a>Schlüssel-APIs und -SDKs

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/key)<br>[Schnellstart](../keys/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault/)<br>[Schnellstart](../keys/quick-create-powershell.md)|[Referenz](/rest/api/keyvault/#key-operations)|[Referenz](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Schnellstart](../keys/quick-create-template.md)|[Referenz](/dotnet/api/azure.security.keyvault.keys)<br>[Schnellstart](../keys/quick-create-net.md)|[Referenz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Schnellstart](../keys/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Schnellstart](../keys/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-keys/)<br>[Schnellstart](../keys/quick-create-node.md)|

### <a name="certificates-apis-and-sdks"></a>Zertifikat-APIs und -SDKs

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/certificate)<br>[Schnellstart](../certificates/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault)<br>[Schnellstart](../certificates/quick-create-powershell.md)|[Verweis](/rest/api/keyvault/#certificate-operations)|–|[Referenz](/dotnet/api/azure.security.keyvault.certificates)<br>[Schnellstart](../certificates/quick-create-net.md)|[Referenz](/python/api/overview/azure/keyvault-certificates-readme)<br>[Schnellstart](../certificates/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Schnellstart](../certificates/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-certificates/)<br>[Schnellstart](../certificates/quick-create-node.md)|

### <a name="secrets-apis-and-sdks"></a>Geheimnis-APIs und -SDKs

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/secret)<br>[Schnellstart](../secrets/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault/)<br>[Schnellstart](../secrets/quick-create-powershell.md)|[Referenz](/rest/api/keyvault/#secret-operations)|[Referenz](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Schnellstart](../secrets/quick-create-template.md)|[Referenz](/dotnet/api/azure.security.keyvault.secrets)<br>[Schnellstart](../secrets/quick-create-net.md)|[Referenz](/python/api/overview/azure/keyvault-secrets-readme)<br>[Schnellstart](../secrets/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Schnellstart](../secrets/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-secrets/)<br>[Schnellstart](../secrets/quick-create-node.md)|

### <a name="secrets-usage"></a>Verwendung von Geheimnissen
Die Azure Key Vault-Geheimnisse sollten nur zum Speichern von Geheimnissen für Ihre Anwendung verwendet werden. 

Beispiele für Geheimnisse, die in Key Vault gespeichert werden sollen:
    - Geheimnisse der Clientanwendung
    - Verbindungszeichenfolgen
    - Kennwörter
    - Freigegebene Zugriffsschlüssel
    - SSH-Schlüssel

Darüber hinaus können alle geheimnisbezogenen Informationen wie Benutzernamen oder Anwendungs-IDs als Tag im Geheimnis gespeichert werden. Für alle anderen vertraulichen Konfigurationseinstellungen sollten Sie [Azure App Configuration](../../azure-app-configuration/overview.md) verwenden.
 
### <a name="references"></a>Referenzen 

Weitere Informationen zu Installationspaketen und Quellcode finden Sie unter [Clientbibliotheken](client-libraries.md).

Weitere Informationen zur Sicherheit der Key Vault-Datenebene finden Sie unter [Azure Key Vault-Sicherheitsfunktionen](security-features.md).

### <a name="using-key-vault-in-applications"></a>Verwenden von Key Vault in Anwendungen

Es wird empfohlen, verfügbare Key Vault-SDKs für die Verwendung von Geheimnissen, Zertifikaten und Schlüsseln in Ihrer Anwendung zu verwenden, um die neuesten Features in Key Vault nutzen zu können. Key Vault-SDKs und die REST-API werden von unserem Team veröffentlicht, wenn wir neue Features für das Produkt veröffentlichen, und befolgen unsere bewährten Methoden und Richtlinien.

#### <a name="libraries-and-integration-solutions-for-limited-usage-scenarios"></a>Bibliotheken und Integrationslösungen für Szenarien mit eingeschränkter Nutzung
Für grundlegende Szenarien gibt es andere Lösungen für vereinfachte Nutzung mit Unterstützung durch Partnerteams oder Open Source-Communitys.

##### <a name="certificates"></a>Certificates:
- Die Key Vault-VM-Erweiterung ermöglicht automatische Aktualisierung von Zertifikaten, die in einer Azure Key Vault-Instanz gespeichert sind. 
    - [Key Vault-VM-Erweiterung für Windows](../../virtual-machines/extensions/key-vault-windows.md)
    - [Key Vault-VM-Erweiterung für Linux](../../virtual-machines/extensions/key-vault-linux.md)
    - [Key Vault-VM-Erweiterung für Azure Arc-fähige Server](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
 - Key Vault-Zertifikatintegration von Azure App Service, damit Zertifikate aus Key Vault importiert und automatisch aktualisiert werden können
     - [Bereitstellen eines Azure-Web-App-Zertifikats über Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)

##### <a name="secrets"></a>Geheimnisse:
- Verwenden von Key Vault-Geheimnissen mit App Service-Anwendungseinstellungen
    - [Verwenden von Key Vault-Verweisen für App Service und Azure Functions](../../app-service/app-service-key-vault-references.md)
- Verwenden von Key Vault-Geheimnissen mit dem App Configuration-Dienst für Anwendungen, die auf einer Azure-VM gehostet werden
    - [Konfigurieren von Anwendungen mit App Configuration und Key Vault](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)

## <a name="code-examples"></a>Codebeispiele

Vollständige Beispiele für die Verwendung von Key Vault mit Ihren Anwendungen finden Sie hier:

- [Azure Key Vault-Codebeispiele](https://azure.microsoft.com/resources/samples/?service=key-vault) – Codebeispiele für Azure Key Vault. 

## <a name="how-tos"></a>Vorgehensweisen

Die folgenden Artikel und Szenarien bieten aufgabenspezifische Anleitungen für die Arbeit mit Azure Key Vault:

- [Zugreifen auf Schlüsseltresore hinter der Firewall:](access-behind-firewall.md) Für den Zugriff auf einen Schlüsseltresor muss Ihre Schlüsseltresor-Clientanwendung auf mehrere Endpunkte für unterschiedliche Funktionen zugreifen können.
- Bereitstellen von Zertifikaten für VMs aus Key Vault – [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md): Eine in einer VM in Azure ausgeführte Cloudanwendung benötigt ein Zertifikat. Wie wird dieses Zertifikat derzeit für eine solche VM bereitgestellt?
- Weisen Sie ein Zugriffsrichtlinie zu ([CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [Portal](assign-access-policy-portal.md)). 
- [Verwenden des vorläufigen Löschens in Key Vault mit der CLI](./key-vault-recovery.md) enthält Informationen zur Verwendung und zum Lebenszyklus eines Schlüsseltresors und verschiedener Schlüsseltresorobjekte, für den bzw. die vorläufiges Löschen aktiviert ist.
- [Übergeben sicherer Werte während der Bereitstellung:](../../azure-resource-manager/templates/key-vault-parameter.md) Wenn Sie während der Bereitstellung einen sicheren Wert (z.B. ein Kennwort) als Parameter übergeben müssen, können Sie diesen Wert als Geheimnis in Azure Key Vault speichern und in anderen Resource Manager-Vorlagen auf ihn verweisen.

## <a name="integrated-with-key-vault"></a>Key Vault-Integration

Diese Artikel enthalten Informationen zu anderen Szenarien und Diensten, in denen Key Vault oder eine Key Vault-Integration zum Einsatz kommt.

- Die [Verschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md) ermöglicht die Codierung (Verschlüsselung) von Daten, sobald diese gespeichert werden. Datenverschlüsselungsschlüssel werden häufig mit einem Schlüssel für die Schlüsselverschlüsselung in Azure Key Vault verschlüsselt, um den Zugriff weiter einzuschränken.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) ermöglicht Ihnen, Ihren eigenen Mandantenschlüssel zu verwalten. Anstatt z.B. Ihren Mandantenschlüssel von Microsoft verwalten zu lassen (Standard), können Sie Ihren eigenen Mandantenschlüssel verwalten, um bestimmte Vorschriften zu erfüllen, die in Ihrer Organisation gelten. Das Verwalten Ihres eigenen Mandantenschlüssels wird auch als „Bring Your Own Key“ (BYOK) bezeichnet.
- Mit dem [Azure Private Link-Dienst](private-link-service.md) können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste wie Azure Key Vault, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.
- Durch die Integration von Key Vault in [Event Grid](../../event-grid/event-schema-key-vault.md) können Benutzer benachrichtigt werden, wenn sich der Status eines im Schlüsseltresor gespeicherten Geheimnisses geändert hat. Sie können eine neue Version von Geheimnissen an Anwendungen verteilen oder Geheimnisse rotieren, die bald ablaufen, um Ausfälle zu verhindern.
- Sie können Ihre [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault)-Geheimnisse vor unerwünschtem Zugriff in Key Vault schützen.
- [Verwenden des in Key Vault in DataBricks gespeicherten Geheimnisses, um eine Verbindung mit Azure Storage herzustellen](./integrate-databricks-blob-storage.md)
- Konfigurieren und Ausführen des Azure Key Vault-Anbieters für den [Secrets Store CSI-Treiber](./key-vault-integrate-kubernetes.md) unter Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault-Übersichten und -Konzepte

- [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](soft-delete-overview.md) beschreibt ein Feature, dass die Wiederherstellung gelöschter Objekte unabhängig davon ermöglicht, ob die Löschung versehentlich oder absichtlich erfolgte.
- [Anleitung zur Drosselung von Azure Key Vault](overview-throttling.md) informiert Sie über die grundlegenden Konzepte der Drosselung und bietet einen Ansatz für Ihre App.
- [Sicherheitsumgebungen und geografische Grenzen von Azure Key Vault](overview-security-worlds.md) beschreibt die Beziehungen zwischen Regionen und Sicherheitsbereichen.

## <a name="social"></a>Soziale Netzwerke

- [Key Vault Blog](/archive/blogs/kv/)
- [Key Vault Forum](https://aka.ms/kvforum)
- [Stack Overflow für Key Vault](https://stackoverflow.com/questions/tagged/azure-keyvault)
