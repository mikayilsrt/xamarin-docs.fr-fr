---
title: Suivi des doigts multipoint dans Xamarin. iOS
description: Ce document décrit comment suivre des doigts individuels dans les gestes multipoint dans une application Xamarin. iOS. Il est axé sur un exemple d’application de peinture par doigt.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: c3998424c8f4e9482a41e2891e65f0d13d8ac2f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009187"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Suivi des doigts multipoint dans Xamarin. iOS

_Ce document montre comment suivre les événements tactiles à partir de plusieurs doigts_

Il arrive parfois qu’une application multipoint doive suivre les doigts individuels lorsqu’ils se déplacent simultanément sur l’écran. Une application classique est un programme de peinture par doigt. Vous souhaitez que l’utilisateur soit en mesure de dessiner avec un seul doigt, mais également de dessiner avec plusieurs doigts à la fois. À mesure que votre programme traite plusieurs événements tactiles, il doit faire la distinction entre ces doigts.

Quand un doigt touche l’écran pour la première fois, iOS crée un objet [`UITouch`](xref:UIKit.UITouch) pour ce doigt. Cet objet reste le même que le doigt se déplace sur l’écran, puis monte de l’écran, à partir duquel l’objet est supprimé. Pour effectuer le suivi des doigts, un programme doit éviter de stocker cet objet `UITouch` directement. Au lieu de cela, il peut utiliser la propriété [`Handle`](xref:Foundation.NSObject.Handle) de type `IntPtr` pour identifier de manière unique ces objets `UITouch`.

Presque toujours, un programme qui suit des doigts individuels conserve un dictionnaire pour le suivi tactile. Pour un programme iOS, la clé de dictionnaire est la valeur `Handle` qui identifie un doigt particulier. La valeur du dictionnaire dépend de l’application. Dans le programme [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , chaque trait de doigt (du toucher au lancement) est associé à un objet qui contient toutes les informations nécessaires pour afficher la ligne dessinée avec ce doigt. Le programme définit une petite classe de `FingerPaintPolyline` à cet effet :

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Chaque polyligne a une couleur, une largeur de trait et un objet [`CGPath`](xref:CoreGraphics.CGPath) iOS Graphics pour accumuler et afficher plusieurs points de la ligne au fur et à mesure qu’elle est dessinée.

Tout le reste du code présenté ci-dessous est contenu dans une `UIView` dérivée nommée `FingerPaintCanvasView`. Cette classe gère un dictionnaire d’objets de type `FingerPaintPolyline` pendant la durée pendant laquelle ils sont activement dessinés par un ou plusieurs doigts :

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Ce dictionnaire permet à la vue d’obtenir rapidement les informations `FingerPaintPolyline` associées à chaque doigt en fonction de la propriété `Handle` de l’objet `UITouch`.

La classe `FingerPaintCanvasView` gère également un objet `List` pour les polylignes qui ont été terminées :

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Les objets de cette `List` se trouvent dans l’ordre dans lequel ils ont été dessinés.

`FingerPaintCanvasView` remplace cinq méthodes définies par `View`:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

Les différents `Touches` remplacements accumulent les points qui composent les polylignes.

La substitution [`Draw`] dessine les polylignes terminées, puis les polylignes en cours :

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Chaque `Touches` remplace potentiellement les actions de plusieurs doigts, indiquées par un ou plusieurs objets `UITouch` stockés dans l’argument `touches` de la méthode. Le `TouchesBegan` remplace la boucle à travers ces objets. Pour chaque objet `UITouch`, la méthode crée et initialise un nouvel objet `FingerPaintPolyline`, y compris le stockage de l’emplacement initial du doigt obtenu à partir de la méthode `LocationInView`. Cet objet `FingerPaintPolyline` est ajouté au dictionnaire `InProgressPolylines` à l’aide de la propriété `Handle` de l’objet `UITouch` en tant que clé de dictionnaire :

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

La méthode conclut en appelant `SetNeedsDisplay` pour générer un appel à la `Draw` substitution et pour mettre à jour l’écran.

À mesure que le doigt ou les doigts se déplacent à l’écran, le `View` obtient plusieurs appels à son `TouchesMoved` remplacement. Cette substitution effectue une boucle de la même façon dans les objets `UITouch` stockés dans l’argument `touches` et ajoute l’emplacement actuel du doigt au tracé graphique :

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

La collection `touches` contient uniquement les objets `UITouch` pour les doigts qui ont été déplacés depuis le dernier appel à `TouchesBegan` ou `TouchesMoved`. Si vous avez besoin de `UITouch` objets correspondant à *tous* les doigts actuellement en contact avec l’écran, ces informations sont disponibles via la propriété `AllTouches` de l’argument `UIEvent` de la méthode.

Le `TouchesEnded` remplacement a deux tâches. Il doit ajouter le dernier point au chemin d’accès graphique et transférer l’objet `FingerPaintPolyline` du dictionnaire `inProgressPolylines` à la liste `completedPolylines` :

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

Le `TouchesCancelled` remplacement est géré en abandonnant simplement l’objet `FingerPaintPolyline` dans le dictionnaire :

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

Ensemble, ce traitement permet au programme [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) d’effectuer le suivi des doigts individuels et de dessiner les résultats à l’écran :

[![](touch-tracking-images/image01.png "Tracking individual fingers and drawing the results on the screen")](touch-tracking-images/image01.png#lightbox)

Vous avez maintenant vu comment vous pouvez suivre des doigts individuels sur l’écran et les distinguer.

## <a name="related-links"></a>Liens associés

- [Guide Xamarin Android équivalent](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
