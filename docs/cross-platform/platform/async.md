---
title: Présentation de la prise en charge asynchrone
description: Ce document décrit la programmation avec Async et await, les concepts C# introduits dans 5 pour faciliter l’écriture de code asynchrone.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 8978dbce97948d02d520b788d024fb50f4884635
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488879"
---
# <a name="async-support-overview"></a>Présentation de la prise en charge asynchrone

_C#5 a introduit deux mots clés pour simplifier les programmations asynchrones : Async et await. Ces mots clés vous permettent d’écrire du code simple qui utilise la bibliothèque parallèle de tâches pour exécuter des opérations de longue durée (telles que l’accès réseau) dans un autre thread et pour accéder facilement aux résultats à l’achèvement. Les dernières versions de Xamarin. iOS et Xamarin. Android prennent en charge Async et await. ce document fournit des explications et un exemple d’utilisation de la nouvelle syntaxe avec Xamarin._

La prise en charge asynchrone de Xamarin est basée sur la Fondation mono 3,0 et met à niveau le profil d’API à partir d’une version conviviale de Silverlight pour être une version mobile de .NET 4,5.

## <a name="overview"></a>Vue d'ensemble de

Ce document présente les nouveaux mots clés Async et await, puis décrit quelques exemples simples qui implémentent des méthodes asynchrones dans Xamarin. iOS et Xamarin. Android.

Pour une description plus complète des nouvelles fonctionnalités asynchrones de C# 5 (y compris un grand nombre d’exemples et des scénarios d’utilisation différents), reportez-vous à l’article [programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async).

L’exemple d’application effectue une requête Web asynchrone simple (sans bloquer le thread principal), puis met à jour l’interface utilisateur avec le code HTML et le nombre de caractères téléchargés.

 [![](async-images/AsyncAwait_427x368.png "The sample application makes a simple asynchronous web request without blocking the main thread then updates the UI with the downloaded html and character count")](async-images/AsyncAwait.png#lightbox)

La prise en charge asynchrone de Xamarin est basée sur la Fondation mono 3,0 et met à niveau le profil d’API à partir d’une version conviviale de Silverlight pour être une version mobile de .NET 4,5.

## <a name="requirements"></a>Configuration requise pour

C#5 les fonctionnalités requièrent mono 3,0 qui est inclus dans Xamarin. iOS 6,4 et Xamarin. Android 4,8. Vous serez invité à mettre à niveau vos mono, Xamarin. iOS, Xamarin. Android et Xamarin. Mac pour en tirer parti.

## <a name="using-async-amp-await"></a>Utilisation d' &amp; Async await

 `async` et `await` sont de C# nouvelles fonctionnalités de langage qui fonctionnent conjointement avec la bibliothèque parallèle de tâches pour faciliter l’écriture de code thread afin d’effectuer des tâches de longue durée sans bloquer le thread principal de votre application.

## <a name="async"></a>async

### <a name="declaration"></a>Déclaration

Le mot clé `async` est placé dans une déclaration de méthode (ou sur une méthode lambda ou anonyme) pour indiquer qu’il contient du code qui peut s’exécuter de façon asynchrone, par ex. ne bloque pas le thread de l’appelant.

Une méthode marquée avec `async` doit contenir au moins une expression ou une instruction await. Si aucune instruction `await` n’est présente dans la méthode, elle s’exécute de façon synchrone (comme s’il n’existait aucun modificateur `async`). Cela entraîne également un avertissement du compilateur (mais pas une erreur).

### <a name="return-types"></a>Types de retours

Une méthode Async doit retourner un `Task`, `Task<TResult>` ou `void`.

Spécifiez le type de retour `Task` si la méthode ne retourne aucune autre valeur.

Spécifiez `Task<TResult>` si la méthode doit retourner une valeur, où `TResult` est le type retourné (par exemple, un `int`, par exemple).

Le type de retour `void` est principalement utilisé pour les gestionnaires d’événements qui l’exigent. Le code qui appelle des méthodes asynchrones qui retournent void ne peut pas `await` sur le résultat.

### <a name="parameters"></a>Parameters

Les méthodes asynchrones ne peuvent pas déclarer des paramètres `ref` ou `out`.

## <a name="await"></a>await

L’opérateur await peut être appliqué à une tâche au sein d’une méthode marquée comme Async. La méthode arrête l’exécution à ce stade et attend que la tâche se termine.

L’utilisation de await ne bloque pas le thread de l’appelant, mais le contrôle est retourné à l’appelant. Cela signifie que le thread appelant n’est pas bloqué. par exemple, le thread d’interface utilisateur n’est pas bloqué lors de l’attente d’une tâche.

Lorsque la tâche se termine, la méthode reprend l’exécution au même point dans le code. Cela comprend le retour à l’étendue try d’un bloc try-catch-finally (le cas échéant). await ne peut pas être utilisé dans un bloc catch ou finally.

En savoir plus sur [await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) sur Microsoft docs.

## <a name="exception-handling"></a>Gestion des exceptions

Les exceptions qui se produisent à l’intérieur d’une méthode Async sont stockées dans la tâche et levées lorsque la tâche est `await`Ed. Ces exceptions peuvent être interceptées et gérées à l’intérieur d’un bloc try-catch.

## <a name="cancellation"></a>Annulation

Les méthodes asynchrones qui prennent beaucoup de temps à se terminer doivent prendre en charge l’annulation. En règle générale, l’annulation est appelée comme suit :

- Un objet `CancellationTokenSource` est créé.
- L’instance `CancellationTokenSource.Token` est passée à une méthode asynchrone annulable.
- L’annulation est demandée en appelant la méthode `CancellationTokenSource.Cancel`.

La tâche est ensuite annulée et accuse réception de l’annulation.

Pour plus d’informations sur l’annulation, consultez [Réglage de votre application Async (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application).

## <a name="example"></a>Exemple

Téléchargez l' [exemple de solution Xamarin](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/) (pour iOS et Android) pour voir un exemple fonctionnel de `async` et `await` dans Mobile Apps. L’exemple de code est abordé plus en détail dans cette section.

### <a name="writing-an-async-method"></a>Écriture d’une méthode Async

La méthode suivante montre comment coder une méthode `async` avec une tâche `await`Ed :

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("https://visualstudio.microsoft.com/xamarin"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Notez les points suivants :

- La déclaration de méthode comprend le mot clé `async`.
- Le type de retour est `Task<int>`, de sorte que le code appelant peut accéder à la valeur `int` calculée dans cette méthode.
- L’instruction return est `return exampleInt;` qui est un objet entier, le fait que la méthode retourne `Task<int>` fait partie des améliorations du langage.

### <a name="calling-an-async-method-1"></a>Appel d’une méthode Async 1

Ce gestionnaire d’événements de clic de bouton se trouve dans l’exemple d’application Android pour appeler la méthode décrite ci-dessus :

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Remarques :

- Le délégué anonyme a le préfixe de mot clé Async.
- La méthode asynchrone DownloadHomepage retourne une tâche\<int > stockée dans la variable sizeTask.
- Le code attend la variable sizeTask.  *Il* s’agit de l’emplacement où la méthode est suspendue et le contrôle est retourné au code appelant jusqu’à ce que la tâche asynchrone se termine sur son propre thread.
- L’exécution ne s’interrompt *pas* lorsque la tâche est créée sur la première ligne de la méthode, malgré la tâche en cours de création. Le mot clé await désigne l’emplacement où l’exécution est suspendue.
- Lorsque la tâche asynchrone se termine, intresult est défini et l’exécution se poursuit sur le thread d’origine, à partir de la ligne await.

### <a name="calling-an-async-method-2"></a>Appel d’une méthode Async 2

Dans l’exemple d’application iOS, l’exemple est écrit légèrement différemment pour illustrer une autre approche. Au lieu d’utiliser un délégué anonyme, cet exemple déclare un gestionnaire d’événements `async` qui est assigné comme un gestionnaire d’événements standard :

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

La méthode de gestionnaire d’événements est ensuite définie comme indiqué ici :

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Quelques points importants :

- La méthode est marquée comme `async` mais retourne `void`. Cela s’effectue généralement uniquement pour les gestionnaires d’événements (sinon, vous retournez une `Task` ou `Task<TResult>`).
- Le mot clé `await` sur la méthode `DownloadHomepage` est directement affecté à une variable (`intResult`), contrairement à l’exemple précédent où nous avons utilisé une variable `Task<int>` intermédiaire pour référencer la tâche.  *Il* s’agit de l’emplacement où le contrôle est retourné à l’appelant jusqu’à ce que la méthode asynchrone soit terminée sur un autre thread.
- Quand la méthode asynchrone se termine et retourne, l’exécution reprend au `await`, ce qui signifie que le résultat de l’entier est retourné, puis restitué dans un widget d’interface utilisateur.

## <a name="summary"></a>Récapitulatif

L’utilisation de Async et await simplifie grandement le code requis pour générer des opérations de longue durée sur les threads d’arrière-plan sans bloquer le thread principal. Ils facilitent également l’accès aux résultats lorsque la tâche est terminée.

Ce document a donné une vue d’ensemble des nouveaux mots clés de langage et des exemples pour Xamarin. iOS et Xamarin. Android.

## <a name="related-links"></a>Liens associés

- [AsyncAwait (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)
- [Rappels en tant qu’instruction Go de la génération](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Données (iOS) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/data/)
- [HttpClient (iOS) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch (iOS) (exemple)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async)
- [Ajuster une application Async (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await, et l’interface utilisateur et les blocages ! Mon Dieu!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Traitement des tâches au fur et à mesure de leur exécution)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [Modèle asynchrone basé sur les tâches (TAP, Task-based Asynchronous Pattern)](https://msdn.microsoft.com/library/hh873175.aspx)
- [Asynchronie dans C# 5 (blog de Eric Lippert) : à propos de l’introduction des mots clés](https://blogs.msdn.microsoft.com/ericlippert/2010/11/11/asynchrony-in-c-5-part-six-whither-async/)
