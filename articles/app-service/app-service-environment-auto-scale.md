<properties 
	pageTitle="Mise à l’échelle automatique et environnement App Service" 
	description="Mise à l’échelle automatique et environnement App Service" 
	services="app-service"
	documentationCenter=""
	authors="btardif" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2016" 
	ms.author="byvinyal"
/>
	
#Mise à l’échelle automatique et environnement App Service

##Introduction
**Les environnements App Service ** prennent en charge la mise à l’échelle automatique. Ils rendent cette opération possible grâce à la mise à l’échelle automatique des pools de Workers individuels par le biais d’indicateurs de mesure ou de la planification.
 
![][intro]
 
La mise à l’échelle automatique vous permet d’optimiser l’utilisation des ressources en agrandissant et en réduisant automatiquement un **environnement App Service** afin de l’adapter à votre budget et/ou à votre profil de charge.

##Configuration de la mise à l’échelle automatique des pools de Workers 
Vous pouvez accéder à la fonctionnalité de mise à l’échelle automatique à partir de l’onglet Paramètres du **pool de Workers**.
 
![][settings-scale]

À partir de là, l’interface doit vous paraître assez familière, car elle est similaire à celle de la mise à l’échelle d’un **plan de Service d’application**. Vous pourrez saisir une valeur de mise à l’échelle manuellement
 
![][scale-manual]
 
Ou configurer un profil de mise à l’échelle automatique :
 
![][scale-profile]
 
Les profils de mise à l’échelle automatique sont utiles pour définir des limites à votre mise à l’échelle. Ainsi, vous pouvez obtenir des résultats satisfaisants en définissant une valeur de mise à l’échelle limite inférieure (1) et une valeur de plafonnement prévisible en définissant une limite supérieure (2).
 
![][scale-profile2]
 
Une fois qu’un profil est défini, les règles de mise à l’échelle automatique en fonction de mesures peuvent être ajoutées pour augmenter ou réduire le nombre d’instances dans le **pool de Workers** dans les limites définies par le profil.

![][scale-rule]

 Toutes les règles de mesure du **pool de Workers** ou du **serveur Front-end** peuvent être utilisées pour définir des règles de mise à l’échelle automatique. Ces mesures sont les mêmes que celles que vous pouvez surveiller dans les graphiques de panneau de ressource ou pour lesquelles vous pouvez définir une alerte.
 
##Exemple de mise à l’échelle automatique
La mise à l’échelle automatique d’un **environnement App Service** est mieux illustrée par un scénario. Dans cet article, nous allons examiner pas à pas toutes les étapes du paramétrage d’une mise à l’échelle automatique et toutes les interactions qui ont lieu lorsque nous réalisons une mise à l’échelle automatique d’**environnements App Service ** hébergés dans une ASE.

###Présentation du scénario
Frank est administrateur système pour une entreprise. Il a migré une partie des charges de travail qu’il gère vers un **environnement App Service **.

L’**environnement App Service** est configuré sur mise à l'échelle manuelle, comme suit :
* Serveurs frontaux : 3
* pool de Workers 1 : 10
* pool de Workers 2 : 5
* pool de Workers 3 : 5

Le **pool de Workers 1** est utilisé pour les charges de travail, tandis que le **pool de Workers 2** et le **pool de Workers 3** sont utilisés pour les charges de travail de développement et d’assurance qualité.

Les **plans de service d’application** utilisés pour l’assurance qualité et le développement sont configurés pour une **mise à l’échelle manuelle**, mais le **plan de service d’application** de production est défini sur **mise à l’échelle automatique** pour gérer les variations de charge et de trafic.

Frank connaît très bien l’application et sait que les heures de pointe de charge se situent entre 9 h 00 et 18 h 00, car il s’agit d’une **application métier** et qu’elle est utilisée par les employés lorsqu’ils sont au bureau. Le taux d’utilisation chute une fois que les utilisateurs ont fini leur journée de travail. Cependant, il existe encore une charge, car les utilisateurs peuvent y accéder à distance avec leurs appareils mobiles ou leurs ordinateurs domestiques. Le **plan de service d’application** de production est déjà configuré sur la **mise à l’échelle automatique** basée sur le taux d’utilisation de l’unité centrale avec les règles suivantes :

![][asp-scale]

|	**Profil de la mise à l’échelle automatique – Jours de semaine – Plan de service de l’application** |	** Profil de la mise à l’échelle automatique – Week-ends – Plan de Service de l’application** |
|	----------------------------------------------------	|	----------------------------------------------------	|
|	**Nom :** profil jour de semaine |	**Nom :** profil week-end |
|	**Mise à l’échelle selon :** Planification et règles de performances |	**Mise à l’échelle selon :** Planification et règles de performances |
|	**Profil :** jours de la semaine |	**Profil :** week-end |
|	**Type :** périodicité |	**Type :** périodicité |
|	**Plage cible :** 5 à 20 instances |	**Plage cible :** 3 à 10 instances |
|	**Jours :** lundi, mardi, mercredi, jeudi, vendredi |	**Jours :** samedi, dimanche |
|	**Heure de début :** 9 h 00 |	**Heure de début :** 9 h 00 |
|	**Fuseau horaire :** UTC - 08 |	**Fuseau horaire :** UTC - 08 |
| | |
|	**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |	**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
|	**Ressource :** Production (environnement App Service) |	**Ressource :** Production (environnement App Service) |
|	**Mesure :** % d’utilisation de l’unité centrale |	**Mesure :** % d’utilisation de l’unité centrale |
|	**Fonctionnement :** supérieur à 60 % |	**Fonctionnement :** supérieur à 80 % |
|	**Durée :** 5 minutes |	**Durée :** 10 minutes |
|	**Agrégation de temps :** moyenne |	**Agrégation de temps :** moyenne |
|	**Action :** augmenter le nombre de 2 |	**Action :** augmenter le nombre de 1 |
|	**Refroidissement (minutes) :** 15 |	**Refroidissement (minutes) :** 20 |
| | |
|	**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |	**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
|	**Ressource :** Production (environnement App Service) |	**Ressource :** Production (environnement App Service) |
|	**Mesure :** % d’utilisation de l’unité centrale |	**Mesure :** % d’utilisation de l’unité centrale |
|	**Fonctionnement :** inférieur à 30 % |	**Fonctionnement :** inférieur à 20 % |
|	**Durée :** 10 minutes |	**Durée :** 15 minutes |
|	**Agrégation de temps :** moyenne |	**Agrégation de temps :** moyenne |
|	**Action :** diminuer le nombre de 1 |	**Action :** diminuer le nombre de 1 |
|	**Refroidissement (minutes) :** 20 |	**Refroidissement (minutes) :** 10 |

###Taux d’inflation du plan de service de l’application
Les **plans de service de l’application** sont configurés pour une mise à l’échelle automatique, et fonctionneront ainsi au taux maximal par heure. Cette vitesse peut être calculée en fonction des valeurs fournies sur la règle de mise à l’échelle automatique.

La compréhension et le calcul du **taux d’inflation du plan de service d’application ** est importante pour la mise à l’échelle du **pool de Workers** de l’**environnement de Service d’application** car les modifications d’un **pool de Workers** ne sont pas instantanées et prennent un certain temps à s’appliquer.

Le taux d’inflation du **plan de service d’application** est calculé comme suit :

![][ASP-Inflation]

Si l’on prend la règle *mise à l’échelle automatique - Mise à l’échelle supérieure* du profil *Jour de semaine * le **plan de service d’application** de production devrait se présenter comme suit :

![][Equation1]

Dans le cas de la règle *mise à l’échelle automatique – Mise à l’échelle supérieure* pour le profil *week-ends*, la formule du **plan du service d’application** de production se présentera ainsi :

![][Equation2]

Cette valeur peut également être calculée pour les opérations de réduction :

Selon la règle de *mise à l’échelle automatique - Mise à l’échelle inférieure* pour le profil *jour de semaine*, le **plan de service d’application** de production se présente comme suit :

![][Equation3]

Dans le cas de la règle de *mise à l’échelle automatique – Mise à l’échelle inférieure* pour le profil *week-ends*, la formule du **plan de service d’application** de production se présente comme suit :

![][Equation4]

Cela signifie que le **plan de service d’application** de production peut augmenter d’un taux maximal de **8** instances par heure durant la semaine et de **4** instances par heure durant le week-end. Et il peut libérer des instances à un taux maximal de **4** instances par heure durant la semaine et de **6** instances par heure durant les week-ends.

Si plusieurs **plans App Service** sont hébergés dans un **pool de Workers**, le **taux total d’inflation** doit être calculé et représenter la *somme* du taux d’inflation de tous les **plans App Service** hébergés dans ce **pool de Workers**.

![][ASP-Total-Inflation]

###Utilisation du taux d’inflation du plan de service d’application pour définir des règles de mise à l’échelle automatique du pool de Workers
Les **pools de Workers** qui hébergent **des plans de service d’application** configurés avec mise à l’échelle automatique devront recevoir un espace tampon d’une capacité permettant des opérations d’augmentation/de réduction de mise à l’échelle automatique du **plan de service d’application** en fonction des besoins. La mémoire tampon minimale correspondrait au **taux total d’inflation du plan de service d’application** calculé.

Comme les opérations de mise à l’échelle de **l’environnement de service d’application** prennent un certain temps, toute modification doit tenir compte des demandes de modification pouvant se produire lorsqu’une opération de mise à l’échelle est en cours. Pour ce faire, nous recommandons l’utilisation du **Taux d’inflation du plan de service d’application total** calculé en tant que nombre minimal d’instances ajoutées pour chaque Opération de mise à l’échelle automatique.

Avec cette information, Frank peut définir le profil et les règles de mise à l’échelle automatique suivants

![][Worker-Pool-Scale]

|	**Profil de mise à l’échelle automatique : jours ouvrables** |	**Profil de mise à l’échelle automatique : les week-ends** |
|	----------------------------------------------------	|	--------------------------------------------	|
|	**Nom :** profil jour de semaine |	**Nom :** profil week-end |
|	**Mise à l’échelle selon :** Planification et règles de performances |	**Mise à l’échelle selon :** Planification et règles de performances |
|	**Profil :** jours de la semaine |	**Profil :** week-end |
|	**Type :** périodicité |	**Type :** périodicité |
|	**Plage cible :** 13 à 25 instances |	**Plage cible :** 6 à 15 instances |
|	**Jours :** lundi, mardi, mercredi, jeudi, vendredi |	**Jours :** samedi, dimanche |
|	**Heure de début :** 7 h 00 |	**Heure de début :** 9 h 00 |
|	**Fuseau horaire :** UTC - 08 |	**Fuseau horaire :** UTC - 08 |
| | |
|	**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |	**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
|	**Ressource :** pool de Workers 1 |	**Ressource :** pool de Workers 1 |
|	**Mesure :** Employés disponibles |	**Mesure :** Employés disponibles |
|	**Fonctionnement :** inférieur à 8 |	**Fonctionnement :** inférieur à 3 |
|	**Durée :** 20 minutes |	**Durée :** 30 minutes |
|	**Agrégation de temps :** moyenne |	**Agrégation de temps :** moyenne |
|	**Action :** augmenter le nombre de 8 |	**Action :** augmenter le nombre de 3 |
|	**Refroidissement (minutes) :** 180 |	**Refroidissement (minutes) :** 180 |
| | |
|	**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |	**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
|	**Ressource :** pool de Workers 1 |	**Ressource :** pool de Workers 1 |
|	**Mesure :** Employés disponibles |	**Mesure :** Employés disponibles |
|	**Fonctionnement :** supérieur à 8 |	**Fonctionnement :** supérieur à 3 |
|	**Durée :** 20 minutes |	**Durée :** 15 minutes |
|	**Agrégation de temps :** moyenne |	**Agrégation de temps :** moyenne |
|	**Action :** diminuer le nombre de 2 |	**Action :** diminuer le nombre de 3 |
|	**Refroidissement (minutes) :** 120 |	**Refroidissement (minutes) :** 120 |

La plage cible définie dans le profil est calculée par le nombre d’instances minimales du profil pour le **plan de service d’application** + la mémoire tampon.

La plage maximale est la somme du maximum de toutes les plages pour tous les **plans de service d’application** hébergés dans le **pool de Workers**.

L’augmentation correspondant aux règles de mise à l’échelle doit être définie comme au moins 1 fois le **taux d’inflation du plan App Service** pour la mise à l’échelle supérieure.

La réduction correspondant aux règles de mise à l’échelle doit être définie sur un chiffre compris entre 1/2 X et 1 X le **taux d’inflation du plan de service d’application** pour une mise à l’échelle inférieure.

###Mise à l’échelle automatique de pool Front-end
Les règles de mise à l’échelle automatique du **serveur Front-end** sont plus simples que celles qui s’appliquent aux **pools de Workers**. Il faut s’assurer que la durée des mesures et les minuteurs de refroidissement tiennent compte du fait que la mise à l’échelle des opérations sur un **plan de service d’application** n’est pas instantanée.

Pour ce scénario, Frank sait que le taux d’erreur augmente une fois que les serveurs frontaux ont atteint un taux d’utilisation du processeur de 80 %. Pour éviter ce problème, il définit la règle de mise à l’échelle automatique pour augmenter le nombre d’instances comme suit :
 
![][Front-End-Scale]
  
|	**Profil de l’échelle automatique : serveurs frontaux** |
|	--------------------------------------------	|
|	**Nom :** Mise à l’échelle automatique – serveurs frontaux |
|	**Mise à l’échelle selon :** Planification et règles de performances |
|	**Profil :** tous les jours |
|	**Type :** périodicité |
|	**Plage cible :** 3 à 10 instances |
|	**Jours :** tous les jours |
|	**Heure de début :** 9 h 00 |
|	**Fuseau horaire :** UTC - 08 |
| |
|	**Règle de mise à l’échelle automatique (mise à l’échelle supérieure)** |
|	**Ressource :** Pool Front-end |
|	**Mesure :** % d’utilisation de l’unité centrale |
|	**Fonctionnement :** supérieur à 60 % |
|	**Durée :** 20 minutes |
|	**Agrégation de temps :** moyenne |
|	**Action :** augmenter le nombre de 3 |
|	**Refroidissement (minutes) :** 120 |
| |
|	**Règle de mise à l’échelle automatique (mise à l’échelle inférieure)** |
|	**Ressource :** pool de Workers 1 |
|	**Mesure :** % d’utilisation de l’unité centrale |
|	**Fonctionnement :** inférieur à 30 % |
|	**Durée :** 20 minutes |
|	**Agrégation de temps :** moyenne |
|	**Action :** diminuer le nombre de 3 |
|	**Refroidissement (minutes) :** 120 |

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

<!---HONumber=AcomDC_0518_2016-->