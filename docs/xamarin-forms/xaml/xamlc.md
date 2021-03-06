---
title : "compilation XAML dans Xamarin.Forms " Description : "cet article explique comment XAML peut éventuellement être compilé directement en langage intermédiaire (il) avec le Xamarin.Forms compilateur XAML (XAMLC).
ms. Prod : xamarin ms. AssetID : 9A2D10A6-5DFC-485F-A75A-2F7B98314025 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 08/22/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-compilation-in-xamarinforms"></a>Compilation XAML dansXamarin.Forms

_XAML peut être éventuellement compilé directement en langage intermédiaire (IL) avec le compilateur XAML (XAMLC)._

La compilation XAML offre un certain nombre d’avantages :

- Il effectue une vérification au moment de la compilation du code XAML et informe l’utilisateur des erreurs rencontrées.
- Il supprime une partie du temps de chargement et d’instanciation pour les éléments XAML.
- Il permet de réduire la taille de fichier de l’assembly final en n’incluant plus les fichiers .xaml.

La compilation XAML est désactivée par défaut pour garantir la compatibilité descendante. Il peut être activé au niveau de l’assembly et de la classe en ajoutant l' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attribut.

L’exemple de code suivant montre comment activer la compilation XAML au niveau de l’assembly :

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

Dans cet exemple, la vérification au moment de la compilation de tout le code XAML contenu dans l’assembly sera effectuée, avec les erreurs XAML signalées au moment de la compilation plutôt qu’au moment de l’exécution. Par conséquent, le `assembly` préfixe de l' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attribut spécifie que l’attribut s’applique à l’assembly entier.

> [!NOTE]
> L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attribut et l' [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) énumération résident dans l' `Xamarin.Forms.Xaml` espace de noms, qui doit être importé pour les utiliser.

L’exemple de code suivant montre comment activer la compilation XAML au niveau de la classe :

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

Dans cet exemple, la vérification au moment de la compilation du code XAML de la `HomePage` classe sera effectuée et des erreurs seront signalées dans le cadre du processus de compilation.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les Xamarin.Forms applications. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Liens connexes

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
