---
ms.openlocfilehash: 2b524c657a95035773d18fb17bb86308a78ce37c
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389966"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Label`](xref:Xamarin.Forms.Label) pour modifier son apparence visuelle :

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Ce code définit les propriétés qui modifient l’apparence visuelle de [`Label`](xref:Xamarin.Forms.Label). La propriété [`TextColor`](xref:Xamarin.Forms.Label.TextColor) définit la couleur du texte `Label`. La propriété [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) définit la police de l’étiquette en italique et la propriété [`FontSize`](xref:Xamarin.Forms.Label.FontSize) définit la taille de police. En outre, un ornement de texte souligné est appliqué à `Label` en définissant sa propriété [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations), et le texte est centré horizontalement en définissant la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) sur [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Vous remarquerez que l’apparence de l’élément [`Label`](xref:Xamarin.Forms.Label) a changé :

    [![Capture d’écran d’une étiquette dont l’apparence a changé, sur iOS et Android](../images/change-label-appearance.png "Étiquette dont l’apparence a changé")](../images/change-label-appearance-large.png#lightbox "Étiquette dont l’apparence a changé")

    Pour plus d’informations sur la configuration de l’apparence de [`Label`](xref:Xamarin.Forms.Label), consultez le guide [Xamarin.Forms Label](~/xamarin-forms/user-interface/text/label.md) guide.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Label`](xref:Xamarin.Forms.Label) pour modifier son apparence visuelle :

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Ce code définit les propriétés qui modifient l’apparence visuelle de [`Label`](xref:Xamarin.Forms.Label). La propriété [`TextColor`](xref:Xamarin.Forms.Label.TextColor) définit la couleur du texte `Label`. La propriété [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) définit la police de l’étiquette en italique et la propriété [`FontSize`](xref:Xamarin.Forms.Label.FontSize) définit la taille de police. En outre, un ornement de texte souligné est appliqué à `Label` en définissant sa propriété [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations), et le texte est centré horizontalement en définissant la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) sur [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Vous remarquerez que l’apparence de l’élément [`Label`](xref:Xamarin.Forms.Label) a changé :

    [![Capture d’écran d’une étiquette dont l’apparence a changé, sur iOS et Android](../images/change-label-appearance.png "Étiquette dont l’apparence a changé")](../images/change-label-appearance-large.png#lightbox "Étiquette dont l’apparence a changé")

    Pour plus d’informations sur la configuration de l’apparence de [`Label`](xref:Xamarin.Forms.Label), consultez le guide [Xamarin.Forms Label](~/xamarin-forms/user-interface/text/label.md) guide.
