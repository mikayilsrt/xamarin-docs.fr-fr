---
title: Distribution interne pour les applications Xamarin.iOS
description: Ce document fournit une brève présentation de la distribution d’applications en interne, en tant que membre du programme Developer Enterprise d’Apple.
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 854fecd7945c1090b475b3571678388b8e1cf127
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573233"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Distribution interne pour les applications Xamarin.iOS

_Ce document fournit une brève présentation de la distribution d’applications en interne, en tant que membre du programme Developer Enterprise d’Apple._

Une fois votre application Xamarin.iOS développée, la prochaine étape du cycle de vie du développement du logiciel consiste à la distribuer aux utilisateurs. Vous pouvez distribuer les applications propriétaires selon le mode *Interne* (appelé auparavant Entreprise) via le **programme Developer Enterprise** d’Apple, qui offre les avantages suivants :

- Votre application n’a pas besoin d’être soumise à l’évaluation d’Apple.
- Il n’existe aucune limite à la quantité d’appareils sur lesquels vous pouvez déployer une application
  - Il est important de noter qu’Apple indique très clairement que les applications internes sont réservées à un usage interne.

Il est également important de noter les points suivants pour le programme Developer Enterprise :

- Il ne permet pas d’accéder à iTunes Connect pour la distribution ou les tests (notamment TestFlight).
- Le coût de l’adhésion annuelle est de 299 $.

Toutes les applications doivent quand même être signées par Apple.

<a name="testing"></a>

## <a name="testing-your-application"></a>Test de votre application

Le test de votre application s’effectue via la distribution ad hoc. Pour plus d’informations sur la procédure de test, suivez les étapes du guide [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Sachez que vous pouvez tester uniquement 100 appareils au maximum.

<a name="setup"></a>

## <a name="getting-set-up-for-distribution"></a>Préparation à la distribution

Comme pour d’autres programmes Developer d’Apple, dans le cadre du programme Developer Enterprise d’Apple, seuls les administrateurs et les agents d’équipe peuvent créer des certificats de distribution et des profils de provisionnement.

Les certificats du programme Developer Enterprise d’Apple durent trois ans, et les profils de provisionnement expirent au-delà d’un an.

Il est important de noter que vous ne pouvez pas renouveler les certificats arrivés à expiration. À la place, vous devez remplacer le certificat arrivé à expiration par un nouveau certificat, comme indiqué [ci-dessous](#certificate).

<a name="certificate"></a>

## <a name="creating-a-distribution-certificate"></a>Création d’un certificat de distribution

1. Accédez à la section *Certificates, Identifiers & Profiles* (Certificats, identificateurs et profils) du Member Center du programme Developer d’Apple.
2. Sous *Certificates*, sélectionnez **Production**.
3. Cliquez sur le **+** bouton pour créer un nouveau certificat.
4. Sous le titre *Production*, sélectionnez **In-House and Ad Hoc (Interne et ad hoc)**  :

   [![](in-house-distribution-images/createcertmanually01.png "Select In-House and Ad Hoc")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. Cliquez sur Continue, puis suivez les instructions permettant de créer une demande de signature de certificat via Trousseaux d’accès :

   [![](in-house-distribution-images/createcertmanually02.png "Create a Certificate Signing Request via Keychain Access")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. Une fois votre demande de signature de certificat créée, cliquez sur Continue, puis chargez-la sur le Member Center :

   [![](in-house-distribution-images/createcertmanually03.png "Upload the CSR to the Member Center")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. Cliquez sur Generate pour créer votre certificat.
8. Téléchargez le certificat rempli, puis double-cliquez sur le fichier pour l’installer.
9. À ce stade, votre certificat doit être installé sur la machine, mais vous devrez peut-être actualiser vos profils pour vérifier qu’ils sont visibles dans Xcode.

Sinon, il est possible de demander un certificat via la boîte de dialogue Preferences de Xcode. Pour ce faire, procédez comme suit :

1. Sélectionnez votre équipe, puis cliquez sur *Afficher les détails* :

   [![](in-house-distribution-images/selectteam.png "Select your team")](in-house-distribution-images/selectteam.png#lightbox)

2. Cliquez ensuite sur le bouton **Créer** à côté de **iOS Distribution Certificate (Certificat de distribution iOS)**  :

   [![](in-house-distribution-images/selectcert.png "Create the iOS Distribution Certificate")](in-house-distribution-images/selectcert.png#lightbox)

3. Cliquez ensuite sur le bouton **plus (+)**, puis sélectionnez **iOS App Store (App Store iOS)**  :

   [![](in-house-distribution-images/selectcert.png "Select iOS App Store")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile"></a>

## <a name="creating-a-distribution-provisioning-profile"></a>Création d’un profil de provisionnement de distribution

<a name="appid"></a>

### <a name="creating-an-app-id"></a>Création d’un ID d’application

Comme pour tout profil de provisionnement que vous créez, un ID d’application est nécessaire pour identifier l’application que vous allez distribuer sur l’appareil de l’utilisateur. Si vous ne l’avez pas déjà créé, suivez les étapes ci-dessous pour en créer un :

1. Dans [Apple Developer Center](https://developer.apple.com/account/overview.action), accédez à la section *Certificates, Identifiers & Profiles* (Certificats, identificateurs et profils). Sélectionnez **App IDs** sous **Identifiers**.
2. Cliquez sur le **+** bouton et fournissez un **nom** qui l’identifiera dans le portail.
3. Le préfixe d’application doit déjà être défini à l’aide de votre ID d’équipe. Il est impossible de le changer. Sélectionnez un ID d’application explicite ou avec des caractères génériques, puis entrez un ID de bundle au format DNS inversé : **Explicite** : com.[DomainName].[AppName] **Avec des caractères génériques** : com.[DomainName].*
4. Sélectionnez les [services d’application](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services) nécessaires à votre application.
5. Cliquez sur le bouton **Continuer** et suivez les instructions à l’écran pour créer l’ID d’application.

Une fois que vous avez les composants nécessaires pour créer un profil de distribution, suivez les étapes ci-dessous pour le créer :

1. Revenez au portail de provisionnement Apple et sélectionnez distribution de **provisionnement**  >  **Distribution**:

   [![](in-house-distribution-images/distribute01.png "Select Provisioning > Distribution")](in-house-distribution-images/distribute01.png#lightbox)

2. Cliquez sur le **+** bouton et sélectionnez le type de profil de distribution que vous souhaitez créer en **interne**:

   [![](in-house-distribution-images/distribute02.png "Create an In-House Distribution Profile")](in-house-distribution-images/distribute02.png#lightbox)

3. Cliquez sur le bouton **Continue**, puis dans la liste déroulante, sélectionnez l’ID d’application pour lequel vous souhaitez créer un profil de distribution :

   [![](in-house-distribution-images/distribute03.png "Select App ID from the dropdown list")](in-house-distribution-images/distribute03.png#lightbox)

4. Cliquez sur le bouton **Continue**, puis sélectionnez le certificat de distribution nécessaire pour signer l’application :

   [![](in-house-distribution-images/distribute04.png "Select distribution certificate required to sign the application")](in-house-distribution-images/distribute04.png#lightbox)

5. Cliquez sur le bouton **Continue**, puis entrez un **Name (Nom)** pour le nouveau profil de distribution :

   [![](in-house-distribution-images/distribute06.png "Enter a Name for the new Distribution Profile")](in-house-distribution-images/distribute06.png#lightbox)

6. Cliquez sur le bouton **Generate** pour créer le profil et finaliser le processus.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 Vous devrez peut-être quitter Visual Studio pour Mac et obliger Xcode à actualiser sa liste d’identités de signature et de profils de provisionnement disponibles (en suivant les instructions de la section [Demande d’identités de signature](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) pour qu’un nouveau profil de distribution soit disponible dans Visual Studio pour Mac.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Vous devrez peut-être quitter Visual Studio et obliger Xcode (sur le Mac de l’hôte de build) à actualiser sa liste d’identités de signature et de profils de provisionnement disponibles (en suivant les instructions de la section [Demande d’identités de signature](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) pour qu’un nouveau profil de distribution soit disponible dans Visual Studio.

-----

<a name="inhouse"></a>

## <a name="distributing-your-app-in-house"></a>Distribution de votre application en interne

Avec le programme Developer Enterprise d’Apple, le licencié est la personne responsable de la distribution de l’application et du respect des [recommandations](https://developer.apple.com/programs/enterprise/) définies par Apple.

Vous distribuez votre application de manière sécurisée à l’aide de différents moyens, notamment :

- Localement via iTunes
- Serveur MDM
- Serveur web interne sécurisé
- E-mail

Pour distribuer votre application selon l’une de ces méthodes, vous devez d’abord créer un fichier IPA, comme expliqué dans la section suivante.

### <a name="creating-an-ipa-for-in-house-deployment"></a>Création d’un fichier IPA pour un déploiement interne

Une fois les applications provisionnées, elles peuvent être placées dans un paquet appelé fichier *IPA*. Il s’agit d’un fichier zip qui contient l’application, ainsi que des métadonnées et des icônes supplémentaires. Le fichier IPA permet d’ajouter une application localement à iTunes pour qu’elle puisse être synchronisée directement sur un appareil inclus dans le profil de provisionnement.

Pour plus d’informations sur la création d’un fichier IPA, consultez le guide [Prise en charge de fichier IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="summary"></a>Résumé

Cet article a fourni une brève présentation de la distribution d’applications Xamarin.iOS en interne.

## <a name="related-links"></a>Liens connexes

- [Distribution de l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Le fichier iTunesMetadata. plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Support de la Loi](~/ios/deploy-test/app-distribution/ipa-support.md)
