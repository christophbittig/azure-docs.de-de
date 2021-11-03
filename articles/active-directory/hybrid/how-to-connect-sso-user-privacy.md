---
title: Datenschutz und nahtloses einmaliges Anmelden von Azure AD | Microsoft-Dokumentation
description: In diesem Artikel geht es um das nahtlose einmalige Anmelden für Azure Active Directory (Azure AD) und die Einhaltung der DSGVO.
services: active-directory
keywords: Was ist Azure AD Connect, DSGVO, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21d4253da1d3e4b190cfad92349ee885541766c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067983"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Datenschutz und nahtloses einmaliges Anmelden von Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Übersicht


Beim nahtlosen einmaligen Anmelden für Azure AD wird der folgende Protokolltyp erstellt, der personenbezogene Daten umfassen kann: 

- Azure AD Connect-Ablaufverfolgungsprotokoll-Dateien

Verbessern Sie den Datenschutz für nahtloses einmaliges Anmelden auf zwei Arten:

1.  Extrahieren von Daten für eine Person und Entfernen der Daten dieser Person aus Installationen auf Anforderung
2.  Sicherstellen, dass keine Daten länger als 48 Stunden beibehalten werden

Wir raten Ihnen dringend, die zweite Option zu verwenden, da diese einfacher zu implementieren und zu verwalten ist. Hier sind die Anleitungen für die einzelnen Protokolltypen angegeben:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Löschen von Azure AD Connect-Ablaufverfolgungsprotokoll-Dateien

Überprüfen Sie den Inhalt des Ordners **%ProgramData%\AADConnect**, und löschen Sie den Inhalt des Ablaufverfolgungsprotokolls (**trace-\*.log**-Dateien) des Ordners innerhalb von 48 Stunden nach dem Installieren oder Aktualisieren von Azure AD Connect oder dem Ändern des nahtlosen einmaligen Anmeldens. Der Grund ist, dass mit dieser Aktion ggf. Daten erstellt werden, die unter die DSGVO fallen.

>[!IMPORTANT]
>Löschen Sie nicht die Datei **PersistedState.xml** in diesem Ordner, da diese Datei zum Verwalten des Zustands der vorherigen Installation von Azure AD Connect und nach einer Upgradeinstallation verwendet wird. Diese Datei enthält niemals Daten zu einer Person und sollte nie gelöscht werden.

Sie können diese Ablaufverfolgungsprotokoll-Dateien entweder mit Windows-Explorer überprüfen und löschen, oder Sie können das folgende PowerShell-Skript verwenden, um die erforderlichen Aktionen durchzuführen:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Speichern Sie das Skript in einer Datei mit der Erweiterung „.PS1“. Führen Sie dieses Skript je nach Bedarf aus.

Weitere Informationen zu verwandten DSGVO-Anforderungen für Azure AD Connect finden Sie in [diesem Artikel](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Hinweis zu Domänencontrollerprotokollen

Wenn die Überwachungsprotokollierung aktiviert ist, kann dieses Produkt Sicherheitsprotokolle für Ihre Domänencontroller generieren. Weitere Informationen zum Konfigurieren von Überwachungsrichtlinien finden Sie in [diesem Artikel](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Nächste Schritte

* [Die Microsoft-Datenschutzrichtlinie im Trust Center](https://www.microsoft.com/trustcenter)
  - [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
  - [**UserVoice:**](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) Verfassen neuer Feature-Anforderungen