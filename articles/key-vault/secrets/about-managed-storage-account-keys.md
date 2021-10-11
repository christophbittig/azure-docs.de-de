---
title: 'Informationen zu mit Azure Key Vault verwalteten Speicherkontoschlüsseln: Azure Key Vault'
description: Übersicht über mit Azure Key Vault verwaltete Speicherkontoschlüssel
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390653"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>Informationen zu mit Azure Key Vault verwalteten Speicherkontoschlüsseln

> [!IMPORTANT]
> Wir empfehlen die Verwendung der Azure Storage-Integration in Azure Active Directory (Azure AD), dem cloudbasierten Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Die Azure AD-Integration ist für [Azure-Blobs und -Warteschlangen](../../storage/blobs/authorize-access-azure-active-directory.md) verfügbar und bietet tokenbasierten OAuth2-Zugriff auf Azure Storage (genau wie Azure Key Vault). Azure AD ermöglicht es Ihnen, Ihre Clientanwendung zu authentifizieren, indem Sie eine Anwendungs- oder Benutzeridentität anstelle von Speicherkontoanmeldeinformationen verwenden. Sie können eine [von Azure AD verwaltete Identität](../../active-directory/managed-identities-azure-resources/index.yml) verwenden, wenn Sie Ihre Clientanwendung in Azure ausführen. Verwaltete Identitäten machen die Clientauthentifizierung und das Speichern von Anmeldeinformationen in oder mit Ihrer Anwendung überflüssig. Verwenden Sie die folgende Lösung nur, wenn keine Azure AD Authentifizierung möglich ist.

Ein Azure-Speicherkonto verwendet Anmeldeinformationen, die sich aus einem Kontonamen und einem Schlüssel zusammensetzen. Der Schlüssel wird automatisch generiert und fungiert eher als ein Kennwort denn als ein kryptografischer Schlüssel. Key Vault verwaltet Speicherkontoschlüssel, indem sie im Speicherkonto regelmäßig neu generiert werden, und stellt SAS-Token für den delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto zur Verfügung.

Sie können das Key Vault-Feature für verwaltete Speicherkontoschlüssel verwenden, um Schlüssel für ein Azure Storage-Konto aufzulisten (synchronisieren) und die Schlüssel in regelmäßigen Abständen erneut zu generieren (rotieren). Sie können Schlüssel sowohl für Speicherkonten als auch für klassische Speicherkonten verwalten.

## <a name="azure-storage-account-key-management"></a>Verwaltung eines Azure-Speicherkontoschlüssels

Key Vault kann [Azure-Speicherkontoschlüssel](../../storage/common/storage-account-overview.md) verwalten:

- Intern kann Key Vault die Schlüssel eines Azure-Speicherkontos auflisten (synchronisieren). 
- Key Vault kann die Schlüssel in regelmäßigen Abständen erneut generieren (rotieren).
- Schlüsselwerte werden nie als Antwort an den Aufrufer zurückgegeben.
- Key Vault verwaltet Schlüssel von Speicherkonten und klassischen Speicherkonten.

Weitere Informationen finden Sie unter
- [Speicherkonto-Zugriffsschlüssel](../../storage/common/storage-account-keys-manage.md)
- [Verwaltung von Speicherkontoschlüsseln in Azure Key Vault](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>Speicherkonto-Zugriffssteuerung

Die folgenden Berechtigungen können beim Autorisieren eines Benutzer- oder Anwendungsprinzipals zur Ausführung von Vorgängen in einem verwalteten Speicherkonto verwendet werden:  

- Berechtigungen für Vorgänge in verwalteten Speicherkonten und SAS-Definitionen
  - *get*: Abrufen von Informationen zu einem Speicherkonto 
  - *list*: Auflisten der von einem Schlüsseltresor verwalteten Speicherkonten
  - *update*: Aktualisieren eines Speicherkontos
  - *delete*: Löschen von Speicherkonten  
  - *recover*: Wiederherstellen eines gelöschten Speicherkontos
  - *backup*: Sichern eines Speicherkontos
  - *restore*: Wiederherstellen eines gesicherten Speicherkontos in einem Schlüsseltresor
  - *set*: Erstellen oder Aktualisieren eines Speicherkontos
  - *regeneratekey*: Erneutes Generieren eines angegebenen Schlüsselwerts für ein Speicherkonto
  - *getsas*: Abrufen von Informationen über eine SAS-Definition für ein Speicherkonto
  - *listsas*: Auflisten der Speicher-SAS-Definitionen für ein Speicherkonto
  - *deletesas*: Löschen einer SAS-Definition aus einem Speicherkonto
  - *setsas*: Erstellen oder Aktualisieren einer neuen SAS-Definition bzw. von neuen SAS-Attributen für ein Speicherkonto

- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines verwalteten Speicherkontos

Weitere Informationen finden Sie in der [REST-API-Referenz für Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Speicherkontoschlüsseln mit Key Vault und der Azure-Befehlszeilenschnittstelle](overview-storage-keys.md)
- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../general/about-keys-secrets-certificates.md)
- [Best Practices für die Geheimnisverwaltung in Key Vault](secrets-best-practices.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
