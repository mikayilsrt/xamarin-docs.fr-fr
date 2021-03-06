---
title : « ScrollView content touche on iOS » Description : «la plateforme spécifique vous permet d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si un ScrollView gère un mouvement tactile ou le transmet à son contenu.»
ms. Prod : xamarin ms. AssetID : 99F823DB-B379-40F0-A343-A9783C341120 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="scrollview-content-touches-on-ios"></a>Le contenu ScrollView touche sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Un minuteur implicite est déclenché lorsqu’un mouvement tactile commence dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView) sur iOS et que le `ScrollView` décide, en fonction de l’action de l’utilisateur dans l’étendue du minuteur, s’il doit gérer le mouvement ou le passer à son contenu. Par défaut, iOS `ScrollView` retarde le contenu, mais cela peut provoquer des problèmes dans certaines circonstances, avec le `ScrollView` contenu qui n’a pas remporté le geste quand c’est le cas. Par conséquent, ce contrôle spécifique à la plateforme détermine si un `ScrollView` gère un mouvement tactile ou le passe à son contenu. Il est consommé en XAML en affectant `ScrollView.ShouldDelayContentTouches` une valeur à la propriété jointe `boolean` :

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

La `ScrollView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `ScrollView.SetShouldDelayContentTouches` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour déterminer si un [`ScrollView`](xref:Xamarin.Forms.ScrollView) gère un mouvement tactile ou le passe à son contenu. En outre, la `SetShouldDelayContentTouches` méthode peut être utilisée pour basculer entre les touches de décalage de contenu en appelant la `ShouldDelayContentTouches` méthode pour retourner si les touches de contenu sont retardées :

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Le résultat est qu’un [`ScrollView`](xref:Xamarin.Forms.ScrollView) peut désactiver le retardement de la réception du contenu, de sorte que dans ce scénario, le [`Slider`](xref:Xamarin.Forms.Slider) reçoit le geste plutôt que la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) page du [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "ScrollView Delay Content Touches Platform-Specific")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
