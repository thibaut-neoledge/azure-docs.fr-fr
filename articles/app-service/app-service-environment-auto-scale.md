---
title: Mise à l’échelle automatique et environnement App Service| Microsoft Docs
description: Mise à l’échelle automatique et environnement App Service
services: app-service
documentationcenter: ''
author: btardif
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2016
ms.author: byvinyal

---
# Mise à l’échelle automatique et environnement App Service
Les environnements Azure App Service prennent en charge la *mise à l’échelle automatique*. Vous pouvez mettre à l’échelle automatiquement des pools de Workers individuels en fonction de mesures ou de la planification.

![Options de mise à l’échelle automatique d’un pool de Workers.][intro]

La mise à l’échelle automatique permet d’optimiser l’utilisation de vos ressources en agrandissant et en réduisant automatiquement un environnement App Service afin de l’adapter à votre budget et/ou à votre profil de charge.

## Configurer la mise à l’échelle automatique du pool de Workers
Vous pouvez accéder à la fonctionnalité de mise à l’échelle automatique à partir de l’onglet **Paramètres** du pool de Workers.

![Onglet Paramètres du pool de Workers.][settings-scale]

À partir de là, l’interface doit vous paraître assez familière, car elle est similaire à celle de la mise à l’échelle d’un plan App Service. Vous pourrez saisir une valeur de mise à l’échelle manuellement.

![Paramètres de mise à l’échelle manuelle.][scale-manual]

Vous pouvez également configurer un profil de mise à l’échelle automatique.

![Paramètres de mise à l’échelle automatique.][scale-profile]

Les profils de mise à l’échelle automatique sont utiles pour définir des limites à votre mise à l’échelle. Ainsi, vous pouvez obtenir des résultats satisfaisants en définissant une valeur de mise à l’échelle limite inférieure (1) et une valeur de plafonnement prévisible en définissant une limite supérieure (2).

![Paramètres de mise à l’échelle dans le profil.][scale-profile2]

Une fois qu’un profil est défini, vous pouvez ajouter des règles de mise à l’échelle automatique pour augmenter ou réduire le nombre d’instances dans le pool de Workers dans les limites définies par le profil. Les règles de mise à l’échelle automatique sont basées sur les mesures.

![Règle de mise à l’échelle.][scale-rule]

 Toutes les règles de mesure du pool de Workers ou du serveur frontal peuvent être utilisées pour définir des règles de mise à l’échelle automatique. Ces mesures sont les mêmes que celles que vous pouvez surveiller dans les graphiques de panneau de ressource ou pour lesquelles vous pouvez définir une alerte.

## Exemple de mise à l’échelle automatique
La mise à l’échelle automatique d’un environnement App Service est mieux illustrée par un scénario.

Cet article explique tous les éléments à prendre en compte lors du paramétrage d’une mise à l’échelle automatique et toutes les interactions qui ont lieu lorsque vous réalisez une mise à l’échelle automatique d’environnements App Service hébergés dans un environnement App Service.

### Présentation du scénario
Frank est administrateur système pour une entreprise. Il a migré une partie des charges de travail qu’il gère vers un environnement App Service.

L’environnement App Service est configuré sur mise à l’échelle manuelle, comme suit :

* **Serveurs frontaux** : 3
* **Pool de Workers 1** : 10
* **Pool de Workers 2** : 5
* **Pool de Workers 3** : 5

Le pool de Workers 1 est utilisé pour les charges de travail, tandis que le pool de Workers 2 et le pool de Workers 3 sont utilisés pour les charges de travail de développement et d’assurance qualité.

Les plans App Service utilisés pour l’assurance qualité et le développement sont configurés pour une mise à l’échelle manuelle, mais le plan App Service de production est défini sur mise à l’échelle automatique pour gérer les variations de charge et de trafic.

Frank connaît bien l’application. Il sait que les heures de pointe de charge sont situées entre 9 h et 18 h, car il s’agit d’une application métier (LOB) utilisée par les employés lorsqu’ils sont au bureau. Le taux d’utilisation chute une fois que les utilisateurs ont fini leur journée de travail. En dehors des heures de pointe, il existe toujours une charge, car les utilisateurs peuvent accéder à distance à l’application via leurs périphériques mobiles ou leurs ordinateurs personnels. Le plan App Service de production est déjà configuré sur la mise à l’échelle automatique basée sur le taux d’utilisation du processeur avec les règles suivantes :

![Paramètres spécifiques pour une application métier.][asp-scale]

| **Profil de la mise à l’échelle automatique – Jours de semaine – Plan App Service** | **Profil de la mise à l’échelle automatique – Week-ends – Plan App Service** |
| --- | --- |
| **Nom :** profil jour de semaine |**Nom :** profil week-end |
| **Mise à l’échelle selon :** Planification et règles de performances |**Mise à l’échelle selon :** Planification et règles de performances |
| **Profil :** jours de la semaine |**Profil :** week-end |
| **Type :** périodicité |**Type :** périodicité |
| **Plage cible :** 5 à 20 instances |**Plage cible :** 3 à 10 instances |
| **Jours :** lundi, mardi, mercredi, jeudi, vendredi |**Jours :** samedi, dimanche |
| **Heure de début :** 9 h |**Heure de début :** 9 h |
| **Fuseau horaire :** UTC-08 |**Fuseau horaire :** UTC-08 |
|  | |
| **Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
| **Ressource :** Production (environnement App Service) |**Ressource :** Production (environnement App Service) |
| **Mesure :** % d’utilisation de l’unité centrale |**Mesure :** % d’utilisation de l’unité centrale |
| **Fonctionnement :** supérieur à 60 % |**Fonctionnement :** supérieur à 80 % |
| **Durée :** 5 minutes |**Durée :** 10 minutes |
| **Agrégation de temps :** moyenne |**Agrégation de temps :** moyenne |
| **Action :** augmenter le nombre de 2 |**Action :** augmenter le nombre de 1 |
| **Refroidissement (minutes) :** 15 |**Refroidissement (minutes) :** 20 |
|  | |
| **Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
| **Ressource :** Production (environnement App Service) |**Ressource :** Production (environnement App Service) |
| **Mesure :** % d’utilisation de l’unité centrale |**Mesure :** % d’utilisation de l’unité centrale |
| **Fonctionnement :** inférieur à 30 % |**Fonctionnement :** inférieur à 20 % |
| **Durée :** 10 minutes |**Durée :** 15 minutes |
| **Agrégation de temps :** moyenne |**Agrégation de temps :** moyenne |
| **Action :** diminuer le nombre de 1 |**Action :** diminuer le nombre de 1 |
| **Refroidissement (minutes) :** 20 |**Refroidissement (minutes) :** 10 |

### Taux d’inflation du plan App Service
Les plans App Service sont configurés pour une mise à l’échelle automatique, et fonctionneront ainsi au taux maximal par heure. Cette vitesse peut être calculée en fonction des valeurs fournies sur la règle de mise à l’échelle automatique.

La compréhension et le calcul du *taux d’inflation du plan App Service* sont importants pour la mise à l’échelle du pool de Workers de l’environnement App Service, car les modifications d’un pool de Workers ne sont pas instantanées.

Le taux d’inflation du plan App Service est calculé comme suit :

![Calcul du taux d’inflation du plan App Service.][ASP-Inflation]

Si l’on prend la règle Mise à l’échelle automatique - Mise à l’échelle supérieure du profil Jour de semaine, le plan App Service de production devrait se présenter comme suit :

![Taux d’inflation du plan App Service pour les jours de la semaine selon la règle Mise à l’échelle automatique – Mise à l’échelle supérieure.][Equation1]

Dans le cas de la règle Mise à l’échelle automatique – Mise à l’échelle supérieure pour le profil Week-end, la formule du plan App Service de production se présentera ainsi :

![Taux d’inflation du plan App Service pour les week-ends selon la règle Mise à l’échelle automatique – Mise à l’échelle supérieure.][Equation2]

Cette valeur peut également être calculée pour les opérations de réduction.

Selon la règle Mise à l’échelle automatique - Mise à l’échelle inférieure pour le profil Jour de semaine, le plan App Service de production se présente comme suit :

![Taux d’inflation du plan App Service pour les jours de la semaine selon la règle Mise à l’échelle automatique – Mise à l’échelle inférieure.][Equation3]

Dans le cas de la règle Mise à l’échelle automatique – Mise à l’échelle inférieure pour le profil Week-end, la formule du plan App Service de production se présentera ainsi :

![Taux d’inflation du plan App Service pour les week-ends selon la règle Mise à l’échelle automatique – Mise à l’échelle inférieure.][Equation4]

Cela signifie que le plan App Service de production peut augmenter d’un taux maximal de huit instances par heure durant la semaine et de quatre instances par heure durant le week-end. Et il peut libérer des instances à un taux maximal de quatre instances par heure durant la semaine et de six instances par heure durant les week-ends.

Si plusieurs plans App Service sont hébergés dans un pool de Workers, vous devez calculer le *taux total d’inflation* en fonction de la somme du taux d’inflation de tous les plans App Service hébergés dans ce pool de Workers.

![Calcul du taux d’inflation total pour plusieurs plans App Service hébergés dans un pool de Workers.][ASP-Total-Inflation]

### Utilisation du taux d’inflation du plan App Service pour définir des règles de mise à l’échelle automatique du pool de Workers
Les pools de Workers qui hébergent des plans App Service configurés pour la mise à l’échelle automatique devront disposer d’une mémoire tampon adaptée. La mémoire tampon permet aux opérations de mise à l’échelle automatique d’augmenter et de réduire le plan App Service en fonction des besoins. La mémoire tampon minimale correspond au taux total d’inflation du plan App Service calculé.

Comme les opérations de mise à l’échelle de l’environnement App Service prennent un certain temps, toute modification doit tenir compte des demandes de modification pouvant se produire lorsqu’une opération de mise à l’échelle est en cours. Pour ce faire, nous recommandons l’utilisation du taux d’inflation du plan App Service total calculé en tant que nombre minimal d’instances ajoutées pour chaque opération de mise à l’échelle automatique.

Grâce à ces informations, Frank peut définir le profil et les règles de mise à l’échelle automatique suivants :

![Règles de profil de mise à l’échelle automatique pour un exemple d’application métier.][Worker-Pool-Scale]

| **Profil de mise à l’échelle automatique – Jours de la semaine** | **Profil de mise à l’échelle automatique – Week-ends** |
| --- | --- |
| **Nom :** profil jour de semaine |**Nom :** profil week-end |
| **Mise à l’échelle selon :** Planification et règles de performances |**Mise à l’échelle selon :** Planification et règles de performances |
| **Profil :** jours de la semaine |**Profil :** week-end |
| **Type :** périodicité |**Type :** périodicité |
| **Plage cible :** 13 à 25 instances |**Plage cible :** 6 à 15 instances |
| **Jours :** lundi, mardi, mercredi, jeudi, vendredi |**Jours :** samedi, dimanche |
| **Heure de début :** 7 h |**Heure de début :** 9 h |
| **Fuseau horaire :** UTC-08 |**Fuseau horaire :** UTC-08 |
|  | |
| **Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
| **Ressource :** pool de Workers 1 |**Ressource :** pool de Workers 1 |
| **Mesure :** Employés disponibles |**Mesure :** Employés disponibles |
| **Fonctionnement :** inférieur à 8 |**Fonctionnement :** inférieur à 3 |
| **Durée :** 20 minutes |**Durée :** 30 minutes |
| **Agrégation de temps :** moyenne |**Agrégation de temps :** moyenne |
| **Action :** augmenter le nombre de 8 |**Action :** augmenter le nombre de 3 |
| **Refroidissement (minutes) :** 180 |**Refroidissement (minutes) :** 180 |
|  | |
| **Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
| **Ressource :** Pool de Workers 1 |**Ressource :** Pool de Workers 1 |
| **Mesure :** Employés disponibles |**Mesure :** Employés disponibles |
| **Fonctionnement :** supérieur à 8 |**Fonctionnement :** supérieur à 3 |
| **Durée :** 20 minutes |**Durée :** 15 minutes |
| **Agrégation de temps :** moyenne |**Agrégation de temps :** moyenne |
| **Action :** diminuer le nombre de 2 |**Action :** diminuer le nombre de 3 |
| **Refroidissement (minutes) :** 120 |**Refroidissement (minutes) :** 120 |

La plage cible définie dans le profil est calculée par le nombre d’instances minimales du profil pour le plan App Service + la mémoire tampon.

La plage maximale est la somme du maximum de toutes les plages pour tous les plans App Service hébergés dans le pool de Workers.

L’augmentation correspondant aux règles de mise à l’échelle doit être définie comme au moins 1 fois le taux d’inflation du plan App Service pour la mise à l’échelle supérieure.

La réduction correspondant aux règles de mise à l’échelle doit être définie sur un chiffre compris entre 1/2 fois et 1 fois le taux d’inflation du plan App Service pour une mise à l’échelle inférieure.

### Mise à l’échelle automatique du pool frontal
Les règles de mise à l’échelle automatique des serveurs frontaux sont plus simples que pour les pools de Workers. Le plus important est de vous assurer que la durée de la mesure et des temps de recharge prenne en compte le fait que les opérations de mise à l’échelle sur un plan App Service ne sont pas instantanées.

Dans ce scénario, Frank sait que le taux d’erreur augmente une fois que les serveurs frontaux atteignent 80 % d’utilisation du processeur. Pour éviter cela, il définit la règle de mise à l’échelle automatique pour augmenter les instances comme suit :

![Paramètres de mise à l’échelle automatique du pool frontal.][Front-End-Scale]

| **Profil de l’échelle automatique : serveurs frontaux** |
| --- |
| **Nom :** Mise à l’échelle automatique – Serveurs frontaux |
| **Mise à l’échelle selon :** Planification et règles de performances |
| **Profil :** tous les jours |
| **Type :** périodicité |
| **Plage cible :** 3 à 10 instances |
| **Jours :** tous les jours |
| **Heure de début :** 9 h |
| **Fuseau horaire :** UTC-08 |
|  |
| **Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
| **Ressource :** Pool frontal |
| **Mesure :** % d’utilisation de l’unité centrale |
| **Fonctionnement :** supérieur à 60 % |
| **Durée :** 20 minutes |
| **Agrégation de temps :** moyenne |
| **Action :** augmenter le nombre de 3 |
| **Refroidissement (minutes) :** 120 |
|  |
| **Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
| **Ressource :** Pool de Workers 1 |
| **Mesure :** % d’utilisation de l’unité centrale |
| **Fonctionnement :** inférieur à 30 % |
| **Durée :** 20 minutes |
| **Agrégation de temps :** moyenne |
| **Action :** diminuer le nombre de 3 |
| **Refroidissement (minutes) :** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!---HONumber=AcomDC_0817_2016-->