---
title: Verwenden von Azure Purview Studio
description: In diesem Artikel wird beschrieben, wie Sie Azure Purview Studio verwenden.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 2e3bcd7e51226f437f42f03fa43b144bee434f33
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456136"
---
# <a name="use-purview-studio"></a>Verwenden von Purview Studio

Dieser Artikel enthält eine Übersicht über die wichtigsten Features von Azure Purview.

## <a name="prerequisites"></a>Voraussetzungen

* Im Azure-Portal wurde bereits ein Active Purview-Konto erstellt, und der Benutzer verfügt über die Berechtigungen zum Zugreifen auf [Purview Studio](https://web.purview.azure.com/resource/).

## <a name="launch-purview-account"></a>Starten des Purview-Kontos

* Navigieren Sie zum Starten Ihres Purview-Kontos im Azure-Portal zu den Purview-Konten, wählen Sie das Konto aus, das Sie starten möchten, und führen Sie den Startvorgang durch.

  :::image type="content" source="./media/use-purview-studio/open-purview-studio.png" alt-text="Screenshot des Purview-Fensters im Azure-Portal mit hervorgehobener Schaltfläche „Purview Studio“" border="true":::

* Eine weitere Möglichkeit zum Starten des Purview-Kontos besteht darin, zu `https://web.purview.azure.com` zu navigieren, die Option **Azure Active Directory** auszuwählen und auf den Namen des gewünschten Kontos zu klicken.

## <a name="home-page"></a>Startseite

Unten ist die **Startseite** des Azure Purview-Clients dargestellt.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot: Startseite":::

In der folgenden Liste sind die Hauptfunktionen der **Startseite** zusammengefasst. Jede Zahl in der Liste entspricht einer hervorgehobenen Zahl im obigen Screenshot.

1. Anzeigename des Katalogs. Sie können den Katalognamen unter **Verwaltung** > **Kontoinformationen** festlegen.

2. In der Kataloganalyse wird jeweils die Anzahl für die folgenden Elemente angezeigt:

   * Datenquellen
   * Objekte
   * Glossarbegriffe

3. Im Suchfeld können Sie im gesamten Datenkatalog nach Datenressourcen suchen.

4. Über die Schaltflächen für den Schnellzugriff können Sie auf die häufig verwendeten Funktionen der Anwendung zugreifen. Die jeweils angezeigten Schaltflächen richten sich danach, welche Rolle Ihrem Benutzerkonto in der Stammressourcenliste zugewiesen ist.

   * Für einen *Sammlungsadministrator* ist die Schaltfläche **Knowledge Center** verfügbar.
   * Für einen *Datenkurator* sind die Schaltflächen **Browse Assets** (Ressourcen durchsuchen), **Manage Glossary** (Glossar verwalten) und **Knowledge Center** vorhanden.
   * Für einen *Datenleser* sind die Schaltflächen **Browse Assets** (Ressourcen durchsuchen), **View Glossary** (Glossar anzeigen) und **Knowledge Center** vorhanden.
   * Für einen *Datenquellenadministrator* + *Datenkurator* sind die Schaltflächen **Browse Assets** (Ressourcen durchsuchen), **Manage Glossary** (Glossar verwalten) und **Knowledge Center** vorhanden.
   * Für einen *Datenquellenadministrator* + *Datenleser* sind die Schaltflächen **Browse Assets** (Ressourcen durchsuchen), **View Glossary** (Glossar anzeigen) und **Knowledge Center** vorhanden.
  
   > [!NOTE]
   > Weitere Informationen zu Azure Purview-Rollen finden Sie unter [Zugriffssteuerung in Azure Purview](catalog-permissions.md).

5. Über die Navigationsleiste auf der linken Seite können Sie auf die Hauptseiten der Anwendung zugreifen.   
6. Auf der Registerkarte **Zuletzt verwendet** wird eine Liste mit den Datenressourcen angezeigt, auf die zuletzt zugegriffen wurde. Weitere Informationen zum Zugreifen auf Ressourcen finden Sie unter [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md) und [Durchsuchen des Datenkatalogs anhand des Ressourcentyps](how-to-browse-catalog.md).  Die Registerkarte **Meine Elemente** enthält eine Liste mit Datenressourcen, die sich im Besitz des angemeldeten Benutzers befinden.
7. Unter **Links** finden Sie Links zum Regionsstatus, zur Dokumentation, zu den Preisen, zur Übersicht und zum Purview-Status.
8. Die obere Navigationsleiste enthält Abschnitte mit Informationen zu Versionshinweisen/Updates, Änderung des Purview-Kontos, Benachrichtigungen, Hilfe und Feedback.

## <a name="knowledge-center"></a>Knowledge Center

Im Knowledge Center finden Sie alle Videos und Tutorials zu Purview.

## <a name="guided-tours"></a>Führungen

Auf jeder Benutzeroberfläche in Azure Purview Studio finden Sie Einführungen, mit denen Sie sich einen Überblick über die jeweilige Seite verschaffen können. Wählen Sie zum Starten der Einführung in der oberen Leiste die Option **Hilfe** und dann **Guided Tours** (Führungen) aus.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot: Einführung":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen eines Sicherheitsprinzipals](tutorial-scan-data.md)
