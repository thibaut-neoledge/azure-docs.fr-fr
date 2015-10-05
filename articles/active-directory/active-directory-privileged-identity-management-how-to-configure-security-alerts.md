<properties
   pageTitle="Azure Privileged Identity Management : comment configurer des alertes de sécurité"
   description="Découvrez comment configurer des alertes de sécurité pour l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management : comment configurer des alertes de sécurité
## Vue d’ensemble des alertes de sécurité
Azure Privileged Identity Management propose les alertes configurables suivantes. Les alertes de sécurité peuvent être affichées dans la section Alertes du tableau de bord de PIM.

| Alerte | Déclencheur |
| ------------- | ------------- |
| **Attaque d’activation permanente suspectée** | Un administrateur a activé son rôle temporaire en dehors de PIM. |
| **Renouvellement d’activation de rôles privilégiés suspect** | Il y a eu trop de réactivations du même rôle par rapport à la durée autorisée dans les paramètres. |
| **Utilisation suspecte d’un utilisateur Administrateur général auquel est associé un jeton de miel** | L’utilisation d’un utilisateur « pot de miel » a été détectée.|
| **L’authentification faible est configurée pour l’activation du rôle** | Les paramètres comportent des rôles sans authentification MFA. |
| **Des administrateurs redondants augmentent votre surface d’attaque** | Des administrateurs temporaires n’ont pas activé leurs rôles dans le délai en jours défini dans les paramètres. |
| **Un nombre excessif d’administrateurs généraux augmentent votre surface d’attaque** | Le nombre d’administrateurs généraux est supérieur au nombre autorisé dans les paramètres. |

## Configuration des alertes de sécurité

### Configurer l’alerte « Renouvellement d’activation de rôles privilégiés suspect »
1. Dans la section **Activité** du tableau de bord, sélectionnez **Alertes de sécurité**. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Définissez la **Période de renouvellement des activations** en déplaçant le curseur ou en entrant le nombre de minutes dans le champ de texte. Le nombre maximal autorisé est 100.
4. Définissez le **Nombre de renouvellements des activations** pendant la période de renouvellement des activations en déplaçant le curseur ou en entrant le nombre de renouvellements dans le champ de texte. Le nombre maximal de renouvellements est 100.
5. Cliquez sur **Enregistrer**.

### Configurer l’alerte « Des administrateurs redondants augmentent votre surface d’attaque »
1. Dans la section **Activité** du tableau de bord, sélectionnez **Alertes de sécurité**. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Sélectionnez le nombre de jours autorisés sans activation de rôle en ajustant le curseur ou en entrant le nombre de jours dans le champ de texte.
4. Cliquez sur **Enregistrer**.

### Configurer l’alerte « Un nombre excessif d’administrateurs généraux augmentent votre surface d’attaque »

Cette alerte repose sur deux paramètres susceptibles de la déclencher. Le nombre minimal d’administrateurs généraux déclenche l’alerte si le nombre d’administrateurs est supérieur au nombre autorisé. Si le pourcentage d’administrateurs généraux par rapport à l’ensemble des types d’administrateurs est supérieur au pourcentage défini dans les paramètres, l’alerte est également déclenchée.

1. Dans la section **Activité** du tableau de bord, sélectionnez **Alertes de sécurité**. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Définissez le **Nombre minimal d’administrateurs généraux** en ajustant le curseur ou en entrant le numéro dans le champ de texte.
4. Définissez le **Pourcentage des administrateurs généraux** en ajustant le curseur ou en entrant le numéro dans le champ de texte.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->