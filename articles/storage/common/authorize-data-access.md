---
title: Autorisieren von Datenvorgängen
titleSuffix: Azure Storage
description: Erhalten Sie Informationen über die verschiedenen Methoden zum Autorisieren des Zugriffs auf Azure Storage, z. B. Azure Active Directory, Autorisierung mit gemeinsam verwendetem Schlüssel oder Shared Access Signatures (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 490b0b109e4b89b01b748d2d6aa71b4477f8a8ef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560587"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Autorisieren des Zugriffs auf Daten in Azure Storage

Immer dann, wenn Sie auf Daten in Ihrem Speicherkonto zugreifen, sendet der Client eine Anforderung über HTTP/HTTPS an Azure Storage. Jede Anforderung an eine sichere Ressource muss autorisiert sein, damit der Dienst sicherstellt, dass der Client über die erforderlichen Berechtigungen zum Zugriff auf die Daten verfügt.

In der folgenden Tabelle werden die Optionen beschrieben, die in Azure Storage zum Autorisieren des Ressourcenzugriffs zur Verfügung stehen:

| Azure-Artefakt | Gemeinsam verwendeter Schlüssel (Speicherkontoschlüssel) | Shared Access Signature (SAS) | Azure Active Directory (Azure AD) | Lokale Active Directory Domain Services | Anonymer öffentlicher Lesezugriff |
|--|--|--|--|--|--|
| Azure-Blobs | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](authorize-data-access.md) | Nicht unterstützt | [Unterstützt](../blobs/anonymous-read-access-configure.md) |
| Azure Files (SMB) | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | Nicht unterstützt | [Unterstützt, aber nur mit AAD Domain Services](../files/storage-files-active-directory-overview.md) | [Unterstützt, Anmeldeinformationen müssen mit Azure AD synchronisiert werden](../files/storage-files-active-directory-overview.md) | Nicht unterstützt |
| Azure Files (REST) | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt |
| Azure-Warteschlangen | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](authorize-data-access.md) | Nicht unterstützt | Nicht unterstützt |
| Azure-Tabellen | [Unterstützt](/rest/api/storageservices/authorize-with-shared-key/) | [Unterstützt](storage-sas-overview.md) | [Unterstützt](../tables/authorize-access-azure-active-directory.md) (Vorschauversion) | Nicht unterstützt | Nicht unterstützt |

Im Anschluss werden die einzelnen Autorisierungsoptionen kurz erläutert:

- **Integration von Azure Active Directory (Azure AD)** für Blobs, Warteschlangen und Tabellen. Azure bietet rollenbasierte Zugriffssteuerung in Azure (Azure RBAC, Azure Role-Based Access Control) für eine Steuerung des Zugriffs eines Clients auf Ressourcen in einem Speicherkonto. Microsoft empfiehlt, falls möglich, die Verwendung von Azure AD für optimale Sicherheit und Benutzerfreundlichkeit. Weitere Informationen zur Integration Azure AD finden Sie unter [Autorisieren des Zugriffs auf Daten in Azure Storage](authorize-data-access.md).

- **Azure Active Directory Domain Services-Authentifizierung (Azure AD DS)** für Azure Files. Azure Files unterstützt die identitätsbasierte Autorisierung per Server Message Block (SMB) über Azure AD DS. Sie können die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für eine präzise Steuerung des Clientzugriffs auf Azure Files-Ressourcen in einem Speicherkonto verwenden. Weitere Informationen zur Azure Files-Authentifizierung mithilfe von Domänendiensten finden Sie in der [Übersicht](../files/storage-files-active-directory-overview.md).

- **Authentifizierung mit lokalen Active Directory Domain Services (AD DS oder lokale AD DS)** für Azure Files. Azure Files unterstützen die identitätsbasierte Autorisierung über SMB durch AD DS. Ihre AD DS-Umgebung kann auf lokalen Computern oder auf Azure-VMs gehostet werden. Der SMB-Zugriff auf Dateien wird mithilfe von AD DS-Anmeldeinformationen von Computern aus unterstützt, die in die Domäne eingebunden sind – entweder lokal oder in Azure. Sie können eine Kombination aus Azure RBAC für die Zugriffssteuerung auf Freigabeebene und aus NTFS-DACLs für die Berechtigungserzwingung auf Verzeichnis-/Dateiebene verwenden. Weitere Informationen zur Azure Files-Authentifizierung mithilfe von Domänendiensten finden Sie in der [Übersicht](../files/storage-files-active-directory-overview.md).

- **Autorisierung mit gemeinsam verwendetem Schlüssel** für Blobs, Dateien, Warteschlangen und Tabellen. Ein Client mit gemeinsam verwendetem Schlüssel übergibt mit jeder Anforderung einen Header, der mit dem Speicherkonto-Zugriffsschlüssel signiert wird. Weitere Informationen finden Sie unter [Authentifizieren mit gemeinsam verwendetem Schlüssel](/rest/api/storageservices/authorize-with-shared-key/).

- **Shared Access Signatures** für Blobs, Dateien, Warteschlangen und Tabellen. Shared Access Signatures (SAS) ermöglichen den begrenzten delegierten Zugriff auf Ressourcen in einem Speicherkonto. Einschränkungen des Zeitintervalls, für das die Signatur gültig ist, oder von Berechtigungen, die sie gewährt, bieten Flexibilität beim Verwalten des Zugriffs. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures (SAS)](storage-sas-overview.md).
- **Anonymer öffentlicher Lesezugriff** für Container und Blobs. Autorisierung ist nicht erforderlich. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/anonymous-read-access-configure.md).

## <a name="next-steps"></a>Nächste Schritte

- [Autorisieren des Zugriffs auf Daten in Azure Storage](authorize-data-access.md)
- [Autorisieren mit einem gemeinsam verwendeten Schlüssel](/rest/api/storageservices/authorize-with-shared-key/)
- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md)
