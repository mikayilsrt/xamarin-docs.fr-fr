---
title : « NavigationPage Text bar mode Color on iOS » Description : «la plateforme spécifique vous permet d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si la couleur de texte de la barre d’État sur un NavigationPage correspond à la luminosité de la barre de navigation.»
ms. Prod : xamarin ms. AssetID : 03698A44-39F1-4030-9AF5-F10A6713828A ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-text-color-mode-on-ios"></a>Mode de couleur du texte de la barre NavigationPage sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle propre à la plateforme détermine si la couleur de texte de la barre d’État sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation. Il est consommé en XAML en affectant [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) à la propriété jointe une valeur de l' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) énumération :

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

La `NavigationPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `NavigationPage.SetStatusBarTextColorMode` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, Xamarin.Forms . PlatformConfiguration. iOSSpecific. StatusBarTextColorMode)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle si la couleur de texte de la barre d’État sur le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation, avec l' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) énumération qui fournit deux valeurs possibles :

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust): indique que la couleur du texte de la barre d’État ne doit pas être ajustée.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity): indique que la couleur du texte de la barre d’État doit correspondre à la luminosité de la barre de navigation.

En outre, le [ `GetStatusBarTextColorMode` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}))) peut être utilisé pour récupérer la valeur actuelle de l' [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) énumération appliquée au [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

Le résultat est que la couleur de texte de la barre d’état d’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) peut être ajustée pour correspondre à la luminosité de la barre de navigation. Dans cet exemple, la couleur de texte de la barre d’état change lorsque l’utilisateur bascule entre les [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) pages et d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

![](status-bar-text-color-images/status-bar-text-color-mode.png "Status Bar Text Color Mode Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
