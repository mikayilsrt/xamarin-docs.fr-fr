---
title: Renderers Xamarin.Forms Shell personnalisés
description: Les applications Xamarin.Forms Shell sont hautement personnalisables via les propriétés et méthodes qui exposent les différentes classes Shell. Toutefois, il est également possible de créer un renderer Shell personnalisé lorsque des personnalisations plus sophistiquées spécifiques à une plateforme sont requises.
ms.prod: xamarin
ms.assetid: 3B1A6AE8-1D1E-4C34-B9AB-48F4444FEF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: ecb68d662c64b65346ffd04f0d3d3cd525533151
ms.sourcegitcommit: 6ad272c2c7b0c3c30e375ad17ce6296ac1ce72b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66178033"
---
# <a name="xamarinforms-shell-custom-renderers"></a>Renderers Xamarin.Forms Shell personnalisés

Un des avantages des applications Xamarin.Forms Shell est la capacité de personnaliser leur apparence et leur comportement via les propriétés et méthodes qui exposent les différentes classes Shell. Toutefois, il est également possible de créer un renderer Shell personnalisé lorsque des personnalisations plus sophistiquées spécifiques à une plateforme sont requises. Comme avec d’autres renderers personnalisés, un renderer Shell personnalisé peut être ajouté à un projet de plateforme unique afin de personnaliser son apparence et son comportement, tout en autorisant le comportement par défaut sur l’autre plate-forme ; ou un renderer Shell différent peut être ajouté à chaque projet de plateforme pour personnaliser son apparence et son comportement à la fois sur iOS et Android.

Les applications Shell sont rendues à l’aide de la classe `ShellRenderer` sur iOS et Android. Sur iOS, la classe `ShellRenderer` figure dans l’espace de noms `Xamarin.Forms.Platform.iOS`. Sur Android, la classe `ShellRenderer` figure dans l’espace de noms `Xamarin.Forms.Platform.Android`.

Le processus de création d’un renderer personnalisé Shell est le suivant :

1. Créez une sous-classe de la classe `Shell`. Cette opération sera déjà effectuée dans votre application Shell.
1. Consommez la classe `Shell` définie comme une sous-classe. Cette opération sera déjà effectuée dans votre application Shell.
1. Créez une classe de renderer personnalisée dérivée de la classe `ShellRenderer`, sur les plateformes requises.

## <a name="create-a-custom-renderer-class"></a>Créer une classe de renderer personnalisée

Le processus de création d’une classe de renderer personnalisée Shell est le suivant :

1. Créez une sous-classe de la classe `ShellRenderer`.
1. Remplacez les méthodes requises pour effectuer la personnalisation nécessaire.
1. Ajoutez un élément `ExportRendererAttribute` à la sous-classe `ShellRenderer` pour indiquer qu’elle est utilisée pour le rendu de l’application Shell. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Cet attribut est facultatif pour fournir un renderer personnalisé Shell dans chaque projet de plateforme. Si aucun renderer personnalisé n’est inscrit, la valeur de classe par défaut `ShellRenderer` sera utilisée.

La classe `ShellRenderer` expose les méthodes substituables suivantes :

| iOS | Android |
| --- | --- |
| `SetElementSize`<br />`CreateFlyoutRenderer`<br />`CreateNavBarAppearanceTracker`<br />`CreatePageRendererTracker`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellItemTransition`<br />`CreateShellSearchResultsRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTabBarAppearanceTracker`<br />`Dispose`<br />`OnCurrentItemChanged`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateBackgroundColor` | `CreateFragmentForPage`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellFlyoutRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTrackerForToolbar`<br />`CreateToolbarAppearanceTracker`<br />`CreateTabLayoutAppearanceTracker`<br />`CreateBottomNavViewAppearanceTracker`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`SwitchFragment`<br />`Dispose` |

Les classes `FlyoutItem` et `TabBar` sont des alias pour la classe `ShellItem`, et la classe `Tab` est un alias pour la classe `ShellSection`. La méthode `CreateShellItemRenderer` doit donc être substituée lors de la création d’un renderer personnalisé pour des objets `FlyoutItem` et la méthode `CreateShellSectionRenderer` doit être substituée lors de la création d’un renderer personnalisé pour des objets `Tab`.

> [!IMPORTANT]
> Il existe des classes de renderer Shell supplémentaires, telles que `ShellSectionRenderer` et `ShellItemRenderer`, sur iOS et Android. Toutefois, ces classes de renderer supplémentaires sont créées par des remplacements dans la classe `ShellRenderer`. Par conséquent, il est possible de personnaliser le comportement de ces classes de renderer supplémentaires en les sous-classant et en créant une instance de la sous-classe dans la substitution correspondante de la classe `ShellRenderer` sous-classée.

### <a name="ios-example"></a>Exemple iOS

L’exemple de code suivant montre une classe `ShellRenderer` sous-classée pour iOS, qui définit une image d’arrière-plan dans la barre de navigation de l’application Shell :

```csharp
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.iOS.MyShellRenderer))]
namespace Xaminals.iOS
{
    public class MyShellRenderer : ShellRenderer
    {
        protected override IShellSectionRenderer CreateShellSectionRenderer(ShellSection shellSection)
        {
            var renderer = base.CreateShellSectionRenderer(shellSection);
            if (renderer != null)
            {
                (renderer as ShellSectionRenderer).NavigationBar.SetBackgroundImage(UIImage.FromFile("monkey.png"), UIBarMetrics.Default);
            }
            return renderer;
        }
    }
}
```

La classe `MyShellRenderer` remplace la méthode `CreateShellSectionRenderer`, puis récupère le renderer créé par la classe de base. Elle modifie ensuite le renderer en définissant une image d’arrière-plan dans la barre de navigation, avant de retourner le renderer.

### <a name="android-example"></a>Exemple Android

L’exemple de code suivant montre une classe `ShellRenderer` sous-classée pour Android, qui définit une image d’arrière-plan dans la barre de navigation de l’application Shell :

```csharp
using Android.Content;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.Droid.MyShellRenderer))]
namespace Xaminals.Droid
{
    public class MyShellRenderer : ShellRenderer
    {
        public MyShellRenderer(Context context) : base(context)
        {
        }

        protected override IShellToolbarAppearanceTracker CreateToolbarAppearanceTracker()
        {
            return new MyShellToolbarAppearanceTracker(this);
        }
    }
}
```

La classe `MyShellRenderer` remplace la méthode `CreateToolbarAppearanceTracker`, puis retourne une instance de la classe `MyShellToolbarAppearanceTracker`. La classe `MyShellToolbarAppearanceTracker` qui dérive de la classe `ShellToolbarAppearanceTracker`, est illustrée dans l’exemple suivant :

```csharp
using Android.Support.V7.Widget;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

namespace Xaminals.Droid
{
    public class MyShellToolbarAppearanceTracker : ShellToolbarAppearanceTracker
    {
        public MyShellToolbarAppearanceTracker(IShellContext context) : base(context)
        {
        }

        public override void SetAppearance(Toolbar toolbar, IShellToolbarTracker toolbarTracker, ShellAppearance appearance)
        {
            base.SetAppearance(toolbar, toolbarTracker, appearance);
            toolbar.SetBackgroundResource(Resource.Drawable.monkey);
        }
    }
}
```

La classe `MyShellToolbarAppearanceTracker` remplace la méthode `SetAppearance`, puis modifie la barre d’outils en définissant une image d’arrière-plan sur celle-ci.

> [!IMPORTANT]
> Il est uniquement nécessaire d’ajouter l’attribut `ExportRendererAttribute` à un renderer personnalisé qui dérive de la classe `ShellRenderer`. Les classes de renderer Shell sous-classées supplémentaires sont créées par les classes `ShellRenderer` sous-classées.

## <a name="related-links"></a>Liens connexes

- [Renderers Xamarin.Forms personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)