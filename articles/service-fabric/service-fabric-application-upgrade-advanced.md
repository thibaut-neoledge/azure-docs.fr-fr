<properties
   pageTitle="Mise à niveau d'une application Service Fabric : rubriques avancées"
   description="Cet article traite de sujets avancés se rapportant à la mise à niveau d'une application Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>

# Mise à niveau d'une application Service Fabric : rubriques avancées

## Mode de mise à niveau manuelle

> [AZURE.NOTE]Le mode manuel non surveillé ne peut être envisagé que pour une mise à niveau ayant échoué ou suspendue. Le mode surveillé est le mode de mise à niveau recommandé pour les applications Service Fabric.

Service Fabric fournit plusieurs modes de mise à niveau pour prendre en charge les clusters de développement et de production. Chacune des options de déploiement convient particulièrement en fonction de l'environnement. La mise à niveau d'application propagée surveillée est la mise à niveau la plus courante à utiliser dans un environnement de production. Quand la stratégie de mise à niveau est spécifiée, Service Fabric s'assure que l'application est saine avant que la mise à niveau ne se poursuive. Dans certains cas, quand une stratégie d'évaluation d'intégrité plus complexe ou personnalisée est nécessaire ou qu'une mise à niveau non conventionnelle est appropriée (par exemple, si l'application est déjà victime d'une perte de données), l'administrateur d'application peut utiliser le mode de mise à niveau d'application propagée manuelle pour avoir un contrôle total de la progression de la mise à niveau via les différents domaines de mise à niveau. Enfin, la mise à niveau d'application propagée automatisée permet à un environnement de développement ou de test de fournir un cycle d'itération rapide pendant le développement du service.

**Mode manuel** : arrêter la mise à niveau de l'application au domaine de mise à niveau actuel et passer en mode de mise à niveau manuelle non surveillée. L'administrateur doit appeler manuellement **MoveNextApplicationUpgradeDomainAsync** pour procéder à la mise à niveau ou déclencher une restauration en initiant une nouvelle mise à niveau. Une fois que la mise à niveau est en mode manuel, elle demeure dans ce mode jusqu'à ce qu'une nouvelle mise à niveau soit initiée. La commande **GetApplicationUpgradeProgressAsync** renvoie FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Mise à niveau avec un package différentiel

Une application Service Fabric peut être mise à niveau via une mise en service avec un package d'application autonome et complet. Une application peut également être mise à niveau à l'aide d'un package différentiel qui contient uniquement les fichiers d'application mis à jour et les fichiers manifeste de service et manifeste d'application mis à jour.

Un package d'application complet contient tous les fichiers nécessaires pour démarrer et exécuter une application Service Fabric. Un package différentiel contient uniquement les fichiers qui ont changé entre la dernière mise en service et la mise à niveau actuelle, ainsi que les fichiers manifeste d'application et manifeste de service complets. Si Service Fabric ne parvient pas à trouver dans la disposition de la build une référence indiquée dans le manifeste d'application ou de service, il recherche cette référence dans le magasin d'images (lien à déterminer).

Des packages d'application complets sont nécessaires pour la première installation d'une application sur le cluster. Les mises à jour suivantes peuvent être un package d'application complet ou un package différentiel.

Situations qui se prêtent à l'utilisation d'un package différentiel :

* Un package différentiel est préférable quand vous disposez d'un package d'application volumineux qui référence plusieurs fichiers manifeste de service et/ou plusieurs packages de code, de configuration ou de données.

* Un package différentiel est préférable quand vous disposez d'un système de déploiement qui génère la disposition de la build directement à partir de votre processus de génération d'application. Dans ce cas, même si rien dans le code n'a changé, les assemblys nouvellement générés auront une somme de contrôle différente. Si vous utilisez un package d'application complet, vous devez mettre à jour la version installée sur tous les packages de code. Si vous utilisez un package différentiel, vous fournissez uniquement les fichiers qui ont changé et les fichiers manifeste dont la version a changé.

## Étapes suivantes

[Didacticiel de mise à niveau](service-fabric-application-upgrade-tutorial.md)

[Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md)

[Sérialisation des données](service-fabric-application-upgrade-data-serialization.md)

[Résolution des problèmes de mise à niveau d'une application](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=August15_HO6-->