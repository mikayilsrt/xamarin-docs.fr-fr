---
title : "Description de la police d’entrée sur iOS" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui met à l’échelle la taille de police d’une entrée.»
ms. Prod : xamarin ms. AssetID : E8881D4E-902B-4397-A43E-916B2885EC87 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="entry-font-size-on-ios"></a>Taille de la police d’entrée sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour mettre à l’échelle la taille de police d’un [`Entry`](xref:Xamarin.Forms.Entry) pour s’assurer que le texte entré tient dans le contrôle. Il est consommé en XAML en affectant [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

La `Entry.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `Entry.EnableAdjustsFontSizeToFitWidth` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. EnableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})), dans l’espace de noms, permet de mettre à [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) l’échelle la taille de police du texte entré pour s’assurer qu’il s’ajuste à [`Entry`](xref:Xamarin.Forms.Entry) . En outre, la [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe de l' `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espace de noms possède également un [ `DisableAdjustsFontSizeToFitWidth` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. DisableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})) qui désactive ce spécifique à la plateforme et un [ `SetAdjustsFontSizeToFitWidth` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}, System. Boolean)) qui peut être utilisée pour activer ou désactiver la mise à l’échelle de la taille de police en appelant la méthode [ `AdjustsFontSizeToFitWidth` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. AdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}))) :

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Le résultat est que la taille de police de [`Entry`](xref:Xamarin.Forms.Entry) est mise à l’échelle pour garantir que le texte entré est ajusté dans le contrôle :

![](entry-font-size-images/entry-font-size.png "Adjust Entry Font Size Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
