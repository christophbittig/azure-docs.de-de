---
title: Übersicht über Microsoft Azure Confidential Ledger
description: Hier finden Sie eine Übersicht über Microsoft Azure Confidential Ledger, einen äußerst sicheren Dienst für die Verwaltung vertraulicher Datensätze.
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2f0636d5ef4b4e1fa3a9576912630470af7fa388
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337792"
---
# <a name="microsoft-azure-confidential-ledger-preview"></a>Microsoft Azure Confidential Ledger (Vorschauversion)

Microsoft Azure Confidential Ledger (ACL) ist ein neuer und äußerst sicherer Dienst für die Verwaltung vertraulicher Datensätze. Azure Confidential Ledger basiert auf einem genehmigten Blockchainmodell und bietet Vorteile für die Datenintegrität, die Sie nirgendwo anders finden. Dazu zählen die Unveränderlichkeit, sodass der Ledger nur Anfügevorgänge unterstützt, und der Manipulationsschutz, sodass alle Datensätze garantiert intakt bleiben.

Confidential Ledger kann ausschließlich auf hardwaregestützten Secure Enclaves ausgeführt werden. Diese stark überwachte und isolierte Runtimeumgebung verhindert potenzielle Angriffe. Darüber hinaus steht niemand über dem Ledger, nicht einmal Microsoft. Indem wir uns selbst aus der Lösung herausgenommen haben, wird Azure Confidential Ledger auf Grundlage einer minimalistischen Trusted Computing Base (TCB) ausgeführt, wodurch der Zugriff nur durch Ledger-Dienstentwickler, Rechenzentrumstechniker und Cloudadministratoren möglich ist.

Azure Confidential Ledger ist für Anwendungsfälle vorgesehen, in denen essenzielle Metadaten-Datensätze nicht geändert werden dürfen, u. a. dauerhaft zur Einhaltung gesetzlicher Bestimmungen und für Archivierungszwecke. Hier finden Sie ein paar Beispiele dafür, was im Ledger gespeichert werden kann:

- Datensätze zu Geschäftstransaktionen (z. B. Geldüberweisungen oder Änderungen an vertraulichen Dokumenten)
- Änderungen an vertrauenswürdigen Ressourcen (z. B. Kernanwendungen oder Verträge)
- Änderungen an der Verwaltung und Steuerung (z. B. das Erteilen von Zugriffsberechtigungen)
- Ereignisse in Bezug auf die betriebliche Sicherheit und die IT-Sicherheit (z. B. Microsoft Defender für Cloud-Warnungen)

Sehen Sie sich die [Demo zu Azure Confidential Ledger von der Microsoft Ignite 2020](https://mediusprodstatic.studios.ms/asset-b88de19d-4187-40c4-98f2-a65efc419e2a/OD221_1920x1080_AACAudio_1461.mp4?sv=2018-03-28&sr=b&sig=k5roi6WXnlqK1zP0fs5KYlJd4FD3Nuaf97z%2B2gV0aTs%3D&st=2020-09-22T08%3A05%3A01Z&se=2025-09-22T08%3A10%3A01Z&sp=r&rscd=filename%3DIG20-OD221-Inside%2BAzure%2BDatacenter%2BArchitecture%2Bwith%2BMark%2BRu.mp4) an, um mehr zu erfahren.

## <a name="key-features"></a>Wichtige Funktionen

Confidential Ledger wird über REST-APIs verfügbar gemacht, die in neue oder vorhandene Anwendungen integriert werden können. Confidential Ledger kann über Verwaltungs-APIs (Steuerungsebene) durch Administratoren verwaltet werden. Der Dienst kann über funktionale APIs (Datenebene) auch direkt durch Anwendungscode aufgerufen werden. Die Verwaltungs-APIs unterstützen grundlegende Vorgänge wie das Erstellen, Aktualisieren, Abrufen und Löschen. Die funktionalen APIs ermöglichen die direkte Interaktion mit Ihrem instanziierten Ledger und Vorgänge wie das Speichern und Abrufen von Daten.

## <a name="ledger-security"></a>Ledgersicherheit

In diesem Abschnitt werden die Sicherheitsmaßnahmen für den Ledger definiert. Die Ledger-APIs verwenden die clientzertifikatbasierte Authentifizierung. Derzeit unterstützt der Ledger den zertifikatbasierten Authentifizierungsprozess mit Besitzerrollen. Die Unterstützung für die AAD-basierte Authentifizierung (Azure Active Directory) und den rollenbasierten Zugriff (z. B. Besitzer, Leser oder Mitwirkender) werden noch hinzugefügt.

Daten werden über eine TLS 1.2-Verbindung an den Ledger gesendet. Diese Verbindung wird in den hardwaregestützten Secure Enclaves (Intel® SGX-Enclaves) dann getrennt. Dadurch wird sichergestellt, dass niemand die Verbindung zwischen dem Client eines Kunden und den Confidential Ledger-Serverknoten abfangen kann.

### <a name="ledger-storage"></a>Ledgerspeicher

Confidential Ledger-Instanzen werden als Blöcke in Blobspeichercontainern erstellt, die zu einem Azure Storage-Konto gehören. Transaktionsdaten können je nach Ihren Anforderungen entweder verschlüsselt oder als Klartext gespeichert werden. Wenn Sie einen Ledger erstellen, ordnen Sie ein Speicherkonto mithilfe der unter [Registrieren eines Confidential Ledger-Dienstprinzipals](register-ledger-service-principal.md) beschriebenen Schritte zu.

Confidential Ledger kann über Verwaltungs-APIs (Steuerungsebene) durch Administratoren verwaltet und über funktionale APIs (Datenebene) direkt durch Anwendungscode aufgerufen werden. Die Verwaltungs-APIs unterstützen grundlegende Vorgänge wie das Erstellen, Aktualisieren, Abrufen und Löschen.

Die funktionalen APIs ermöglichen die direkte Interaktion mit Ihrer Confidential Ledger-Instanz und Vorgänge wie das Speichern und Abrufen von Daten.

## <a name="preview-limitations"></a>Einschränkungen der Vorschauversion

- Sobald eine Confidential Ledger-Instanz erstellt wurde, können Sie den Ledgertyp nicht mehr ändern.
- Confidential Ledger unterstützt die standardmäßig verwendete Azure-Notfallwiederherstellung derzeit nicht. Azure Confidential Ledger bietet jedoch eine integrierte Redundanz innerhalb der Azure-Region, da der Dienst auf mehreren unabhängigen Knoten ausgeführt wird.
- Die Löschung von Azure Confidential Ledger-Instanzen ist endgültig, sodass Ihre Daten anschließend nicht wiederhergestellt werden können.
- Namen für Azure Confidential Ledger-Instanzen müssen global eindeutig sein. Ledger mit dem gleichen Namen sind unabhängig von ihrem Typ nicht zulässig.

## <a name="terminology"></a>Begriff

| Begriff | Definition |
|--|--|
| ACL | Azure Confidential Ledger |
| Ledger | Ein unveränderlicher Anfügedatensatz für Transaktionen (auch als Blockchain bezeichnet) |
| Commit | Hierbei handelt es sich um eine Bestätigung, dass für eine Transaktion ein lokaler Commit auf einen Knoten ausgeführt wurde. Ein lokaler Commit allein garantiert nicht, dass eine Transaktion Teil des Ledgers ist. |
| Globaler Commit | Eine Bestätigung, dass für die Transaktion ein globaler Commit ausgeführt wurde und sie Teil des Ledgers ist |
| Rechnung | Der Nachweis, dass die Transaktion vom Ledger verarbeitet wurde |

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Azure Confidential Ledger-Architektur](architecture.md)
- [Schnellstart: Azure-Portal](quickstart-portal.md)
- [Schnellstart: Python](quickstart-python.md)
- [Schnellstart: ARM-Vorlage (Azure Resource Manager)](quickstart-portal.md)
