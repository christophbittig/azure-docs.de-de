---
title: Sicherheitsempfehlungen für Queue Storage
titleSuffix: Azure Storage
description: Erfahren Sie mehr über Sicherheitsempfehlungen für Queue Storage. Die Umsetzung dieser Anleitung erleichtert es Ihnen, Ihre Sicherheitspflichten zu erfüllen, die in unserem Modell der gemeinsamen Verantwortung beschrieben werden.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: 0fdd17b924fe8588e0ece24fc185456bd5dc3bc5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312147"
---
# <a name="security-recommendations-for-queue-storage"></a>Sicherheitsempfehlungen für Queue Storage

Dieser Artikel enthält Sicherheitsempfehlungen für Queue Storage. Die Umsetzung dieser Empfehlungen erleichtert es Ihnen, Ihre Sicherheitspflichten zu erfüllen, die in unserem Modell der gemeinsamen Verantwortung beschrieben werden. Weitere Informationen dazu, wie Microsoft seiner Verantwortung als Dienstanbieter nachkommt, finden Sie unter [Gemeinsame Verantwortung für das Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Einige der in diesem Artikel enthaltenen Empfehlungen können automatisch von Microsoft Defender für Cloud überwacht werden. Microsoft Defender für Cloud ist die erste Verteidigungslinie zum Schutz Ihrer Ressourcen in Azure. Weitere Informationen zu Microsoft Defender für Cloud finden Sie unter [Was ist Microsoft Defender für Cloud?](../../security-center/security-center-introduction.md)

Microsoft Defender für Cloud analysiert regelmäßig den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie damit umgegangen werden kann. Weitere Informationen zu Empfehlungen in Microsoft Defender für Cloud finden Sie unter [Sicherheitsempfehlungen in Microsoft Defender für Cloud](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Schutz von Daten

| Empfehlung | Kommentare | Defender für Cloud |
|-|----|--|
| Verwenden des Azure Resource Manager-Bereitstellungsmodells | Erstellen Sie neue Speicherkonten mit dem Azure Resource Manager-Bereitstellungsmodell, um von wichtigen Sicherheitsverbesserungen zu profitieren. Dazu gehören eine differenzierte rollenbasierte Zugriffssteuerung (Azure RBAC) und Überwachung in Azure, Resource Manager-basierte Bereitstellung und Governance, Zugriff auf verwaltete Identitäten, Zugriff auf Azure Key Vault für Geheimnisse sowie Azure AD-basierte Authentifizierung und Autorisierung für den Zugriff auf Azure Storage-Daten und -Ressourcen. Migrieren Sie nach Möglichkeit vorhandene Speicherkonten, die das klassische Bereitstellungsmodell verwenden, zu Azure Resource Manager. Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md). | - |
| Aktivieren von Advanced Threat Protection für alle Speicherkonten | [Microsoft Defender für Storage](../../security-center/defender-for-storage-introduction.md) stellt eine zusätzliche Sicherheitsebene bereit, die ungewöhnliche und potenziell schädliche Versuche erkennt, auf Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Sicherheitswarnungen werden in Microsoft Defender für Cloud ausgelöst, wenn anormale Aktivitäten auftreten, und auch als E-Mail mit Details zu verdächtigen Aktivitäten und Empfehlungen, wie Bedrohungen untersucht und behoben werden können, an Abonnementadministratoren gesendet. Weitere Informationen finden Sie unter [Schützen von Azure Storage-Konten](../../security-center/defender-for-storage-introduction.md). | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Beschränken von SAS-Token (Shared Access Signature) auf HTTPS-Verbindungen | Durch das verbindliche Verwenden von HTTPS, wenn ein Client ein SAS-Token für den Zugriff auf Warteschlangendaten nutzt, kann das Risiko eines Lauschangriffs reduziert werden. Weitere Informationen finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Empfehlung | Kommentare | Defender für Cloud |
|-|----|--|
| Verwenden von Azure Active Directory (Azure AD) zum Autorisieren des Zugriffs auf Warteschlangendaten | Azure AD bietet überlegene Sicherheit und Benutzerfreundlichkeit über die Autorisierung des gemeinsam verwendeten Schlüssels für die Autorisierung von Anforderungen an Queue Storage. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf Daten in Azure Storage](../common/authorize-data-access.md). | - |
| Beachten Sie das Prinzip der geringsten Rechte beim Zuweisen von Berechtigungen für einen Azure AD-Sicherheitsprinzipal über Azure RBAC. | Wenn Sie einem Benutzer, einer Gruppe oder einer Anwendung eine Rolle zuweisen, erteilen Sie diesem Sicherheitsprinzipal nur die Berechtigungen, die zum Ausführen der jeweiligen Aufgaben erforderlich sind. Durch Einschränken des Zugriffs auf Ressourcen kann sowohl ein unbeabsichtigter als auch böswilliger Missbrauch Ihrer Daten verhindert werden. | - |
| Schützen Ihrer Kontozugriffsschlüssel mit Azure Key Vault | Microsoft empfiehlt die Verwendung von Azure AD zum Autorisieren von Anforderungen an Azure Storage. Wenn jedoch eine Autorisierung mit gemeinsam verwendetem Schlüssel erforderlich ist, schützen Sie Ihre Kontoschlüssel mit Azure Key Vault. Sie können die Schlüssel zur Laufzeit aus dem Schlüsseltresor abrufen, anstatt Sie mit der Anwendung zu speichern. | - |
| Regelmäßiges Neugenerieren der Kontoschlüssel | Durch regelmäßiges Rotieren der Kontoschlüssel verringert sich das Risiko, dass Ihre Daten für böswillige Akteure offengelegt werden. | - |
| Beachten des Prinzips der geringsten Rechte beim Zuweisen von Berechtigungen für eine SAS | Geben Sie beim Erstellen einer SAS nur die Berechtigungen an, die der Client zum Ausführen seiner Funktion benötigt. Durch Einschränken des Zugriffs auf Ressourcen kann sowohl ein unbeabsichtigter als auch böswilliger Missbrauch Ihrer Daten verhindert werden. | - |
| Bereithalten eines Sperrungsplans für alle SAS, die Sie für Clients ausstellen | Wenn eine SAS gefährdet ist, sollten Sie diese SAS so bald wie möglich widerrufen. Zum Widerrufen einer SAS für die Benutzerdelegierung widerrufen Sie den Benutzerdelegierungsschlüssel, um alle diesem Schlüssel zugeordneten Signaturen schnell ungültig zu machen. Zum Widerrufen einer Dienst-SAS, die einer gespeicherten Zugriffsrichtlinie zugeordnet ist, können Sie die gespeicherte Zugriffsrichtlinie löschen, die Richtlinie umbenennen oder die Ablaufzeit in einen Zeitpunkt in der Vergangenheit ändern. Weitere Informationen finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../common/storage-sas-overview.md).  | - |
| Festlegen der Ablaufzeit auf maximal eine Stunde, wenn eine Dienst-SAS keiner gespeicherten Zugriffsrichtlinie zugeordnet ist | Eine Dienst-SAS, die keiner gespeicherten Zugriffsrichtlinie zugeordnet ist, kann nicht widerrufen werden. Aus diesem Grund wird empfohlen, die Ablaufzeit so einzuschränken, dass die SAS höchstens eine Stunde lang gültig ist. | - |

## <a name="networking"></a>Netzwerk

| Empfehlung | Kommentare | Defender für Cloud |
|-|----|--|
| Configure the minimum required version of Transport Layer Security (TLS) for a storage account. (Konfigurieren Sie die erforderliche Mindestversion der Transport Layer Security (TLS) für ein Speicherkonto.)  | Legen Sie fest, dass Clients beim Senden von Anforderungen an ein Azure Storage-Konto eine sicherere TLS-Version verwenden, indem Sie die TLS-Mindestversion für dieses Konto konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der erforderlichen Mindestversion der Transport Layer Security (TLS) für ein Speicherkonto](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Aktivieren der Option **Sichere Übertragung erforderlich** für alle Speicherkonten | Wenn Sie die Option **Sichere Übertragung erforderlich** aktivieren, müssen alle an das Speicherkonto gerichteten Anforderungen über sichere Verbindungen erfolgen. Bei Anforderungen über HTTP treten Fehler auf. Weitere Informationen finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Aktivieren von Firewallregeln | Konfigurieren Sie Firewallregeln, um den Zugriff auf Ihr Speicherkonto auf Anforderungen zu beschränken, die aus angegebenen IP-Adressen oder -Adressbereichen oder einer Liste von Subnetzen in einem virtuellen Azure-Netzwerk (VNET) stammen. Weitere Informationen zum Konfigurieren von Firewallregeln finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Gewähren des Zugriffs vertrauenswürdiger Microsoft-Dienste auf das Speicherkonto | Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden eingehende Datenanforderungen standardmäßig blockiert – es sei denn, die Anforderungen stammen von einem Dienst, der innerhalb eines Azure-VNET agiert, oder aus zulässigen öffentlichen IP-Adressen. Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert. Sie können Anforderungen von anderen Azure-Diensten zulassen, indem Sie eine Ausnahme hinzufügen, um vertrauenswürdigen Microsoft-Diensten den Zugriff auf das Speicherkonto zu gewähren. Weitere Informationen zum Hinzufügen einer Ausnahme für vertrauenswürdige Microsoft-Dienste finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Verwenden privater Endpunkte | Ein privater Endpunkt weist dem Speicherkonto eine private IP-Adresse aus Ihrem Azure-VNET zu. Er sichert den gesamten Datenverkehr zwischen Ihrem VNET und dem Speicherkonto über einen privaten Link. Weitere Informationen zu privaten Endpunkten finden Sie unter [Herstellen einer privaten Verbindung mit einem Speicherkonto mithilfe eines privaten Azure-Endpunkts](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Verwenden von VNET-Diensttags | Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern. Weitere Informationen zu den von Azure Storage unterstützten Diensttags finden Sie unter [Übersicht über Azure-Diensttags](../../virtual-network/service-tags-overview.md). Ein Tutorial, das veranschaulicht, wie mithilfe von Diensttags Netzwerk-Ausgangsregeln erstellt werden, finden Sie unter [Einschränken des Zugriffs auf PaaS-Ressourcen](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Einschränken des Netzwerkzugriffs auf bestimmte Netzwerke | Das Einschränken des Netzwerkzugriffs auf Netzwerke, auf denen Clients mit erforderlichem Zugriff gehostet werden, verringert die Gefährdung Ihrer Ressourcen durch Netzwerkangriffe. | [Ja](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

| Empfehlung | Kommentare | Defender für Cloud |
|-|----|--|
| Nachverfolgen der Autorisierung von Anforderungen | Aktivieren Sie die Azure Storage-Protokollierung, um zu verfolgen, wie jede für Azure Storage durchgeführte Anforderung autorisiert wurde. Die Protokolle geben an, ob eine Anforderung anonym, mithilfe eines OAuth 2.0-Tokens, mit einem gemeinsam verwendeten Schlüssel oder mithilfe einer SAS (Shared Access Signature) durchgeführt wurde. Weitere Informationen finden Sie unter [Azure Storage Analytics-Protokollierung](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Nächste Schritte

- [Dokumentation zur Azure-Sicherheit](../../security/index.yml)
- [Dokumentation zur sicheren Entwicklung](../../security/develop/index.yml)
