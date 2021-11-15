---
title: LSR (Late State Reprojection)
description: Informationen zu LSR (Late Stage Projection) und ihrer Verwendung.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 13a950f67053d9eaaea39e2df34df3dd12ed00c1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028722"
---
# <a name="late-stage-reprojection"></a>LSR (Late State Reprojection)

*Late Stage Projection* (LSR) ist ein Hardwarefeature, mit dem Hologramme bei Bewegungen des Benutzers stabilisiert werden.

Bei statischen Modellen wird erwartet, dass sie ihre Position visuell beibehalten, wenn sich Benutzer darin bewegen. Wenn Sie instabil scheinen, kann dieses Verhalten auf LSR-Probleme hindeuten. Beachten Sie, dass zusätzliche dynamische Transformationen (etwa Animationen oder Explosionsansichten) dieses Verhalten maskieren können.

Sie können zwischen zwei verschiedenen LSR-Modi wählen, nämlich zwischen **Planar-LSR** und **Tiefen-LSR**. Beide LSR-Modi verbessern die Hologrammstabilität, auch wenn sie jeweils Einschränkungen aufweisen. Beginnen Sie mit dem Testen von Tiefen-LSR, da damit in den meisten Fällen bessere Ergebnisse erzielt werden.

## <a name="how-to-set-the-lsr-mode"></a>Festlegen des LSR-Modus

Welcher der LSR-Modi verwendet wird, hängt davon ab, ob die Clientanwendung einen Tiefenpuffer übermittelt. Wenn der Tiefenpuffer übermittelt wird, wird **Tiefen-LSR** verwendet, andernfalls **planare LSR**.

In den folgenden Abschnitten wird erläutert, wie die Übermittlung des Tiefenpuffers in Unity bzw. nativen Anwendungen erfolgt.

### <a name="unity"></a>Unity

Wechseln Sie im Unity-Editor zu *:::no-loc text="File > Build Settings":::* . Wählen Sie links unten *:::no-loc text="Player Settings":::* aus, und überprüfen Sie dann unter *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* , ob **:::no-loc text="Enable Depth Buffer Sharing":::** aktiviert ist:

![Flag für aktivierte Tiefenpufferfreigabe](./media/unity-depth-buffer-sharing-enabled.png)

Wenn dies der Fall ist, verwendet Ihre App Tiefen-LSR, andernfalls wird Planar-LSR verwendet.

Bei Verwendung von OpenXR sollte der Tiefenpuffer immer übermittelt werden. Die Einstellung finden Sie unter *:::no-loc text="XR Plug-in Management > OpenXR":::* . Der Neuprojektionsmodus kann dann über eine Erweiterung im OpenXR-Plug-In geändert werden:

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

### <a name="native-c-applications"></a>Native C++-Anwendungen

Die Übermittlung des Tiefenpuffers steht unabhängig von der WMR- oder OpenXR-Version vollständig unter der Kontrolle des nativen C++-Bindungscodes. Die einzig geltende Bedingung ist, dass zum Zeitpunkt des Aufrufs von `GraphicsBinding::BlitRemoteFrame` ein Tiefenpuffer an die Grafik-API gebunden sein muss.

## <a name="depth-lsr"></a>Tiefen-LSR

Damit Tiefen-LSR funktioniert, muss die Clientanwendung einen gültigen Tiefenpuffer bereitstellen, der die gesamte relevante Geometrie enthält, die während des LSR-Vorgangs berücksichtigt werden muss.

Tiefen-LSR versucht, den Videoframe basierend auf dem Inhalt des angegebenen Tiefenpuffers zu stabilisieren. Folglich können Inhalte, die nicht in ihm gerendert wurden (z. B. transparente Objekte), nicht von LSR angepasst werden, und es kann zu Instabilität und Umprojizierungsartefakten kommen.

Um Instabilitäten der Neuprojektion bei transparenten Objekten zu vermeiden, können Sie das Schreiben von Tiefenpuffern erzwingen. Weitere Informationen zu den [Farb](color-materials.md)- und [PBR](pbr-materials.md)-Materialien finden Sie im Materialflag *TransparencyWritesDepth*. Beachten Sie jedoch, dass die visuelle Qualität der Interaktion zwischen transparenten/nicht transparenten Objekten beim Aktivieren dieses Flags beeinträchtigt werden kann.

## <a name="planar-lsr"></a>Planar-LSR

Planar-LSR nutzt keine Tiefeninformationen pro Pixel wie Tiefen-LSR. Stattdessen wird der gesamte Inhalt auf Grundlage einer Ebene umprojiziert, die Sie für jeden Frame bereitstellen müssen.

Planar-LSR projiziert diese Objekte am besten um, die in der Nähe der angegebenen Ebene liegen. Je weiter entfernt ein Objekt ist, desto instabiler sieht es aus. Obwohl Tiefen-LSR bei der Umprojizierung von Objekten in unterschiedlichen Tiefen besser ist, kann Planar-LSR besser für Inhalte geeignet sein, die sich gut an einer Ebene ausrichten lassen.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurieren von Planar-LSR in Unity

Die Ebenenparameter werden von einem so genannten *Fokuspunkt* abgeleitet. Verwenden Sie WMR, muss der Fokuspunkt bei jedem Bild durch `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` festgelegt werden. Ausführliche Informationen dazu finden Sie in der [Fokuspunkt-API von Unity](/windows/mixed-reality/focus-point-in-unity). Für OpenXR muss der Fokuspunkt wie im vorherigen Abschnitt gezeigt über die `ReprojectionSettings` festgelegt werden.
Wenn Sie keinen Fokuspunkt festlegen, wird ein Fallback für Sie ausgewählt. Dieser automatische Fallback führt jedoch häufig zu suboptimalen Ergebnissen.

Sie können den Fokuspunkt selbst berechnen, aber es kann sinnvoll sein, ihn auf dem vom Remote Rendering-Host berechneten Wert basieren zu lassen. Rufen Sie `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` auf, um diesen Wert abzurufen.

Normalerweise rendern sowohl der Client als auch der Host Inhalte, die der jeweils anderen Seite unbekannt sind, z. B. Benutzeroberflächenelemente auf dem Client. Daher ist es möglicherweise sinnvoll, den Remotefokuspunkt mit einem lokal berechneten zu kombinieren.

Die Fokuspunkte, die in zwei aufeinander folgenden Frames berechnet werden, können sich erheblich unterscheiden. Wenn Sie diese einfach unverändert verwenden, kann dies dazu führen, dass Hologramme zu „springen“ scheinen. Um dieses Verhalten zu verhindern, ist eine Interpolation zwischen den vorherigen und den aktuellen Fokuspunkten empfehlenswert.

## <a name="reprojection-pose-modes"></a>Posenmodi für die Neuprojektion

Der allgemeine Problembereich beim Hybridrendering kann in dieser Weise angegeben werden: Remote- und lokale Inhalte befinden sich in unterschiedlichen Posen (d. h. Koordinatenräumen), da die Remotepose vom Server vorhergesagt wird, während die lokale Pose die reale aktuelle Pose ist. Am Ende eines Renderingframes müssen jedoch sowohl Remote- als auch lokaler Inhalt ausgerichtet und zur Anzeige gebracht werden. Die folgende Abbildung zeigt ein Beispiel, bei dem lokale und Remoteposen im Vergleich zum Anzeige-Viewport verschoben werden:

![Diagramm, das die Remote- und die lokale Pose im Verhältnis zum Ziel-Viewport veranschaulicht.](./media/reprojection-remote-local.png)

ARR bietet zwei Neuprojektionsmodi, die orthogonal zum oben erörterten LSR-Modus funktionieren. Diese Modi werden als **:::no-loc text="Remote pose mode":::** und **:::no-loc text="Local pose mode":::** bezeichnet. Im Gegensatz zum LSR-Modus definieren die Posenmodi, wie Remote- und lokale Inhalte kombiniert werden. Die Wahl des Modus nimmt eine Einschränkung der Darstellungsqualität lokaler Inhalte zugunsten der Leistung zur Laufzeit in Kauf, daher sollte bei Anwendungen sorgfältig überlegt werden, welche Option geeignet ist. Weitere Überlegungen dazu finden Sie weiter unten.

### :::no-loc text="Remote pose mode":::

:::no-loc text="Remote pose mode"::: ist der Standardmodus in ARR. In diesem Modus wird der lokale Inhalt über dem eingehenden Remotebildstream mithilfe der Remotepose aus dem Remoteframe gerendert. Anschließend wird das kombinierte Ergebnis für die endgültige Neuprojektion an das Betriebssystem weitergeleitet. Bei diesem Ansatz wird zwar nur eine Neuprojektion verwendet, die endgültige Korrektur basiert jedoch auf dem Roundtripintervall, sodass der vollständige Neuprojektionsfehler auch auf den lokalen Inhalt angewendet wird. Infolgedessen kann das große Korrekturdelta zu erheblichen Verzerrungen der lokalen Geometrie führen, einschließlich Elementen der Benutzeroberfläche.

Ausgehend von der Abbildung oben wird in :::no-loc text="Remote pose mode"::: die folgende Transformation angewendet:

![Neuprojektionsschritte im Remoteposenmodus.](./media/reprojection-pose-mode-remote.png)

### :::no-loc text="Local pose mode":::

In diesem Modus wird die Neuprojektion in zwei unterschiedliche Schritte aufgeteilt: Im ersten Schritt wird der Remoteinhalt neu in den lokalen Posenraum projiziert, das ist der Raum, in dem standardmäßig bei VR/AR-Geräten der lokale Inhalt gerendert wird. Anschließend wird der lokale Inhalt mithilfe der üblichen lokalen Pose auf diesem vorab verschobenen Bild gerendert. Im zweiten Schritt wird das kombinierte Ergebnis für die endgültige Neuprojektion an das Betriebssystem weitergeleitet. Da bei dieser zweiten Neuprojektion nur ein kleines Delta auftritt – tatsächlich dasselbe Delta, das verwendet würde, wenn ARR nicht vorhanden wäre – werden die Verzerrungsartefakte für lokale Inhalte erheblich verringert.

Entsprechend sieht die Abbildung wie diese aus:

![Schritte zur Neuprojektion im lokalen Posenmodus.](./media/reprojection-pose-mode-local.png)

### <a name="performance-and-quality-considerations"></a>Überlegungen zu Leistung und Qualität

Die Wahl des Posenmodus hat Auswirkungen auf die Darstellungsqualität und die Leistung. Die zusätzlichen Laufzeitkosten auf der Clientseite für das Ausführen der zusätzlichen Neuprojektion im :::no-loc text="Local pose mode"::: auf einem HoloLens 2-Gerät betragen etwa 1 Millisekunde GPU-Zeit pro Frame. Diese Zusatzkosten müssen berücksichtigt werden, wenn die Clientanwendung dem Rahmenbudget von 16 Millisekunden bereits nahe kommt. Andererseits gibt es Anwendungstypen ohne lokalen Inhalt oder mit lokalem Inhalt, der nicht anfällig für Verzerrungsartefakte ist. In diesen Fällen bietet :::no-loc text="Local pose mode"::: keinen visuellen Vorteil, da die Qualität der Neuprojektion von Remoteinhalten nicht betroffen ist.

Der allgemeine Rat wäre daher, die Modi für jeden Anwendungsfall einzeln zu testen und zu prüfen, ob der Gewinn an Darstellungsqualität den zusätzlichen Leistungsaufwand rechtfertigt. Es ist auch möglich, den Modus dynamisch umschalten, z. B. den lokalen Modus nur zu aktivieren, wenn wichtige Elemente der Benutzeroberfläche angezeigt werden.

### <a name="how-to-change-the-no-loc-textpose-mode-at-runtime"></a>Ändern des :::no-loc text="Pose mode"::: zur Laufzeit

Die folgende Client-API kann verwendet werden, um den Modus zur Laufzeit zu ändern:

```cs
RenderingSession session = ...;
session.GraphicsBinding.SetPoseMode(PoseMode.Local); // set local pose mode
```
 
```cpp
ApiHandle<RenderingSession> session = ...;
session->GetGraphicsBinding()->SetPoseMode(PoseMode::Local); // set local pose mode
```

Der Modus kann jederzeit geändert werden, wenn das Grafikbindungsobjekt verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

* [Serverseitige Leistungsabfragen](performance-queries.md)