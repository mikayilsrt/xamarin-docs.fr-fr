---
title : « VisualElement Legacy Color mode on Android » Description : «la plateforme spécifique vous permet d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui désactive le mode de Xamarin.Forms couleurs hérité.»
ms. Prod : xamarin ms. AssetID : 37D95A2D-74AC-488A-B903-2BDD799EAA5C ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-legacy-color-mode-on-android"></a>VisualElement mode de couleurs héritées sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Certains des Xamarin.Forms affichages sont dotés d’un mode de couleurs hérité. Dans ce mode, lorsque la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété de la vue est définie sur `false` , la vue se substitue aux couleurs définies par l’utilisateur avec les couleurs natives par défaut pour l’état désactivé. À des fins de compatibilité descendante, ce mode de couleurs hérité reste le comportement par défaut pour les vues prises en charge.

Cette plateforme Android spécifique désactive ce mode de couleurs hérité, de sorte que les couleurs définies sur une vue par l’utilisateur restent même lorsque la vue est désactivée. Il est consommé en XAML en affectant [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) à la propriété jointe la valeur `false` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

La `VisualElement.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. VisualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . VisualElement}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, permet de contrôler si le mode de couleurs hérité est désactivé. En outre, le [ `VisualElement.GetIsLegacyColorModeEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. VisualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . VisualElement}))) peut être utilisé pour retourner une valeur indiquant si le mode de couleurs hérité est désactivé.

Le résultat est que le mode de couleurs hérité peut être désactivé, afin que les couleurs définies sur une vue par l’utilisateur restent même lorsque la vue est désactivée :

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> Lors de la définition d’un [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) sur une vue, le mode de couleurs hérité est complètement ignoré. Pour plus d’informations sur les États visuels, consultez [le Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
