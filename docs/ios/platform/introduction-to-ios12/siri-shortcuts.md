---
title: Raccourcis Siri dans Xamarin. iOS
description: Ce document explique comment utiliser les raccourcis Siri dans iOS 12. Il aborde NSUserActivities, les intentions personnalisées, l’affectation de raccourcis vocaux, les raccourcis pertinents et bien plus encore.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/08/2018
ms.openlocfilehash: 40b7adbed3489d449e583b22fa477287d11bdf42
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031870"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Raccourcis Siri dans Xamarin. iOS

Dans [iOS 10](~/ios/platform/sirikit/index.md), Apple a introduit SiriKit, ce qui permet de créer des applications de messagerie, d’appel VoIP, de paiement, d’entraînement, de réservation et de recherche de photos qui interagissent avec Siri.

Dans [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit a obtenu la prise en charge d’un plus grand nombre d’applications et d’une plus grande flexibilité pour la personnalisation de l’interface utilisateur.

iOS 12 ajoute des raccourcis Siri, ce qui permet à tous les types d’applications d’exposer leurs fonctionnalités à Siri. Siri apprend que certaines tâches basées sur les applications sont les plus pertinentes pour l’utilisateur et utilise ces connaissances pour suggérer des actions potentielles via des _raccourcis_. En appuyant sur un raccourci ou en l’appelant avec une commande vocale, vous ouvrez une application ou vous exécutez une tâche en arrière-plan.

Les raccourcis doivent être utilisés pour accélérer la capacité d’un utilisateur à accomplir une tâche courante (dans de nombreux cas sans même ouvrir l’application en question).

## <a name="sample-app-soup-chef"></a>Exemple d’application : soupe chef

Pour mieux comprendre les raccourcis Siri, jetez un coup d’œil à l’exemple d’application de [soupe chef](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef) . Soupe chef permet aux utilisateurs de passer des commandes à partir d’un restaurant de soupe imaginaire, d’afficher leur historique des commandes et de définir des expressions à utiliser lors du tri des soupes en interagissant avec Siri.

> [!TIP]
> Avant de tester soupe chef sur un appareil ou un simulateur iOS 12, activez les deux paramètres suivants, qui sont utiles lors du débogage de raccourcis :
>
> - Dans l’application **paramètres** , activez les **> de développement afficher les raccourcis récents**.
> - Dans l’application **paramètres** , activez les **> de développement afficher les dons sur l’écran de verrouillage**.
>
> Ces paramètres de débogage facilitent la recherche de raccourcis créés récemment (au lieu de prédits) sur l’écran de verrouillage et l’écran de recherche iOS.

Pour utiliser l’exemple d’application :

- Installez et exécutez l’exemple d’application de soupe chef sur un [appareil](#testing-on-device)ou un simulateur IOS 12.
- Cliquez sur le bouton **+** dans l’angle supérieur droit pour créer une nouvelle commande.
- Sélectionnez un type de soupe, spécifiez une quantité et des options, puis cliquez sur **passer une commande**.
- Dans l’écran **historique des commandes** , cliquez sur la commande qui vient d’être créée pour en afficher les détails.
- En bas de l’écran détails de la commande, appuyez sur **Ajouter à Siri**.
- Enregistrez une expression vocale à associer à la commande, puis appuyez sur **terminé**.
- Réduisez à z chef, appelez Siri et replacez la commande à l’aide de l’expression vocale que vous venez d’enregistrer.
- Une fois que Siri a terminé la commande, rouvrez le chef et notez que la nouvelle commande est indiquée sur l’écran **historique des commandes** .

L’exemple d’application montre comment :

- [Utilisez un raccourci NSUserActivity pour ouvrir une application](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Utilisez un raccourci d’intention personnalisé pour effectuer une tâche](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Fournissez une interface utilisateur pour une intention personnalisée](#providing-a-user-interface-for-a-custom-intent).
- [Créez un raccourci vocal](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info. plist et habilitations. plist

Avant de vous plonger plus profondément dans le code de la chef du chef, jetez un coup d’œil à ses fichiers **info. plist** et **contitles. plist** .

### <a name="infoplist"></a>Info.plist

Le fichier **info. plist** du projet **SoupChef** définit l' **identificateur de Bundle** comme `com.xamarin.SoupChef`. Cet identificateur de Bundle sera utilisé comme préfixe pour les identificateurs de Bundle des intentions et des extensions d’interface utilisateur pour les intentions présentées plus loin dans ce document.

Le fichier **info. plist** contient également les éléments suivants :

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Cette paire clé/valeur `NSUserActivityTypes` indique que le chef de soupe sait comment gérer un `OrderSoupIntent`, et un [`NSUserActivity`](xref:Foundation.NSUserActivity) ayant un [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) « com. Xamarin. SoupChef. viewMenu ».

Les activités et les intentions personnalisées passées à l’application elle-même, par opposition à ses extensions, sont gérées dans le `AppDelegate` (une [`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) par la méthode [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) .

### <a name="entitlementsplist"></a>Entitlements.plist

Le fichier **Entitlements. plist** du projet **SoupChef** contient les éléments suivants :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Cette configuration indique que l’application utilise le groupe d’applications « Group. com. xamarin. SoupChef ». L’extension d’application **SoupChefIntents** utilise ce même groupe d’applications, ce qui permet aux deux projets de partager des [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
Données.

La clé de `com.apple.developer.siri` indique que l’application interagit avec Siri.

> [!NOTE]
> La configuration de build du projet **SoupChef** définit des **habilitations personnalisées** dans **Entitlements. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Utilisation d’un raccourci NSUserActivity pour ouvrir une application

Pour créer un raccourci qui ouvre une application afin d’afficher un contenu spécifique, créez une `NSUserActivity` et attachez-la au contrôleur d’affichage de l’écran que vous souhaitez ouvrir.

### <a name="setting-up-an-nsuseractivity"></a>Configuration d’un NSUserActivity

Dans l’écran du menu, `SoupMenuViewController` crée une `NSUserActivity` et l’assigne à la propriété [`UserActivity`](xref:UIKit.UIResponder.UserActivity) du contrôleur d’affichage :

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

La définition de la _propriété `UserActivity`_ donne l’activité à Siri. À partir de ce don, Siri obtient des informations sur le moment et l’endroit où cette activité est pertinente pour l’utilisateur et apprend à mieux la suggérer à l’avenir.

`NSUserActivityHelper` est une classe utilitaire incluse dans la solution **SoupChef** , dans la bibliothèque de classes **SoupKit** . Il crée une `NSUserActivity` et définit différentes propriétés relatives à Siri et à la recherche :

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Notez les points suivants en particulier :

- L’affectation de la valeur `true` à `EligibleForPrediction` indique que les Siri peuvent prédire cette activité et la surfacer sous la forme d’un raccourci.
- Le tableau [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet) est un [`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) standard utilisé pour inclure un `NSUserActivity` dans les résultats de la recherche iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) est une expression que Siri suggère à l’utilisateur comme un choix potentiel lors de l’affectation d’une expression à un raccourci.

### <a name="handling-an-nsuseractivity-shortcut"></a>Gestion d’un raccourci NSUserActivity

Pour gérer un raccourci de `NSUserActivity` appelé par un utilisateur, une application iOS doit substituer la méthode `ContinueUserActivity` de la classe `AppDelegate`, en répondant en fonction du champ `ActivityType` de l’objet `NSUserActivity` passé :

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Cette méthode appelle `HandleUserActivity`, qui recherche le segue sur l’écran de menu et l’appelle :

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Affectation d’une expression à un NSUserActivity

Pour affecter une expression à un `NSUserActivity`, ouvrez l’application **paramètres** iOS et choisissez **Siri & Rechercher > Mes raccourcis**. Sélectionnez ensuite le raccourci (dans ce cas, « Order déjeuner ») et enregistrez une expression.

L’appel de Siri et l’utilisation de cette phrase ouvrent le chef de soupe à l’écran de menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Utilisation d’un raccourci d’intention personnalisé pour effectuer une tâche

### <a name="defining-a-custom-intent"></a>Définition d’une intention personnalisée

Pour fournir un raccourci permettant à un utilisateur d’effectuer rapidement une tâche spécifique liée à votre application, créez une intention personnalisée. Une intention personnalisée représente une tâche qu’un utilisateur peut vouloir effectuer, les paramètres relatifs à cette tâche et les réponses potentielles résultant de l’exécution de la tâche. Selon la façon dont une intention personnalisée est définie, l’appel de celle-ci peut ouvrir votre application ou exécuter une tâche en arrière-plan.

Utilisez Xcode 10 pour créer des intentions personnalisées. Dans le [référentiel SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), l’intention personnalisée est définie dans **OrderSoupIntentCodeGen**, un projet objective-C. Ouvrez ce projet et sélectionnez le fichier intentions **. intentdefinition** dans le navigateur de **projet** pour afficher l’intention **OrderSoup** .

Notez les points suivants :

- L’intention a une **catégorie** de **commande**. Il existe différentes catégories prédéfinies qui peuvent être utilisées pour les intentions personnalisées ; sélectionnez celui qui correspond le mieux à la tâche que votre intention personnalisée activera. Étant donné qu’il s’agit d’une application de commande de soupe, **OrderSoupIntent** utilise **Order**.
- La case à cocher **confirmation** indique si Siri doit demander une confirmation avant d’exécuter la tâche. Pour l’intention de la commande de l' **ordre de tri** dans le chef de soupe, cette option est activée puisque l’utilisateur effectue un achat.
- La section **Parameters** du fichier. intentdefinition définit les paramètres relatifs à un raccourci. Pour placer une commande de soupe, le chef de soupe doit connaître le type de soupe, sa quantité et toutes les options associées.
Chaque paramètre a un type ; le paramètre qui ne peut pas être représenté par un type prédéfini est défini comme **personnalisé**.
- L’interface des **types de raccourcis** décrit les différentes combinaisons de paramètres que Siri peut utiliser pour suggérer votre raccourci. Les sections **titre** et **sous-titre** associées vous permettent de définir les messages que Siri utilisera lors de la présentation d’un raccourci suggéré à l’utilisateur.
- La case à cocher **prend en charge l’exécution en arrière-plan** doit être activée pour tous les raccourcis qui peuvent être exécutés sans ouvrir l’application pour une interaction supplémentaire de l’utilisateur.

### <a name="defining-custom-intent-responses"></a>Définition de réponses d’intention personnalisées

L’élément de **réponse** imbriqué sous l’intention **OrderSoup** représente les réponses potentielles qui résultent d’un ordre de soupe.

Dans la définition de la réponse de l’intention **OrderSoup** , notez les points suivants :

- Les **Propriétés** d’une réponse peuvent être utilisées pour personnaliser le message renvoyé à l’utilisateur. La réponse intentionnelle **OrderSoup** a des propriétés de **soupe** et **délai** .
- Les **modèles de réponse** spécifient les divers messages de réussite et d’échec qui peuvent être utilisés pour indiquer l’état après la fin de la tâche d’un objectif.
- La case à cocher **succès** doit être activée pour les réponses indiquant une réussite.
- La réponse de réussite **OrderSoupIntent** utilise les propriétés de **soupe** et de **délai** pour fournir un message convivial qui décrit le moment où la commande de la soupe est prête.

### <a name="generating-code-for-the-custom-intent"></a>Génération de code pour l’intention personnalisée

Si vous générez le projet XCode contenant cette définition d’intention personnalisée, Xcode génère du code qui peut être utilisé pour interagir par programmation avec l’intention personnalisée et ses réponses.

Pour afficher ce code généré :

- Ouvrez **AppDelegate. m**.
- Ajoutez une importation au fichier d’en-tête de l’intention personnalisée : `#import "OrderSoupIntent.h"`
- Dans toute méthode de la classe, ajoutez une référence à `OrderSoupIntent`.
- Cliquez avec le bouton droit sur `OrderSoupIntent` et choisissez **atteindre la définition**.
- Cliquez avec le bouton droit dans le fichier récemment ouvert, **OrderSoupIntent. h**, puis sélectionnez **afficher dans le Finder**.
- Cela ouvre une fenêtre **Finder** qui contient un fichier. h et. m contenant le code généré.

Ce code généré comprend les éléments suivants :

- `OrderSoupIntent` : classe qui représente l’intention personnalisée.
- `OrderSoupIntentHandling` : protocole qui définit les méthodes qui seront utilisées pour confirmer que l’intention doit être exécutée et la méthode qui l’exécute réellement.
- `OrderSoupIntentResponseCode` : énumération qui définit différents États de réponse.
- `OrderSoupIntentResponse` : classe qui représente la réponse à l’exécution d’une intention.

### <a name="creating-a-binding-to-the-custom-intent"></a>Création d’une liaison à l’intention personnalisée

Pour utiliser le code généré par Xcode dans une application Xamarin. iOS, créez une C# liaison pour celui-ci.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Création d’une bibliothèque statique C# et de définitions de liaison

Dans le [référentiel SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), examinez le dossier **OrderSoupIntentStaticLib** , puis ouvrez le projet XCode **OrderSoupIntentStaticLib. Xcodeproj** .

Ce projet de **bibliothèque statique de cacao tactile** contient les fichiers **OrderSoupIntent. h** et **OrderSoupIntent. m** générés par Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configuration des paramètres de génération du projet de bibliothèque statique

Dans le **navigateur de projet**Xcode, sélectionnez le projet de niveau supérieur **OrderSoupIntentStaticLib**, puis naviguez jusqu’à **phases de build > compiler les sources**.
Notez que **OrderSoupIntent. m** (qui importe **OrderSoupIntent. h**) est répertorié ici. Dans **lien Binary avec les bibliothèques**, Notez que **intentions. Framework** et **Foundation. Framework** sont inclus.
Une fois ces paramètres en place, l’infrastructure sera correctement générée.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Génération de la bibliothèque statique et C# génération de définitions de liaisons

Pour générer la bibliothèque statique et générer C# des définitions de liaisons pour celle-ci, procédez comme suit :

- [Installer la netteté objective](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), outil utilisé pour générer des définitions de liaisons à partir des fichiers. h et. m créés par Xcode.

- Configurez votre système pour utiliser les outils en ligne de commande Xcode 10 :

  > [!WARNING]
  > La mise à jour des outils en ligne de commande sélectionnés a un impact sur toutes les versions installées de XCode sur votre système. Une fois que vous avez fini d’utiliser l’exemple d’application de soupe chef, veillez à rétablir la configuration d’origine de ce paramètre.

  - Dans Xcode, choisissez **xcode > préférences > emplacements** et définissez les **outils en ligne de commande** sur l’installation de Xcode 10 la plus récente disponible sur votre système.

- Dans le terminal, `cd` au répertoire **OrderSoupIntentStaticLib** .

- `make`de type, qui génère :

  - Bibliothèque statique, **libOrderSoupIntentStaticLib. a**
  - Dans le répertoire de sortie Bo C# , définitions de liaisons :
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

Le projet **OrderSoupIntentBindings** , qui s’appuie sur cette bibliothèque statique et ses définitions de liaisons associées, génère automatiquement ces éléments.
Toutefois, l’exécution manuelle par le biais du processus ci-dessus permet de s’assurer qu’il s’exécute comme prévu.

Pour plus d’informations sur la création d’une bibliothèque statique et l’utilisation de C# la finesse objective pour créer des définitions de liaisons, consultez la rubrique relative à la [liaison d’une bibliothèque objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) .

#### <a name="creating-a-bindings-library"></a>Création d’une bibliothèque de liaisons

Avec la bibliothèque statique et les C# définitions de liaisons créées, la partie restante nécessaire pour utiliser le code intentionnel généré par Xcode dans un projet Xamarin. IOS est une bibliothèque de liaisons.

Dans le [référentiel de soupe chef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), ouvrez le fichier **SoupChef. sln** . Entre autres choses, cette solution contient **OrderSoupIntentBinding**, une bibliothèque de liaisons pour la bibliothèque statique générée ci-dessus.

Notez en particulier que ce projet comprend les éléments suivants :

- **ApiDefinitions.cs** : fichier généré ci-dessus par la netteté objective et ajouté à ce projet. L’action de **génération** de ce fichier est définie sur **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** : autre fichier généré ci-dessus par la finesse objective et ajouté à ce projet. L’action de **génération** de ce fichier est définie sur **ObjcBindingCoreSource**.
- Une **référence Native** à **libOrderSoupIntentStaticLib. a**, la bibliothèque statique créée ci-dessus.

> [!NOTE]
> **ApiDefinitions.cs** et **StructsAndEnums.cs** contiennent des attributs tels que `[Watch (5,0), iOS (12,0)]`. Ces attributs, générés par la netteté objective, ont été commentés, car ils ne sont pas nécessaires pour ce projet.

Pour plus d’informations sur la C# création d’une bibliothèque de liaisons, consultez la procédure de [liaison d’une bibliothèque objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) .

Notez que le projet **SoupChef** contient une référence à **OrderSoupIntentBinding**, ce qui signifie qu’il peut désormais accéder, C#dans, aux classes, interfaces et énumérations qu’il contient :

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Ajout du fichier. intentdefinition à votre solution

Dans la C# solution **SoupChef** , le projet **SoupKit** contient du code partagé entre l’application et ses extensions. Le fichier **Intents. intentdefinition** a été placé dans le **répertoire base. lproj** de **SoupKit**, et a une **action de génération** de **contenu**. Le processus de génération copie ce fichier dans l’offre groupée d’applications de soupe, où il est nécessaire pour que l’application fonctionne correctement.

### <a name="donating-an-intent"></a>Don d’une intention

Pour que Siri suggère un raccourci, il doit tout d’abord comprendre quand le raccourci est pertinent.

Pour donner à Siri cette compréhension, soupe _chef donne_ une intention à Siri chaque fois que l’utilisateur place une commande de soupe. En fonction de ce don, quand il a été retenu, où il a été Don, les paramètres qu’il contient, Siri apprennent quand suggérer le raccourci à l’avenir.

**SoupChef** utilise la classe `SoupOrderDataManager` pour placer les dons.
Lorsqu’elle est appelée pour placer une commande de soupe pour un utilisateur, la méthode `PlaceOrder` appelle à son tour les [`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Après avoir extrait une intention, elle est incluse dans un wrapper dans un [`INInteraction`](xref:Intents.INInteraction).
La `INInteraction` reçoit une [`Identifier`](xref:Intents.INInteraction.Identifier*)
qui correspond à l’ID unique de la commande (cela sera utile ultérieurement lors de la suppression de dons intentionnels qui ne sont plus valides). L’interaction est ensuite Reverse dans Siri.

L’appel à l’accesseur Get `order.Intent` extrait un `OrderSoupIntent` qui représente l’ordre en définissant son `Quantity`, `Soup`, `Options`et une image, et une expression d’appel à utiliser comme suggestion lorsque l’utilisateur enregistre une expression que Siri doit associer à l’intention :

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Suppression de dons non valides

Il est important de supprimer les dons qui ne sont plus valides afin que Siri ne fasse pas de suggestions de raccourcis non utiles ou confuses.

Dans soupe chef, l’écran de **menu configurer** peut être utilisé pour marquer un élément de menu comme non disponible. Siri ne doit plus suggérer de raccourci pour commander un élément de menu non disponible. par conséquent, la méthode `RemoveDonation` de `SoupMenuManager` supprime les dons pour les éléments de menu qui ne sont plus disponibles. Pour ce faire, il effectue les opérations suivantes :

- Recherche des commandes associées à l’élément de menu désormais non disponible.
- Saisie de leurs identificateurs.
- Suppression des interactions qui ont ce même identificateur.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Création d’une extension intentions

Le code qui s’exécute quand Siri appelle une intention est placé dans une extension intentions, qui peut être ajoutée en tant que nouveau projet à la même solution qu’une application Xamarin. iOS existante telle que soupe chef. Dans la solution **SoupChef** , l’extension est appelée **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – info. plist et habilitations. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – info. plist

Le fichier **info. plist** du projet **SoupChefIntents** définit l' **identificateur de Bundle** comme `com.xamarin.SoupChef.SoupChefIntents`.

Le fichier **info. plist** contient également les éléments suivants :

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

Dans le fichier **info. plist**ci-dessus :

- `IntentsRestrictedWhileLocked` répertorie les intentions qui doivent être gérées uniquement lorsque l’appareil est déverrouillé.
- `IntentsSupported` répertorie les intentions gérées par cette extension.
- `NSExtensionPointIdentifier` spécifie le type d’extension d’application (pour plus d’informations, consultez la [documentation d’Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) ).
- `NSExtensionPrincipalClass` spécifie la classe qui doit être utilisée pour gérer les intentions prises en charge par cette extension.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – habilitations. plist

La fonctionnalité des **groupes d’applications** du projet **SoupChefIntents** contient les **droits. plist.** Cette fonctionnalité est configurée pour utiliser le même groupe d’applications que le projet **SoupChef** :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soupe chef conserve les données avec `NSUserDefaults`. Pour partager des données entre l’application et l’extension de l’application, elles font référence au même groupe d’applications dans leurs fichiers **Entitlements. plist** .

> [!NOTE]
> La configuration de build du projet **SoupChefIntents** définit des **habilitations personnalisées** dans **Entitlements. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Gestion d’une tâche en arrière-plan OrderSoupIntent

Une extension intentions exécute les tâches d’arrière-plan nécessaires pour un raccourci selon une intention personnalisée.

Siri appelle la méthode [`GetHandler`](xref:Intents.INExtension.GetHandler*) de la classe `IntentHandler` (définie dans **info. plist** comme `NSExtensionPrincipalClass`) pour obtenir une instance d’une classe qui étend `OrderSoupIntentHandling`, qui peut être utilisé pour gérer un `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, défini dans le projet **SoupKit** de code partagé, implémente deux méthodes importantes :

- `ConfirmOrderSoup` : confirme si la tâche associée à l’intention doit réellement être exécutée
- `HandleOrderSoup` : place l’ordre de soupe et répond à l’utilisateur en appelant le gestionnaire d’achèvement passé.

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Gestion d’un OrderSoupIntent qui ouvre l’application

Une application doit gérer correctement les intentions qui ne s’exécutent pas en arrière-plan.
Celles-ci sont gérées de la même façon que `NSUserActivity` les raccourcis, dans la méthode `ContinueUserActivity` de `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Fournir une interface utilisateur pour une intention personnalisée

Une extension d’interface utilisateur Intent fournit une interface utilisateur personnalisée pour une extension intentions. Dans la solution **SoupChef** , **SoupChefIntentsUI** est une extension d’interface utilisateur Intent qui fournit une interface pour **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – info. plist et habilitations. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – info. plist

Le fichier **info. plist** du projet **SoupChefIntentsUI** définit l' **identificateur de Bundle** comme `com.xamarin.SoupChef.SoupChefIntentsui`.

Le fichier **info. plist** contient également les éléments suivants :

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

Dans le fichier **info. plist**ci-dessus :

- `IntentsSupported` indique que le `OrderSoupIntent` est géré par cette extension d’interface utilisateur Intent.
- `NSExtensionPointIdentifier` spécifie le type d’extension d’application (pour plus d’informations, consultez la [documentation d’Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) ).
- `NSExtensionMainStoryboard` spécifie la table de montage séquentiel qui définit l’interface principale de cette extension

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – habilitations. plist

Le projet **SoupChefIntentsUI** n’a pas besoin d’un fichier **Entitlements. plist** .

### <a name="creating-the-user-interface"></a>Création de l’interface utilisateur

Étant donné que le fichier **info. plist** pour **SoupChefIntentsUI** affecte la valeur `MainInterface`à la clé `NSExtensionMainStoryboard`, le fichier **MainInterace. Storyboard** définit l’interface de l’extension d’interface utilisateur Intents.

Dans cette table de montage séquentiel, il existe un seul contrôleur d’affichage, de type **IntentViewController**. Il fait référence à deux vues :

- **invoiceView**, de type `InvoiceView`
- **confirmationView**, de type `ConfirmOrderView`

> [!NOTE]
> Les interfaces pour **invoiceView** et **confirmationView** sont définies dans **main. Storyboard** comme vues secondaires. Le concepteur iOS dans Visual Studio pour Mac et Visual Studio 2017 ne prend pas en charge l’affichage ou la modification des vues secondaires. pour ce faire, ouvrez **main. Storyboard** dans le Interface Builder de Xcode.

`IntentViewController` implémente l' [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interface, utilisée pour fournir une interface personnalisée lors de l’utilisation d’intentions Siri. [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
la méthode est appelée pour personnaliser l’interface, en affichant la confirmation ou la facture, selon que l’interaction est confirmée ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) ou a été exécutée avec succès ([`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)) :

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Pour plus d’informations sur la méthode `ConfigureView`, regardez la présentation d’Apple WWDC 2017, [What’s New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Création d’un raccourci vocal

Soupe chef fournit une interface permettant d’attribuer un raccourci vocal à chaque commande, ce qui permet de commander un soupe avec Siri. En fait, l’interface utilisée pour enregistrer et affecter des raccourcis vocaux est fournie par iOS et requiert peu de code personnalisé.

Dans `OrderDetailViewController`, lorsqu’un utilisateur appuie sur la ligne **Add to Siri** de la table, la méthode [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*) présente un écran qui permet d’ajouter ou de modifier un raccourci vocal :

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

Selon qu’il existe ou non un raccourci vocal existant pour l’ordre actuellement affiché, `RowSelected` présente un contrôleur d’affichage de type [`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController) ou [`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController).
Dans chaque cas, `OrderDetailViewController` se définit comme `Delegate`du contrôleur d’affichage, ce qui explique pourquoi il implémente également [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
et [`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Test sur l’appareil

Pour exécuter soupe chef sur un appareil, suivez les instructions ci-dessous. Lisez également la [Remarque relative à l’approvisionnement automatique](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Groupe d’applications, ID d’application, profils de provisionnement

Dans la section **certificats, id & profils** du [portail des développeurs Apple](https://developer.apple.com/), procédez comme suit :

- Créez un groupe d’applications pour partager des données entre l’application de soupe chef et ses extensions. Par exemple : **Group. com. yourcompanyname. SoupChef**

- Créez trois ID d’application : l’un pour l’application elle-même, l’autre pour l’extension intentions et l’autre pour l’extension d’interface utilisateur Intents. Exemple :

  - Application : **com. yourcompanyname. SoupChef**
    - Pour cet ID d’application, affectez les fonctionnalités SiriKit et **app Groups** .

  - Extension intentes : **com. yourcompanyname. SoupChef. intentions**
    - Pour cet ID d’application, affectez la fonctionnalité **groupes d’applications** .

  - Extension d’interface utilisateur Intents : **com. yourcompanyname. SoupChef. Intentsui**
    - Cet ID d’application n’a pas besoin de fonctionnalités spéciales.

- Après avoir créé les ID d’application ci-dessus, modifiez la capacité des **groupes d’applications** affectée à l’application et à l’extension intentions, en spécifiant le groupe d’applications spécifique créé ci-dessus.

- Créez trois nouveaux profils de provisionnement de développement, un pour chacun des nouveaux ID d’application.

- Téléchargez ces profils de provisionnement et double-cliquez sur chacun d’eux pour l’installer. Si Visual Studio pour Mac ou Visual Studio 2017 est déjà en cours d’exécution, redémarrez-le pour vous assurer qu’il inscrit les nouveaux profils de provisionnement.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Modification d’info. plist, de Entitlements. plist et du code source

Dans Visual Studio pour Mac ou Visual Studio 2017, procédez comme suit :

- Mettez à jour les divers fichiers **info. plist** dans la solution. Définissez les ID d’application définis ci-dessus pour l’extension application, intentions et l' **identificateur de Bundle** d’extension d’interface utilisateur Intents.

  - Application : **com. yourcompanyname. SoupChef**
  - Extension intentes : **com. yourcompanyname. SoupChef. intentions**
  - Extension d’interface utilisateur Intents : **com. yourcompanyname. SoupChef. Intentsui**

- Mettez à jour le fichier **habilitations. plist** pour le projet **SoupChef** :
  - Pour la fonctionnalité **groupes d’applications** , définissez le groupe sur le nouveau groupe d’applications créé ci-dessus (dans l’exemple ci-dessus, il s’agissait de **Group. com. yourcompanyname. SoupChef**).
  - Assurez-vous que **SiriKit** est activé.

- Mettez à jour le fichier **habilitations. plist** pour le projet **SoupChefIntents** :
  - Pour la fonctionnalité **groupes d’applications** , définissez le groupe sur le nouveau groupe d’applications créé ci-dessus (dans l’exemple ci-dessus, il s’agissait de **Group. com. yourcompanyname. SoupChef**).

- Enfin, ouvrez **NSUserDefaultsHelper.cs**. Affectez à la variable `AppGroup` la valeur de votre nouveau groupe d’applications (par exemple, affectez-lui la valeur `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Configuration des paramètres de build

Dans Visual Studio pour Mac ou Visual Studio 2017 :

- Ouvrez les options/propriétés du projet **SoupChef** . Sous l’onglet **iOS Bundle Signing** , attribuez à l' **identité de signature** la valeur automatique et profil de **provisionnement** le nouveau profil de configuration spécifique à l’application que vous avez créé ci-dessus.

- Ouvrez les options/propriétés du projet **SoupChefIntents** . Sous l’onglet **iOS Bundle Signing (signature d’offre groupée iOS** ), affectez la valeur Automatic à l' **identité de signature** et profil de **provisionnement** au nouveau profil de provisionnement spécifique de l’extension intentions créée ci-dessus.

- Ouvrez les options/propriétés du projet **SoupChefIntentsUI** . Sous l’onglet **iOS Bundle Signing (signature d’offre groupée iOS** ), définissez l' **identité de signature** sur Automatic et le profil de **provisionnement** pour le nouveau profil d’approvisionnement spécifique à l’extension d’interface utilisateur que vous avez créé ci-dessus.

Une fois ces modifications effectuées, l’application s’exécute sur un appareil iOS.

### <a name="automatic-provisioning"></a>Approvisionnement automatique

Notez que vous pouvez utiliser l' [approvisionnement automatique](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) pour effectuer un grand nombre de ces tâches d’approvisionnement directement dans l’IDE.
Toutefois, l’approvisionnement automatique ne configure pas les groupes d’applications. Vous devez configurer manuellement les fichiers **habilitations. plist** avec le nom du groupe d’applications que vous souhaitez utiliser, accéder au portail des développeurs Apple pour créer le groupe d’applications, attribuer ce groupe d’applications à chaque ID d’application créé par l’approvisionnement automatique, régénérer les profils de provisionnement (extension App, astentes, extension d’IU) pour inclure le groupe d’applications nouvellement créé, puis les télécharger et les installer.

## <a name="related-links"></a>Liens connexes

- [Soupe chef (Xamarin)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)
- [Soupe chef (SWIFT)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Présentation des raccourcis Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Génération de la voix avec des raccourcis Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Siri les raccourcis sur la face Siri-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Nouveautés de SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Créer une extension d’application astentes (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
