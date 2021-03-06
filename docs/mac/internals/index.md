---
title: En coulisses dans Xamarin. Mac
description: Ce document contient des liens vers divers guides qui décrivent le fonctionnement interne de Xamarin. Mac. Les documents liés traitent de la compilation à l’avance, de l’architecture Xamarin. Mac et du Bureau d’enregistrement Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: c13f8db60d296b8fa684e1d6861ba5caf38a7680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029945"
---
# <a name="under-the-hood-in-xamarinmac"></a>En coulisses dans Xamarin. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Compilation anticipée (AOA)](aot.md)

La compilation à l’avance (AOA) est une technique d’optimisation puissante pour améliorer les performances de démarrage. Toutefois, cela affecte également le temps de génération, la taille de l’application et l’exécution du programme de manière profonde, donc il est utile de comprendre son fonctionnement.

## <a name="mac-architecturearchitecturemd"></a>[Architecture Mac](architecture.md)

Relation de Xamarin. Mac avec Objective-C, y compris les concepts tels que la compilation, les sélecteurs, les bureaux d’enregistrement, le lancement d’applications et le générateur.

## <a name="xamarinmac-registrarregistrarmd"></a>[Bureau d’enregistrement Xamarin. Mac](registrar.md)

Xamarin. Mac permet de combler l’écart entre le runtime géré par le monde et le cacao, ce qui permet aux classes managées d’appeler des classes objective-C non managées et d’être rappelées lorsque des événements se produisent. Le travail requis pour préformer ce « Magic » est géré par le Bureau d’enregistrement, mais comprendre ce qui se passe « en coulisse » peut parfois être utile.
