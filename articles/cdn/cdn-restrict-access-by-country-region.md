---
title: Einschränken von Azure-CDN-Inhalten nach Land/Region
description: Hier erfahren Sie, wie Sie den Zugriff auf Ihre Azure CDN-Inhalte mithilfe der Geofilterung nach Ländern/Regionen einschränken.
services: cdn
documentationcenter: ''
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 07/07/2021
ms.author: duau
ms.openlocfilehash: d51c723821290c3ae1c97ba3289c97bd78649a5e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426542"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Einschränken von Azure-CDN-Inhalten nach Land/Region

## <a name="overview"></a>Übersicht
Wenn ein Benutzer Ihre Inhalte anfordert, werden diese für Benutzer an allen Standorten bereitgestellt. Es kann ratsam sein, den Zugriff auf Ihre Inhalte nach Land oder Region zu beschränken. 

Mit dem Feature für die *Geofilterung* können Sie Regeln für bestimmte Pfade Ihres CDN-Endpunkts erstellen. Sie können die Regeln festlegen, mit denen Inhalte für ausgewählte Länder bzw. Regionen zugelassen oder blockiert werden.

> [!IMPORTANT]
> Profile vom Typ **Azure CDN Standard von Microsoft** unterstützen die pfadbasierte Geofilterung nicht.
> 

## <a name="standard-profiles"></a>Standardprofile

Diese Anleitung gilt für Profile vom Typ **Azure CDN Standard von Akamai** und **Azure CDN Standard von Verizon**.

Für Profile des Typs **Azure CDN Premium von Verizon** müssen Sie die Geofilterung im **Verwaltungsportal** aktivieren. Weitere Informationen finden Sie unter [Profile des Typs „Azure CDN Premium von Verizon“](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definieren des Verzeichnispfads
Um auf das Geofilterungsfeature zuzugreifen, wählen Sie Ihren CDN-Endpunkt im Portal aus, und wählen Sie dann im linken Menü unter „EINSTELLUNGEN“ **Geofilterung** aus. 

![Screenshot der ausgewählten Geofilterung im Menü für einen Endpunkt](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Geben Sie im Feld **PFAD** den relativen Pfad zu dem Speicherort an, für den Benutzern der Zugriff gewährt oder verweigert wird. 

Sie können Geofilterung durch Eingabe eines Schrägstrichs (/) auf alle Dateien anwenden oder bestimmte Ordner durch Angabe von Verzeichnispfaden (z.B. */pictures/* ) auswählen. Sie können Geofilterung auch auf eine einzelne Datei anwenden (z.B. */pictures/city.png*). Es sind mehrere Regeln zulässig. Nachdem Sie eine Regel eingegeben haben, wird eine leere Zeile angezeigt, in der Sie die nächste Regel eingeben können.

Alle folgenden Verzeichnispfadfilter sind z.B. gültig:   
*/*                                 
*/Photos/*      
*/Photos/Straßburg /*      
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definieren des Aktionstyps

Wählen Sie in der Liste **AKTION** die Option **Zulassen** oder **Blockieren** aus: 

- **Zulassen**: Nur für Benutzer aus den angegebenen Ländern/Regionen wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen zugelassen.

- **Blockieren**: Benutzern aus den angegebenen Ländern/Regionen wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen verweigert. Wenn keine anderen Filteroptionen für Länder/Regionen für diesen Standort konfiguriert wurden, wird der Zugriff für alle anderen Benutzer zugelassen.

Eine Geofilterungsregel zum Blockieren des Pfads */Photos/Strasbourg/* filtert z.B. die folgenden Dateien:     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definieren der Länder/Regionen

Wählen Sie in der Liste **LÄNDER-/REGIONSCODES** die Länder/Regionen aus, die Sie für den Pfad zulassen oder blockieren möchten. 

Wählen Sie nach dem Auswählen der Länder/Regionen **Speichern** aus, um die neue Geofilterungsregel zu aktivieren. 

![Screenshot der LÄNDER-/REGIONSCODES, die zum Blockieren oder Zulassen von Ländern oder Regionen verwendet werden.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um eine Regel zu löschen, wählen Sie sie in der Liste auf der Seite **Geofilterung** aus, und wählen Sie dann **Löschen**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profile des Typs „Azure CDN Premium von Verizon“

Für Profile vom Typ **Azure CDN Premium von Verizon** sieht die Benutzeroberfläche zum Erstellen einer Geofilterungsregel anders aus:

1. Wählen Sie im obersten Menü Ihres Azure CDN-Profils **Verwalten** aus.

2. Wählen Sie im Verizon-Portal **HTTP Groß** und dann **Länderfilterung** aus.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Screenshot: Auswahl der Länderfilterung in Azure CDN" border="true":::
  
3. Wählen Sie **Länderfilter hinzufügen** aus.

4. Geben Sie unter **Schritt 1** den Verzeichnispfad ein. Wählen Sie **Blockieren** oder **Hinzufügen** und dann **Weiter** aus.

    > [!IMPORTANT]
    > Der Endpunktname muss im Pfad enthalten sein.  Beispiel: **/myendpoint8675/myfolder**.  Ersetzen Sie **myendpoint8675** durch den Namen Ihres Endpunkts.
    > 
    
5. Wählen Sie in **Schritt 2** in der Liste mindestens ein Land oder eine Region aus. Wählen Sie **Fertig stellen** aus, um die Regel zu aktivieren. 
    
    Die neue Regel wird in der Tabelle auf der Seite **Länderfilterung** angezeigt.
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Screenshot: Anzeige der Regel in der Länderfilterung" border="true":::
 
### <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wählen Sie in der Filterungsregeltabelle für Länder/Regionen das Löschsymbol neben einer Regel aus, um sie zu löschen, oder das Bearbeitungssymbol, um sie zu bearbeiten.

## <a name="considerations"></a>Überlegungen
* Änderungen an der Konfiguration der Geofilterung werden nicht sofort wirksam:
   * Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
   * Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
   * Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
 
* Dieses Feature unterstützt keine Platzhalterzeichen (z. B. „*“).

* Die dem relativen Pfad zugeordnete Geofilterkonfiguration wird rekursiv auf diesen Pfad angewendet.

* Auf einen relativen Pfad kann immer nur jeweils eine Regel angewendet werden. Sie können also nicht mehrere Filter für Länder/Regionen erstellen, die auf den gleichen relativen Pfad verweisen. Da Filter für Länder/Regionen rekursiv sind, kann ein Ordner über mehrere Filter für Länder/Regionen verfügen. Anders ausgedrückt: Ein Unterordner eines zuvor konfigurierten Ordners kann einem anderen Filter für Länder/Regionen zugewiesen werden.

* Das Geofilterungsfeature verwendet Länder-/Regionscodes, um die Länder/Regionen zu definieren, von denen aus eine Anforderung für ein sicheres Verzeichnis zugelassen oder blockiert wird.  Azure CDN-Profile von **Verizon** und von **Akamai** verwenden ISO 3166-1 alpha-2-Ländercodes, um die Länder zu definieren, aus denen eine Anforderung für ein gesichertes Verzeichnis zugelassen oder blockiert wird. 

