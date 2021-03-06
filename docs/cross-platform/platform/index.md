---
title: Prise en charge des langages de programmation dans Xamarin
description: Ce document décrit les différents langages de programmation pris en charge par Xamarin. Il traite C#des modèles F#,, des Basic.net visuels portables et des modèles Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: davidortinau
ms.author: daortin
ms.date: 02/18/2018
ms.openlocfilehash: db963a38322e809d1aa82c02fbb9ae5cc4a650fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014629"
---
# <a name="programming-language-support-in-xamarin"></a>Prise en charge des langages de programmation dans Xamarin

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)

La version 5 C# introduit deux nouveaux mots clés pour exprimer des opérations asynchrones : Async et await. Ces mots clés vous permettent d’écrire du code simple qui utilise la bibliothèque parallèle de tâches pour exécuter des opérations de longue durée (telles que l’accès réseau) dans un autre thread et pour accéder facilement aux résultats à l’achèvement. Les dernières versions de Xamarin. iOS et Xamarin. Android prennent en charge Async et await. ce document fournit des explications et un exemple d’utilisation de la nouvelle syntaxe avec Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Fonctionnalités du langage C# 6](~/cross-platform/platform/csharp-six.md)

La version la plus récente C# du langage (version 6) continue à évoluer le langage de façon à ce qu’il soit moins réutilisable, plus clair et plus cohérent. La syntaxe d’initialisation du nettoyeur, la possibilité d’utiliser des `await` dans des blocs `catch/finally` et l’opérateur de `?` conditionnelle NULL sont particulièrement utiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Création d’applications mobiles F# avec et Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Basic.NET visuel portable](~/cross-platform/platform/visual-basic/index.md)

Visual Studio prend en charge la création de bibliothèques de classes portables à l’aide de Visual Basic.NET, qui peut ensuite être incorporé dans des applications Xamarin. Cet article explique comment créer un Visual Basic PCL, puis l’utiliser dans un exemple d’application Xamarin. iOS, Xamarin. Android et Windows Phone.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Génération de vues HTML à l’aide de modèles Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permet aux développeurs de tirer parti du moteur de création de modèles Razor, introduit à l’origine C# avec ASP.NET MVC, et de combiner facilement des données avec HTML, JavaScript et CSS sans devoir créer manuellement des chaînes html dans le code.
Cet article montre comment utiliser les modèles Razor avec Xamarin pour Android et iOS.
