---
title: Übersicht über verwaltetes Azure-HSM | Microsoft-Dokumentation
description: Verwaltetes Azure-HSM ist ein Clouddienst zum Schutz Ihrer kryptografischen Schlüssel für Cloudanwendungen.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 5cbb4496b9317e64c0cdec8e78bf825d19d8bd38
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258621"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Was ist verwaltetes HSM von Azure Key Vault?

Verwaltetes HSM von Azure Key Vault ist ein vollständig verwalteter, hochverfügbarer und standardkonformer Einzelmandanten-Clouddienst. Dieser Dienst ermöglicht es Ihnen, kryptografische Schlüssel für Ihre Cloudanwendungen über Hardwaresicherheitsmodule (HSMs) zu schützen, die mit **FIPS 140-2 Level 3** validiert sind. Informationen zu den Preisen finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/) im Abschnitt „Verwaltete HSM-Pools“. 

## <a name="why-use-managed-hsm"></a>Gründe für die Verwendung von verwaltetem HSM

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Vollständig verwaltetes, hochverfügbares Einzelmandanten-HSM als Dienst

- **Vollständig verwaltet:** Bereitstellung, Konfiguration, Patching und Wartung des HSM werden durch den Dienst erledigt. 
- **Hochverfügbar und zonenresilient** (wenn Verfügbarkeitszonen unterstützt werden): Jeder HSM-Cluster besteht aus mehreren HSM-Partitionen, die sich über mindestens zwei Verfügbarkeitszonen erstrecken. Im Falle eines Hardwarefehlers werden Mitgliedspartitionen für Ihren HSM-Cluster automatisch zu fehlerfreien Knoten migriert.
- **Ein Mandant:** Jede verwaltete HSM-Instanz ist für einen einzelnen Kunden dediziert und besteht aus einem Cluster mit mehreren HSM-Partitionen. Jeder HSM-Cluster verwendet eine separate, kundenspezifische Sicherheitsdomäne, um die HSM-Cluster der einzelnen Kunden kryptografisch zu isolieren.


### <a name="access-control-enhanced-data-protection--compliance"></a>Zugriffssteuerung, verbesserter Schutz von Daten und Konformität

- **Zentrale Schlüsselverwaltung:** Verwalten Sie kritische Schlüssel mit hohem Wert für Ihre gesamte Organisation an einem zentralen Ort. Dank präziser schlüsselspezifischer Berechtigungen können Sie den Zugriff auf die einzelnen Schlüssel gemäß dem Prinzip der möglichst geringen Zugriffsberechtigungen steuern.
- **Isolierte Zugriffssteuerung:** Mit dem Zugriffssteuerungsmodell der lokalen RBAC für verwaltete HSMs haben designierte HSM-Clusteradministratoren die volle Kontrolle über die HSMs und damit sogar Vorrang vor Verwaltungsgruppen-, Abonnement- und Ressourcengruppenadministratoren.
- **Private Endpunkte**: Verwenden Sie private Endpunkte, um von Ihrer Anwendung, die in einem virtuellen Netzwerk ausgeführt wird, eine sichere und private Verbindung mit dem verwalteten HSM herzustellen.
- **FIPS 140-2 Level 3-zertifizierte HSMs**: Schützen Sie Ihre Daten, und erfüllen Sie Complianceanforderungen mit FIPS 140-2 Level 3-zertifizierten HSMs (Federal Information Protection Standard). Verwaltete HSMs verwenden HSM-Adapter von Marvell LiquidSecurity.
- **Überwachen und Überprüfen:** Profitieren Sie von der vollständigen Azure Monitor-Integration. Erhalten Sie umfassende Protokolle aller Aktivitäten über Azure Monitor. Verwenden Sie Azure Log Analytics für Analysen und Warnungen.
- **Datenresidenz:** Von verwaltetem HSM werden Kundendaten nicht außerhalb der Region gespeichert oder verarbeitet, in der der Kunde die HSM-Instanz bereitstellt.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integration in Azure- und Microsoft-Dienste (PaaS/SaaS) 

- Generieren Sie Schlüssel (oder importieren Sie sie unter Verwendung von [BYOK](hsm-protected-keys-byok.md)), und verwenden Sie sie zum Verschlüsseln ruhender Daten in Azure-Diensten wie [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) und [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Verwendung der gleichen API und Verwaltungsschnittstellen wie Key Vault

- Bereits vorhandene Anwendungen, die einen Tresor (mehrere Mandanten) nutzen, können ganz einfach für die Verwendung verwalteter HSMs migriert werden.
- Nutzen Sie für alle Ihre Anwendungen die gleichen Muster für die Anwendungsentwicklung und -bereitstellung – unabhängig von der verwendeten Schlüsselverwaltungslösung (Tresore mit mehreren Mandanten oder verwaltete HSMs mit einem einzelnen Mandanten).

### <a name="import-keys-from-your-on-premise-hsms"></a>Importieren von Schlüsseln aus Ihren lokalen HSMs

- Generieren Sie durch HSM geschützte Schlüssel in Ihrem lokalen HSM, und importieren Sie sie sicher in verwaltete HSMs.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md). Dort erfahren Sie, wie Sie ein verwaltetes HSM erstellen und aktivieren.
- Informieren Sie sich über [bewährte Methoden bei der Verwendung verwalteter HSMs von Azure Key Vault](best-practices.md).
- [Verwaltetes HSM: Status](https://status.azure.com)
- [SLA für Verwaltetes HSM](https://azure.microsoft.com/support/legal/sla/key-vault-managed-hsm/v1_0/)
- [Regionale Verfügbarkeit von verwaltetem HSM](https://azure.microsoft.com/global-infrastructure/services/?products=key-vault)
