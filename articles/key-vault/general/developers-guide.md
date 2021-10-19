---
title: Entwicklerleitfaden zu Azure Key Vault
description: Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c1b4b6437b14039284fb938405fa7069b0e239c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667552"
---
# <a name="azure-key-vault-developers-guide"></a>Entwicklerleitfaden zu Azure Key Vault

Azure Key Vault ermöglicht den sicheren Zugriff auf vertrauliche Daten in Ihren Anwendungen:

- Schlüssel, Geheimnisse und Zertifikate werden geschützt, ohne dass Sie den Code selbst schreiben müssen, und können auf einfache Weise in Ihren Anwendungen verwendet werden.
- Sie gestatten Kunden, dass sie ihre eigenen Schlüssel, Geheimnisse und Zertifikate verwalten, sodass Sie sich auf die Bereitstellung der Hauptfunktionen der Software konzentrieren können. Auf diese Weise müssen Ihre Anwendungen weder die Verantwortung noch eine mögliche Haftung für die Mandantenschlüssel, Geheimnisse und Zertifikate Ihrer Kunden übernehmen.
- Ihre Anwendungen können Signatur- und Verschlüsselungsschlüssel verwenden, und die Schlüssel werden außerhalb der Anwendung verwaltet. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Schlüsseln](../keys/about-keys.md).
- Sie können Anmeldeinformationen wie Kennwörter, Zugriffsschlüssel und SAS-Token verwalten, indem Sie diese in Key Vault als Geheimnisse speichern. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Geheimnissen](../secrets/about-secrets.md).
- Verwalten Sie Zertifikate. Weitere Informationen finden Sie unter [Informationen zu Azure Key Vault-Zertifikaten](../certificates/about-certificates.md).

Allgemeine Informationen zu Azure Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md).

## <a name="public-previews"></a>Öffentliche Vorschauversionen

Wir veröffentlichen in regelmäßigen Abständen eine öffentliche Vorschau des neuen Key Vault-Features. Testen Sie die Funktionen der öffentlichen Vorschau, und teilen Sie uns Ihre Meinung über die folgende E-Mail-Adresse für Feedback mit: azurekeyvault@microsoft.com.

## <a name="create-and-manage-key-vaults"></a>Erstellen und Verwalten von Key Vault-Instanzen

Wie andere Azure-Dienste auch wird Key Vault über [Azure Resource Manager](../../azure-resource-manager/management/overview.md) verwaltet. Azure Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Sie können den Dienst zum Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto verwenden. 

Die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../../role-based-access-control/overview.md) steuert den Zugriff auf die Verwaltungsschicht, die auch als [Verwaltungsebene](security-features.md#managing-administrative-access-to-key-vault) bezeichnet wird. Sie verwenden die Verwaltungsebene in Key Vault zum Erstellen und Verwalten von Key Vault-Instanzen und deren Attributen, einschließlich Zugriffsrichtlinien. Sie verwenden die *Datenebene* zum Verwalten von Schlüsseln, Zertifikaten und Geheimnissen. 

Sie können die vordefinierte Rolle „Key Vault-Mitwirkender“ verwenden, um Verwaltungszugriff auf Key Vault zu gewähren.     

### <a name="apis-and-sdks-for-key-vault-management"></a>APIs und SDKs für die Key Vault-Verwaltung

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault)<br>[Schnellstart](quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault)<br>[Schnellstart](quick-create-powershell.md)|[Referenz](/rest/api/keyvault/)|[Referenz](/azure/templates/microsoft.keyvault/vaults)<br>[Schnellstart](./vault-create-template.md)|[Referenz](/dotnet/api/microsoft.azure.management.keyvault)|[Referenz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referenz](/java/api/com.microsoft.azure.management.keyvault)|[Referenz](/javascript/api/@azure/arm-keyvault)|

Installationspakete und Quellcode finden Sie unter [Clientbibliotheken](client-libraries.md).

## <a name="authenticate-to-key-vault-in-code"></a>Authentifizieren bei Key Vault mit Code

Key Vault verwendet die Azure Active Directory-Authentifizierung (Azure AD), die einen Azure AD Sicherheitsprinzipal erfordert, um Zugriff zu gewähren. Ein Azure AD-Sicherheitsprinzipal kann ein Benutzer, ein Anwendungsdienstprinzipal, eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) oder eine Gruppe eines beliebigen dieser Typen sein.

### <a name="authentication-best-practices"></a>Bewährte Methoden für die Authentifizierung

Es empfiehlt sich, eine verwaltete Identität für in Azure bereitgestellte Anwendungen zu verwenden. Wenn Sie Azure-Dienste verwenden, die keine verwaltete Identitäten unterstützen, oder wenn Anwendungen lokal bereitgestellt werden, ist ein [Dienstprinzipal mit einem Zertifikat](../../active-directory/develop/howto-create-service-principal-portal.md) eine mögliche Alternative. In diesem Szenario sollte das Zertifikat in Key Vault gespeichert und regelmäßig rotiert werden.

Verwenden Sie einen Dienstprinzipal mit einem Geheimnis für Entwicklungs- und Testumgebungen. Verwenden Sie einen Benutzerprinzipal für die lokale Entwicklung und Azure Cloud Shell.

Die folgenden Sicherheitsprinzipale werden für die jeweilige Umgebung empfohlen:
- **Produktionsumgebung**: verwaltete Identität oder Dienstprinzipal mit Zertifikat
- **Test- und Entwicklungsumgebungen**: verwaltete Identität, Dienstprinzipal mit Zertifikat oder Dienstprinzipal mit Geheimnis
- **Lokale Entwicklung**: Benutzerprinzipal oder Dienstprinzipal mit Geheimnis

### <a name="azure-identity-client-libraries"></a>Azure Identity-Clientbibliotheken

Die oben beschriebenen Authentifizierungsszenarien werden von der *Azure Identity-Clientbibliothek* unterstützt und sind in Key Vault SDKs integriert. Sie können die Azure Identity-Clientbibliothek umgebungs- und plattformübergreifend verwenden, ohne den Code ändern zu müssen. Die Bibliothek ruft automatisch Authentifizierungstoken von Benutzern ab, die über die Azure CLI, Visual Studio, Visual Studio Code oder auf anderem Weg als Azure-Benutzer angemeldet sind. 

Weitere Informationen zur Azure Identity-Clientbibliothek finden Sie hier:

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> Wir haben die [App Authentication-Bibliothek](/dotnet/api/overview/azure/service-to-service-authentication) für Key Vault .NET SDK, Version 3, empfohlen, aber diese ist jetzt als veraltet markiert. Befolgen Sie die Anweisungen im [Leitfaden zur Migration von AppAuthentication zu Azure.Identity](/dotnet/api/overview/azure/app-auth-migration), um zu Key Vault .NET SDK, Version 4, zu migrieren.

Tutorials zum Authentifizieren bei Key Vault in Anwendungen finden Sie unter:
- [Verwenden von Azure Key Vault mit einem virtuellen Windows in .NET](./tutorial-net-virtual-machine.md)
- [Verwenden von Azure Key Vault mit einem virtuellen Windows in Python](./tutorial-python-virtual-machine.md)
- [Verwenden einer verwalteten Identität für die Verbindungsherstellung zwischen Key Vault und einer Azure-Web-App in .NET](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Verwalten von Schlüsseln, Zertifikaten und Geheimnissen

Die Datenebene steuert den Zugriff auf Schlüssel, Zertifikate und Geheimnisse. Sie können lokale Tresorzugriffsrichtlinien oder Azure RBAC für die Zugriffssteuerung über die Datenebene verwenden.

### <a name="apis-and-sdks-for-keys"></a>APIs und SDKs für Schlüssel

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/key)<br>[Schnellstart](../keys/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault/)<br>[Schnellstart](../keys/quick-create-powershell.md)|[Referenz](/rest/api/keyvault/#key-operations)|[Referenz](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Schnellstart](../keys/quick-create-template.md)|[Referenz](/dotnet/api/azure.security.keyvault.keys)<br>[Schnellstart](../keys/quick-create-net.md)|[Referenz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Schnellstart](../keys/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Schnellstart](../keys/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-keys/)<br>[Schnellstart](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>APIs und SDKs für Zertifikate

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/certificate)<br>[Schnellstart](../certificates/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault)<br>[Schnellstart](../certificates/quick-create-powershell.md)|[Verweis](/rest/api/keyvault/#certificate-operations)|–|[Referenz](/dotnet/api/azure.security.keyvault.certificates)<br>[Schnellstart](../certificates/quick-create-net.md)|[Referenz](/python/api/overview/azure/keyvault-certificates-readme)<br>[Schnellstart](../certificates/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Schnellstart](../certificates/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-certificates/)<br>[Schnellstart](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>APIs und SDKs für Geheimnisse

| Azure CLI | PowerShell | REST-API | Ressourcen-Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Verweis](/cli/azure/keyvault/secret)<br>[Schnellstart](../secrets/quick-create-cli.md)|[Referenz](/powershell/module/az.keyvault/)<br>[Schnellstart](../secrets/quick-create-powershell.md)|[Referenz](/rest/api/keyvault/#secret-operations)|[Referenz](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Schnellstart](../secrets/quick-create-template.md)|[Referenz](/dotnet/api/azure.security.keyvault.secrets)<br>[Schnellstart](../secrets/quick-create-net.md)|[Referenz](/python/api/overview/azure/keyvault-secrets-readme)<br>[Schnellstart](../secrets/quick-create-python.md)|[Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Schnellstart](../secrets/quick-create-java.md)|[Referenz](/javascript/api/@azure/keyvault-secrets/)<br>[Schnellstart](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>Verwendung von Geheimnissen
Speichern Sie nur Geheimnisse für Ihre Anwendung in Azure Key Vault. Beispiele für Geheimnisse, die in Key Vault gespeichert werden sollten:

- Geheimnisse der Clientanwendung
- Verbindungszeichenfolgen
- Kennwörter
- Freigegebene Zugriffsschlüssel
- SSH-Schlüssel

Jegliche geheimnisbezogenen Informationen wie Benutzernamen oder Anwendungs-IDs können als Tag in einem Geheimnis gespeichert werden. Für alle anderen vertraulichen Konfigurationseinstellungen sollten Sie [Azure App Configuration](../../azure-app-configuration/overview.md) verwenden.
 
### <a name="references"></a>Referenzen 

Installationspakete und Quellcode finden Sie unter [Clientbibliotheken](client-libraries.md).

Weitere Informationen zur Datenebenensicherheit in Key Vault finden Sie unter [Azure Key Vault – Sicherheit](security-features.md).

## <a name="use-key-vault-in-applications"></a>Verwenden von Key Vault in Anwendungen

Um von den neuesten Features in Key Vault zu profitieren, empfiehlt es sich, die verfügbaren Key Vault SDKs für die Verwendung von Geheimnissen, Zertifikaten und Schlüsseln in Ihrer Anwendung zu nutzen. Die Key Vault SDKs und die REST-API werden aktualisiert, wenn neue Features für das Produkt veröffentlicht werden, und beide folgen Best Practices und Richtlinien.

Für einfache Szenarien gibt es auch andere Bibliotheken und Integrationslösungen, die sich einfacher nutzen lassen. Der Support erfolgt hierbei durch Microsoft-Partner oder Open-Source-Communitys.

Für Zertifikate können Sie Folgendes verwenden:

- Eine Key Vault-VM-Erweiterung. Diese ermöglicht eine automatische Aktualisierung von Zertifikaten, die in einer Azure Key Vault-Instanz gespeichert sind. Weitere Informationen finden Sie unter 
  - [Key Vault-VM-Erweiterung für Windows](../../virtual-machines/extensions/key-vault-windows.md)
  - [Key Vault-VM-Erweiterung für Linux](../../virtual-machines/extensions/key-vault-linux.md)
  - [Key Vault-VM-Erweiterung für Azure Arc-fähige Server](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- Eine Azure App Service-Integration, die Zertifikate aus Key Vault importieren und automatisch aktualisieren kann. Weitere Informationen finden Sie unter [Importieren eines Zertifikats aus Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault).

Für Geheimnisse können Sie Folgendes verwenden:

- Key Vault-Geheimnisse mit App Service-Anwendungseinstellungen. Weitere Informationen erhalten Sie unter [Verwenden von Key Vault-Verweisen in App Service und Azure Functions](../../app-service/app-service-key-vault-references.md).
- Key Vault-Geheimnisse mit dem App Configuration-Dienst für Anwendungen, die auf einer Azure-VM gehostet werden. Weitere Informationen finden Sie unter [Konfigurieren von Anwendungen mit App Configuration und Key Vault](/samples/azure/azure-sdk-for-net/app-secrets-configuration/).

## <a name="code-examples"></a>Codebeispiele

Vollständige Beispiele für die Verwendung von Key Vault mit Anwendungen finden Sie in den [Codebeispielen für Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault). 

## <a name="task-specific-guidance"></a>Aufgabenspezifischer Leitfaden

Die folgenden Artikel und Szenarien bieten aufgabenspezifische Anleitungen für die Arbeit mit Azure Key Vault:

- Für den Zugriff auf eine Key Vault-Instanz muss Ihre Clientanwendung für unterschiedliche Funktionalitäten auf mehrere Endpunkte zugreifen können. Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](access-behind-firewall.md). 
- Eine Cloudanwendung, die auf einer Azure-VM ausgeführt wird, benötigt ein Zertifikat. Wie stellen Sie dieses Zertifikat für eine solche VM bereit? Informationen finden Sie unter [Key Vault-VM-Erweiterung für Windows](../../virtual-machines/extensions/key-vault-windows.md) und [Key Vault-VM-Erweiterung für Linux](../../virtual-machines/extensions/key-vault-linux.md).
- Informationen zum Zuweisen einer Zugriffsrichtlinie über die Azure CLI, PowerShell oder das Azure-Portal finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie](assign-access-policy.md). 
- Anleitungen zur Verwendung und zum Lebenszyklus einer Key Vault-Instanz und verschiedener Key Vault-Objekte mit aktiviertem vorläufigem Löschen finden Sie unter [Azure Key Vault-Wiederherstellungsverwaltung mit Schutz durch vorläufiges Löschen und Bereinigungsschutz](./key-vault-recovery.md).
- Wenn Sie während der Bereitstellung einen sicheren Wert (z. B. ein Kennwort) als Parameter übergeben müssen, können Sie diesen Wert als Geheimnis in einer Key Vault-Instanz speichern und in anderen Resource Manager-Vorlagen auf den Wert verweisen. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="integration-with-key-vault"></a>Integration in Key Vault

Die folgenden Dienste und Szenarien verwenden oder integrieren Key Vault:

- Die [Verschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md) ermöglicht die Codierung (Verschlüsselung) von Daten, wenn diese dauerhaft gespeichert werden. Datenverschlüsselungsschlüssel werden häufig mit einem Schlüssel für die Schlüsselverschlüsselung in Azure Key Vault verschlüsselt, um den Zugriff weiter einzuschränken.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) ermöglicht Ihnen, Ihren eigenen Mandantenschlüssel zu verwalten. Anstatt z.B. Ihren Mandantenschlüssel von Microsoft verwalten zu lassen (Standard), können Sie Ihren eigenen Mandantenschlüssel verwalten, um bestimmte Vorschriften zu erfüllen, die in Ihrer Organisation gelten. Die Verwaltung des eigenen Mandantenschlüssels wird auch als *Bring Your Own Key* (BYOK) bezeichnet.
- Mit [Azure Private Link](private-link-service.md) können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste wie Azure Key Vault, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.
- Durch die Integration von Key Vault in [Azure Event Grid](../../event-grid/event-schema-key-vault.md) können Benutzer benachrichtigt werden, wenn sich der Status eines in Key Vault gespeicherten Geheimnisses geändert hat. Sie können neue Versionen von Geheimnissen an Anwendungen verteilen oder Geheimnisse rotieren, die bald ablaufen, um Ausfälle zu verhindern.
- Schützen Sie Ihre [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault)-Geheimnisse vor unerwünschtem Zugriff in Key Vault.
- Verwenden Sie in Key Vault gespeicherte Geheimnisse, um [eine Verbindung mit Azure Storage aus Azure Databricks](./integrate-databricks-blob-storage.md) herzustellen.
- Sie können den Azure Key Vault-Anbieter für den [Secrets Store CSI-Treiber](./key-vault-integrate-kubernetes.md) in Kubernetes konfigurieren und ausführen. 

## <a name="key-vault-overviews-and-concepts"></a>Key Vault-Übersichten und -Konzepte

Sie erhalten Informationen zu folgenden Themen:

- Die [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](soft-delete-overview.md) beschreibt ein Feature, dass die Wiederherstellung gelöschter Objekte unabhängig davon ermöglicht, ob die Löschung versehentlich oder absichtlich erfolgte.
- Informationen zu den grundlegenden Konzepten der Drosselung und einen Ansatz für Ihre App finden Sie in der [Anleitung zur Drosselung von Azure Key Vault](overview-throttling.md).
- Informationen zu den Beziehungen zwischen Regionen und Sicherheitsbereichen finden Sie unter [Sicherheitsumgebungen und geografische Grenzen von Azure Key Vault](overview-security-worlds.md).

## <a name="social"></a>Soziale Netzwerke

- [Blog zu Key Vault](/archive/blogs/kv/)
- [Microsoft Q&A (Fragen und Antworten)](https://docs.microsoft.com/answers/products/)
- [Stack Overflow für Key Vault](https://stackoverflow.com/questions/tagged/azure-keyvault)
