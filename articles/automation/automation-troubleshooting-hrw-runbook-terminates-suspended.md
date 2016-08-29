<properties
   pageTitle="Runbook Worker hybride : une tâche de runbook se termine avec l’état suspendu | azure.microsoft.com/ Azure"
   description="Causes des problèmes et résolutions en cas d’erreur provoquant la fin de la tâche Runbook Worker hybride."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# Runbook Worker hybride : une tâche de runbook se termine avec l’état suspendu

## Résumé

Le runbook est interrompu peu après la tentative de l’exécuter trois fois. Il existe des conditions susceptibles d’interrompre l’exécution correcte du runbook et le message d’erreur lié n’inclut pas d’informations supplémentaires indiquant pourquoi. Cet article fournit des étapes de dépannage pour des problèmes concernant les échecs d’exécution du runbook worker hybride.

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.azure.microsoft.com/.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur [Twitter (@AzureSupport)](https://twitter.com/AzureSupport). Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.azure.microsoft.com/.com/support/options/).

## Symptôme

L’exécution du runbook échoue et l’erreur est retournée est « L’action de la tâche ’Activate’ ne peut pas être exécutée, car le processus s’est arrêté inopinément. L’action de la tâche a été tentée 3 fois. »


## Cause :

Il existe plusieurs causes possibles pour cette erreur :

  1. Le worker hybride est derrière un pare-feu ou un proxy
  2. L’ordinateur sur lequel le worker hybride s’exécute ne respecte pas les [exigences](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) de la configuration matérielle minimale
  3. Les runbooks ne peuvent pas s’authentifier auprès des ressources locales


## Cause 1 : Le runbook worker hybride est derrière un proxy ou un pare-feu

L’ordinateur sur lequel s’exécute le runbook worker hybride est derrière un pare-feu ou un serveur proxy, et un accès réseau sortant peut ne pas être autorisé ou configuré correctement.

### Solution

Vérifiez que l’ordinateur dispose d’un accès sortant à *.cloudapp.net sur les ports 443, 9354 et de 30000 à 30199.

## Cause 2 : L’ordinateur ne dispose pas de la configuration minimale requise

Les ordinateurs qui exécutent les workers hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Sinon, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et de la contention due aux runbooks lors de l’exécution, l’ordinateur sera surchargé, entraînant des retards ou des délais d’attente pour la tâche du runbook.

### Solution 

Vérifiez tout d’abord que l’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride répond à la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et de Windows. S’il existe une surcharge de la mémoire ou du processeur, cela peut indiquer la nécessité de mettre à niveau ou d’ajouter des processeurs supplémentaires, ou d’augmenter la mémoire pour résoudre le goulot d’étranglement des ressources et résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et évoluer lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

## Cause 3 : Les runbooks ne peuvent pas s’authentifier auprès des ressources locales

### Solution

Vérifiez dans le journal des événements **azure.microsoft.com/-SMA** la présence d’un événement correspondant avec la description *Le processus Win32 s’est terminé avec le code [4294967295]*. La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations d’identification Exécuter en tant que pour le groupe Worker hybride. Veuillez consulter les [autorisations du Runbook](automation-hybrid-runbook-worker.md#runbook-permissions) pour confirmer que l’authentification a été correctement configurée pour vos Runbooks.


 

<!---HONumber=AcomDC_0817_2016-->