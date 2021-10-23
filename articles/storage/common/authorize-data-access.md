---
title: Autorisieren von Datenvorgängen
titleSuffix: Azure Storage
description: Hier erfahren Sie mehr über die verschiedenen Möglichkeiten zum Autorisieren des Zugriffs auf Daten in Azure Storage. Azure Storage unterstützt die Autorisierung mit Azure Active Directory, Shared Key-Autorisierung oder Shared Access Signatures (SAS) und außerdem den anonymen Zugriff auf Blobs.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 911db718fd7c88501006f89a549a371cd12adef4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178180"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorisieren des Zugriffs auf Daten in Azure Storage

Immer wenn Sie auf Daten in Ihrem Speicherkonto zugreifen, sendet Ihre Clientanwendung eine Anforderung über HTTP/HTTPS an Azure Storage. Standardmäßig ist jede Ressource in Azure Storage geschützt, und jede Anforderung an eine sichere Ressource muss autorisiert werden. Die Autorisierung stellt sicher, dass die Clientanwendung die entsprechenden Berechtigungen für den Zugriff auf Daten in Ihrem Speicherkonto hat.

In der folgenden Tabelle werden die Optionen beschrieben, die in Azure Storage zum Autorisieren des Zugriffs auf Daten zur Verfügung stehen:

| Azure-Artefakt | Gemeinsam verwendeter Schlüssel (Speicherkontoschlüssel) | Shared Access Signature (SAS) | Azure Active Directory (Azure AD) | Lokale Active Directory Domain Services | Anonymer öffentlicher Lesezugriff |
|--|--|--|--|--|--|
| Azure-Blobs | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](../blobs/authorize-access-azure-active-directory.md) | Nicht unterstützt | [Unterstützt](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | Nicht unterstützt | [Unterstützt, aber nur mit AAD Domain Services](../files/storage-files-active-directory-overview.md) | [Unterstützt, Anmeldeinformationen müssen mit Azure AD synchronisiert werden](../files/storage-files-active-directory-overview.md) | Nicht unterstützt |
| Azure Files (REST) | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt |
| Azure-Warteschlangen | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](../queues/authorize-access-azure-active-directory.md) | Nicht unterstützt | Nicht unterstützt |
| Azure-Tabellen | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](../tables/authorize-access-azure-active-directory.md) (Vorschauversion) | Nicht unterstützt | Nicht unterstützt |

Im Anschluss werden die einzelnen Autorisierungsoptionen kurz erläutert:

- **Azure Active Directory (Azure AD)-Integration** zum Autorisieren von Anforderungen an Blob-, Warteschlangen- und Tabellenressourcen. Microsoft empfiehlt die Verwendung von Azure AD-Anmeldeinformationen zum Autorisieren von Anforderungen an Daten für optimale Sicherheit und einfache Bedienung, sofern möglich. Weitere Informationen zur Azure AD-Integration finden Sie unter [Autorisieren des Zugriffs auf Daten in Azure Storage](authorize-data-access.md).

    Sie können mithilfe der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) die Berechtigungen eines Sicherheitsprinzipals für Blob-, Warteschlangen- und Tabellenressourcen in einem Speicherkonto verwalten. Außerdem können Sie mit der attributbasierten Zugriffssteuerung (Attribute-Based Access Control, ABAC) von Azure Bedingungen zu Azure-Rollenzuweisungen für Blobressourcen hinzufügen. Weitere Informationen zu RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md). Weitere Informationen zu ABAC finden Sie unter [Was ist die attributbasierte Zugriffssteuerung von Azure (Azure ABAC)? (Vorschau)](../../role-based-access-control/conditions-overview.md).

- **Azure Active Directory Domain Services-Authentifizierung (Azure AD DS)** für Azure Files. Azure Files unterstützt die identitätsbasierte Autorisierung per Server Message Block (SMB) über Azure AD DS. Sie können die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für eine präzise Steuerung des Clientzugriffs auf Azure Files-Ressourcen in einem Speicherkonto verwenden. Weitere Informationen zur Azure Files-Authentifizierung mithilfe von Domänendiensten finden Sie in der [Übersicht](../files/storage-files-active-directory-overview.md).

- **Authentifizierung mit lokalen Active Directory Domain Services (AD DS oder lokale AD DS)** für Azure Files. Azure Files unterstützen die identitätsbasierte Autorisierung über SMB durch AD DS. Ihre AD DS-Umgebung kann auf lokalen Computern oder auf Azure-VMs gehostet werden. Der SMB-Zugriff auf Dateien wird mithilfe von AD DS-Anmeldeinformationen von Computern aus unterstützt, die in die Domäne eingebunden sind – entweder lokal oder in Azure. Sie können eine Kombination aus Azure RBAC für die Zugriffssteuerung auf Freigabeebene und aus NTFS-DACLs für die Berechtigungserzwingung auf Verzeichnis-/Dateiebene verwenden. Weitere Informationen zur Azure Files-Authentifizierung mithilfe von Domänendiensten finden Sie in der [Übersicht](../files/storage-files-active-directory-overview.md).

- **Autorisierung mit gemeinsam verwendetem Schlüssel** für Blobs, Dateien, Warteschlangen und Tabellen. Ein Client mit gemeinsam verwendetem Schlüssel übergibt mit jeder Anforderung einen Header, der mit dem Speicherkonto-Zugriffsschlüssel signiert wird. Weitere Informationen finden Sie unter [Authentifizieren mit gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key/).

    Sie können die Autorisierung mit gemeinsam verwendetem Schlüssel für ein Speicherkonto verweigern. Wenn die Autorisierung mit gemeinsam verwendetem Schlüssel unzulässig ist, müssen Clients Azure AD verwenden, um Anforderungen für Daten in diesem Speicherkonto zu autorisieren. Weitere Informationen finden Sie unter [Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto](shared-key-authorization-prevent.md).

- **Shared Access Signatures** für Blobs, Dateien, Warteschlangen und Tabellen. Shared Access Signatures (SAS) ermöglichen den begrenzten delegierten Zugriff auf Ressourcen in einem Speicherkonto. Einschränkungen des Zeitintervalls, für das die Signatur gültig ist, oder von Berechtigungen, die sie gewährt, bieten Flexibilität beim Verwalten des Zugriffs. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures (SAS)](storage-sas-overview.md).

- **Anonymer öffentlicher Lesezugriff** für Container und Blobs. Wenn anonymer Zugriff konfiguriert wurde, können Clients Blobdaten ohne Autorisierung lesen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/anonymous-read-access-configure.md).

    Sie können den anonymen öffentlichen Lesezugriff für ein Speicherkonto verweigern. Wenn der anonyme öffentliche Lesezugriff verweigert wird, können Benutzer Container nicht so konfigurieren, dass der anonyme Zugriff aktiviert wird, und alle Anforderungen müssen autorisiert werden. Weitere Informationen finden Sie unter [Verhindern des anonymem, öffentlichen Lesezugriffs auf Container und Blobs](../blobs/anonymous-read-access-prevent.md).

## <a name="next-steps"></a>Nächste Schritte

- [Autorisieren des Zugriffs auf Daten in Azure Storage](authorize-data-access.md)
- [Autorisieren mit einem gemeinsam verwendeten Schlüssel](/rest/api/storageservices/authorize-with-shared-key/)
- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md)
        