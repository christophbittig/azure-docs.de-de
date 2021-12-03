---
title: 'Schnellstart: Bereitstellen Ihrer ersten Container-App über das Azure-Portal'
description: Stellen Sie Ihre erste Anwendung über das Azure-Portal in der Azure Container Apps-Vorschauversion bereit.
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: v-bcatherine
ms.custom: ''
ms.openlocfilehash: dc0550e4e32e84bbf99456a337a66c3da8f6cf57
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526715"
---
# <a name="quickstart-deploy-your-first-container-app-using-the-azure-portal"></a>Schnellstart: Bereitstellen Ihrer ersten Container-App über das Azure-Portal

Mit Azure Container Apps (Vorschauversion) können Sie Microservices und Containeranwendungen auf einer serverlosen Plattform ausführen. Mit Container Apps genießen Sie die Vorteile von Containern und müssen sich nicht mehr um die manuelle Konfiguration von Cloudinfrastrukturen und komplexen Containerorchestratoren kümmern.

In dieser Schnellstartanleitung erstellen Sie über das Azure-Portal eine sichere Container Apps-Umgebung und stellen Ihre erste Container-App bereit.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement ist erforderlich. Falls Sie noch über keins verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="setup"></a>Einrichten

Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.
<!--
Do we need to include steps to login?  Probably not..
-->

## <a name="create-a-container-app"></a>Erstellen einer Container-App

Beginnen Sie auf der Startseite des Azure-Portals, um Ihre Container-App zu erstellen.

1. Suchen Sie in der oberen Suchleiste nach **Container Apps**.
1. Wählen Sie in den Suchergebnissen **Container Apps** aus.
1. Wählen Sie die Schaltfläche **Erstellen**.

### <a name="basics-tab"></a>Registerkarte „Grundlagen“

Gehen Sie auf der Registerkarte *Grundeinstellungen* wie folgt vor:

#### <a name="enter-project-details"></a>Eingeben der Projektdetails

| Einstellung | Aktion |
|---|---|
| Subscription | Wählen Sie Ihr Azure-Abonnement. |
| Resource group | Wählen Sie **Neu erstellen** aus, und geben Sie **my-container-apps** ein. |
| Name der Container-App |  Geben Sie **my-container-app** ein. |

#### <a name="create-an-environment"></a>Erstellen einer Umgebung
 
1. Wählen Sie im Feld *Container-App-Umgebung erstellen* die Option **Neu erstellen** aus.
1. Geben Sie auf der Seite *Container-App-Umgebung erstellen* auf der Registerkarte *Grundlagen* die folgenden Werte ein:

    | Einstellung | Wert |
    |---|---|
    | Umgebungsname | Geben Sie **my-environment** ein. | 
    | Region | Wählen Sie **Kanada, Mitte** aus. |

1. Wählen Sie die Registerkarte **Überwachung** aus, um einen Log Analytics-Arbeitsbereich zu erstellen.
1. Wählen Sie im Feld *Log Analytics-Arbeitsbereich* die Option **Neu erstellen** aus.
1. Geben Sie im Dialogfeld *Neuen Log Analytics-Arbeitsbereich erstellen* in das Feld *Name* den Namen **my-container-apps-logs** ein.
  
    Im Feld *Standort* ist bereits *Kanada, Mitte* angegeben.
1. Klicken Sie auf **OK**.
1. Wählen Sie unten auf der Seite *Container-App-Umgebung erstellen* die Schaltfläche **Erstellen** aus.
1. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: App-Einstellungen** aus.

### <a name="app-settings-tab"></a>Registerkarte mit App-Einstellungen

Führen Sie auf der Registerkarte *App-Einstellungen* die folgenden Aktionen aus:

| Einstellung | Aktion |
|---|---|
| Verwenden des Schnellstartimages | **Deaktivieren** Sie das Kontrollkästchen. |
| Name | Geben Sie **my-app** ein. <!-- I don't know what name to use --> |
| Imagequelle | Wählen Sie **Docker Hub oder andere Registrierungen** aus. |
| Imagetyp | Wählen Sie **Öffentlich** aus. |
| Anmeldeserver für die Registrierung | Geben Sie `mcr.microsoft.com` ein. |  
| Image und Tag | Geben Sie **/azuredocs/containerapps-helloworld:latest** ein. |

#### <a name="application-ingress-settings"></a>Anwendungseingangseinstellungen

| Einstellung | Aktion |
|---|---|
| Eingehende Daten | Wählen Sie **Aktiviert**. |
| Eingangssichtbarkeit | Wählen Sie **Extern** aus. |
| Zielport | Geben Sie **80** ein. |

### <a name="deploying-the-container-app"></a>Bereitstellen der Container-App

1. Wählen Sie unten auf der Seite die Schaltfläche **Überprüfen und erstellen** aus.  

    Als Nächstes werden die Einstellungen in der Container-App überprüft. Wenn keine Fehler gefunden werden, wird die Schaltfläche *Erstellen* aktiviert.  

    Werden Fehler gefunden, wird jede Registerkarte, die Fehler enthält, mit einem roten Punkt markiert.  Navigieren Sie zur entsprechenden Registerkarte. Felder mit einem Fehler sind rot hervorgehoben.  Wenn Sie alle Fehler behoben haben, wählen Sie erneut **Überprüfen und erstellen** aus.

1. Klicken Sie auf **Erstellen**.

    Eine Seite mit der Meldung *Bereitstellung wird durchgeführt* wird angezeigt.  Nachdem die Bereitstellung erfolgreich abgeschlossen wurde, wird die Meldung „Ihre Bereitstellung wurde abgeschlossen.“ angezeigt.

### <a name="view-your-deployed-application"></a>Anzeigen der bereitgestellten Anwendung

Wählen Sie **Zu Ressource wechseln**, um Ihre neue Container-App anzuzeigen.  Wählen Sie den Link neben *Anwendungs-URL* aus, um Ihre Anwendung anzuzeigen. In Ihrem Browser wird die folgende Meldung angezeigt:

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Ihre erste Azure Container Apps-Bereitstellung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden möchten, können Sie die Azure Container Apps-Instanz und alle zugehörigen Dienste löschen, indem Sie die Ressourcengruppe entfernen.

1. Wählen Sie im Abschnitt *Übersicht* die Ressourcengruppe **my-container-apps** aus.
1. Wählen Sie oben in der *Übersicht* der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie im Bestätigungsdialogfeld *Möchten Sie "my-container-apps" löschen?* den Ressourcengruppennamen **my-container-apps** ein.
1. Klicken Sie auf **Löschen**.  
    Der Vorgang zum Löschen der Ressourcengruppe kann einige Minuten dauern.


> [!TIP]
> Treten Probleme auf? Informieren Sie uns über GitHub, indem Sie ein Problem im [Azure Container Apps-Repository](https://github.com/microsoft/azure-container-apps) öffnen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Umgebungen in Azure Container Apps](environment.md)
