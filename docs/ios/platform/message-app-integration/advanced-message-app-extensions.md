---
title: Extensions des applications de message avancées dans Xamarin. iOS
description: Cet article présente des techniques avancées pour l’utilisation des extensions d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application messages et présente de nouvelles fonctionnalités à l’utilisateur.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: fb721e36a6b66b90e9660a1c7d5db9e5124e8715
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031735"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Extensions des applications de message avancées dans Xamarin. iOS

_Cet article présente des techniques avancées pour l’utilisation des extensions d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application messages et présente de nouvelles fonctionnalités à l’utilisateur._

Nouveauté d’iOS 10, une extension d’application de message s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs.

## <a name="about-message-app-extensions"></a>À propos des extensions d’application de message

Comme indiqué ci-dessus, une extension d’application de message s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs. Deux types d’extension d’application de message sont disponibles :

- **Vignettes-packs** : contient une collection d’autocollants que l’utilisateur peut ajouter à un message. Les packs d’autocollants peuvent être créés sans écrire de code.
- **application IMessage** : peut présenter une interface utilisateur personnalisée dans l’application messages pour sélectionner des autocollants, entrer du texte, y compris des fichiers multimédias (avec conversions de type facultatives) et créer, modifier et envoyer des messages d’interaction.

Les extensions de message Apps fournissent trois types de contenu principaux :

- **Messages interactifs** : type de contenu de message personnalisé généré par une application, lorsque l’utilisateur appuie sur le message, l’application est lancée au premier plan.
- **Autocollants** : images générées par l’application qui peuvent être incluses dans les messages envoyés entre les utilisateurs. Pour obtenir un exemple d’implémentation d’une application de Pack d’autocollants, consultez notre exemple d’application [Ice CREME Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) .
- **Autre contenu pris en charge** : l’application peut fournir du contenu tel que des photos, des vidéos, du texte ou des liens du type qui a toujours été pris en charge par l’application messages.

Nouveauté d’iOS 10, l’application message intègre désormais son propre App Store dédié et intégré. Toutes les applications qui incluent des extensions de message apps sont affichées et promues dans ce magasin. Le tiroir de l’application nouveaux messages affiche toutes les applications qui ont été téléchargées à partir de l’App Store pour fournir un accès rapide aux utilisateurs.

Également nouveau dans iOS 10, Apple a ajouté l’attribution d’application inline qui permet à l’utilisateur de découvrir facilement une application. Par exemple, si un utilisateur envoie du contenu à un autre à partir d’une application que le 2e utilisateur n’a pas installée (comme un autocollant par exemple), le nom de l’application émettrice est listé sous le contenu de l’historique des messages. Si l’utilisateur appuie sur le nom de l’application, le message Store de l’application s’ouvre et l’application est sélectionnée dans le Store.

Les extensions de message apps sont similaires aux applications iOS existantes que le développeur est familiarisé à créer et qui auront accès à tous les frameworks et fonctionnalités standard d’une application iOS standard. Exemple :

- Ils ont accès à l’achat dans l’application.
- Ils ont accès à Apple Pay.
- Ils ont accès au matériel de l’appareil, tel que l’appareil photo.

Les extensions de message apps sont uniquement prises en charge sur iOS 10. Toutefois, le contenu envoyé par ces extensions est affichable sur les appareils Watchos et macOS. La nouvelle _page récents_ ajoutée à Watchos 3 affiche les autocollants récents qui ont été envoyés à partir du téléphone, y compris ceux des extensions de message Apps, et autorise l’utilisateur à envoyer ces autocollants à partir de la montre.

## <a name="about-interactive-messages"></a>À propos des messages interactifs

Les messages interactifs présentent une bulle de message personnalisée et sont fournis par une extension d’application de message. Ils permettent à l’utilisateur de créer un contenu de message interactif, de l’insérer dans le champ d’entrée du message et de l’envoyer.

[![](advanced-message-app-extensions-images/interactive01.png "Creating Interactive Message Content")](advanced-message-app-extensions-images/interactive01.png#lightbox)

L’utilisateur récepteur peut répondre à un message interactif en appuyant sur sa bulle de message dans l’historique des messages pour charger l’extension de l’application de message qui l’a créé. L’extension est lancée en mode plein écran et permet à l’utilisateur de composer une réponse et de la renvoyer à l’utilisateur d’origine.

[![](advanced-message-app-extensions-images/interactive02.png "The Extension launched full-screen")](advanced-message-app-extensions-images/interactive02.png#lightbox)

Les rubriques suivantes sont traitées en détail ci-dessous :

- Vue d’ensemble de l’API messages
- Cycle de vie de l’extension
- Composition d’un message
- Envoi d’un message

## <a name="messages-api-overview"></a>Vue d’ensemble de l’API messages

Lorsqu’elle est appelée par l’utilisateur, une extension d’application de message s’affiche au bas de l’historique des messages en mode d’affichage compact :

[![](advanced-message-app-extensions-images/interactive03.png "Messages API Overview")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. L’objet `MSMessageAppViewController` dans l’extension de l’application de message est la classe principale qui est appelée lorsque la vue de l’extension est affichée à l’utilisateur.
2. La conversation est présentée à l’utilisateur sous la forme d’une instance d’objet `MSConversation`.
3. La classe `MSMessage` représente une bulle de message donnée dans la conversation.
4. `MSSession` contrôle la manière dont un message est envoyé.
5. `MSMessageTemplateLayout` contrôle le mode d’affichage du message

## <a name="the-extension-lifecycle"></a>Cycle de vie de l’extension

Jetez un coup d’œil au processus d’activation d’une extension d’application de message :

[![](advanced-message-app-extensions-images/interactive04.png "The process of a Message App Extension becoming active")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Quand une extension est lancée (par exemple à partir du tiroir d’application), l’application de message lance un processus.
2. La méthode `DidBecomeActive` est appelée et reçoit une `MSConversation` qui représente la conversation dans laquelle l’extension de l’application de message s’exécute.
3. Étant donné que l’extension est basée sur une `UIViewController` `ViewWillAppear` et `ViewDidAppear` sont appelées.

Ensuite, examinez le processus d’une extension d’application de message qui devient désactivée :

[![](advanced-message-app-extensions-images/interactive05.png "The process of a Message App Extension becoming deactivated")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Lorsque l’extension de l’application de message est désactivée, la méthode `ViewWillDisappear` est appelée en premier.
2. La méthode `ViewDidDisappear` sera alors appelée.
3. La méthode `WillResignActive` est appelée et reçoit une `MSConversation` qui représente la conversation dans laquelle l’extension de l’application de message s’exécute. À ce stade, la connexion entre l’application messages et l’extension est sur le point d’être libérée.
4. À un moment ultérieur, le processus est terminé par l’application messages.

Étant donné qu’une extension est un processus de courte durée, elle est terminée de manière agressive par le système pour économiser le traitement et la puissance de la batterie. Le développeur doit garder cela à l’esprit lors de la conception et de l’implémentation d’une extension d’application de message.

## <a name="composing-a-message"></a>Composition d’un message

Une fois que l’extension de l’application de message s’exécute dans un processus et qu’elle a affiché son interface utilisateur, le code suivant peut être utilisé pour composer un nouveau message :

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Ce code crée un nouveau `MSMessage` et définit plusieurs propriétés (telles que `Url`). Si le message ne peut être créé que sur iOS, il peut être envoyé à la fois à iOS et à macOS.

Si l’utilisateur clique sur la bulle de message dans la conversation sur macOS, le Mac essaiera d’ouvrir l’adresse spécifiée dans l’URL dans le navigateur Web. Par conséquent, le site Web du développeur doit pouvoir afficher une représentation du message dans le navigateur Web sur des ordinateurs macOS.

La propriété `AccessibilityLabel` est utilisée par les lecteurs d’écran pour lire la transcription de la conversation à l’utilisateur. La propriété `Layout` spécifie la manière dont le message doit être affiché. actuellement, seul le `MSMessageTemplateLayout` est pris en charge et ressemble à ceci :

[![](advanced-message-app-extensions-images/interactive06.png "The MSMessageTemplateLayout template")](advanced-message-app-extensions-images/interactive06.png#lightbox)

La propriété `Image` du `MSMessageTemplateLayout` fournit du contenu pour le corps principal du MessageBubble à l’écran. La propriété `MediaFileUrl` fournit également du contenu pour le corps de la bulle de message, mais autorise le contenu qui n’est pas pris en charge par `UIImage` (tel qu’un fichier vidéo qui s’exécuterait en boucle en arrière-plan). Si les propriétés `Image` et `MediaFileUrl` sont fournies, la propriété `Image` est prioritaire. Le `MediaFileUrl` prend en charge les formats de média PNG, JPEG, GIF et vidéo (dans n’importe quel format pouvant être lu par Media Player Framework).

La taille de média recommandée est de 300 x 300 pixels à la résolution 3x. Les ressources légèrement plus grandes et plus petites sont également acceptées et Apple suggère des tests avec des tailles différentes pour obtenir les meilleurs résultats. L’application de message est interrompue-échantillonne et met à l’échelle ce média en fonction des besoins.

Lorsque les ressources sont envoyées au récepteur, tout support attaché est automatiquement transcodé par l’application messages pour les optimiser à partir du transfert sur les réseaux. Pour cette raison, Apple déconseille d’inclure du texte dans le média joint au message, car le support est mis à l’échelle et compressé en vue de sa transmission, ce qui peut rendre le texte illisible.

Les propriétés `ImageTitle` et `ImageSubtitle` fournissent une description du support présenté dans la bulle de message. Ces propriétés sont envoyées en tant que texte à l’appareil de réception, où elles seront rendues dans le coin inférieur gauche de l’image.

Les propriétés `Caption`, `SubCaption`, `TrailingCaption` et `TrailingSubcaption` décrivent plus en détail l’image et sont rendues dans une section sous l’image. La définition de toutes ces propriétés sur `null` créera une bulle de message sans la zone de légende :

[![](advanced-message-app-extensions-images/interactive07.png "A Message Bubble without the Caption Area")](advanced-message-app-extensions-images/interactive07.png#lightbox)

La dernière chose à noter est que l’application messages dessinera l’icône de l’extension d’application de message dans le coin supérieur gauche de la bulle de message.

## <a name="sending-a-message"></a>Envoi d’un message

Une fois qu’une `MSMessage` a été composée, le code suivant peut être utilisé pour l’envoyer :

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

La propriété `ActiveConversation` du `MSMessagesAppViewController` contiendra la conversation en cours dans laquelle l’extension de l’application de message a été lancée.

Appelez la `InsertMessage` du `MSConversation` pour inclure le message dans la conversation et gérer les erreurs qui peuvent survenir. Si le message est correctement inclus, la bulle de message s’affiche dans le champ d’entrée.

En outre, l’extension peut envoyer différents types de données à la conversation, par exemple :

- **Texte** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Pièces jointes** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- Les **autocollants** - `ActiveConversation.InsertSticker (sticker, (obj) => {...});` où `sticker` est `MSSticker`.

Une fois que le nouveau contenu se trouve dans le champ d’entrée, l’utilisateur est en mesure d’envoyer le message en appuyant sur le bouton bleu **Envoyer** (de la même façon qu’un message standard). Il n’existe aucun moyen pour l’extension d’application de message d’envoyer automatiquement du contenu, ce processus est entièrement sous le contrôle de l’utilisateur.

## <a name="handling-the-compact-and-expanded-modes"></a>Gestion des modes compact et développé

Une extension d’application de message peut être affichée dans l’un des deux modes d’affichage suivants :

[![](advanced-message-app-extensions-images/interactive08.png "A Message App Extension displayed in two different view modes: Compact & Expanded")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** : il s’agit du mode par défaut dans lequel l’extension de l’application de message occupe les 25% inférieurs de la vue du message. En mode compact, l’application n’a pas accès au clavier, au défilement horizontal ou aux détecteurs de mouvement de balayage. L’application a accès au champ d’entrée et les appels à `InsertMessage` s’affichent instantanément à l’utilisateur.
- **Développé** : l’extension de l’application de message remplit l’intégralité de l’affichage des messages. Elle n’a pas accès au champ d’entrée, mais a accès au clavier, au défilement horizontal et aux détecteurs de mouvement de balayage.

Une extension d’application de message peut passer d’un mode à l’autre, soit par programmation, soit manuellement par l’utilisateur à tout moment et doit réagir instantanément à toute modification apportée au mode d’affichage.

Jetez un coup d’œil à l’exemple suivant pour gérer le basculement entre les deux modes d’affichage. Deux contrôleurs d’affichage différents seront nécessaires pour chaque État. Le `StickerBrowserViewController` gère l’affichage **compact** et le `AddStickerViewController` gérera la vue **développée** :

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

La méthode `DidTransition` est remplacée pour gérer le basculement entre les deux modes :

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

Si vous le souhaitez, l’application peut utiliser la méthode `WillTransition` pour gérer la modification du mode d’affichage avant qu’elle ne soit présentée à l’utilisateur (comme c’est le cas dans l’exemple Icecream Builder ci-dessus). Pour plus d’informations, consultez notre documentation sur la [Personnalisation des autocollants](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) .

## <a name="replying-to-a-message"></a>Réponse à un message

Il existe deux cas dans lesquels une extension d’application de message doit être gérée lors de la réponse à un message :

[![](advanced-message-app-extensions-images/interactive09.png "The Message App Extension in the Inactive and Active modes")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- L' **extension est inactive** : il existe une des bulles de messages de l’extension d’application de message dans la transcription de message sur laquelle l’utilisateur peut cliquer pour activer les extensions et poursuivre la conversation interactive.
- L' **extension est active** : l’utilisateur peut appuyer sur la bulle de message de l’extension de l’application de message dans la transcription du message pour passer en mode d’affichage développé et poursuivre le processus interactif là où il s’était arrêté.

### <a name="the-extension-is-inactive"></a>L’extension est inactive

Quand une bulle de message est frappée par l’utilisateur dans la transcription du message et que l’extension de l’application de message est inactive, le processus suivant se produit :

[![](advanced-message-app-extensions-images/interactive10.png "Handling an inactive Message Bubble")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. L’utilisateur appuie sur la bulle de message de l’extension.
2. Lorsqu’une extension est lancée, l’application de message lance un processus.
3. La méthode `DidBecomeActive` est appelée et reçoit une `MSConversation` qui représente la conversation dans laquelle l’extension de l’application de message s’exécute.
4. Étant donné que l’extension est basée sur une `UIViewController` `ViewWillAppear` et `ViewDidAppear` sont appelées.

Une fois le processus terminé, l’extension de l’application de message est présentée en mode d’affichage développé.

### <a name="the-extension-is-active"></a>L’extension est active

Quand une bulle de message est appuyée par l’utilisateur dans la transcription du message et que l’extension de l’application de message est active, le processus suivant se produit :

[![](advanced-message-app-extensions-images/interactive11.png "Handling an active Message Bubble")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. L’utilisateur appuie sur la bulle de message de l’extension.
2. Étant donné que l’extension de l’application de message est déjà active, la méthode `WillTransition` de la `MSMessagesAppViewController` est appelée pour gérer le basculement du Compact vers le mode d’affichage développé.
3. La méthode `DidSelectMessage` de la `MSMessagesAppViewController` est appelée et reçoit le `MSMessage` et `MSConversation` auquel la bulle de message appartient.
4. La méthode `DidTransition` de la `MSMessagesAppViewController` est appelée pour gérer le basculement du Compact vers le mode d’affichage développé.

Là encore, lorsque le processus est terminé, l’extension de l’application de message est présentée en mode d’affichage développé.

### <a name="accessing-the-selected-message"></a>Accès au message sélectionné

Dans les deux cas, lorsque l’utilisateur appuie sur une bulle de message appartenant à l’extension de l’application de message, il doit accéder à la `MSMessage` qui a été exploitée à l’aide de la propriété `SelectedMessage` du `MSConversation`.

Exemple :

```csharp
using System;
using Foundation;
using Messages;
using UIKit;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

Le message sélectionné doit s’afficher dans l’interface utilisateur de l’extension d’application de message et l’utilisateur doit être autorisé à composer une réponse.

## <a name="removing-partially-completed-messages"></a>Suppression des messages partiellement remplis

Au cours du processus d’envoi des différentes étapes d’une conversation interactive entre les deux utilisateurs de la conversation, les bulles de message partiellement terminées peuvent commencer à encombrer la transcription du message :

[![](advanced-message-app-extensions-images/interactive12.png "The partially completed Message Bubbles can cluttering the Message Transcript")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Au lieu de cela, l’extension de l’application de message doit réduire les bulles de messages précédentes dans un commentaire succinct dans la transcription du message :

[![](advanced-message-app-extensions-images/interactive13.png "Collapsing the previous Message Bubbles in the Message Transcript")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Cela est géré à l’aide d’un `MSSession` pour réduire toutes les étapes existantes. La méthode `DidSelectMessage` de la classe `MSMessagesAppViewController` peut donc être modifiée pour ressembler à ce qui suit :

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Si le message sélectionné a déjà un `MSSession`de sortie, il est utilisé sinon une nouvelle `MSSession` est créée. La propriété `SummaryText` du `MSMessage` est utilisée pour ajouter une légende aux étapes précédentes réduites. Si la propriété `SummaryText` est définie sur `null`, les étapes précédentes de la conversation seront complètement supprimées de la transcription de conversation.

## <a name="advanced-message-api-features"></a>Fonctionnalités avancées de l’API de message

Avec les fonctionnalités de base de la nouvelle API de message abordées en détail ci-dessus, examinez quelques-unes des fonctionnalités les plus avancées intégrées à Apple dans l’infrastructure.

Tout d’abord, il existe plusieurs autres méthodes override dans la classe `MSMessagesAppViewController` qui fournissent un accès plus profond à la conversation :

- `DidStartSendingMessage` : cette méthode est appelée lorsque l’utilisateur clique sur le bouton Envoyer. Cela ne signifie pas que le message a effectivement été remis au destinataire, mais que le processus d’envoi a été démarré.
- `DidCancelSendingMessage` : cela se produit quand l’utilisateur appuie sur le bouton *X* dans l’angle supérieur droit de la bulle de message dans la transcription de conversation.
- `DidReceiveMessage` : cette méthode est appelée lorsque l’extension de l’application de message est active. un nouveau message a été reçu de l’un des participants de la conversation.

### <a name="group-conversations"></a>Conversations de groupe

Une extension d’application de message peut être utilisée pendant que les utilisateurs sont impliqués dans des conversations de groupe (avec au moins 3 personnes) et cela doit être pris en considération lors de la conception et de l’implémentation d’une extension d’application de message.

Jetez un coup d’œil à l’interaction suivante dans une conversation de groupe avec trois utilisateurs :

[![](advanced-message-app-extensions-images/interactive14.png "Interaction in a group conversation with three users")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. L’utilisateur 1 envoie un message interactif de groupe demandant à l’utilisateur 2 et à l’utilisateur 3 de choisir un actuellement votre hamburger.
2. L’utilisateur 2 choisit les tomates.
3. L’utilisateur 3 choisit condiments.
4. Les choix de l’utilisateur 2 et de l’utilisateur 3 sont renvoyés à l’utilisateur 1 presque en même temps. Par conséquent, le choix de l’utilisateur 2 est réduit en une ligne de résumé et n’est pas disponible. Ce cas peut également avoir été retourné, le choix de l’utilisateur 2 étant affiché et l’utilisateur 3 est réduit.

Dans les deux cas, ce comportement n’est pas souhaité, car l’utilisateur 1 doit pouvoir accéder aux choix de l’utilisateur 2 et de l’utilisateur 3. Pour gérer cette situation, Apple suggère que l’extension de l’application de message stocke l’état du message dans le Cloud et utilise la propriété URL de l' `MSMessage` (qui est envoyé entre les utilisateurs) pour accéder à cet État.

Lorsque l’utilisateur envoie un message, un jeton de session est généré et envoyé dans le Cloud avec l’état actuel du message. Lorsqu’un utilisateur appuie sur une bulle de message dans la transcription de conversation, le jeton de session est utilisé pour récupérer l’état de session actuel à partir du Cloud.

### <a name="sender-identifiers"></a>Identificateurs d’expéditeur

Pour discuter de l’accès à l’identificateur de l’expéditeur d’un message, prenez l’exemple d’une conversation de groupe donnée ci-dessus :

[![](advanced-message-app-extensions-images/interactive15.png "Group conversation sending Identifiers")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Là encore, l’utilisateur 1 envoie un message interactif de groupe demandant à l’utilisateur 2 et à l’utilisateur 3 de choisir un actuellement votre hamburger.
2. L’utilisateur 3 choisit condiments.
3. Le choix de l’utilisateur 3 est renvoyé à l’utilisateur 1 et l’utilisateur 2 n’a pas encore répondu.
4. Comme Apple s’intéresse à la confidentialité de l’utilisateur, l’extension de l’application de message ne connaît qu’un identificateur unique (en tant que `NSUUID`) qui est affecté à chaque participant de la conversation. Sur l’appareil local, seul l’identificateur de l’utilisateur actuel est connu.
5. La `MSMessage` a une propriété `SenderIdentifier` qui correspond à l’un des utilisateurs de la liste des participants connue de l’extension.
6. Chaque périphérique utilisateur possède sa propre copie de la liste des participants, là encore, seul l’identificateur de l’utilisateur local est connu.
7. Lorsqu’un message est envoyé, sa propriété `SenderIdentifier` est également connue comme étant celle de l’utilisateur local.

Les identificateurs de l’expéditeur peuvent être utilisés des manières suivantes :

- En examinant la liste des participants, l’extension peut connaître le nombre d’utilisateurs dans la conversation.
- Lorsque l’extension reçoit un message d’un utilisateur, elle peut effectuer le suivi de l’identificateur de l’expéditeur. S’il reçoit un autre message avec le même identificateur d’expéditeur, l’extension sait qu’elle provient du même utilisateur.
- Ils peuvent être utilisés pour aider à identifier un utilisateur spécifique dans la conversation.

L’identificateur de l’expéditeur peut être utilisé dans n’importe quel champ de texte du `MSMessageTemplateLayout` en le préfixant par un signe dollar (`$`). Exemple :

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Lorsque l’application messages affiche une bulle de message avec ce type de mise en forme, elle remplace le `$uuid...` par le nom de contact du participant dans la conversation qui a envoyé le message.

L’identificateur de l’expéditeur est unique sur chaque appareil. par conséquent, si vous examinez le diagramme ci-dessus, Notez que l’appareil de l’utilisateur 1 et l’appareil de l’utilisateur 3 ont un identificateur d’expéditeur unique différent pour chaque participant de la conversation.

Les identificateurs de l’expéditeur sont étendus à l’installation de l’extension de l’application de message. Par conséquent, si un utilisateur désinstalle et réinstalle l’extension de l’application de message, les nouveaux identificateurs d’expéditeur sont générés pour la nouvelle installation.

Pour accéder aux identificateurs de l’expéditeur, l’extension peut utiliser le code suivant :

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Plateformes prises en charge

Les messages interactifs générés par une extension d’application de message sont remis sur les plateformes Apple suivantes :

- Watchos 3
- macOS Sierra
- iOS 10

Parmi les trois plateformes, seul iOS 10 permet à l’utilisateur de générer un message interactif. Sur macOS Sierra, si l’utilisateur clique sur une bulle de message interactive, l’URL attachée au `MSMessage` s’ouvre dans Safari et une représentation du message doit s’afficher.

Sur Watchos, l’application messages peut transférer un message interactif à un appareil iOS attaché, où l’utilisateur peut composer une réponse.

La nouvelle API de messages prend en charge le secours si le message interactif est reçu sur des plateformes Apple plus anciennes :

- Watchos 2 +
- OS X 10,11 +
- iOS 9 +

Ils seront remis dans un format de secours sous forme de deux messages distincts :

- L’une d’entre elles sera l’image fournie par la disposition du modèle.
- L’autre sera l’URL fournie dans le `MSMessage`.

## <a name="summary"></a>Récapitulatif

Cet article a présenté des techniques avancées pour l’utilisation des extensions d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur.

## <a name="related-links"></a>Liens associés

- [Générateur de crème glacée (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Référence des messages](https://developer.apple.com/reference/messages)
- [Guide de programmation de l’extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
