---
title: Utilisation des graphiques de base et des animations principales dans Xamarin. iOS
description: Cet article explique pas à pas comment créer une application qui utilise des graphiques de base et des animations principales. Il montre comment dessiner à l’écran en réponse à une touche utilisateur et comment animer une image pour se déplacer le long d’un tracé.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b35e88cfdc0bce321068951f1617885c90331c83
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032449"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Utilisation des graphiques de base et des animations principales dans Xamarin. iOS

Pour cette procédure pas à pas, nous allons dessiner un chemin d’accès à l’aide de graphiques principaux en réponse à une entrée tactile. Ensuite, nous allons ajouter une `CALayer` contenant une image que nous allons animer le long du chemin.

La capture d’écran suivante montre l’application terminée :

![](graphics-animation-walkthrough-images/00-final-app.png "The completed application")

Avant de commencer, téléchargez l’exemple *GraphicsDemo* qui accompagne ce guide. Vous pouvez le télécharger [ici](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation) et se trouver dans le répertoire **GraphicsWalkthrough** pour lancer le projet nommé **GraphicsDemo_starter** en double-cliquant dessus, puis ouvrir la classe `DemoView`.

## <a name="drawing-a-path"></a>Dessin d’un tracé

1. Dans `DemoView` ajoutez une variable `CGPath` à la classe et instanciez-la dans le constructeur. Vous pouvez également déclarer deux variables `CGPoint`, `initialPoint` et `latestPoint`, que nous utiliserons pour capturer le point tactile à partir duquel nous créons le chemin d’accès :

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. Ajoutez les directives d’utilisation suivantes :

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Ensuite, remplacez `TouchesBegan` et `TouchesMoved,` et ajoutez les implémentations suivantes pour capturer le point tactile initial et chacun des points tactiles suivants, respectivement :

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` est appelée chaque fois que l’étape se déplace, afin que `Draw` soit appelée lors du passage de boucle Next Run.

4. Nous allons ajouter des lignes au chemin d’accès dans la méthode `Draw` et utiliser une ligne en pointillés rouge. [Implémentez `Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md) avec le code présenté ci-dessous :

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Si nous exécutons l’application maintenant, nous pouvons toucher pour dessiner sur l’écran, comme illustré dans la capture d’écran suivante :

![](graphics-animation-walkthrough-images/01-path.png "Drawing on the screen")

## <a name="animating-along-a-path"></a>Animation sur un tracé

Maintenant que nous avons implémenté le code pour permettre aux utilisateurs de dessiner le tracé, nous allons ajouter le code pour animer une couche le long du tracé dessiné.

1. Tout d’abord, ajoutez une [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) variable à la classe et créez-la dans le constructeur :

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. Ensuite, nous allons ajouter la couche en tant que sous-couche de la couche de la vue lorsque l’utilisateur soulève son doigt de l’écran. Ensuite, nous allons créer une animation d’image clé à l’aide du chemin d’accès, en animant le `Position`de la couche.

    Pour ce faire, nous devons remplacer la `TouchesEnded` et ajouter le code suivant :

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Exécutez l’application maintenant et après le dessin, une couche avec une image est ajoutée et se déplace le long du tracé dessiné :

![](graphics-animation-walkthrough-images/00-final-app.png "A layer with an image is added and travels along the drawn path")

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons parcouru un exemple qui reliait les graphiques et les concepts d’animation. Tout d’abord, nous avons montré comment utiliser des graphiques de base pour dessiner un chemin d’accès dans un `UIView` en réponse à la touche utilisateur. Nous avons ensuite montré comment utiliser l’animation principale pour faire circuler une image sur ce chemin.

## <a name="related-links"></a>Liens associés

- [Animation de base](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Graphismes de base](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Recettes de l’animation principale](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
