---
title: Azure Key Vault für Azure Australien
description: Leitfaden zum Konfigurieren und Verwenden von Azure Key Vault für die Schlüsselverwaltung in den australischen Regionen, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: f14a2264769e1bb1fca8e4372ba1359e1b99e560
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346019"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault für Azure Australien

Die sichere Speicherung von kryptografischen Schlüsseln und die Verwaltung des Lebenszyklus von kryptografischen Schlüsseln sind wichtige Elemente von Kryptografiesystemen.  Der Dienst, mit dem diese Funktion in Azure bereitgestellt wird, heißt Azure Key Vault. Key Vault verfügt über IRAP-Sicherheitszugriff und eine ACSC-Schutzzertifizierung (PROTECTED).  In diesem Artikel werden die wichtigsten Aspekte bei der Verwendung von Key Vault beschrieben, die zur Einhaltung der [Kontrollmaßnahmen im Information Security Manual (ISM)](https://acsc.gov.au/infosec/ism/) (Leitfaden zur Informationssicherheit) des Australian Signals Directorate (ASD) beachtet werden müssen.

Azure Key Vault ist ein Clouddienst, mit dem Verschlüsselungsschlüssel und Geheimnisse sicher aufbewahrt werden können. Da es sich hierbei um vertrauliche und unternehmenskritische Daten handelt, ermöglicht Key Vault den sicheren Zugriff auf Schlüsseltresore nur für autorisierte Benutzer und Anwendungen. Von Key Vault werden drei Hauptartefakte verwaltet und kontrolliert:

- keys
- secrets
- certificates

In diesem Artikel geht es um die Verwaltung von Schlüsseln mit Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagramm 1: Azure Key Vault*

## <a name="key-design-considerations"></a>Wichtige Entwurfsaspekte

### <a name="deployment-options"></a>Bereitstellungsoptionen

Es gibt zwei Möglichkeiten zum Erstellen von Azure Key Vault-Instanzen. Bei beiden Ansätzen wird die Cipher nShield-Familie für Hardwaresicherheitsmodule (HSMs) genutzt, die Federal Information Processing Standards (FIPS) werden überprüft, und es ist eine Genehmigung zum Speichern von Schlüsseln in geschützten Umgebungen (PROTECTED) vorhanden. Die Optionen sind:

- **Softwaregeschützte Tresore:** FIPS 140-2 Level 1 überprüft Schlüssel werden auf einem HSM gespeichert. Verschlüsselungs- und Entschlüsselungsvorgänge werden auf Computeressourcen in Azure durchgeführt.
- **HSM-geschützte Tresore:** FIPS 140-2 Level 2 überprüft Schlüssel werden auf einem HSM gespeichert. Verschlüsselungs- und Entschlüsselungsvorgänge werden auf dem HSM durchgeführt.

Key Vault unterstützt Schlüssel vom Typ „Rivest-Shamir-Adleman“ (RSA) und „Kryptografie für elliptische Kurve“ (Elliptic Curve Cryptography, ECC). In der Standardeinstellung werden RSA-Schlüssel mit 2048 Bit verwendet, aber es gibt auch eine erweiterte Option für RSA 3072 Bit-, RSA 4096 Bit- und ECC-Schlüssel.  Alle Schlüssel erfüllen die ISM-Kontrollvorgaben, aber „Elliptische Kurve“ ist die bevorzugte Wahl.

### <a name="resource-operations"></a>Ressourcenvorgänge

Für die Verwaltung von Azure Key Vault werden mehrere Personen benötigt:

- **Key Vault Administrator:** Verwaltet den Lebenszyklus des Tresors
- **Schlüsseladministrator:** Verwaltet den Lebenszyklus der Schlüssel im Tresor
- **Developer/Operator:** Integriert Schlüssel aus dem Tresor in Anwendungen und Dienste
- **Überwachungsfachkraft:** Überwacht die Nutzung der Schlüssel und den Zugriff darauf
- **Anwendungen:** Verwenden von Schlüsseln zum Schützen von Informationen

Azure Key Vault ist durch zwei separate Schnittstellen geschützt:

- **Verwaltungsebene:** Auf dieser Ebene wird der Tresor verwaltet, und sie ist durch Azure RBAC geschützt.
- **Datenebene:** Auf dieser Ebene geht es um die Verwaltung und den Zugriff auf die Artefakte im Tresor.  Als Schutz wird die Key Vault-Zugriffsrichtlinie verwendet.

Laut Vorgabe im ISM müssen eine korrekte Authentifizierung und Autorisierung durchgeführt werden, bevor ein Aufrufer (Benutzer oder Anwendung) über eine der Ebenen Zugriff auf den Schlüsseltresor erhalten kann.

Azure RBAC verfügt für Key Vault über die integrierte Rolle [Key Vault-Mitwirkender](../role-based-access-control/built-in-roles.md#key-vault-contributor), mit der die Verwaltung der Key Vault-Instanzen gesteuert wird. Für die Verwaltung Ihrer Key Vault-Instanz empfehlen wir Ihnen, benutzerdefinierte Rollen zu erstellen, die feiner abgestimmt sind.

>[!WARNING]
>Wenn der Zugriff auf Schlüssel über die Key Vault-Zugriffsrichtlinie ermöglicht wird, hat der Benutzer bzw. die Anwendung entsprechenden Zugriff auf alle Schlüssel im Schlüsseltresor (mit dem Zugriffstyp „Löschen“ kann ein Benutzer beispielsweise alle Schlüssel löschen).  Aus diesem Grund sollten mehrere Schlüsseltresore in Übereinstimmung mit den Sicherheitsdomänen bzw. -grenzen bereitgestellt werden.

### <a name="networking"></a>Netzwerk

Sie können Key Vault-Firewalls und virtuelle Netzwerke konfigurieren, um den Zugriff auf die Datenebene zu steuern.  Sie können den Zugriff auf Benutzer oder Anwendungen in den angegebenen Netzwerken zulassen und gleichzeitig den Zugriff auf Benutzer oder Anwendungen in allen anderen Netzwerken verweigern. [Vertrauenswürdige Dienste](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) stellen eine Ausnahme für diese Kontrolle dar, wenn die Option „Vertrauenswürdige Dienste zulassen“ aktiviert ist.  Die Kontrolle für virtuelle Netzwerke gilt nicht für die Verwaltungsebene.

Der Zugriff auf Key Vaults sollte explizit auf die minimale Gruppe von Netzwerken mit Benutzern oder Anwendungen beschränkt sein, die Zugriff auf die Schlüssel benötigen.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Für Key Vault wird BYOK unterstützt.  Mit BYOK können Benutzer Schlüssel aus ihren vorhandenen Schlüsselinfrastrukturen importieren. Das BYOK-Toolset unterstützt die sichere Übertragung und den Import von Schlüsseln von einem externen HSM (z. B. mit einer Offlinearbeitsstation generierte Schlüssel) in Key Vault.

Wechseln Sie zum Microsoft Download Center, und [laden Sie das BYOK-Toolset für Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) für Australien herunter. Der Name des herunterzuladenden Pakets und sein entsprechender SHA-256-Pakethash lauten:

|Paketname|SHA-256 Hash|
|---|---|
|KeyVault-BYOK-Tools-Australia.zip|CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A|
|

### <a name="key-vault-auditing-and-logging"></a>Key Vault-Überwachung und -Protokollierung

Laut Vorgabe des ACSC müssen Commonwealth-Entitäten die richtigen Azure-Dienste verwenden, um für ihre Azure-Workloads die Echtzeitüberwachung und Berichterstellung durchzuführen.

Die Protokollierung wird aktiviert, indem für Schlüsselwerte die Diagnoseeinstellung **_„AuditEvent“_** aktiviert wird.  Überwachungsereignisse werden im angegebenen Speicherkonto protokolliert.  Der Zeitraum **_„RetentionInDays“_** sollte gemäß der Richtlinie zur Datenaufbewahrung festgelegt werden.  [Vorgänge](../key-vault/general/logging.md#interpret-your-key-vault-logs) auf Verwaltungs- und Datenebene werden überprüft und protokolliert. Sie können die [Azure Key Vault-Lösung in Azure Monitor](../azure-monitor/insights/key-vault-insights-overview.md) zum Überprüfen von Key Vault-AuditEvent-Protokollen verwenden.  Es können auch einige andere Azure-Dienste genutzt werden, um Key Vault-AuditEvents zu verarbeiten und zu verteilen.

### <a name="key-rotation"></a>Schlüsselrotation

Durch die Speicherung von Schlüsseln in Key Vault wird ein zentraler Punkt zum Verwalten von Schlüsseln außerhalb von Anwendungen geschaffen, damit Schlüssel aktualisiert werden können, ohne dass sich dies auf das Verhalten der Anwendungen auswirkt. Mit dem Speichern von Schlüsseln in Azure Key Vault können verschiedene Strategien zur Unterstützung der Schlüsselrotation verfolgt werden:

- Manuell
- Programmgesteuert über APIs
- Automatisierungsskripts (z. B. mit PowerShell und Azure Automation)

Mit diesen Optionen können Schlüssel auf regelmäßiger Basis rotiert werden, um Konformitätsanforderungen zu erfüllen, oder ad hoc, falls Bedenken bestehen, dass Schlüssel kompromittiert wurden.

#### <a name="key-rotation-strategies"></a>Strategien für die Schlüsselrotation

Es ist wichtig, für Schlüssel, die in Key Vault gespeichert werden, eine geeignete Strategie für die Schlüsselrotation zu entwickeln.  Bei Verwendung des falschen Schlüssels werden Informationen falsch entschlüsselt, und der Verlust von Schlüsseln kann zum vollständigen Verlust des Zugriffs auf Informationen führen.  Beispiele für Strategien zur Schlüsselrotation für verschiedene Szenarien sind:

- **Inflight-Daten:** Zwischen zwei Parteien werden flüchtige Informationen übertragen.  Wenn ein Schlüssel rotiert wird, müssen beide Parteien über einen Mechanismus zum synchronen Abrufen der aktualisierten Schlüssel aus dem Schlüsseltresor verfügen.
- **Daten im Ruhezustand:** Eine Partei speichert verschlüsselte Daten und entschlüsselt sie dann zur späteren Verwendung.  Wenn ein Schlüssel rotiert werden soll, müssen die Daten mit dem alten Schlüssel entschlüsselt und dann mit dem neuen rotierten Schlüssel wieder verschlüsselt werden.  Es gibt verschiedene Ansätze zur Minimierung der Auswirkungen des Ent- bzw. Verschlüsselungsprozesses mit Schlüsselverschlüsselungsschlüsseln (siehe Beispiel).  Microsoft verwaltet den Großteil der Prozesse in Bezug auf die Schlüsselrotation für Azure Storage (siehe…).
- **Zugriffsschlüssel:** Einige Azure-Dienste verfügen über Zugriffsschlüssel, die in Key Vault gespeichert werden können (z. B. Cosmos DB).  Die Azure-Dienste haben primäre und sekundäre Zugriffsschlüssel.  Es ist wichtig, dass nicht beide Schlüssel gleichzeitig rotiert werden.  Aus diesem Grund sollte zuerst ein Schlüssel rotiert werden, und nachdem etwas abgewartet und der Schlüsselvorgang überprüft wurde, kann der zweite Schlüssel rotiert werden.

### <a name="high-availability"></a>Hochverfügbarkeit

Im ISM sind mehrere Kontrollmechanismen beschrieben, die sich auf die Geschäftskontinuität beziehen.
Azure Key Vault verfügt über mehrere Redundanzebenen, für die Inhalte innerhalb der Region und in der sekundären [gekoppelten Region](../best-practices-availability-paired-regions.md) repliziert werden.

Wenn sich der Schlüsseltresor in einem Failoverzustand befindet, ist der Modus „Schreibgeschützt“ aktiv. Der Lese-/Schreibmodus wird wieder aktiviert, nachdem der primäre Dienst wiederhergestellt wurde.

Im ISM sind mehrere Kontrollmechanismen beschrieben, die sich auf die Sicherung beziehen.  Es ist wichtig, für Tresore und die zugehörigen Schlüssel geeignete Sicherungs- und Wiederherstellungspläne zu entwickeln und auszuführen.

## <a name="key-lifecycle"></a>Lebenszyklus des Schlüssels

### <a name="key-operations"></a>Schlüsselvorgänge

Key Vault unterstützt für Schlüssel die folgenden Vorgänge:

- **Erstellen:** Ermöglicht einem Client, einen Schlüssel in Key Vault zu erstellen. Der Wert des Schlüssels wird von Key Vault generiert und gespeichert und nicht für den Client freigegeben. In Key Vault können asymmetrische Schlüssel erstellt werden.
- **Importieren:** Ermöglicht einem Client, einen vorhandenen Schlüssel in Key Vault zu importieren. Asymmetrische Schlüssel können mithilfe einer Reihe unterschiedlicher Paketerstellungsmethoden in einem JWK-Konstrukt in Key Vault importiert werden.
- **Aktualisieren:** Ermöglicht einem Client mit ausreichenden Berechtigungen, die Metadaten (Schlüsselattribute) zu ändern, die einem zuvor in Key Vault gespeicherten Schlüssel zugeordnet sind.
- **Löschen:** Ermöglicht einem Client mit ausreichenden Berechtigungen das Löschen eines Schlüssels aus Key Vault.
- **Auflisten:** Ermöglicht einem Client das Auflisten aller Schlüssel in einem bestimmten Key Vault.
- **Auflisten von Versionen:** Ermöglicht einem Client das Auflisten aller Versionen eines bestimmten Schlüssels in einem bestimmten Key Vault.
- **Abrufen:** Ermöglicht einem Client das Abrufen des öffentlichen Teils eines bestimmten Schlüssels in einem Key Vault.
- **Sichern:** Exportiert einen Schlüssel in einer geschützten Form.
- **Wiederherstellen:** Importiert einen zuvor gesicherten Schlüssel.

Es ist ein entsprechender Satz mit Berechtigungen vorhanden, die Benutzern, Dienstprinzipalen oder Anwendungen über Einträge für die Key Vault-Zugriffssteuerung gewährt werden können, um Schlüsselvorgänge auszuführen.

Key Vault verfügt über eine Funktion für vorläufiges Löschen, um die Wiederherstellung von gelöschten Tresoren und Schlüsseln zuzulassen. Das **_„vorläufige Löschen“_** ist standardmäßig nicht aktiviert. Nach dem Aktivieren werden Objekte aber 90 Tage lang aufbewahrt (Aufbewahrungszeitraum), während es so aussieht, als ob sie gelöscht wurden.  Mit der Berechtigung **_„Löschen“_** können Schlüssel dauerhaft gelöscht werden, wenn die Option **_„Bereinigungsschutz“_** deaktiviert ist.

Beim Erstellen oder Importieren eines vorhandenen Schlüssels wird eine neue Version des Schlüssels erstellt.

### <a name="cryptographic-operations"></a>Kryptografische Vorgänge

Key Vault unterstützt auch Kryptografievorgänge mit Schlüsseln:

- **Signieren und Überprüfen:** Hierbei wird der Vorgang „Hash signieren“ bzw. „Hash überprüfen“ durchgeführt. Key Vault weist keine Unterstützung für das Hashing von Inhalten beim Erstellen der Signatur auf.
- **Schlüsselverschlüsselung/-umschließung:** Dieser Vorgang wird zum Schützen eines anderen Schlüssels durchgeführt.
- **Verschlüsseln und Entschlüsseln:** Der gespeicherte Schlüssel wird verwendet, um einen einzelnen Datenblock zu ver- bzw. entschlüsseln.

Es ist ein entsprechender Satz mit Berechtigungen vorhanden, die Benutzern, Dienstprinzipalen oder Anwendungen über Einträge für die Key Vault-Zugriffssteuerung gewährt werden können, um Kryptografievorgänge durchzuführen.

Es können drei Schlüsselattribute festgelegt werden, um zu kontrollieren, ob ein Schlüssel aktiviert ist und für Kryptografievorgänge genutzt werden kann:

- **enabled**
- **nbf:** Keine Aktivierung vor dem angegebenen Datum.
- **exp:** Ablaufdatum

## <a name="storage-and-keys"></a>Speicher und Schlüssel

Vom Kunden verwaltete Schlüssel sind flexibler und ermöglichen den Zugriff auf zu kontrollierende Schlüssel und deren Verwaltung. Darüber hinaus ermöglichen sie die Überprüfung der Verschlüsselungsschlüssel, die zum Schützen von Daten verwendet werden.
Für den Speicher und die in Key Vault gespeicherten Schlüssel sollten drei wichtige Aspekte beachtet werden:

- Mit Key Vault verwaltete Speicherkontoschlüssel
- Azure-Speicherdienstverschlüsselung (Storage Service Encryption, SSE) für ruhende Daten
- Verwaltete Datenträger und Azure Disk Encryption

Die Verwaltung des Azure Storage-Kontoschlüssels von Key Vault ist eine Erweiterung des Key Vault-Schlüsseldiensts, die eine Synchronisierung und erneute Generierung (Rotation) von Speicherkontoschlüsseln unterstützt.  Die Nutzung der [Azure Storage-Integration in Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md) (Vorschauversion) wird empfohlen (nach der Veröffentlichung), weil sie damit eine höhere Sicherheit und Benutzerfreundlichkeit erzielen.
SSE verwendet zwei Schlüssel, um die Verschlüsselung von ruhenden Daten zu verwalten:

- Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK)
- Datenverschlüsselungsschlüssel (Data Encryption Key, DEK)

Die DEKs werden von Microsoft verwaltet, aber für SSE gibt es eine Option für vom Benutzer verwaltete KEKs, die in Key Vault gespeichert werden können. Dies ermöglicht die Rotation von Schlüsseln in Azure Key Vault gemäß den entsprechenden Konformitätsrichtlinien. Bei einer Schlüsselrotation wird der Kontoverschlüsselungsschlüssel für dieses Speicherkonto von Azure Storage erneut verschlüsselt. Dies führt nicht zu einer erneuten Verschlüsselung aller Daten, und es sind keine weiteren Maßnahmen erforderlich.

SSE wird für verwaltete Datenträger verwendet, aber vom Kunden verwaltete Schlüssel werden nicht unterstützt.  Die Verschlüsselung von verwalteten Datenträgern kann durchgeführt werden, indem Azure Disk Encryption mit vom Kunden verwalteten KEK-Schlüsseln in Key Vault verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel zum Thema [Identitätsverbund](identity-federation.md).

Sehen Sie sich die weitere Azure Key Vault-Dokumentation und die Tutorials in der [Referenzbibliothek](reference-library.md) an.