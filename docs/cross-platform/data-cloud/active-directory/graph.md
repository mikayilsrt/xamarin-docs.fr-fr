---
title: Accès à l’API Graph
description: Ce document décrit comment ajouter Azure Active Directory l’authentification à une application mobile créée avec Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7ccfc082f86d0a0c6f8d29a477101edb72f9c92f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016629"
---
# <a name="accessing-the-graph-api"></a>Accès à l’API Graph

Procédez comme suit pour utiliser le API Graph à partir d’une application Xamarin :

1. L' [inscription auprès de Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sur le portail *WindowsAzure.com* , puis
2. [Configurez les services](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Étape 3. Ajout d’Active Directory l’authentification à une application

Dans votre application, ajoutez une référence à **Azure Active Directory bibliothèque d’authentification (Azure Adal)** à l’aide du gestionnaire de package NuGet dans Visual Studio ou Visual Studio pour Mac.
Veillez à sélectionner **afficher les packages** en préversion pour inclure ce package, car il est toujours en version préliminaire.

> [!IMPORTANT]
> Remarque : Azure ADAL 3,0 est actuellement une version préliminaire et il peut y avoir des modifications avec rupture avant la publication de la version finale. 

![](graph-images/06.-adal-nuget-package.jpg "Add a reference to Azure Active Directory Authentication Library (Azure ADAL)")

Dans votre application, vous devez maintenant ajouter les variables de niveau de classe suivantes qui sont requises pour le processus d’authentification.

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

Une chose à noter ici est `commonAuthority`. Lorsque le point de terminaison d’authentification est `common`, votre application devient **mutualisée**, ce qui signifie que tout utilisateur peut utiliser la connexion avec ses informations d’identification de Active Directory. Après l’authentification, cet utilisateur travaillera dans le contexte de son propre Active Directory, c’est-à-dire qu’il verra les détails relatifs à son Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Méthode Write pour acquérir un jeton d’accès

Le code suivant (pour Android) démarre l’authentification et, à la fin, assigne le résultat dans `authResult`. Les implémentations iOS et Windows Phone diffèrent légèrement : le deuxième paramètre (`Activity`) est différent sur iOS et absent sur Windows Phone.

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

Dans le code ci-dessus, l' `AuthenticationContext` est responsable de l’authentification avec commonAuthority. Il a une méthode `AcquireTokenAsync`, qui prend les paramètres comme une ressource qui doit être accessible, dans ce cas `graphResourceUri`, `clientId`et `returnUri`. L’application revient à l' `returnUri` lorsque l’authentification est terminée. Ce code reste le même pour toutes les plateformes. Toutefois, le dernier paramètre, `AuthorizationParameters`, est différent sur différentes plateformes et est responsable de la Régie du workflow d’authentification.

Dans le cas d’Android ou d’iOS, nous transmettons `this` paramètre à `AuthorizationParameters(this)` pour partager le contexte, tandis que dans Windows, il est passé sans aucun paramètre en tant que nouveau `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Gestion de la continuation pour Android

Une fois l’authentification terminée, le Flow doit revenir à l’application. Dans le cas d’Android, il est géré par le code suivant, qui doit être ajouté à **MainActivity.cs**:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>Gérer la continuation pour Windows Phone

Pour Windows Phone modifiez la méthode `OnActivated` dans le fichier **app.Xaml.cs** avec le code ci-dessous :

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

Maintenant, si vous exécutez l’application, une boîte de dialogue d’authentification doit s’afficher.
Une fois l’authentification réussie, elle vous demandera vos autorisations d’accès aux ressources (dans notre cas API Graph) :

![](graph-images/08.-authentication-flow.jpg "Upon successful authentication, it will ask your permissions to access the resources in our case Graph API")

Si l’authentification réussit et que vous avez autorisé l’application à accéder aux ressources, vous devez disposer d’un `AccessToken` et d’un `RefreshToken` combo dans `authResult`. Ces jetons sont requis pour d’autres appels d’API et pour l’autorisation avec Azure Active Directory en arrière-plan.

![](graph-images/07.-access-token-for-authentication.jpg "These tokens are   required for further API calls and for authorization with Azure Active Directory behind the scenes")

Par exemple, le code ci-dessous vous permet d’obtenir une liste d’utilisateurs à partir de Active Directory. Vous pouvez remplacer l’URL de l’API Web par votre API Web qui est protégée par Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```
