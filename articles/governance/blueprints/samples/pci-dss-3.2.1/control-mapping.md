---
title: Steuerungen des Blaupausenbeispiels „PCI-DSS v3.2.1“
description: Steuerungszuordnung des Blaupausenbeispiels „Payment Card Industry Data Security Standard v3.2.1“ zu Azure Policy und Azure RBAC
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 9233c8d2ec6f72dc08b67f318bceca29de597058
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580280"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Steuerungszuordnung des Blaupausenbeispiels PCI-DSS v3.2.1

In diesem Artikel wird erläutert, wie das Blaupausenbeispiel PCI-DSS v3.2.1 von Azure Blueprints den Steuerungen von PCI-DSS v3.2.1 zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Die folgenden Zuordnungen gelten für die Steuerungen unter **PCI-DSS v3.2.1:2018**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **PCI v3.2.1:2018**, und wählen Sie sie aus.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 und 1.3.4 Schutz von Grenzen

Mit dieser Blaupause können Sie Netzwerke verwalten und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die Netzwerksicherheitsgruppen mit wenig einschränkenden Regeln überwachen. Regeln, die zu wenig einschränkend sind, können einen unbeabsichtigten Netzwerkzugriff ermöglichen und sollten überprüft werden. Mit dieser Blaupause wird auch eine Azure Policy-Definition zugewiesen, die nicht geschützte Endpunkte, Anwendungen und Speicherkonten überwacht. Endpunkte und Anwendungen, die nicht durch eine Firewall geschützt sind, sowie Speicherkonten mit uneingeschränktem Zugriff können einen unbeabsichtigten Zugriff auf Informationen im Informationssystem ermöglichen.

- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen
- Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h und 6.5.3 Kryptografischer Schutz

Die Blaupause hilft Ihnen, Ihre Richtlinie für die Verwendung von Kryptokontrollen durchzusetzen, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die bestimmte Kryptokontrollen durchsetzen und die Verwendung schwacher kryptographischer Einstellungen überwachen. Wenn Sie wissen, wo Ihre Azure-Ressourcen möglicherweise nicht optimale kryptografische Konfigurationen aufweisen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Ressourcen entsprechend Ihrer Richtlinie zur Informationssicherheit konfiguriert sind. Die von dieser Blaupause zugewiesenen Richtlinien erfordern insbesondere eine transparente Datenverschlüsselung für SQL-Datenbank-Instanzen, die Überwachung fehlender Verschlüsselung für Speicherkonten und Automation-Kontovariablen. Es gibt auch Richtlinien, die sich auf das Überwachen unsicherer Verbindungen mit Speicherkonten, Funktions-Apps, WebApp, API-Apps und Redis Cache beziehen und die unverschlüsselte Service Fabric-Kommunikation überwachen.

- Zugriff auf Funktions-App nur über HTTPS gestatten
- Zugriff auf Webanwendung nur über HTTPS gestatten
- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Automation-Kontovariablen sollten verschlüsselt werden.
- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Service Fabric Cluster sollten die Eigenschaft ClusterProtectionLevel auf EncryptAndSign setzen
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- Bereitstellen der transparenten SQL DB-Datenbankverschlüsselung

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 und 11.2.1 Überprüfung auf Sicherheitsrisiken und Systemupdates

Mit dieser Blaupause können Sie Sicherheitsrisiken im Informationssystem verwalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die fehlende Systemupdates, Sicherheitsrisiken des Betriebssystems, SQL-Sicherheitsrisiken und Sicherheitsrisiken von virtuellen Computern in Azure Security Center überwachen. Azure Security Center umfasst Funktionen zur Berichterstellung, über die Sie in Echtzeit Einblick in den Sicherheitsstatus von bereitgestellten Azure-Ressourcen erhalten.

- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server
- Bereitstellen von Bedrohungserkennung auf SQL-Servern
- Systemupdates sollten auf Ihren Computern installiert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 und 7.1.3 Aufgabentrennung

Bei nur einem Azure-Abonnementbesitzer ist keine administrative Redundanz möglich. Im umgekehrten Fall können zu viele Azure-Abonnementbesitzer das Potenzial einer Sicherheitsverletzung durch ein kompromittiertes Besitzerkonto erhöhen. Mit dieser Blaupause können Sie eine angemessene Anzahl von Azure-Abonnementbesitzern beibehalten, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, die die Anzahl der Besitzer für Azure-Abonnements überwachen. Über die Verwaltung von Berechtigungen für Abonnementbesitzer können Sie eine entsprechende Aufgabentrennung implementieren.

- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a und 8.3.1.b Verwaltung von Rechten für den privilegierten Zugriff

Mit dieser Blaupause können Sie Rechte für den privilegierten Zugriff einschränken und steuern, indem [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen werden, um externe Konten mit Besitzer-, Schreib- und/oder Leseberechtigungen und Mitarbeiterkonten mit Besitzer- und/oder Schreibberechtigungen ohne aktivierte mehrstufige Authentifizierung zu überwachen. Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie verwalten, wer Zugriff auf Azure-Ressourcen hat. Wenn Sie wissen, wo benutzerdefinierte Azure RBAC-Regeln implementiert sind, können Sie den Bedarf und die ordnungsgemäße Implementierung überprüfen, da benutzerdefinierte Azure RBAC-Regeln fehleranfällig sind. Diese Blaupause weist auch [Azure Policy](../../../policy/overview.md)-Definitionen zu, um die Verwendung der Azure Active Directory-Authentifizierung für SQL-Server-Instanzen zu überwachen. Die Verwendung der Azure Active Directory-Authentifizierung vereinfacht die Verwaltung von Berechtigungen und zentralisiert die Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten.

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden
- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein
- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden
- Verwendung benutzerdefinierter RBAC-Regeln überwachen

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 und 8.1.5 Ansatz der geringsten Rechte und Überprüfung der Benutzerzugriffsrechte

Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat. Über das Azure-Portal können Sie überprüfen, wer Zugriff auf Azure-Ressourcen und die zugehörigen Berechtigungen hat. Mit dieser Blaupause werden [Azure Policy](../../../policy/overview.md)-Definitionen zugewiesen, um Konten zu überwachen, die für die Überprüfung priorisiert sind, einschließlich veralteter Konten und externer Konten mit erhöhten Rechten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Entfernung oder Anpassung von Zugriffsrechten

Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie verwalten, wer Zugriff auf Ressourcen in Azure hat.
Mithilfe von Azure Active Directory und Azure RBAC können Sie Benutzerrollen aktualisieren, um Organisationsänderungen umzusetzen. Bei Bedarf kann die Anmeldung für Konten blockiert werden (oder Konten können entfernt werden), wodurch die Zugriffsrechte für Azure-Ressourcen sofort entfernt werden. Diese Blaupause weist [Azure Policy](../../../policy/overview.md)-Definitionen zur Überprüfung des veralteten Kontos zu, die bei der Entfernung berücksichtigt werden sollten.

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b und 8.2.5 Kennwortbasierte Authentifizierung

Diese Blaupause hilft Ihnen bei der Durchsetzung sicherer Kennwörter, indem sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweist, die Windows-VMs überwachen, die keine Mindeststärke und andere Kennwortanforderungen durchsetzen. Aufgrund der Informationen zu virtuellen Computern, die gegen die Richtlinie zur Kennwortsicherheit verstoßen, können Sie Korrekturmaßnahmen ergreifen, um sicherzustellen, dass die Kennwörter für alle Benutzerkonten auf virtuellen Computern mit der Richtlinie konform sind.

- \[Vorschau\]: Windows-VMs überwachen, für die kein maximales Kennwortalter von 70 Tagen gilt
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, für die kein maximales Kennwortalter von 70 Tagen gilt
- \[Vorschau\]: Windows-VMs überwachen, für die keine Mindestkennwortlänge von 14 Zeichen festgelegt ist
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, für die keine Mindestkennwortlänge von 14 Zeichen gilt
- \[Vorschau\]: Windows-VMs überwachen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen
- \[Vorschau\]: Anforderungen zum Überwachen von Windows-VMs bereitstellen, die eine Wiederverwendung der vorherigen 24 Kennwörter zulassen

## <a name="103-and-1054-audit-generation"></a>10.3 und 10.5.4 Generierung von Überwachungsdatensätzen

Diese Blaupause hilft Ihnen, sicherzustellen, dass Systemereignisse protokolliert werden, indem Sie [Azure Policy](../../../policy/overview.md)-Definitionen zuweisen, die die Einstellungen des Überwachungsprotokolls auf Azure-Ressourcen überprüfen.
Diagnoseprotokolle bieten Einblicke in Vorgänge, die in Azure-Ressourcen ausgeführt werden. Azure-Protokolle basieren auf synchronisierten internen Uhren, um eine zeitkorrelierte Aufzeichnung von Ereignissen über Ressourcen hinweg zu erstellen.

- Überwachung muss für Advanced Data Security-Einstellungen für SQL Server aktiviert sein.
- Überwachen der Diagnoseeinstellung
- Überwachungseinstellungen auf SQL Server-Ebene überwachen
- Bereitstellen von Überwachung auf SQL-Server-Instanzen
- Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.
- VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden

## <a name="1236-and-1237-information-security"></a>12.3.6 und 12.3.7 Informationssicherheit

Diese Blaupause hilft Ihnen bei der Verwaltung und Steuerung Ihres Netzwerks durch Zuweisung von [Azure Policy](../../../policy/overview.md)-Definitionen, die die zulässigen Netzwerkadressen und die genehmigten Unternehmensprodukte, die für die Umwelt zugelassen sind, überwachen. Diese können von jedem Unternehmen über die Richtlinienparameter innerhalb jeder dieser Richtlinien angepasst werden.

- Allowed locations (Zulässige Speicherorte)
- Zulässige Speicherorte für Ressourcengruppen

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun die Steuerungszuordnung der PCI-DSS v3.2.1-Blaupause angesehen haben, lesen Sie die folgenden Artikel, um eine Übersicht zu erhalten und zu erfahren, wie Sie dieses Beispiel bereitstellen:

> [!div class="nextstepaction"]
> [Steuerungszuordnung des Blaupausenbeispiels PCI-DSS v3.2.1](./index.md)
> [PCI-DSS v3.2.1-Blaupause – Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
