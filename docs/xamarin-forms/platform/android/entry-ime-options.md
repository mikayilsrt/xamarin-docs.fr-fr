---
titre : « options de l’éditeur de méthode d’entrée sur Android » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui définit les options de l’éditeur de méthode d’entrée pour le clavier logiciel pour une entrée.»
ms. Prod : xamarin ms. AssetID : 7909C738-04B2-4476-9A3B-A6D79BC3B9B2 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="entry-input-method-editor-options-on-android"></a>Options de l’éditeur de méthode d’entrée d’entrée sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android définit les options de l’éditeur de méthode d’entrée (IME) pour le clavier logiciel d’un [`Entry`](xref:Xamarin.Forms.Entry) . Cela comprend la définition du bouton d’action de l’utilisateur dans le coin inférieur du clavier logiciel et les interactions avec le `Entry` . Il est consommé en XAML en affectant [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) à la propriété jointe une valeur de l' [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) énumération :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

La `Entry.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `Entry.SetImeOptions` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. entry. SetImeOptions ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Entrée}, Xamarin.Forms . La méthode PlatformConfiguration. AndroidSpecific. ImeFlags)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour définir l’option action de méthode d’entrée pour le clavier logiciel pour le [`Entry`](xref:Xamarin.Forms.Entry) , avec l' [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) énumération qui fournit les valeurs suivantes :

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default): indique qu’aucune clé d’action spécifique n’est requise et que le contrôle sous-jacent produira son propre, si possible. Il peut s’agir `Next` de ou `Done` .
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None): indique qu’aucune clé d’action n’est mise à disposition.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go): indique que la clé d’action effectue une opération « Go », en faisant passer l’utilisateur à la cible du texte qu’il a tapé.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search): indique que la clé d’action effectue une opération de « recherche », en faisant passer l’utilisateur aux résultats de la recherche du texte qu’il a tapé.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send): indique que la clé d’action effectue une opération « Send », en transmettant le texte à sa cible.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next): indique que la clé d’action effectue une opération « Next », en faisant passer l’utilisateur au champ suivant qui accepte le texte.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done): indique que la clé d’action effectue une opération « Done », en fermant le clavier logiciel.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous): indique que la clé d’action effectue une opération « précédente », en faisant passer l’utilisateur au champ précédent qui accepte le texte.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction): masque permettant de sélectionner les options d’action.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning): indique que le vérificateur d’orthographe n’apprend pas l’utilisateur, ni ne suggère des corrections en fonction de ce que l’utilisateur a tapé précédemment.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen): indique que l’interface utilisateur ne doit pas s’afficher en plein écran.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi): indique qu’aucune interface utilisateur ne sera affichée pour le texte extrait.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction): indique qu’aucune interface utilisateur ne sera affichée pour les actions personnalisées.

Le résultat est qu’une [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valeur spécifiée est appliquée au clavier logiciel pour le [`Entry`](xref:Xamarin.Forms.Entry) , qui définit les options de l’éditeur de méthode d’entrée :

[![Éditeur de méthode d’entrée entrée spécifique à la plateforme](entry-ime-options-images/entry-imeoptions.png "Éditeur de méthode d’entrée entrée spécifique à la plateforme")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "Éditeur de méthode d’entrée entrée spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
