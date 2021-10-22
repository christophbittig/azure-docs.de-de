---
title: 'Tutorial: Erstellen einer Linux-VM-Skalierungsgruppe'
description: Hier erfahren Sie, wie Sie eine hochverfügbare Anwendung auf virtuellen Linux-Computern mithilfe einer VM-Skalierungsgruppe erstellen und bereitstellen.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machines
ms.collection: linux
ms.date: 10/15/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 10f7202e2525920edd4c65b2e35cea51b9751abb
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165049"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Tutorial: Erstellen einer VM-Skalierungsgruppe und Bereitstellen einer hochverfügbaren App unter Linux 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Mit VM-Skalierungsgruppen mit [flexibler Orchestrierung](../flexible-virtual-machine-scale-sets.md) können Sie eine Gruppe von VMs mit Lastenausgleich erstellen und verwalten. Die Anzahl von VM-Instanzen kann automatisch erhöht oder verringert werden, wenn sich der Bedarf ändert, oder es kann ein Zeitplan festgelegt werden.

In diesem Tutorial stellen Sie eine VM-Skalierungsgruppe in Azure bereit und lernen Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Ressourcengruppe.
> * Erstellen einer flexiblen Skalierungsgruppe mit einem Lastenausgleich
> * Hinzufügen von nginx zu den Skalierungsgruppeninstanzen
> * Öffnen von Port 80 für HTTP-Datenverkehr
> * Testen der Skalierungsgruppe


## <a name="scale-set-overview"></a>Übersicht über Skalierungsgruppen

Skalierungsgruppen verfügen über die folgenden wichtigen Vorteile:
- Einfaches Erstellen und Verwalten von mehreren VMs
- Bietet Hochverfügbarkeit und Anwendungsresilienz durch das fehlerdomänenübergreifende Bereitstellen von VMs
- Automatische Skalierung der Anwendung bei sich änderndem Ressourcenbedarf
- Großer Umfang

Im Orchestrierungsmodus „Flexibel“ können Sie im gesamten Azure-VM-Ökosystem eine einheitliche Benutzeroberfläche nutzen. Bei der flexiblen Orchestrierung wird Hochverfügbarkeit garantiert (für bis zu 1.000 VMs), indem VMs auf Fehlerdomänen in einer Region oder einer Verfügbarkeitszone verteilt werden. Dies ermöglicht Ihnen das Aufskalieren Ihrer Anwendung bei gleichzeitiger Fehlerdomänenisolation, die für die Ausführung von quorumbasierten oder zustandsbehafteten Workloads von entscheidender Bedeutung ist. Beispiele:
- Quorumbasierte Workloads
- Open-Source-Datenbanken
- Zustandsbehaftete Anwendungen
- Dienste, die Hochverfügbarkeit und eine starke Skalierung erfordern
- Dienste, bei denen VM-Typen gemischt oder Spot- und On-Demand-VMs zusammen genutzt werden sollen
- Vorhandene Verfügbarkeitsgruppen-Anwendungen

Informieren Sie sich unter [Vorschau: Orchestrierungsmodi für VM-Skalierungsgruppen in Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md) über die Unterschiede der Skalierungsgruppen vom Typ „Einheitlich“ und „Flexibel“.



## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Verwenden Sie das Azure-Portal zum Erstellen einer flexiblen Skalierungsgruppe.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie nach **VM-Skalierungsgruppen**, und wählen Sie den Eintrag aus.
1. Wählen Sie auf der Seite **VM-Skalierungsgruppen** die Option **Erstellen** aus. **VM-Skalierungsgruppe erstellen** wird geöffnet.
1. Wählen Sie unter **Abonnement** das gewünschte Abonnement aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie *myVMSSRG* als Name ein, und wählen Sie **OK** aus.
    :::image type="content" source="media/tutorial-create-vmss/flex-project-details.png" alt-text="Projektdetails":::
1. Geben Sie für **Name der VM-Skalierungsgruppe** den Wert *myVMSS* ein.
1. Wählen Sie unter **Region** eine Region in Ihrer Nähe aus, etwa *USA, Osten*.
    :::image type="content" source="media/tutorial-create-vmss/flex-details.png" alt-text="Name und Region":::
1. Lassen Sie **Verfügbarkeitszone** für dieses Beispiel leer.
1. Wählen Sie unter **Orchestrierungsmodus** die Option **Flexibel** aus.
1. Übernehmen Sie für „Anzahl von Fehlerdomänen“ den Standardwert *1*, oder wählen Sie im Dropdownmenü einen anderen Wert aus.
   :::image type="content" source="media/tutorial-create-vmss/flex-orchestration.png" alt-text="Auswählen des Orchestrierungsmodus „Flexibel“":::
1. Wählen Sie unter **Image** die Option *Ubuntu 18.04 LTS* aus.
1. Übernehmen Sie unter **Größe** den Standardwert, oder wählen Sie eine Größe wie etwa *Standard_E2s_V3* aus.
1. Geben Sie in **Benutzername** die Zeichenfolge *azureuser* ein.
1. Für **Öffentliche SSH-Schlüsselquelle** belassen Sie die Voreinstellung **Neues Schlüsselpaar generieren**, und geben Sie dann *myKey* für den **Schlüsselpaarnamen** ein.
    :::image type="content" source="media/tutorial-create-vmss/flex-admin.png" alt-text="Screenshot: Abschnitt „Administratorkonto“, in dem Sie einen Authentifizierungstyp auswählen und die Administratoranmeldeinformationen angeben.":::
1. Wählen Sie auf der Registerkarte **Netzwerk** unter **Lastenausgleich** die Option **Lastenausgleichsmodul verwenden** aus.
1. Übernehmen Sie unter **Optionen für den Lastenausgleich** den Standardwert **Azure Load Balancer**.
1. Wählen Sie unter **Load Balancer auswählen** die Option **Neu erstellen** aus.
    :::image type="content" source="media/tutorial-create-vmss/load-balancer-settings.png" alt-text="Einstellungen für Lastenausgleich":::
1. Geben Sie auf der Seite **Lastenausgleich erstellen** einen Namen für Ihren Lastenausgleich und unter **Öffentliche IP-Adresse** einen Namen für die IP-Adresse ein.
1. Geben Sie unter **Domänennamensbezeichnung** einen Namen ein, der als Präfix für Ihren Domänennamen verwendet werden soll. Dieser Name muss eindeutig sein.
1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.
    :::image type="content" source="media/tutorial-create-vmss/flex-load-balancer.png" alt-text="Erstellen eines Lastenausgleichs":::
1. Übernehmen Sie auf der Registerkarte **Netzwerk** den Standardnamen für den Back-End-Pool.
1. Behalten Sie auf der Registerkarte **Skalierung** den Standardwert *2* für die Instanzenanzahl bei, oder geben Sie einen eigenen Wert ein. Dies ist die Anzahl der virtuellen Computer, die erstellt werden. Beachten Sie daher die Kosten und Grenzwerte für Ihr Abonnement, wenn Sie diesen Wert ändern.
1. Übernehmen Sie für **Scaling policy** (Skalierungsrichtlinie) den festgelegten Wert *Manuell*.
    :::image type="content" source="media/tutorial-create-vmss/flex-scaling.png" alt-text="Skalierungsrichtlinieneinstellungen":::
1. Wählen Sie die Registerkarte **Erweitert** .
1. Kopieren Sie unter **Benutzerdefinierte Daten und cloud-init** den folgenden Abschnitt, und fügen Sie ihn ins Textfeld **Benutzerdefinierte Daten** ein:
    ```yml
    #cloud-config
    package_upgrade: true
    packages:
      - nginx
      - nodejs
      - npm
    write_files:
      - owner: www-data:www-data
      - path: /etc/nginx/sites-available/default
        content: |
          server {
            listen 80;
            location / {
              proxy_pass http://localhost:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection keep-alive;
              proxy_set_header Host $host;
              proxy_cache_bypass $http_upgrade;
            }
          }
      - owner: azureuser:azureuser
      - path: /home/azureuser/myapp/index.js
        content: |
          var express = require('express')
          var app = express()
          var os = require('os');
          app.get('/', function (req, res) {
            res.send('Hello World from host ' + os.hostname() + '!')
          })
          app.listen(3000, function () {
            console.log('Hello world app listening on port 3000!')
          })
    runcmd:
      - service nginx restart
      - cd "/home/azureuser/myapp"
      - npm init
      - npm install express -y
      - nodejs index.js
    ```
1. Wählen Sie abschließend **Überprüfen + Erstellen** aus.
1. Wenn Sie sehen, dass die Überprüfung erfolgreich war, können Sie unten auf der Seite **Erstellen** auswählen, um Ihre Skalierungsgruppe bereitzustellen.
1. Wenn das Fenster **Neues Schlüsselpaar generieren** geöffnet wird, wählen Sie **Privaten Schlüssel herunterladen und Ressource erstellen** aus. Ihre Schlüsseldatei wird als **myKey.pem** heruntergeladen. Vergewissern Sie sich, dass Sie wissen, wo die `.pem`-Datei heruntergeladen wurde. Sie benötigen im nächsten Schritt den Pfad zu dieser Datei.
1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus, um die Skalierungsgruppe anzuzeigen.


## <a name="view-the-vms-in-your-scale-set"></a>Anzeigen der virtuellen Computer in Ihrer Skalierungsgruppe

Wählen Sie auf der Seite für die Skalierungsgruppe im Menü auf der linken Seite **Instanzen** aus. 

Eine Liste der virtuellen Computer wird angezeigt, die Teil Ihrer Skalierungsgruppe sind. Diese Liste enthält:

- Der Name der VM
- Der vom virtuellen Computer verwendete Computername
- Der aktuelle Status des virtuellen Computers, etwa *Wird ausgeführt*
- Der *Bereitstellungsstatus* des virtuellen Computers, etwa *Erfolgreich*

:::image type="content" source="media/tutorial-create-vmss/instances.png" alt-text="Tabelle mit Informationen zu den Skalierungsgruppeninstanzen":::


## <a name="open-port-80"></a>Öffnen von Port 80 

Öffnen Sie Port 80 in Ihrer Skalierungsgruppe, indem Sie Ihrer Netzwerksicherheitsgruppe (NSG) eine Regel für eingehenden Datenverkehr hinzufügen.

1. Wählen Sie auf der Seite für die Skalierungsgruppe im Menü auf der linken Seite **Netzwerk** aus. Die Seite **Netzwerk** wird geöffnet.
1. Wählen Sie **Regel für eingehenden Port hinzufügen** aus. Die Seite **Eingangssicherheitsregel hinzufügen** wird geöffnet.
1. Wählen Sie unter **Dienst** die Option *HTTP* und dann unten auf der Seite die Option **Hinzufügen** aus.

## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe

Testen Sie Ihre Skalierungsgruppe, indem Sie in einem Browser eine Verbindung mit ihr herstellen.

1. Kopieren Sie auf der Seite **Übersicht** für Ihre Skalierungsgruppe die öffentliche IP-Adresse.
1. Öffnen Sie eine weitere Registerkarte im Browser, und fügen Sie die IP-Adresse in die Adressleiste ein.
1. Wenn die Seite geladen wird, notieren Sie sich den angezeigten Computernamen. 
1. Aktualisieren Sie die Seite, bis der geänderte Computername angezeigt wird. 

## <a name="delete-your-scale-set"></a>Löschen Ihrer Skalierungsgruppe

Wenn Sie fertig sind, sollten Sie die Ressourcengruppe löschen. Dadurch werden alle Elemente gelöscht, die Sie für Ihre Skalierungsgruppe bereitgestellt haben.

1. Wählen Sie auf der Seite für Ihre Skalierungsgruppe die **Ressourcengruppe** aus. Die Seite für Ihre Ressourcengruppe wird geöffnet.
1. Wählen Sie am oberen Rand der Seite die Option **Ressourcengruppe löschen** aus.
1. Geben Sie auf der Seite **Möchten Sie den Löschvorgang durchführen?** den Namen der Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Skalierungsgruppe für virtuelle Computer bereitgestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen Sie eine Ressourcengruppe.
> * Erstellen einer flexiblen Skalierungsgruppe mit einem Lastenausgleich
> * Hinzufügen von nginx zu den Skalierungsgruppeninstanzen
> * Öffnen von Port 80 für HTTP-Datenverkehr
> * Testen der Skalierungsgruppe

Im nächsten Tutorial erhalten Sie weitere Informationen zu den Konzepten des Lastenausgleichs für virtuelle Computer.

> [!div class="nextstepaction"]
> [Lastenausgleich für virtuelle Computer](tutorial-load-balancer.md)
