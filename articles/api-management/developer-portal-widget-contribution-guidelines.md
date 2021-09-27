---
title: Beisteuern von Widgets für das Entwicklerportal
titleSuffix: Azure API Management
description: Hier finden Sie Informationen zu empfohlenen Richtlinien für den Fall, dass Sie ein Widget zum Repository des API Management-Entwicklerportals beisteuern möchten.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ae491880dc7dc69a3930e6c967fb7a660db75f38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631474"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Beisteuern von Widgets zum API Management-Entwicklerportal

Führen Sie die drei folgenden Schritte aus, wenn Sie ein Widget zum [GitHub-Repository](https://github.com/Azure/api-management-developer-portal) des API Management-Entwicklerportals beisteuern möchten:

1. Forken Sie das Repository.

1. Implementieren Sie das Widget.

1. Erstellen Sie einen Pull Request für die Aufnahme Ihres Widgets in das offizielle Repository.

Von Ihrem Widget wird die Lizenz des Repositorys geerbt. Es steht in der selbstgehosteten Version des Portals für die [optionale Installation](developer-portal-use-community-widgets.md) zur Verfügung. Möglicherweise wird es vom Entwicklerportalteam auch in die verwaltete Version des Portals aufgenommen.

Ein Beispiel für die Entwicklung eines eigenen Widgets finden Sie im Tutorial [Implementieren von Widgets im Entwicklerportal](developer-portal-implement-widgets.md).

## <a name="contribution-guidelines"></a>Richtlinien für Mitwirkende

Dieser Leitfaden dient dazu, die Sicherheit und dem Datenschutz unserer Kunden und der Besucher ihrer Portale zu gewährleisten. Halten Sie sich an diese Richtlinien, um sicherzustellen, dass Ihr Beitrag akzeptiert wird:

1. Platzieren Sie Ihr Widget im Ordner `community/widgets/<your-widget-name>`.

1. Der Name Ihres Widgets muss aus klein geschriebenen alphanumerischen Zeichen bestehen. Einzelne Wörter müssen jeweils durch Bindestriche getrennt werden. Beispiel: `my-new-widget`.

1. Der Ordner muss einen Screenshot Ihres Widgets in einem veröffentlichten Portal enthalten.

1. Der Ordner muss eine Datei vom Typ `readme.md` enthalten, die auf der Vorlage aus der Datei `/scaffolds/widget/readme.md` basiert.

1. Der Ordner kann eine Datei vom Typ `npm_dependencies` mit npm-Befehlen zum Installieren oder Verwalten der Abhängigkeiten des Widgets enthalten.

    Geben Sie explizit die Version jeder Abhängigkeit an. Beispiel:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Ihr Widget sollte möglichst wenige Abhängigkeiten erfordern. Jede Abhängigkeit wird von den Prüfern sorgfältig geprüft. Insbesondere die Kernlogik Ihres Widgets muss im Ordner Ihres Widgets als Open-Source-Logik bereitgestellt sein. Schließen Sie sie nicht in ein npm-Paket ein.

1. Änderungen an Dateien außerhalb des Ordners Ihres Widgets sind im Rahmen eines Widgetbeitrags nicht zulässig. Dies schließt unter anderem die Datei `/package.json` mit ein.

1. Das Einschleusen von Nachverfolgungsskripts sowie das Senden von Daten, die vom Kunden erstellt wurden, sind untersagt.

    > [!NOTE]
    > Vom Kunden erstellte Daten dürfen ausschließlich über die Schnittstelle `Logger` gesammelt werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Beiträgen finden Sie im [GitHub-Repository](https://github.com/Azure/api-management-developer-portal/) des API Management-Entwicklerportals.

- Unter [Implementieren von Widgets im Entwicklerportal](developer-portal-implement-widgets.md) erfahren Sie Schritt für Schritt, wie Sie Ihr eigenes Widget entwickeln.

- Unter [Verwenden von Widgets aus der Community](developer-portal-use-community-widgets.md) erfahren Sie, wie Sie von der Community beigesteuerte Widgets verwenden.