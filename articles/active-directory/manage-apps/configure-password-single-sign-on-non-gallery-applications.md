---
title: Hinzufügen des kennwortbasierten einmaligen Anmeldens zu einer Anwendung
description: Fügen Sie das kennwortbasierte einmalige Anmelden einer Anwendung in Azure Active Directory hinzu.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 0d54ba24ee13bc0084dbe6f4a14d54e38c94cded
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546840"
---
# <a name="add-password-based-single-sign-on-to-an-application"></a>Hinzufügen des kennwortbasierten einmaligen Anmeldens zu einer Anwendung

In diesem Artikel wird beschrieben, wie Sie das kennwortbasierte einmalige Anmelden (SSO) in Azure Active Directory (Azure AD) einrichten. Beim kennwortbasierten einmaligen Anmelden melden Sie sich beim erstmaligen Zugriff auf eine Anwendung mit einem Benutzernamen und einem Kennwort bei dieser an. Nach der ersten Anmeldung sendet Azure AD den Benutzernamen und das Kennwort an die Anwendung. 

Beim kennwortbasierten einmaligen Anmelden wird der vorhandene Authentifizierungsvorgang der Anwendung verwendet. Wenn Sie das kennwortbasierte einmalige Anmelden für eine Anwendung aktivieren, werden Benutzernamen und Kennwörter für die Anwendung in Azure AD gesammelt und sicher gespeichert. Anmeldeinformationen des Benutzers werden in einem verschlüsselten Zustand im Verzeichnis gespeichert. Das kennwortbasierte einmalige Anmelden wird für jede cloudbasierte Anwendung unterstützt, die über eine HTML-basierte Anmeldeseite verfügt.

Wählen Sie kennwortbasiertes einmaliges Anmelden in folgenden Fällen aus:
- Eine Anwendung unterstützt das SAML SSO-Protokoll nicht.
- Eine Anwendung authentifiziert sich mit Benutzername und Kennwort anstelle von Zugriffstoken und Headern.

Die Konfigurationsseite für das kennwortbasierte einmalige Anmelden ist einfach. Sie enthält nur die URL der Anmeldeseite, die von der Anwendung verwendet wird. Bei dieser Zeichenfolge muss es sich um die Seite handeln, die das Eingabefeld für den Benutzernamen enthält.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren des kennwortbasierten einmaligen Anmeldens in Ihrem Azure AD-Mandanten benötigen Sie Folgendes:
-   Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
-   Eine Anwendung, die kennwortbasiertes einmaliges Anmelden unterstützt

## <a name="configure-password-based-single-sign-on"></a>Konfigurieren des kennwortbasierten einmaligen Anmeldens

1.  Melden Sie sich mit der entsprechenden Rolle beim [Azure-Portal](https://portal.azure.com) an.
1.  Wählen Sie in „Azure-Dienste“ **Azure Active Directory** und dann **Unternehmensanwendungen** aus.
1.  Suchen die Anwendung, der das kennwortbasierte einmalige Anmelden hinzugefügt werden soll, und wählen Sie sie aus.
1.  Wählen Sie **Einmaliges Anmelden** und dann **Kennwortbasiert** aus.
1.  Geben Sie die URL für die Anmeldeseite der Anwendung ein.
1.  Wählen Sie **Speichern** aus. 

Azure AD analysiert den HTML-Code der Anmeldeseite für die Eingabefelder „Benutzername“ und „Kennwort“. Wenn der Versuch erfolgreich ist, sind Sie fertig. Der nächste Schritt ist das [Zuweisen von Benutzern oder Gruppen](add-application-portal-assign-users.md) zur Anwendung. 

Nachdem Sie Benutzer und Gruppen zugewiesen haben, können Sie die Anmeldeinformationen angeben, die für einen Benutzer verwendet werden sollen, wenn sich dieser bei der Anwendung anmeldet. 

1. Wählen Sie **Benutzer und Gruppen** aus, aktivieren Sie das Kontrollkästchen neben der Zeile für den Benutzer bzw. die Gruppe, und wählen Sie anschließend **Anmeldeinformationen aktualisieren** aus. 
1. Geben Sie den Benutzernamen und das Kennwort ein, die für den Benutzer oder die Gruppe verwendet werden sollen. Wenn Sie das nicht tun, werden die Benutzer beim Start aufgefordert, die Anmeldeinformationen selbst einzugeben.

## <a name="manual-configuration"></a>Manuelle Konfiguration

Falls der Analyseversuch von Azure AD nicht erfolgreich ist, können Sie die Anmeldung manuell konfigurieren.

1. Wählen Sie **{Anwendungsname}-Einstellungen für einmaliges Anmelden über ein Kennwort konfigurieren** aus, um die Seite **Anmeldung konfigurieren** anzuzeigen.
1. Wählen Sie **Anmeldefelder manuell erkennen** aus. Weitere Anweisungen zur manuellen Erkennung von Anmeldefeldern werden angezeigt.
1. Wählen Sie **Anmeldefelder erfassen** aus. Auf einer neuen Registerkarte wird eine Seite mit dem Erfassungsstatus geöffnet, auf der die Meldung Die Metadatenerfassung wird aktuell ausgeführt. angezeigt wird.
1. Wenn auf einer neuen Registerkarte das Feld **Erweiterung für „Meine Apps“ erforderlich** angezeigt wird, klicken Sie auf **Jetzt installieren**, um die Browsererweiterung Erweiterung zur sicheren Anmeldung bei „Meine Apps“ zu installieren. (Die Browsererweiterung erfordert Microsoft Edge oder Chrome.) Installieren, starten und aktivieren Sie die Erweiterung, und aktualisieren Sie die Seite mit dem Erfassungsstatus. Die Browsererweiterung wird in einer anderen Registerkarte geöffnet, die die eingegebene URL anzeigt.
1. Führen Sie auf der Registerkarte mit der eingegebenen URL den Anmeldevorgang durch. Füllen Sie die Felder für Benutzername und Kennwort aus, und versuchen Sie, sich anzumelden. (Sie müssen nicht das richtige Kennwort angeben.) Sie werden aufgefordert, die erfassten Anmeldefelder zu speichern.
1. Klicken Sie auf **OK**. Die Browsererweiterung aktualisiert die Seite mit dem Erfassungsstatus mit der Meldung **Die Metadaten für die Anwendung wurden aktualisiert**. Die Registerkarte des Browsers wird geschlossen.
1. Wählen Sie auf der Azure AD-Seite Anmeldung konfigurieren die Option **OK, ich konnte mich erfolgreich bei der App anmelden** aus.
1. Klicken Sie auf **OK**.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)
