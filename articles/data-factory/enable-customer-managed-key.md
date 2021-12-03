---
title: Verschlüsseln von Azure Data Factory mit vom Kunden verwalteten Schlüsseln
description: Verbessern der Data Factory-Sicherheit mit Bring Your Own Key (BYOK)
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: security
ms.topic: quickstart
ms.date: 10/22/2021
ms.reviewer: mariozi
ms.openlocfilehash: b2af772d4f61f933fc6801827233038f8c8919ad
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849323"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Verschlüsseln von Azure Data Factory mit vom Kunden verwalteten Schlüsseln

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory verschlüsselt ruhende Daten (einschließlich Entitätsdefinitionen) und zwischengespeicherte Daten während der Ausführung von Vorgängen. Standardmäßig werden Daten mit einem zufällig generierten und von Microsoft verwalteten Schlüssel verschlüsselt, der Ihrer Data Factory eindeutig zugewiesen ist. Um zusätzliche Sicherheitsgarantien zu erhalten, können Sie jetzt Bring Your Own Key (BYOK) mit vom Kunden verwalteten Schlüsseln in Azure Data Factory aktivieren. Wenn Sie einen vom Kunden verwalteten Schlüssel angeben, verwendet Data Factory zum Verschlüsseln von Kundendaten __sowohl__ den Factory-Systemschlüssel als auch den vom Kunden verwalteten Schlüssel. Wenn einer der Schlüssel fehlt, wird der Zugriff auf die Daten und auf die Data Factory verweigert.

Zum Speichern von kundenseitig verwalteten Schlüsseln ist Azure Key Vault erforderlich. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Der Schlüsseltresor und Data Factory müssen sich im selben Azure Active Directory-Mandanten (Azure AD-Mandanten) und in derselben Region befinden, aber sie können sich in verschiedenen Abonnements befinden. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Informationen zu kundenseitig verwalteten Schlüsseln

Im folgenden Diagramm ist dargestellt, wie Azure Active Directory und Azure Key Vault von Data Factory verwendet werden, um Anforderungen mit dem vom Kunden verwalteten Schlüssel zu senden:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagramm: Funktionsweise kundenseitig verwalteter Schlüssel in Azure Data Factory":::

In der folgenden Liste werden die nummerierten Schritte im Diagramm erläutert:

1. Ein Azure Key Vault-Administrator erteilt der verwalteten Identität, die der Data Factory zugeordnet ist, Berechtigungen für Verschlüsselungsschlüssel.
1. Ein Data Factory-Administrator aktiviert die Funktion für vom Kunden verwaltete Schlüssel in der Factory.
1. Data Factory verwendet die der Factory zugeordnete verwaltete Identität, um den Zugriff auf Azure Key Vault über Azure Active Directory zu authentifizieren.
1. Data Factory umschließt den Verschlüsselungsschlüssel der Factory mit dem Kundenschlüssel in Azure Key Vault.
1. Bei Lese-/Schreibvorgängen sendet Data Factory Anforderungen an Azure Key Vault, um die Umschließung für den Kontoverschlüsselungsschlüssel aufzuheben und so Verschlüsselungs- und Entschlüsselungsvorgänge durchzuführen.

Data Factory-Instanzen können auf zwei Arten mit einer Verschlüsselung mit kundenseitig verwalteten Schlüsseln versehen werden: im Zuge der Factoryerstellung im Azure-Portal oder nach der Factoryerstellung über die Data Factory-Benutzeroberfläche.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Voraussetzungen: Konfigurieren von Azure Key Vault und Generieren von Schlüsseln

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Aktivieren des vorläufigen Löschens und des Bereinigungsschutzes für Azure Key Vault

Wenn Sie vom Kunden verwaltete Schlüssel mit Data Factory verwenden, müssen Sie für den Schlüsseltresor zwei Eigenschaften (__Vorläufiges Löschen__ und __Nicht Bereinigen__) festlegen. Diese Eigenschaften können entweder mit PowerShell oder der Azure-Befehlszeilenschnittstelle (Azure CLI) für einen neuen oder vorhandenen Schlüsseltresor aktiviert werden. Informationen zum Aktivieren dieser Eigenschaften für einen bereits vorhandenen Schlüsseltresor finden Sie unter [Azure Key Vault-Wiederherstellungsverwaltung mit Schutz durch vorläufiges Löschen und Bereinigungsschutz](../key-vault/general/key-vault-recovery.md).

Wenn Sie im Azure-Portal eine neue Azure Key Vault-Instanz erstellen, können __Vorläufiges Löschen__ und __Nicht bereinigen__ wie folgt aktiviert werden:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Screenshot: Aktivieren des vorläufigen Löschens und des Bereinigungsschutzes bei der Erstellung einer Key Vault-Instanz":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Gewähren des Zugriffs auf Azure Key Vault durch Data Factory

Stellen Sie sicher, dass sich Azure Key Vault und Azure Data Factory im gleichen Azure Active Directory-Mandanten und in der _gleichen Region_ befinden. Erteilen Sie Data Factory über die Azure Key Vault-Zugriffssteuerung die folgenden Berechtigungen: _Abrufen_, _Schlüssel entpacken_ und _Schlüssel packen_. Diese Berechtigungen sind erforderlich, um vom Kunden verwaltete Schlüssel in Data Factory aktivieren zu können.

* Wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln [nach der Erstellung der Factory über die Data Factory-Benutzeroberfläche](#post-factory-creation-in-data-factory-ui) hinzufügen möchten, vergewissern Sie sich, dass die verwaltete Dienstidentität (Managed Service Identity, MSI) über die drei Berechtigungen für Key Vault verfügt.
* Wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln [im Zuge der Factory-Erstellung im Azure-Portal](#during-factory-creation-in-azure-portal) hinzufügen möchten, vergewissern Sie sich, dass die benutzerseitig zugewiesene verwaltete Identität (User-Assigned Managed Identity, UA-MI) über die drei Berechtigungen für Key Vault verfügt.

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Screenshot: Ermöglichen des Data Factory-Zugriffs auf Key Vault":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Generieren oder Hochladen eines vom Kunden verwalteten Schlüssels in Azure Key Vault

Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder Schlüssel mithilfe der Azure Key Vault-APIs generieren. Für die Data Factory-Verschlüsselung werden nur 2048-Bit-RSA-Schlüssel unterstützt. Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Screenshot: Generieren kundenseitig verwalteter Schlüssel":::

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln

### <a name="post-factory-creation-in-data-factory-ui"></a>Nach der Factoryerstellung über die Data Factory-Benutzeroberfläche

In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie _nach_ der Factoryerstellung eine Verschlüsselung mit kundenseitig verwalteten Schlüsseln über die Data Factory-Benutzeroberfläche hinzufügen.

> [!NOTE]
> Ein vom Kunden verwalteter Schlüssel kann nur für eine leere Data Factory konfiguriert werden. Die Data Factory darf keine Ressourcen wie z. B. verknüpfte Dienste, Pipelines und Datenflüsse enthalten. Es wird empfohlen, den vom Kunden verwalteten Schlüssel direkt nach der Erstellung der Factory zu aktivieren.

> [!IMPORTANT]
> Bei verwalteten Factorys mit aktiviertem virtuellem Netzwerk kann diese Vorgehensweise nicht verwendet werden. Verwenden Sie die [alternative Route](#during-factory-creation-in-azure-portal), wenn Sie diese Factorys verschlüsseln möchten.

1. Stellen Sie sicher, dass die verwaltete Dienstidentität (Managed Service Identity, MSI) der Data Factory-Instanz über die Berechtigungen _Abrufen_, _Schlüssel entpacken_ und _Schlüssel packen_ für Key Vault verfügt.

1. Stellen Sie sicher, dass die Data Factory leer ist. Die Data Factory-Instanz darf keine Ressourcen wie verknüpfte Dienste, Pipelines oder Datenflüsse enthalten. Derzeit führt das Bereitstellen eines vom Kunden verwalteten Schlüssels in einer nicht leeren Factory zu einem Fehler.

1. Um den Schlüssel-URI im Azure-Portal zu ermitteln, navigieren Sie zu Azure Key Vault, und wählen Sie die Einstellung „Schlüssel“ aus. Wählen Sie den gewünschten Schlüssel aus, um dessen Versionen anzuzeigen. Wählen Sie eine Schlüsselversion aus, um die Einstellungen anzuzeigen.

1. Kopieren Sie den Wert des Felds „Schlüsselbezeichner“, um den URI zu erhalten. :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="Screenshot: Abrufen des Schlüssel-URI aus Key Vault":::

1. Starten Sie das Azure Data Factory-Portal, und wechseln Sie über die Navigationsleiste auf der linken Seite zum Data Factory-Verwaltungsportal.

1. Klicken Sie auf das Symbol __Kundenseitig verwalteter Schlüssel__. :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Screenshot: Aktivieren eines kundenseitig verwalteten Schlüssels über die Data Factory-Benutzeroberfläche":::.

1. Geben Sie den zuvor kopierten URI für den vom Kunden verwalteten Schlüssel ein.

1. Klicken Sie auf __Speichern__. Dann wird die kundenseitig verwaltete Schlüsselverschlüsselung für Data Factory aktiviert.

### <a name="during-factory-creation-in-azure-portal"></a>Im Zuge der Factoryerstellung im Azure-Portal

In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie _während_ der Factorybereitstellung im Azure-Portal eine Verschlüsselung mit kundenseitig verwalteten Schlüsseln hinzufügen.

Um die Factory verschlüsseln zu können, muss von Data Factory zuerst der kundenseitig verwaltete Schlüssel aus Key Vault abgerufen werden. Da die Factorybereitstellung noch nicht abgeschlossen ist, ist die verwaltete Dienstidentität (Managed Service Identity, MSI) noch nicht für die Authentifizierung bei Key Vault verfügbar. Der Kunde muss daher eine benutzerseitig zugewiesene verwaltete Identität (User-Assigned Managed Identity, UA-MI) zuweisen, um diese Vorgehensweise verwenden zu können. Wir übernehmen die in der benutzerseitig zugewiesenen verwalteten Identität definierten Rollen und führen die Authentifizierung bei Key Vault durch.

Weitere Informationen zur benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Arten von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) sowie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Stellen Sie sicher, dass die benutzerseitig zugewiesene verwaltete Identität (User-Assigned Managed Identity, UA-MI) über die Berechtigungen _Abrufen_, _Schlüssel entpacken_ und _Schlüssel packen_ für Key Vault verfügt.

1. Aktivieren Sie auf der Registerkarte __Erweitert__ das Kontrollkästchen _Verschlüsselung anhand eines kundenseitig verwalteten Schlüssels aktivieren_.
  :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="Screenshot: Registerkarte „Erweitert“ für die Data Factory-Erstellung im Azure-Portal":::

1. Geben Sie die URL für den in Key Vault gespeicherten kundenseitig verwalteten Schlüssel an.

1. Wählen Sie eine geeignete benutzerseitig zugewiesene verwaltete Identität für die Authentifizierung bei Key Vault aus.

1. Fahren Sie mit der Factorybereitstellung fort.

## <a name="update-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, aktualisieren Sie die Data Factory, damit sie die neue Version verwendet. Führen Sie ähnliche Schritte wie im [Abschnitt zur Data Factory-Benutzeroberfläche](#post-factory-creation-in-data-factory-ui) aus:

1. Ermitteln Sie den URI für die neue Schlüsselversion über das Azure Key Vault-Portal.

1. Navigieren Sie zur Einstellung __Vom Kunden verwalteter Schlüssel__.

1. Ersetzen Sie den URI für den neuen Schlüssel, indem Sie ihn einfügen.

1. Klicken Sie auf __Speichern__. Data Factory verschlüsselt jetzt mit der neuen Schlüsselversion.

## <a name="use-a-different-key"></a>Verwenden eines anderen Schlüssels

Wenn Sie den für die Data Factory-Verschlüsselung verwendeten Schlüssel ändern möchten, müssen Sie die Einstellungen in Data Factory manuell aktualisieren. Führen Sie ähnliche Schritte wie im [Abschnitt zur Data Factory-Benutzeroberfläche](#post-factory-creation-in-data-factory-ui) aus:

1. Ermitteln Sie den URI für den neuen Schlüssel über das Azure Key Vault-Portal.

1. Navigieren Sie zur Einstellung __Kundenseitig verwalteter Schlüssel__.

1. Ersetzen Sie den URI für den neuen Schlüssel, indem Sie ihn einfügen.

1. Klicken Sie auf __Speichern__. Data Factory verschlüsselt jetzt mit dem neuen Schlüssel.

## <a name="disable-customer-managed-keys"></a>Deaktivieren kundenseitig verwalteter Schlüssel

Nach der Aktivierung der Funktion für vom Kunden verwaltete Schlüssel können Sie den zusätzlichen Sicherheitsschritt nicht mehr entfernen. Zum Verschlüsseln der Factory und der Daten wird immer ein vom Kunden bereitgestellter Schlüssel erwartet.

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>Vom Kunden verwalteter Schlüssel und Continuous Integration und Continuous Deployment

Standardmäßig ist die CMK-Konfiguration in der Vorlage Factory Azure Resource Manager (ARM) nicht enthalten. Vorgehensweise zum Einschließen der vom Kunden verwalteten Verschlüsselungseinstellungen für Schlüssel in die ARM-Vorlage für Continuous Integration (CI/CD):

1. Stellen Sie sicher, dass sich die Factory im Git-Modus befindet
1. Navigieren Sie im Verwaltungsportal zu dem Abschnitt für die vom Kunden verwalteten Schlüssel
1. Aktivieren Sie die Option _in ARM-Vorlage einschließen_

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="Screenshot: Einschließen der Einstellungen für die vom Kunden verwalteten Schlüssel in die ARM-Vorlage.":::

Die folgenden Einstellungen werden zur ARM-Vorlage hinzugefügt. Diese Eigenschaften können in den Continuous Integration- und Delivery-Pipelines parametrisiert werden, indem die [Parameterkonfiguration von Azure Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md) bearbeitet wird

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="Screenshot: Einschließen der Einstellungen für die vom Kunden verwalteten Schlüssel in die Azure Resource Manager-Vorlage.":::

> [!NOTE]
> Durch das Hinzufügen der Verschlüsselungseinstellung zu den ARM-Vorlagen wird eine Einstellung auf Factory-Ebene hinzugefügt, die andere Einstellungen auf Factory-Ebene, also z. B. git-Konfigurationen, in anderen Umgebungen überschreibt. Wenn Sie diese Einstellungen in einer erweiterten Umgebung wie UAT oder PROD aktiviert haben, finden Sie weitere Informationen unter [Globale Parameter in CI/CD](author-global-parameters.md#cicd).

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können.
