---
title: Hello.iOS - Démarrage rapide
description: Cette procédure pas à pas montre comment générer une application Xamarin.iOS simple appelée Hello, iOS. Ce faisant, elle présente les outils et concepts fondamentaux que vous devez comprendre pour créer des applications Xamarin.iOS.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: D3868F3A-4EED-BDDF-45AA-665102C39634
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 5dcc37730008e6e39b96128bc1368f022daa2d06
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022993"
---
# <a name="hello-ios--quickstart"></a>Hello.iOS - Démarrage rapide

Ce guide décrit comment créer une application qui convertit un numéro de téléphone alphanumérique (entré par l’utilisateur) en numéro de téléphone numérique, puis compose ce numéro. L’application finale ressemble à ceci :

 [![](hello-ios-quickstart-images/image1.png "The Hello.iOS Quickstart app")](hello-ios-quickstart-images/image1.png#lightbox)

## <a name="requirements"></a>Spécifications

Le développement iOS avec Xamarin exige :

- Un Mac exécutant macOS High Sierra (10.13) ou version ultérieure.
- Dernière version de Xcode et iOS SDK installée depuis [l’App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) .

::: zone pivot="macos"

Xamarin.iOS fonctionne avec la configuration suivante :

- La dernière version de Visual Studio pour Mac qui respecte les spécifications ci-dessus.

Le [guide d’installation de Xamarin.iOS sur Mac](~/ios/get-started/installation/mac.md) est disponible pour obtenir des instructions pas à pas.

::: zone-end
::: zone pivot="windows"

Xamarin.iOS fonctionne avec la configuration suivante :

- Dernière version de Visual Studio 2019 ou Visual Studio 2019 Community, Professional ou Enterprise sur Windows 10, associée à un hôte de build Mac répondant aux spécifications ci-dessus.

Le [guide d’installation de Xamarin.iOS sur Windows](~/ios/get-started/installation/windows/index.md) est disponible pour obtenir des instructions pas à pas.

::: zone-end

Avant de commencer, téléchargez le [jeu d’icônes d’application Xamarin](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true).

::: zone pivot="macos"

## <a name="visual-studio-for-mac-walkthrough"></a>Procédure pas à pas Visual Studio pour Mac

Cette procédure pas à pas décrit comment créer une application nommée Phoneword qui convertit un numéro de téléphone alphanumérique en numéro de téléphone numérique.

1. Lancez Visual Studio pour Mac à partir du dossier **Applications** ou à partir de **Spotlight** :

    ![](hello-ios-quickstart-images/image2new.png "The Launch screen")

    Dans l’écran de lancement, cliquez sur **Nouveau projet...** pour créer une nouvelle solution Xamarin.iOS :

    ![](hello-ios-quickstart-images/image3new.png "iOS solution")

2. À partir de la **boîte de dialogue Nouvelle Solution**, choisissez le modèle **iOS > Application > Application avec affichage unique**, en vérifiant que C# est sélectionné. Cliquez **sur Next**:

    ![](hello-ios-quickstart-images/image4new.png "Choose Single View Application")

3. Configurez l’application. Attribuez-lui le **nom de ** `Phoneword_iOS` et conservez les valeurs par défaut des autres champs. Cliquez **sur Next**:

    ![](hello-ios-quickstart-images/image5new.png "Enter the app name")

4. Laissez le nom du projet et de la solution en l’état. Choisissez l’emplacement du projet ici ou conservez-le en tant que valeur par défaut :

    ![](hello-ios-quickstart-images/image6new.png "Choose the location of the project")

5. Cliquez sur **Créer** pour créer la **solution**.

6. Ouvrez le fichier **Main.storyboard** en double-cliquant dessus dans le **Panneau Solutions**. Vous obtenez ainsi un moyen visuel de créer une interface utilisateur :

    ![](hello-ios-quickstart-images/image7new.png "The iOS Designer")

    Notez que les _classes de taille_ sont activées par défaut. Reportez-vous au guide sur les [storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) pour en savoir plus à leur sujet.

7. Dans le **Panneau Boîte à outils**, tapez « étiquette » dans la barre de recherche, puis faites glisser une **étiquette** dans l’aire de conception (la zone située au centre) :

    ![](hello-ios-quickstart-images/image8new.png "Drag a Label onto the design surface the area in the center")

    > [!NOTE]
    > Vous pouvez afficher le **Panneau Propriétés** ou la **Boîte à outils** à tout moment en navigant vers **Afficher > Blocs**.

8. Saisissez les poignées des *contrôles de déplacement* (cercles autour des contrôles) pour élargir l’étiquette :

    ![](hello-ios-quickstart-images/image9.png "Make the label wider")

9. Avec l’**étiquette** sélectionnée dans l’aire de conception, utilisez le **Panneau Propriétés** pour remplacer la propriété **Texte** de l’**étiquette** par « Entrez un numéro Phoneword : »

    ![](hello-ios-quickstart-images/image10.png "Set the label to Enter a Phoneword")

10. Recherchez « champ de texte » dans la boîte à outils et faites glisser un **champ de texte** depuis la **boîte à outils** vers l’aire de conception pour le placer sous l’**étiquette**. Ajuster la largeur jusqu’à ce que le **champ de texte** soit de la même largeur que l’étiquette : **Label**

    ![](hello-ios-quickstart-images/image12new.png "Make the Text Field the same width as the Label")

11. Avec le **champ de texte** sélectionné dans l’aire de conception, affectez à la propriété **Nom** du **champ de texte** située dans la section Identité du **Panneau Propriétés** la valeur `PhoneNumberText`, puis remplacez la propriété **Texte** par « 1-855-XAMARIN » :

    ![](hello-ios-quickstart-images/image13new.png "Change the Title property to 1-855-XAMARIN")

12. Faites glisser un **bouton** depuis la **boîte à outils** vers l’aire de conception, puis placez-le sous le **champ de texte**. Ajuster la largeur de sorte que le **bouton** est aussi large que le **champ de texte** et **l’étiquette**:

    ![](hello-ios-quickstart-images/image14new.png "Adjust the width so the Button is as wide as the Text Field and Label")

13. Avec le **bouton** sélectionné dans l’aire de conception, affectez à la propriété **Nom** dans la section **Identité** du **Panneau Propriétés** la valeur `TranslateButton`. Remplacez la propriété **Titre** par « Convertir » :

    ![](hello-ios-quickstart-images/image15new.png "Change the Title property to Translate")

14. Répétez les deux étapes ci-dessus et faites glisser un **bouton** depuis la **boîte à outils** vers l’aire de conception pour le placer sous le premier **bouton**. Ajustez la largeur de sorte que le **bouton** soit aussi large que le premier **bouton** :

    ![](hello-ios-quickstart-images/image16new.png "Adjust the width so the Button is as wide as the first Button")

15. Avec le deuxième **bouton** sélectionné dans l’aire de conception, affectez à la propriété **Nom** dans la section **Identité** du **Panneau Propriétés** la valeur `CallButton`. Remplacez la propriété **Titre** par « Appeler » :

    ![](hello-ios-quickstart-images/image17new.png "Change the Title property to Call")

    Enregistrez les modifications en accédant à **Fichier > Enregistrer** ou en appuyant sur **⌘ + S**.

16. Il est nécessaire d’ajouter une certaine logique à l’application pour convertir des numéros de téléphone alphanumériques en numéros numériques. Ajoutez un fichier au projet en cliquant avec le bouton droit sur le projet **Phoneword_iOS** dans le **Panneau Solutions**, puis en choisissant **Ajouter > Nouveau fichier...** ou en appuyant sur **⌘ + N** :

    ![](hello-ios-quickstart-images/image18.png "Add a new file to the Project")

17. Dans la boîte de dialogue **Nouveau fichier**, sélectionnez **Général > Classe vide** et nommez le nouveau fichier `PhoneTranslator` :

    ![](hello-ios-quickstart-images/image19.png "Select Empty Class and name the new file PhoneTranslator")

18. Cette opération crée une classe C# vide. Supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword_iOS
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Enregistrez le fichier **PhoneTranslator.cs** et fermez-le.

19. Ajoutez du code pour associer l’interface utilisateur. Pour cela, double-cliquez sur **ViewController.cs** dans le **Panneau Solutions** pour l’ouvrir :

    ![](hello-ios-quickstart-images/image20new.png "Add code to wire up the user interface")

20. Commencez par associer le `TranslateButton`. Dans la classe **ViewController**, recherchez la méthode `ViewDidLoad` et ajoutez le code suivant sous l’appel à `base.ViewDidLoad()` :

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(
            PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call ", UIControlState.Normal);
            CallButton.Enabled = false;
        } else {
            CallButton.SetTitle ("Call " + translatedNumber,
                UIControlState.Normal);
            CallButton.Enabled = true;
        }
    };
    ```

    Incluez `using Phoneword_iOS;` si l’espace de noms du fichier est différent.

21. Ajoutez du code pour répondre à l’utilisateur qui appuie sur le deuxième bouton, nommé `CallButton`. Placez le code suivant sous le code pour `TranslateButton` et ajoutez `using Foundation;` au tout début du fichier :

    ```csharp
        CallButton.TouchUpInside += (object sender, EventArgs e) => {
            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl ("tel:" + translatedNumber);

            // ...otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl (url)) {
                var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                PresentViewController (alert, true, null);
            }
        };
    ```

22. Enregistrer les modifications, puis construire l’application en choisissant **Build > Construire tout** ou en appuyant sur **B**.  Si l’application est compilé, un message de réussite apparaîtra en haut de l’IDE :

    ![](hello-ios-quickstart-images/image21.png "A success message will appear at the top of the IDE")

    En cas d’erreurs, examinez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que l’application soit générée.

23. Enfin, testez l’application dans le **simulateur iOS**. En haut à gauche de l’IDE, choisissez **Déboguer** dans la première liste déroulante, et **iPhone XR iOS 12.0** (ou tout autre simulateur disponible) dans la deuxième liste déroulante, puis appuyez sur **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) :

    ![](hello-ios-quickstart-images/image27.png "Select a simulator and press start")

    > [!NOTE]
    > En raison d’une exigence d’Apple, vous devez disposer d’un certificat de développement ou d’une *identité de signature* afin de générer du code pour un appareil ou un simulateur. Suivez les étapes indiquées dans le [guide de provisionnement des appareils](~/ios/get-started/installation/device-provisioning/manual-provisioning.md) pour les obtenir.

24. L’application est alors lancée dans le simulateur iOS :

    ![](hello-ios-quickstart-images/image28.png "The application running inside the iOS Simulator")

    Les appels téléphoniques ne sont pas pris en charge dans le simulateur iOS ; une boîte de dialogue d’alerte s’affiche lors d’une tentative d’appel :

    ![](hello-ios-quickstart-images/image29.png "The alert dialog when trying to place a call")

::: zone-end
::: zone pivot="windows"

## <a name="visual-studio-walkthrough"></a>Procédure pas à pas Visual Studio

Cette procédure pas à pas décrit comment créer une application nommée Phoneword qui convertit un numéro de téléphone alphanumérique en numéro de téléphone numérique.

> [!NOTE]
> Cette procédure pas à pas utilise Visual Studio Enterprise 2017 sur une machine virtuelle Windows 10. Votre configuration peut être différente de celle-ci, tant qu’elle satisfait à la configuration requise ci-dessus. Ainsi, sachez que certaines captures d’écran peuvent aussi être différentes.

> [!NOTE]
> Avant de poursuivre cette procédure pas à pas, vous devez être déjà connecté à votre Mac à partir de Visual Studio. En effet, Xamarin.iOS s’appuie sur des outils Apple pour générer et lancer le concepteur et les applications iOS. Afin de préparer la configuration, suivez les étapes du guide [Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio à partir du menu **Démarrer** :

    ![](hello-ios-quickstart-images/image001-.png "The Start screen")

    Créez une solution Xamarin.iOS en sélectionnant **Fichier > Nouveau > Projet... > Visual C# > iPhone et iPad > Application iOS (Xamarin)** :

    ![Sélectionnez le type de projet iOS App (Xamarin)](hello-ios-quickstart-images/image002.w157.png "Sélectionnez le type de projet iOS App (Xamarin)")

    Dans la boîte de dialogue qui s’affiche ensuite, sélectionnez le modèle **Application avec vue unique** et appuyez sur **OK** pour créer le projet :

    ![Sélectionnez le modèle de projet Single View](hello-ios-quickstart-images/image002-2.w157.png "Sélectionnez le modèle de projet Single View")

1. Vérifiez que l’icône Mac Agent Xamarin est verte dans la barre d’outils.

    ![Vérifier que l’icône Mac Agent Xamarin est verte dans la barre d’outils](hello-ios-quickstart-images/vs-image4.png)

    Si ce n’est pas le cas, aucune connexion n’a été établie à votre hôte de build Mac. Vous devez alors suivre les étapes du [guide de configuration](~/ios/get-started/installation/windows/connecting-to-mac/index.md) pour vous connecter.

1. Ouvrez le fichier **Main.storyboard** dans le concepteur iOS en double-cliquant dessus dans l’**Explorateur de solutions** :

    ![](hello-ios-quickstart-images/vs-image7.png "The iOS Designer")

1. Ouvrez l’onglet **Boîte à outils**, tapez « étiquette » dans la barre de recherche, puis faites glisser une **Étiquette** dans l’aire de conception (la zone située au centre) :

    ![](hello-ios-quickstart-images/vs-image8.png "Drag a Label onto the design surface the area in the center")

1. Saisissez ensuite les poignées des *contrôles de déplacement* pour élargir l’étiquette :

    ![](hello-ios-quickstart-images/vs-image9.png "Make the label wider")

1. Avec l’**étiquette** sélectionnée dans l’aire de conception, utilisez les **fenêtres Propriétés** pour remplacer la propriété **Texte** de l’**étiquette** par « Entrez un numéro Phoneword : »

    ![](hello-ios-quickstart-images/vs-image10.png "Change the Text property of the Label to `Enter a Phoneword`")

    > [!NOTE]
    > Vous pouvez afficher les **Propriétés** ou la **Boîte à outils** à tout moment en navigant vers le menu **Afficher**.

1. Recherchez « champ de texte » dans la boîte à outils et faites glisser un **champ de texte** depuis la **boîte à outils** vers l’aire de conception pour le placer sous l’**étiquette**. Ajuster la largeur jusqu’à ce que le **champ de texte** soit de la même largeur que l’étiquette : **Label**

    ![](hello-ios-quickstart-images/vs-image12.png "Adjust the width until the Text Field is the same width as the Label")

1. Avec le **champ de texte** sélectionné dans l’aire de conception, affectez à la propriété **Nom** du **champ de texte** située dans la section Identité des **propriétés** la valeur `PhoneNumberText`, puis remplacez la propriété **Texte** par « 1-855-XAMARIN » :

    ![](hello-ios-quickstart-images/vs-image13.png "Change the Text property to 1-855-XAMARIN")

1. Faites glisser un **bouton** depuis la **boîte à outils** vers l’aire de conception, puis placez-le sous le **champ de texte**. Ajuster la largeur de sorte que le **bouton** est aussi large que le **champ de texte** et **l’étiquette**:

    ![](hello-ios-quickstart-images/vs-image14.png "Adjust the width so the Button is as wide as the Text Field and Label")

1. Avec le **bouton** sélectionné dans l’aire de conception, affectez à la propriété **Nom** dans la section **Identité** des **propriétés** la valeur `TranslateButton`. Remplacez la propriété **Titre** par « Convertir » :

    ![](hello-ios-quickstart-images/vs-image15.png "Change the Title property to Translate")

1. Répétez les deux étapes précédentes et faites glisser un **bouton** depuis la **boîte à outils** vers l’aire de conception pour le placer sous le premier **bouton**. Ajustez la largeur de sorte que le **bouton** soit aussi large que le premier **bouton** :

    ![](hello-ios-quickstart-images/vs-image16.png "Adjust the width so the Button is as wide as the first Button")

1. Avec le deuxième **bouton** sélectionné dans l’aire de conception, affectez à la propriété **Nom** dans la section **Identité** des **propriétés** la valeur `CallButton`. Remplacez la propriété **Titre** par « Appeler » :

    ![](hello-ios-quickstart-images/vs-image17.png "Change the Title property to Call")

    Enregistrez les modifications en accédant à **Fichier > Enregistrer tout** ou en appuyant sur **Ctrl+S**.

1. Ajoutez du code pour convertir des numéros de téléphone alphanumériques en numéros de téléphone numériques. Pour cela, commencez par ajouter un nouveau fichier au projet en cliquant avec le bouton droit sur le projet **Phoneword** dans l’**Explorateur de solutions**, puis en choisissant **Ajouter > Nouvel élément...** ou en appuyant sur **Ctrl+Maj+A** :

    ![](hello-ios-quickstart-images/vs-image18.png "Add some code to translate phone numbers from alphanumeric to numeric")

1. Dans la boîte de dialogue **Ajouter un nouvel élément** (cliquez avec le bouton droit sur le projet, choisissez Ajouter > Nouvel élément...), sélectionnez **Apple > Classe** et nommez le nouveau fichier `PhoneTranslator` :

    ![](hello-ios-quickstart-images/vs-image19.w157.png "Add a new class named PhoneTranslator")

    > [!IMPORTANT]
    > Assurez-vous de sélectionner le modèle de classe qui comporte un C# dans l’icône. Sinon, vous risquez de ne pas pouvoir faire référence à cette nouvelle classe.

1. Une nouvelle classe C# est alors créée. Supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System.Text;
    using System;

    namespace Phoneword
    {
        public static class PhoneTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw)) {
                    return "";
                } else {
                    raw = raw.ToUpperInvariant();
                }

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c)) {
                        newNumber.Append(c);
                    } else {
                        var result = TranslateToNumber(c);
                        if (result != null) {
                            newNumber.Append(result);
                        }
                    }
                    // otherwise we've skipped a non-numeric char
                }
                return newNumber.ToString();
            }

            static bool Contains (this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static int? TranslateToNumber(char c)
            {
                if ("ABC".Contains(c)) {
                    return 2;
                } else if ("DEF".Contains(c)) {
                    return 3;
                } else if ("GHI".Contains(c)) {
                    return 4;
                } else if ("JKL".Contains(c)) {
                    return 5;
                } else if ("MNO".Contains(c)) {
                    return 6;
                } else if ("PQRS".Contains(c)) {
                    return 7;
                } else if ("TUV".Contains(c)) {
                    return 8;
                } else if ("WXYZ".Contains(c)) {
                    return 9;
                }
                return null;
            }
        }
    }
    ```

    Enregistrez le fichier **PhoneTranslator.cs** et fermez-le.

1. Double-cliquez sur **ViewController.cs** dans l’**Explorateur de solutions** pour l’ouvrir et ajouter une logique aux interactions de poignée avec les boutons :

    ![](hello-ios-quickstart-images/vs-image20.png "Logic added to handle interactions with the buttons")

1. Commencez par associer le `TranslateButton`. Dans la classe **ViewController**, recherchez la méthode `ViewDidLoad`. Ajoutez le code de bouton suivant dans `ViewDidLoad`, l’appel à `base.ViewDidLoad()` :

    ```csharp
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {

        // Convert the phone number with text to a number
        // using PhoneTranslator.cs
        translatedNumber = PhoneTranslator.ToNumber(PhoneNumberText.Text);

        // Dismiss the keyboard if text field was tapped
        PhoneNumberText.ResignFirstResponder ();

        if (translatedNumber == "") {
            CallButton.SetTitle ("Call", UIControlState.Normal);
            CallButton.Enabled = false;
            }
        else {
            CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
            CallButton.Enabled = true;
            }
    };
    ```

    Incluez `using Phoneword;` si l’espace de noms du fichier est différent.

1. Ajoutez du code pour répondre à l’utilisateur qui appuie sur le deuxième bouton, nommé `CallButton`. Placez le code suivant sous le code pour `TranslateButton` et ajoutez `using Foundation;` au tout début du fichier :

    ```csharp
    CallButton.TouchUpInside += (object sender, EventArgs e) => {
        var url = new NSUrl ("tel:" + translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app,
            // otherwise show an alert dialog

        if (!UIApplication.SharedApplication.OpenUrl (url)) {
                        var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                        alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        PresentViewController (alert, true, null);
                    }
    };
    ```

1. Enregistrer les modifications, puis construire l’application en choisissant **Build > Build Solution** ou en appuyant sur **Ctrl - Shift B**.  Si l’application est compilé, un message de réussite apparaîtra au bas de l’IDE :

    ![](hello-ios-quickstart-images/vs-image21.png "A success message will appear at the bottom of the IDE")

    En cas d’erreurs, examinez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que l’application soit générée.

1. Enfin, testez l’application dans **Remoted iOS Simulator**. Dans la barre d’outils de l’IDE, choisissez **Déboguer** et **iPhone 8 Plus iOS x.x** dans les menus déroulants, puis appuyez sur **Démarrer** (le triangle vert qui ressemble à un bouton de lecture) :

    ![](hello-ios-quickstart-images/vs-image27.png "Press Start")

1. L’application est alors lancée dans le simulateur iOS :

    ![](hello-ios-quickstart-images/vs-image28.png "The application running inside the iOS Simulator")

    Les appels téléphoniques ne sont pas pris en charge dans le simulateur iOS ; une boîte de dialogue d’alerte s’affiche lors d’une tentative d’appel :

    ![](hello-ios-quickstart-images/vs-image29.png "An alert dialog will display when trying to place a call")

::: zone-end

Félicitations ! Vous avez terminé votre première application Xamarin.Android.

Maintenant, le moment est venu d’examiner de plus près les outils et compétences mentionnées dans ce guide dans la seconde partie intitulée [Hello, iOS - En profondeur](~/ios/get-started/hello-ios/hello-ios-deepdive.md).

## <a name="related-links"></a>Liens connexes

- [Icônes d’application et images de lancement Xamarin (exemple)](https://github.com/xamarin/ios-samples/blob/master/Hello_iOS/Resources/XamarinAppIconsandLaunchImages.zip?raw=true)
- [Hello, iOS (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Human Interface Guidelines pour iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portail d’approvisionnement iOS](https://developer.apple.com/ios/manage/overview/index.action)
