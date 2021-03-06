---
title: Transfert dans Xamarin. iOS
description: Cet article traite de l’utilisation de la fonction de transfert dans une application Xamarin. iOS pour transférer les activités des utilisateurs entre les applications qui s’exécutent sur les autres appareils de l’utilisateur.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 97a4a90b66e2c205ef8e9081e6989bf0f3650b16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032407"
---
# <a name="handoff-in-xamarinios"></a>Transfert dans Xamarin. iOS

_Cet article traite de l’utilisation de la fonction de transfert dans une application Xamarin. iOS pour transférer les activités des utilisateurs entre les applications qui s’exécutent sur les autres appareils de l’utilisateur._

Apple a introduit la remise dans iOS 8 et OS X Yosemite (10,10) pour fournir un mécanisme commun permettant à l’utilisateur de transférer des activités démarrées sur l’un de ses appareils, à un autre appareil exécutant la même application ou une autre application qui prend en charge la même activité.

[![](handoff-images/handoff02.png "An example of performing a Handoff operation")](handoff-images/handoff02.png#lightbox)

Cet article examine rapidement l’activation du partage d’activités dans une application Xamarin. iOS et couvre l’infrastructure de transfert en détail :

## <a name="about-handoff"></a>À propos de la remise

La remise (également appelée continuité) a été introduite par Apple dans iOS 8 et OS X Yosemite (10,10) pour que l’utilisateur démarre une activité sur l’un de ses appareils (iOS ou Mac) et continue cette même activité sur un autre de ses appareils (identifiés par le iClou de l’utilisateur). Compte d).

Le transfert a été étendu dans iOS 9 pour prendre également en charge les nouvelles fonctionnalités de recherche améliorées. Pour plus d’informations, consultez notre documentation sur les [améliorations apportées](~/ios/platform/search/index.md) à la recherche.

Par exemple, l’utilisateur peut démarrer un e-mail sur son iPhone et continuer en toute transparence l’e-mail sur son Mac, avec les mêmes informations de message et le curseur dans le même emplacement que celui dans lequel ils ont été laissés dans iOS.

Toutes vos applications qui partagent le même _ID d’équipe_ sont éligibles à l’utilisation de la remise pour continuer les activités des utilisateurs sur les applications, à condition que ces applications soient fournies via l’App Store iTunes ou signées par un développeur inscrit (pour les applications Mac, Enterprise ou ad hoc).

Les applications basées sur des `NSDocument` ou des `UIDocument` ont automatiquement la prise en charge de la remise intégrée et nécessitent des modifications minimales pour prendre en charge le transfert.

### <a name="continuing-user-activities"></a>Poursuite des activités de l’utilisateur

La classe `NSUserActivity` (ainsi que quelques petites modifications apportées à `UIKit` et `AppKit`) assure la prise en charge de la définition de l’activité d’un utilisateur qui peut potentiellement être poursuivie sur un autre des appareils de l’utilisateur.

Pour qu’une activité soit passée à un autre des appareils de l’utilisateur, elle doit être encapsulée dans une instance `NSUserActivity`, marquée en tant qu' _activité en cours_, elle doit être définie comme une charge utile (les données utilisées pour effectuer la continuation) et l’activité doit ensuite être transmise à Cet appareil.

La remise passe le minimum d’informations pour définir l’activité à continuer, avec des paquets de données plus volumineux synchronisés via iCloud.

Sur l’appareil de réception, l’utilisateur reçoit une notification indiquant qu’une activité est disponible pour la continuation. Si l’utilisateur choisit de poursuivre l’activité sur le nouvel appareil, l’application spécifiée est lancée (si elle n’est pas déjà en cours d’exécution) et la charge utile de la `NSUserActivity` est utilisée pour redémarrer l’activité.

[![](handoff-images/handoffinteractions.png "An overview of Continuing User Activities")](handoff-images/handoffinteractions.png#lightbox)

Seules les applications qui partagent le même ID d’équipe de développeur et répondent à un _type d’activité_ donné sont éligibles pour la continuation. Une application définit les types d’activités qu’elle prend en charge sous la clé `NSUserActivityTypes` de son fichier **info. plist** . À partir de cela, un appareil continu choisit l’application pour effectuer la continuation en fonction de l’ID d’équipe, du type d’activité et éventuellement du titre de l' _activité_.

L’application réceptrice utilise les informations du dictionnaire `UserInfo` de `NSUserActivity`pour configurer son interface utilisateur et restaurer l’état de l’activité donnée afin que la transition semble transparente pour l’utilisateur final.

Si la continuation requiert plus d’informations qu’il n’est possible d’envoyer efficacement via un `NSUserActivity`, l’application qui reprend peut envoyer un appel à l’application d’origine et établir un ou plusieurs flux pour transmettre les données requises. Par exemple, si l’activité a modifié un grand document texte avec plusieurs images, la diffusion en continu est nécessaire pour transférer les informations nécessaires pour continuer l’activité sur l’appareil de réception. Pour plus d’informations, consultez la section [prise en charge des flux de continuation](#supporting-continuation-streams) ci-dessous.

Comme indiqué ci-dessus, `NSDocument` ou les applications basées sur `UIDocument` disposent automatiquement de la prise en charge de la remise intégrée. Pour plus d’informations, consultez la section [prise en charge du transfert dans les applications basées sur des documents](#supporting-handoff-in-document-based-apps) ci-dessous.

### <a name="the-nsuseractivity-class"></a>La classe NSUserActivity

La classe `NSUserActivity` est l’objet principal d’un échange de remise et est utilisée pour encapsuler l’état d’une activité utilisateur qui est disponible pour la continuation. Une application instancie une copie de `NSUserActivity` pour toutes les activités qu’elle prend en charge et souhaite continuer sur un autre appareil. Par exemple, l’éditeur de document crée une activité pour chaque document actuellement ouvert. Toutefois, seul le document de premier plan (affiché dans la fenêtre ou l’onglet du premier plan) est l' _activité en cours_ et est disponible pour la continuation.

Une instance de `NSUserActivity` est identifiée par ses propriétés `ActivityType` et `Title`. La propriété du dictionnaire `UserInfo` est utilisée pour transporter des informations sur l’état de l’activité. Affectez à la propriété `NeedsSave` la valeur `true` si vous souhaitez charger en différé les informations d’état via le délégué de `NSUserActivity`. Utilisez la méthode `AddUserInfoEntries` pour fusionner les nouvelles données à partir d’autres clients dans le dictionnaire `UserInfo` en fonction des besoins afin de conserver l’état de l’activité.

### <a name="the-nsuseractivitydelegate-class"></a>La classe NSUserActivityDelegate

Le `NSUserActivityDelegate` permet de tenir à jour les informations contenues dans le dictionnaire de `UserInfo` d’un `NSUserActivity`et de les synchroniser avec l’état actuel de l’activité. Quand le système a besoin que les informations de l’activité soient mises à jour (par exemple avant la continuation sur un autre périphérique), il appelle la méthode `UserActivityWillSave` du délégué.

Vous devrez implémenter la méthode `UserActivityWillSave` et apporter des modifications à la `NSUserActivity` (par exemple, `UserInfo`, `Title`, etc.) pour vous assurer qu’elle reflète toujours l’état de l’activité en cours. Lorsque le système appelle la méthode `UserActivityWillSave`, l’indicateur `NeedsSave` sera effacé. Si vous modifiez l’une des propriétés de données de l’activité, vous devez définir `NeedsSave` à `true`.

Au lieu d’utiliser la méthode `UserActivityWillSave` présentée ci-dessus, vous pouvez éventuellement avoir `UIKit` ou `AppKit` gérer l’activité de l’utilisateur automatiquement. Pour ce faire, définissez la propriété `UserActivity` de l’objet répondeur et implémentez la méthode `UpdateUserActivityState`. Pour plus d’informations, consultez la section [prise en charge de la remise dans les répondeurs](#supporting-handoff-in-responders) ci-dessous.

### <a name="app-framework-support"></a>Prise en charge de l’infrastructure d’application

Les `UIKit` (iOS) et `AppKit` (OS X) offrent une prise en charge intégrée de la remise des classes de `NSDocument`, de répondeur (`UIResponder`/`NSResponder`) et de `AppDelegate`. Bien que chaque système d’exploitation implémente un transfert légèrement différent, le mécanisme de base et les API sont les mêmes.

#### <a name="user-activities-in-document-based-apps"></a>Activités de l’utilisateur dans les applications basées sur les documents

Les applications iOS et OS X basées sur des documents disposent automatiquement de la prise en charge de la remise intégrée. Pour activer cette prise en charge, vous devez ajouter une clé de `NSUbiquitousDocumentUserActivityType` et une valeur pour chaque entrée `CFBundleDocumentTypes` dans le fichier **info. plist** de l’application.

Si cette clé est présente, `NSDocument` et `UIDocument` créer automatiquement des instances `NSUserActivity` pour les documents basés sur iCloud du type spécifié. Vous devrez fournir un type d’activité pour chaque type de document pris en charge par l’application et plusieurs types de documents peuvent utiliser le même type d’activité. `NSDocument` et `UIDocument` remplissent automatiquement la propriété `UserInfo` de l' `NSUserActivity` avec la valeur de leur propriété `FileURL`.

Sur OS X, le `NSUserActivity` géré par `AppKit` et associé aux répondeurs devient automatiquement l’activité en cours lorsque la fenêtre du document devient la fenêtre principale. Sur iOS, pour les objets `NSUserActivity` gérés par `UIKit`, vous devez appeler explicitement la méthode `BecomeCurrent` ou faire en sorte que la propriété `UserActivity` du document soit définie sur une `UIViewController` lorsque l’application passe au premier plan.

`AppKit` restaure automatiquement toute propriété `UserActivity` créée de cette manière sur OS X. Cela se produit si la méthode `ContinueUserActivity` retourne `false` ou si elle n’est pas implémentée. Dans ce cas, le document est ouvert avec la méthode `OpenDocument` de la `NSDocumentController` et il reçoit alors un appel de méthode de `RestoreUserActivityState`.

Pour plus d’informations, consultez la section [prise en charge du transfert dans les applications basées sur des documents](#supporting-handoff-in-document-based-apps) ci-dessous.

#### <a name="user-activities-and-responders"></a>Activités et répondeurs de l’utilisateur

`UIKit` et `AppKit` peuvent gérer automatiquement une activité utilisateur si vous la définissez en tant que propriété `UserActivity` d’un objet répondeur. Si l’État a été modifié, vous devez définir la propriété `NeedsSave` du `UserActivity` du répondeur sur `true`. Le système enregistre automatiquement les `UserActivity` quand cela est nécessaire, après avoir donné à l’heure du répondeur de mettre à jour l’État en appelant sa méthode `UpdateUserActivityState`.

Si plusieurs répondeurs partagent une seule instance de `NSUserActivity`, ils reçoivent un rappel `UpdateUserActivityState` lorsque le système met à jour l’objet d’activité utilisateur. Le répondeur doit appeler la méthode `AddUserInfoEntries` pour mettre à jour le dictionnaire `UserInfo` de `NSUserActivity`afin de refléter l’état actuel de l’activité à ce stade. Le dictionnaire `UserInfo` est effacé avant chaque `UpdateUserActivityState` appel.

Pour se dissocier d’une activité, un répondeur peut affecter à sa propriété `UserActivity` la valeur `null`. Quand une instance d’application managée `NSUserActivity` n’a plus de répondeurs ou de documents associés, elle est automatiquement invalidée.

Pour plus d’informations, consultez la section [prise en charge de la remise dans les répondeurs](#supporting-handoff-in-responders) ci-dessous.

#### <a name="user-activities-and-the-appdelegate"></a>Activités de l’utilisateur et AppDelegate

Le `AppDelegate` de votre application est son point d’entrée principal lors du traitement d’une continuation de transfert. Lorsque l’utilisateur répond à une notification de remise, l’application appropriée est lancée (si elle n’est pas déjà en cours d’exécution) et la méthode `WillContinueUserActivityWithType` de la `AppDelegate` est appelée. À ce stade, l’application doit informer l’utilisateur que la continuation est en cours de démarrage.

L’instance de `NSUserActivity` est fournie lorsque la méthode `ContinueUserActivity` du `AppDelegate`est appelée. À ce stade, vous devez configurer l’interface utilisateur de l’application et continuer l’activité donnée.

Pour plus d’informations, consultez la section implémentation de la [remise](#implementing-handoff) ci-dessous.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Activation de la remise dans une application Xamarin

En raison des exigences de sécurité imposées par le transfert, une application Xamarin. iOS qui utilise l’infrastructure de transfert doit être correctement configurée dans le portail des développeurs Apple et dans le fichier de projet Xamarin. iOS.

Effectuez ce qui suit :

1. Connectez-vous au [portail des développeurs Apple](https://developer.apple.com).
2. Cliquez sur **certificats, identificateurs & profils**.
3. Si vous ne l’avez pas déjà fait, cliquez sur **identificateurs** et créez un ID pour votre application (par exemple, `com.company.appname`), sinon modifiez votre ID existant.
4. Assurez-vous que le service **icloud** a été vérifié pour l’ID donné :

    [![](handoff-images/provision01.png "Enable the iCloud service for the given ID")](handoff-images/provision01.png#lightbox)
5. Enregistrez les modifications apportées.
6. Cliquez sur **profils d’approvisionnement** > **développement** et créez un nouveau profil de provisionnement de développement pour votre application :

    [![](handoff-images/provision02.png "Create a new development provisioning profile for the app")](handoff-images/provision02.png#lightbox)
7. Téléchargez et installez le nouveau profil de provisionnement ou utilisez Xcode pour télécharger et installer le profil.
8. Modifiez les options de votre projet Xamarin. iOS et assurez-vous d’utiliser le profil de provisionnement que vous venez de créer :

    [![](handoff-images/provision03.png "Select the provisioning profile just created")](handoff-images/provision03.png#lightbox)
9. Modifiez ensuite votre fichier **info. plist** et assurez-vous que vous utilisez l’ID d’application qui a été utilisé pour créer le profil de provisionnement :

    [![](handoff-images/provision04.png "Set App ID")](handoff-images/provision04.png#lightbox)
10. Faites défiler jusqu’à la section **modes d’arrière-plan** et vérifiez les éléments suivants :

    [![](handoff-images/provision05.png "Enable the required background modes")](handoff-images/provision05.png#lightbox)
11. Enregistrez les modifications apportées à tous les fichiers.

Lorsque ces paramètres sont en place, l’application est maintenant prête à accéder aux API de l’infrastructure de transfert. Pour plus d’informations sur l’approvisionnement, consultez nos guides d’approvisionnement et d' [approvisionnement](~/ios/get-started/installation/device-provisioning/index.md) des [appareils](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="implementing-handoff"></a>Implémentation de la remise

Les activités de l’utilisateur peuvent être poursuivies parmi les applications qui sont signées avec le même ID d’équipe de développement et qui prennent en charge le même type d’activité. L’implémentation de la remise dans une application Xamarin. iOS vous oblige à créer un objet d’activité utilisateur (dans `UIKit` ou `AppKit`), à mettre à jour l’état de l’objet pour effectuer le suivi de l’activité et à poursuivre l’activité sur un appareil de réception.

### <a name="identifying-user-activities"></a>Identification des activités de l’utilisateur

La première étape de l’implémentation de la remise consiste à identifier les types d’activités utilisateur que votre application prend en charge et à déterminer quelles sont les activités les plus adaptées à la continuation sur un autre appareil. Par exemple : une application ToDo peut prendre en charge la modification d’éléments comme un seul _type d’activité utilisateur_et prendre en charge l’exploration de la liste des éléments disponibles en tant qu’autre.

Une application peut créer autant de types d’activité utilisateur que nécessaire, l’un pour les fonctions fournies par l’application. Pour chaque type d’activité utilisateur, l’application doit suivre le moment auquel une activité du type commence et se termine, et doit maintenir à jour les informations d’État pour poursuivre cette tâche sur un autre appareil.

Les activités de l’utilisateur peuvent être poursuivies sur n’importe quelle application signée avec le même ID d’équipe sans mappage un-à-un entre les applications émettrices et réceptrices. Par exemple, une application donnée peut créer quatre types d’activités différents, qui sont consommés par différentes applications individuelles sur un autre appareil. Il s’agit d’une occurrence courante entre une version Mac de l’application (qui peut avoir de nombreuses fonctionnalités et fonctions) et des applications iOS, où chaque application est plus petite et se focalise sur une tâche spécifique.

### <a name="creating-activity-type-identifiers"></a>Création d’identificateurs de type d’activité

L' _identificateur de type d’activité_ est une chaîne abrégée ajoutée au tableau `NSUserActivityTypes` du fichier **info. plist** de l’application, utilisée pour identifier de manière unique un type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité que l’application prend en charge. Apple suggère d’utiliser une notation de style DNS inversé pour l’identificateur de type d’activité afin d’éviter les collisions. Par exemple : `com.company-name.appname.activity` pour des activités spécifiques basées sur des applications ou des `com.company-name.activity` pour des activités pouvant s’exécuter sur plusieurs applications.

L’identificateur de type d’activité est utilisé lors de la création d’une instance de `NSUserActivity` pour identifier le type d’activité. Lorsqu’une activité est poursuivie sur un autre appareil, le type d’activité (ainsi que l’ID d’équipe de l’application) détermine l’application à lancer pour continuer l’activité.

Par exemple, nous allons créer un exemple d’application appelé **MonkeyBrowser** ([Téléchargez ici](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)). Cette application présente quatre onglets, chacun avec une URL différente ouverte dans une vue de navigateur Web. L’utilisateur pourra continuer à accéder à tous les onglets d’un autre appareil iOS exécutant l’application.

Pour créer les identificateurs de type d’activité requis pour prendre en charge ce comportement, modifiez le fichier **info. plist** et basculez en mode **source** . Ajoutez une clé de `NSUserActivityTypes` et créez les identificateurs suivants :

[![](handoff-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](handoff-images/type01.png#lightbox)

Nous avons créé quatre nouveaux identificateurs de type d’activité, un pour chacun des onglets de l’exemple d’application **MonkeyBrowser** . Lorsque vous créez vos propres applications, remplacez le contenu du tableau `NSUserActivityTypes` par les identificateurs de type d’activité spécifiques aux activités prises en charge par votre application.

### <a name="tracking-user-activity-changes"></a>Suivi des modifications de l’activité des utilisateurs

Lorsque nous créons une nouvelle instance de la classe `NSUserActivity`, nous spécifions une instance `NSUserActivityDelegate` pour suivre les modifications apportées à l’état de l’activité. Par exemple, le code suivant peut être utilisé pour effectuer le suivi des modifications d’État :

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

La méthode `UserActivityReceivedData` est appelée lorsqu’un flux de continuation a reçu des données d’un appareil émetteur. Pour plus d’informations, consultez la section [prise en charge des flux de continuation](#supporting-continuation-streams) ci-dessous.

La méthode `UserActivityWasContinued` est appelée lorsqu’un autre appareil a pris le contrôle d’une activité à partir de l’appareil actuel. Selon le type d’activité, par exemple l’ajout d’un nouvel élément à une liste de tâches, l’application peut nécessiter l’abandon de l’activité sur l’appareil émetteur.

La méthode `UserActivityWillSave` est appelée avant que les modifications apportées à l’activité soient enregistrées et synchronisées sur les appareils disponibles localement. Vous pouvez utiliser cette méthode pour apporter des modifications de dernière minute à la propriété `UserInfo` de l’instance `NSUserActivity` avant de l’envoyer.

### <a name="creating-a-nsuseractivity-instance"></a>Création d’une instance NSUserActivity

Chaque activité que votre application souhaite donner à la possibilité de continuer sur un autre appareil doit être encapsulée dans une instance de `NSUserActivity`. L’application peut créer autant d’activités que nécessaire et la nature de ces activités dépend de la fonctionnalité et des fonctionnalités de l’application en question. Par exemple, une application de messagerie peut créer une activité pour la création d’un nouveau message, et une autre pour la lecture d’un message.

Pour notre exemple d’application, un nouveau `NSUserActivity` est créé chaque fois que l’utilisateur entre une nouvelle URL dans l’un des affichages du navigateur Web avec onglets. Le code suivant stocke l’état d’un onglet donné :

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Il crée un `NSUserActivity` à l’aide de l’un des types d’activité utilisateur créés ci-dessus et fournit un titre explicite pour l’activité. Il se connecte à une instance du `NSUserActivityDelegate` créé ci-dessus pour surveiller les modifications d’État et informe iOS que cette activité utilisateur est l’activité en cours.

### <a name="populating-the-userinfo-dictionary"></a>Remplissage du dictionnaire UserInfo

Comme nous l’avons vu ci-dessus, la propriété `UserInfo` de la classe `NSUserActivity` est une `NSDictionary` de paires clé-valeur utilisées pour définir l’état d’une activité donnée. Les valeurs stockées dans `UserInfo` doivent être de l’un des types suivants : `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`ou `NSURL`. `NSURL` valeurs de données qui pointent vers des documents iCloud sont automatiquement ajustées afin qu’elles pointent vers les mêmes documents sur un appareil de réception.

Dans l’exemple ci-dessus, nous avons créé un objet `NSMutableDictionary` et l’avons renseigné avec une clé unique fournissant l’URL actuellement affichée par l’utilisateur sur l’onglet donné. La méthode `AddUserInfoEntries` de l’activité utilisateur a été utilisée pour mettre à jour l’activité avec les données qui seront utilisées pour restaurer l’activité sur l’appareil de réception :

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple suggère de conserver les informations envoyées au minimum le plus simple pour s’assurer que l’activité est envoyée en temps utile au périphérique récepteur. Si des informations plus importantes sont nécessaires, comme une image attachée à un document doit être envoyée, vous devez utiliser des flux de continuation. Pour plus d’informations, consultez la section [prise en charge des flux de continuation](#supporting-continuation-streams) ci-dessous.

### <a name="continuing-an-activity"></a>Poursuite d’une activité

Le transfert informe automatiquement les appareils iOS et OS X locaux qui se trouvent à proximité de l’appareil d’origine et qui sont connectés au même compte iCloud, de la disponibilité des activités utilisateur continues. Si l’utilisateur choisit de poursuivre une activité sur un nouvel appareil, le système lance l’application appropriée (en fonction de l’ID d’équipe et du type d’activité) et les informations sur son `AppDelegate` que la continuation doit se produire.

Tout d’abord, la méthode `WillContinueUserActivityWithType` est appelée afin que l’application puisse informer l’utilisateur que la continuation est sur le début. Nous utilisons le code suivant dans le fichier **AppDelegate.cs** de notre exemple d’application pour gérer une continuation à partir de :

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

Dans l’exemple ci-dessus, chaque contrôleur d’affichage s’inscrit auprès du `AppDelegate` et possède une méthode de `PreparingToHandoff` publique qui affiche un indicateur d’activité et un message indiquant à l’utilisateur que l’activité est sur le paragraphe de l’appareil actif. Exemple :

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

La `ContinueUserActivity` du `AppDelegate` sera appelée pour poursuivre l’activité donnée. Là encore, à partir de notre exemple d’application :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

La méthode de `PerformHandoff` publique de chaque contrôleur d’affichage préforme réellement le transfert et restaure l’activité sur l’appareil actuel. Dans le cas de l’exemple, il affiche la même URL dans un onglet donné que l’utilisateur parcourt sur un autre appareil. Exemple :

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

La méthode `ContinueUserActivity` comprend un `UIApplicationRestorationHandler` que vous pouvez appeler pour la reprise de l’activité basée sur un document ou un répondeur. Vous devez passer un `NSArray` ou des objets pouvant être restaurés au gestionnaire de restauration lorsqu’il est appelé. Exemple :

```csharp
completionHandler (new NSObject[]{Tab4});
```

Pour chaque objet passé, sa méthode `RestoreUserActivityState` est appelée. Chaque objet peut ensuite utiliser les données dans le dictionnaire `UserInfo` pour restaurer son propre État. Exemple :

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Pour les applications basées sur les documents, si vous n’implémentez pas la méthode `ContinueUserActivity` ou si elle retourne `false`, `UIKit` ou `AppKit` pouvez reprendre automatiquement l’activité. Pour plus d’informations, consultez la section [prise en charge du transfert dans les applications basées sur des documents](#supporting-handoff-in-document-based-apps) ci-dessous.

### <a name="failing-handoff-gracefully"></a>Échec du transfert en douceur

Étant donné que le transfert s’appuie sur la transmission d’informations entre un regroupement et des appareils iOS et OS X faiblement connectés, le processus de transfert peut parfois échouer. Vous devez concevoir votre application de manière à ce qu’elle gère ces échecs de manière appropriée et informer l’utilisateur des situations qui se produisent.

En cas d’échec, la méthode `DidFailToContinueUserActivitiy` du `AppDelegate` sera appelée. Exemple :

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Vous devez utiliser le `NSError` fourni pour fournir à l’utilisateur des informations sur la défaillance.

## <a name="native-app-to-web-browser-handoff"></a>Remise de l’application native vers le navigateur Web

Un utilisateur peut vouloir continuer une activité sans qu’une application native appropriée ne soit installée sur l’appareil de votre choix. Dans certains cas, une interface basée sur le Web peut fournir la fonctionnalité requise et l’activité peut toujours être poursuivie. Par exemple, le compte de messagerie de l’utilisateur peut fournir une interface utilisateur de base Web pour la composition et la lecture des messages.

Si l’application native d’origine connaît l’URL de l’interface Web (et la syntaxe requise pour identifier l’élément donné en cours de continuation), elle peut encoder ces informations dans la propriété `WebpageURL` de l’instance `NSUserActivity`. Si l’appareil de réception n’a pas d’application native appropriée installée pour gérer la continuation, l’interface Web fournie peut être appelée.

## <a name="web-browser-to-native-app-handoff"></a>Transfert du navigateur Web vers l’application native

Si l’utilisateur utilisait une interface Web sur l’appareil d’origine, et qu’une application native sur l’appareil de réception revendique la partie domaine de la propriété `WebpageURL`, le système utilisera cette application pour gérer la continuation. Le nouvel appareil reçoit une instance de `NSUserActivity` qui marque le type d’activité comme `BrowsingWeb` et la `WebpageURL` contiendra l’URL visitée par l’utilisateur, le dictionnaire `UserInfo` sera vide.

Pour qu’une application participe à ce type de remise, elle doit revendiquer le domaine dans un `com.apple.developer.associated-domains` droit avec le format `<service>:<fully qualified domain name>` (par exemple : `activity continuation:company.com`).

Si le domaine spécifié correspond à la valeur d’une propriété de `WebpageURL`, le transfert télécharge une liste d’ID d’application approuvés à partir du site Web de ce domaine. Le site Web doit fournir une liste d’ID approuvés dans un fichier JSON signé nommé **Apple-App-site-Association** (par exemple, `https://company.com/apple-app-site-association`).

Ce fichier JSON contient un dictionnaire qui spécifie une liste d’ID d’application sous la forme `<team identifier>.<bundle identifier>`. Exemple :

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Pour signer le fichier JSON (afin qu’il dispose de la `Content-Type` correcte de `application/pkcs7-mime`), utilisez l’application **Terminal** et une commande de `openssl` avec un certificat et une clé émis par une autorité de certification approuvée par iOS (voir [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) pour obtenir une liste). Exemple :

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

La commande `openssl` génère un fichier JSON signé que vous placez sur votre site Web à l’URL **Apple-App-site-Association** . Exemple :

```csharp
https://example.com/apple-app-site-association.
```

L’application recevra toute activité dont le `WebpageURL` domaine est dans son `com.apple.developer.associated-domains` droit. Seuls les protocoles `http` et `https` sont pris en charge, tout autre protocole lève une exception.

## <a name="supporting-handoff-in-document-based-apps"></a>Prise en charge de la remise dans les applications basées sur les documents

Comme indiqué ci-dessus, sur iOS et OS X, les applications basées sur les documents prendront en charge automatiquement le transfert de documents basés sur iCloud si le fichier **info. plist** de l’application contient une clé de `CFBundleDocumentTypes` `NSUbiquitousDocumentUserActivityType`. Exemple :

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

Dans cet exemple, la chaîne est un indicateur d’application DNS inversé avec le nom de l’activité ajoutée. Dans ce cas, les entrées de type d’activité n’ont pas besoin d’être répétées dans le tableau `NSUserActivityTypes` du fichier **info. plist** .

L’objet d’activité utilisateur créé automatiquement (disponible par le biais de la propriété `UserActivity` du document) peut être référencé par d’autres objets dans l’application et utilisé pour restaurer l’état lors de la continuation. Par exemple, pour suivre la sélection des éléments et la position du document. Vous devez définir ces activités `NeedsSave` propriété sur `true` chaque fois que l’état change et mettre à jour le dictionnaire `UserInfo` dans la méthode `UpdateUserActivityState`.

La propriété `UserActivity` peut être utilisée à partir de n’importe quel thread et se conforme au protocole KVO (key-value-observation), afin de pouvoir être utilisée pour maintenir la synchronisation d’un document lors de son déplacement et de son sortie de iCloud. La propriété `UserActivity` est invalidée lorsque le document est fermé.

Pour plus d’informations, consultez [prise en charge de l’activité des utilisateurs d’Apple dans la documentation sur les applications basées sur les documents](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) .

## <a name="supporting-handoff-in-responders"></a>Prise en charge de la remise dans les répondeurs

Vous pouvez associer des répondeurs (hérités de `UIResponder` sur iOS ou `NSResponder` sur OS X) à des activités en définissant leurs propriétés `UserActivity`. Le système enregistre automatiquement la propriété `UserActivity` aux moments appropriés, en appelant la méthode `UpdateUserActivityState` du répondeur pour ajouter les données actuelles à l’objet d’activité utilisateur à l’aide de la méthode `AddUserInfoEntriesFromDictionary`.

## <a name="supporting-continuation-streams"></a>Prise en charge des flux de continuation

Dans certains cas, la quantité d’informations requises pour continuer une activité ne peut pas être transférée efficacement par la charge utile de remise initiale. Dans ces situations, l’application réceptrice peut établir un ou plusieurs flux entre lui-même et l’application d’origine pour transférer les données.

L’application d’origine définit la propriété `SupportsContinuationStreams` de l’instance `NSUserActivity` sur `true`. Exemple :

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

L’application réceptrice peut ensuite appeler la méthode `GetContinuationStreams` de la `NSUserActivity` dans son `AppDelegate` pour établir le flux. Exemple :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Sur l’appareil d’origine, le délégué d’activité de l’utilisateur reçoit les flux en appelant sa méthode `DidReceiveInputStream` pour fournir les données requises pour continuer l’activité de l’utilisateur sur l’appareil de reprise.

Vous allez utiliser un `NSInputStream` pour fournir un accès en lecture seule aux données de flux, et un `NSOutputStream` fournir un accès en écriture seule. Les flux doivent être utilisés dans un mode de requête et de réponse, où l’application réceptrice demande plus de données et l’application d’origine la fournit. Ainsi, les données écrites dans le flux de sortie sur l’appareil d’origine sont lues à partir du flux d’entrée sur le périphérique continu, et vice versa.

Même dans les situations où un flux continu est requis, il doit y avoir une communication minimale entre les deux applications.

Pour plus d’informations, consultez la documentation relative [à l’utilisation de flux continus](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) Apple.

## <a name="handoff-best-practices"></a>Bonnes pratiques de remise

La réussite de l’implémentation de la continuation transparente d’une activité utilisateur via le transfert requiert une conception minutieuse en raison de l’ensemble des différents composants impliqués. Apple suggère d’adopter les meilleures pratiques suivantes pour vos applications pour lesquelles le transfert est activé :

- Concevez vos activités utilisateur pour exiger la charge utile la plus petite possible pour associer l’état de l’activité à la poursuite. Plus la charge utile est importante, plus le démarrage de la continuation est long.
- Si vous devez transférer de grandes quantités de données pour réussir la continuation, prenez en compte les coûts liés à la configuration et à la surcharge du réseau.
- Il est courant pour une application Mac de grande taille de créer des activités d’utilisateur qui sont gérées par plusieurs applications plus petites et spécifiques aux tâches sur des appareils iOS. Les différentes versions de l’application et du système d’exploitation doivent être conçues pour fonctionner correctement ensemble ou échouer correctement.
- Lorsque vous spécifiez vos types d’activités, utilisez la notation DNS inversé pour éviter les collisions. Si une activité est spécifique à une application donnée, son nom doit être inclus dans la définition de type (par exemple `com.myCompany.myEditor.editing`). Si l’activité peut fonctionner sur plusieurs applications, supprimez le nom de l’application de la définition (par exemple `com.myCompany.editing`).
- Si votre application doit mettre à jour l’état d’une activité utilisateur (`NSUserActivity`), affectez à la propriété `NeedsSave` la valeur `true`. À des moments opportuns, le transfert appellera la méthode `UserActivityWillSave` du délégué afin que vous puissiez mettre à jour le dictionnaire `UserInfo` en fonction des besoins.
- Étant donné que le processus de remise peut ne pas s’initialiser instantanément sur l’appareil de réception, vous devez implémenter le `WillContinueUserActivity` du `AppDelegate`et informer l’utilisateur qu’une continuation est sur le le début.

## <a name="example-handoff-app"></a>Exemple d’application de remise

En guise d’exemple d’utilisation de la remise dans une application Xamarin. iOS, nous avons inclus l’exemple d’application [**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser) avec ce guide. L’application comporte quatre onglets que l’utilisateur peut utiliser pour naviguer sur le Web, chacun avec un type d’activité donné : météo, favori, pause café et travail.

Sur n’importe quel onglet, lorsque l’utilisateur entre une nouvelle URL et appuie sur le bouton **OK** , un nouveau `NSUserActivity` est créé pour cet onglet qui contient l’URL actuellement visitée par l’utilisateur :

[![](handoff-images/handoff01.png "Example Handoff App")](handoff-images/handoff01.png#lightbox)

Si l’application **MonkeyBrowser** est installée sur un autre des appareils de l’utilisateur, est connectée à iCloud à l’aide du même compte d’utilisateur, se trouve sur le même réseau et à proximité de l’appareil ci-dessus, l’activité de remise s’affiche sur l’écran d’accueil (dans la partie inférieure angle gauche) :

[![](handoff-images/handoff02.png "The Handoff Activity displayed on the home screen in the lower left hand corner")](handoff-images/handoff02.png#lightbox)

Si l’utilisateur fait glisser vers le haut sur l’icône de remise, l’application est lancée et l’activité utilisateur spécifiée dans le `NSUserActivity` se poursuit sur le nouvel appareil :

[![](handoff-images/handoff03.png "The User Activity continued on the new device")](handoff-images/handoff03.png#lightbox)

Lorsque l’activité de l’utilisateur a été correctement envoyée à un autre appareil Apple, le `NSUserActivity` de l’appareil émetteur reçoit un appel à la méthode `UserActivityWasContinued` sur son `NSUserActivityDelegate` pour lui indiquer que l’activité de l’utilisateur a été transférée avec succès vers un autre appareil.

## <a name="summary"></a>Récapitulatif

Cet article a donné une introduction à l’infrastructure de transfert utilisée pour poursuivre une activité utilisateur entre plusieurs des appareils Apple de l’utilisateur. Ensuite, il a montré comment activer et implémenter la remise dans une application Xamarin. iOS. Enfin, il a abordé les différents types de continuation de transfert disponibles et les meilleures pratiques de remise.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Exemple MonkeyBrowser](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Instructions relatives à l’interface utilisateur HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Référence du Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
