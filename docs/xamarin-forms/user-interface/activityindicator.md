---
titre : « indicateur d’activité dans Xamarin.Forms «Description » : le contrôle ActivityIndicator indique aux utilisateurs que l’application est engagée dans une activité longue, sans donner d’indication de la progression. Cet article explique comment utiliser un ActivityIndicator en XAML et du code.»
ms. Prod : xamarin ms. AssetID : 4CEED02D-5CA3-4C3A-B7ED-3193FC272261 ms. Technology : xamarin-Forms Author : profexorgeek ms. Author : jusjohns ms. Date : 07/10/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-activityindicator"></a>Xamarin.FormsActivityIndicator
[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Le Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) contrôle affiche une animation qui montre que l’application est engagée dans une activité longue. Contrairement à [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , le `ActivityIndicator` n’indique aucune progression. `ActivityIndicator`Hérite de [`View`](xref:Xamarin.Forms.View) .

Les captures d’écran suivantes montrent un `ActivityIndicator` contrôle sur iOS et Android :

![Capture d’écran de ActivityIndicator sur iOS et Android](activityindicator-images/activityindicators-default.png "Capture d’écran de ActivityIndicator sur iOS et Android")

Le `ActivityIndicator` contrôle définit les propriétés suivantes :

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)est une `Color` valeur qui définit la couleur d’affichage du `ActivityIndicator` .
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)est une `bool` valeur qui indique si `ActivityIndicator` doit être visible, animé ou masqué. Lorsque la valeur est `false` `ActivityIndicator` , n’est pas visible.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que `ActivityIndicator` peut être stylisé et être la cible des liaisons de données.

## <a name="create-an-activityindicator"></a>Créer un ActivityIndicator

La `ActivityIndicator` classe peut être instanciée en XAML. Sa `IsRunning` propriété détermine si le contrôle est visible et anime. La `IsRunning` valeur par défaut de la propriété est `false` . L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le `IsRunning` jeu de propriétés facultatif :

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` peut également être créé dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriétés d’apparence ActivityIndicator

La `Color` propriété définit la `ActivityIndicator` couleur. L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le `Color` jeu de propriétés :

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propriété peut également être définie lors de la création d’un `ActivityIndicator` dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Les captures d’écran suivantes montrent le `ActivityIndicator` avec la `Color` propriété définie `Color.Orange` sur iOS et Android :

![Capture d’écran des ActivityIndicator stylisés sur iOS et Android](activityindicator-images/activityindicators-styled.png "Capture d’écran des ActivityIndicator stylisés sur iOS et Android")

## <a name="related-links"></a>Liens connexes

* [Démonstrations ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [Barre de progression](~/xamarin-forms/user-interface/progressbar.md)
