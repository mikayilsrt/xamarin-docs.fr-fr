---
title: Introduction à iOS 6
description: Ce document contient des liens vers des guides qui décrivent les fonctionnalités introduites dans iOS 6. Les vues de collection, PassKit, l’infrastructure sociale et les modifications apportées à StoreKit sont toutes abordées.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d30789a33eb8023f4ce77b4a7c2445fabca0f2db
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031835"
---
# <a name="introduction-to-ios-6"></a>Introduction à iOS 6

_iOS 6 comprend une variété de nouvelles technologies pour le développement d’applications, que Xamarin. iOS 6 C# apporte aux développeurs._

[![](images/ios6-large.jpg "The iOS 6 logo")](images/ios6-large.jpg#lightbox)

Avec iOS 6 et Xamarin. iOS 6, les développeurs disposent désormais d’une multitude de fonctionnalités pour créer des applications iOS, y compris celles qui ciblent iPhone 5.
Ce document répertorie quelques-unes des nouvelles fonctionnalités les plus intéressantes disponibles, ainsi que des liens vers des articles pour chaque rubrique. En outre, il s’intéresse à quelques modifications qui seront importantes à mesure que les développeurs passeront à iOS 6 et à la nouvelle résolution de iPhone 5.

## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Présentation des vues de collection](~/ios/user-interface/controls/uicollectionview.md)

Les vues de collection permettent d’afficher le contenu à l’aide de dispositions arbitraires. Ils permettent de créer facilement des dispositions de type grille prêtes à l’emploi, tout en prenant également en charge les dispositions personnalisées. Pour plus d’informations, consultez le guide [présentation des vues](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md).

## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Présentation de PassKit](~/ios/platform/passkit.md)

L’infrastructure PassKit permet aux applications d’interagir avec les passes numériques qui sont gérées dans l’application du plan d’application. Pour plus d’informations, consultez le [Guide d’introduction au kit Pass](~/ios/platform/passkit.md).

## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Présentation de EventKit](~/ios/platform/eventkit.md)

L’infrastructure EventKit permet d’accéder aux données calendriers, événements de calendrier et rappels stockées dans la base de données de calendrier. L’accès aux calendriers et aux événements de calendrier est disponible depuis iOS 4, mais iOS 6 expose désormais l’accès aux données de rappels. Pour plus d’informations, consultez le guide [I](~/ios/platform/eventkit.md) [Introduction to EventKit](~/ios/platform/eventkit.md) .

## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Présentation de l’infrastructure sociale](~/ios/platform/social-framework.md)

L’infrastructure sociale fournit une API unifiée pour l’interaction avec les réseaux sociaux, y compris Twitter et Facebook, ainsi que SinaWeibo pour les utilisateurs en Chine. Pour plus d’informations, consultez la présentation du guide [de l’infrastructure sociale](~/ios/platform/social-framework.md) .

## <a name="changes-to-storekitchanges-to-storekitmd"></a>[Modifications apportées à StoreKit](changes-to-storekit.md)

Apple a introduit deux nouvelles fonctionnalités dans le kit de boutique : achat et téléchargement d’iTunes ou du contenu App Store à partir de votre application, et Hébergement de vos fichiers de contenu pour les achats dans l’application !. Pour plus d’informations, consultez le guide des [modifications apportées au kit de stockage](changes-to-storekit.md) .

## <a name="other-changes"></a>Autres modifications

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload et ViewDidUnload déconseillé

Les méthodes `ViewWillUnload` et `ViewDidUnload` de `UIViewController` ne sont plus appelées dans iOS 6. Dans les versions précédentes d’iOS, ces méthodes utilisaient peut-être des applications pour enregistrer l’état avant un déchargement de la vue, et nettoyer le code respectivement.

Par exemple, Visual Studio pour Mac créerait une méthode appelée `ReleaseDesignerOutlets`, comme indiqué ci-dessous, qui serait ensuite appelée à partir de `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Toutefois, dans iOS 6, il n’est plus nécessaire d’appeler `ReleaseDesignerOutlets`.   

Pour le code de nettoyage, les applications iOS 6 doivent utiliser `DidReceiveMemoryWarning`. Toutefois, le code qui appelle `Dispose` doit être utilisé avec modération et uniquement pour les objets gourmands en mémoire, comme indiqué ci-dessous :

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Là encore, l’appel de `Dispose` comme ci-dessus doit être rarement nécessaire. En général, la plupart des applications doivent effectuer la suppression de gestionnaires d’événements.

Dans le cas de l’enregistrement de l’État, les applications peuvent effectuer cette `ViewWillDisappear` et `ViewDidDisappear` au lieu de `ViewWillUnload`.

### <a name="iphone-5-resolution"></a>Résolution iPhone 5

les appareils iPhone 5 ont une résolution 640x1136. Les applications qui ciblaient des versions précédentes d’iOS apparaîtront letterboxed lorsqu’elles seront exécutées sur un iPhone 5, comme indiqué ci-dessous :

 [![](images/01-letterboxed.png "Applications that targeted previous versions of iOS will appear letterboxed when run on an iPhone 5")](images/01-letterboxed.png#lightbox)

Pour que l’application s’affiche en plein écran sur iPhone 5, ajoutez simplement une image nommée `Default-568h@2x.png` avec une résolution de 640x1136. La capture d’écran suivante montre l’application en cours d’exécution une fois que cette image a été incluse :

 [![](images/02-fullscreen.png "This screenshot shows the application running after this image has been included")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Sous-classe UINavigationBar

Dans iOS 6 `UINavigationBar` peut être sous-classé. Cela permet un contrôle supplémentaire de l’apparence du `UINavigationBar`. Par exemple, les applications peuvent créer des sous-classes pour ajouter des sous-affichages, animer ces vues et modifier les limites du `UINavigationBar`.

Le code ci-dessous montre un exemple de `UINavigationBar` sous-classée qui ajoute un `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Pour ajouter une `UINavigationBar` sous-classée à un `UINavigationController`, utilisez le constructeur `UINavigationController` qui prend le type de l' `UINavigationBar` et `UIToolbar`, comme indiqué ci-dessous :

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

L’utilisation de cette `UINavigationBar` sous-classe entraîne l’affichage de la vue image, comme illustré dans la capture d’écran suivante :

 [![](images/03-navbar.png "Using this UINavigationBar subclass results in the image view being displayed as shown in this screenshot")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientation de l’interface

Avant les applications iOS 6, il était possible de remplacer `ShouldAutorotateToInterfaceOrientation`, en renvoyant la valeur true pour toutes les orientations prises en charge par le contrôleur particulier. Par exemple, le code suivant est utilisé pour prendre en charge uniquement le mode portrait :

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

Dans iOS 6 `ShouldAutorotateToInterfaceOrientation` est déconseillé.
Au lieu de cela, les applications peuvent remplacer `GetSupportedInterfaceOrientations` sur le contrôleur d’affichage racine comme indiqué ci-dessous :

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

Sur iPad, la valeur par défaut est quatre orientations si `GetSupportedInterfaceOrientation` n’est pas implémenté. Sur iPhone et iPod Touch, la valeur par défaut est toutes les orientations, à l’exception de `PortraitUpsideDown`.
