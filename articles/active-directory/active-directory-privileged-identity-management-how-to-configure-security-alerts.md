<properties
   pageTitle="Configuration des alertes de sécurité | Microsoft Azure"
   description="Découvrez comment configurer des alertes de sécurité pour l’extension Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Comment configurer les alertes de sécurité dans Azure AD Privileged Identity Management

## Alertes de sécurité
Azure Privileged Identity Management (PIM) génère les alertes suivantes, qui peuvent être consultés dans la section Alertes du tableau de bord PIM.

| Alerte | Déclencheur | Recommandation |
| ----- | ------- | -------------- |
| **Activation permanente** | Un administrateur a été définitivement affecté à un rôle, en dehors de PIM. | Révisez la nouvelle attribution de rôle et rendez-la temporaire si nécessaire. |
| **Renouvellement d’activation de rôles privilégiés suspect** | Un trop grand nombre de réactivations du même rôle se sont produites par rapport à la durée autorisée dans les paramètres. | Contactez l'utilisateur pour vous assurer qu’il peut activer le rôle avec succès. |
| **L’authentification faible est configurée pour l’activation du rôle** | Les paramètres comportent des rôles sans authentification MFA. | Envisagez d’exiger l'authentification MFA pour l'activation de tous les rôles. |
| **Administrateurs redondants** | Certains administrateurs temporaires n'ont pas activé leurs rôles récemment. | Supprimer des attributions de rôles qui ne sont plus nécessaires. |
| **Trop d'administrateurs généraux** | Le nombre d’administrateurs généraux est supérieur au nombre recommandé. | Supprimez les affectations de rôles qui ne sont plus nécessaires, ou rendez certaines de ces affectations temporaires. |

## Configurez les paramètres d'alerte de sécurité

### Alerte « Renouvellement d’activation suspect de rôles privilégiés »

Configurez les paramètres **Période de renouvellement de l'activation** et **Nombre de renouvellements de l'activation** pour contrôler le déclenchement de cette alerte.

1. Sélectionnez **Alertes de sécurité** dans la section **Activité** du tableau de bord. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Définissez la **Période de renouvellement des activations** en déplaçant le curseur ou en entrant le nombre de minutes dans le champ de texte. La valeur maximale est 100.
4. Définissez le **Nombre de renouvellements des activations** pendant la période de renouvellement des activations en déplaçant le curseur ou en entrant le nombre de renouvellements dans le champ de texte. La valeur maximale est 100.
5. Cliquez sur **Enregistrer**.

### Alerte « Administrateurs redondants »
1. Sélectionnez **Alertes de sécurité** dans la section **Activité** du tableau de bord. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Sélectionnez le nombre de jours autorisés sans activation de rôle en ajustant le curseur ou en entrant le nombre de jours dans le champ de texte.
4. Cliquez sur **Enregistrer**.

### Alerte « Trop d'administrateurs généraux »

Il existe deux paramètres pouvant déclencher cette alerte :
- **Nombre minimal d’administrateurs généraux** déclenchera l’alerte si le nombre d’administrateurs est supérieur au nombre autorisé.
- **Pourcentage d'administrateurs généraux** déclenchera une alerte si le pourcentage d'administrateurs généraux est supérieur au nombre autorisé par les paramètres.

1. Sélectionnez **Alertes de sécurité** dans la section **Activité** du tableau de bord. Le panneau **Alertes de sécurité actives** s’affiche.
2. Cliquez sur **Paramètres**.
3. Définissez le **Nombre minimal d’administrateurs généraux** en ajustant le curseur ou en entrant le numéro dans le champ de texte.
4. Définissez le **Pourcentage d’administrateurs généraux** en ajustant le curseur ou en entrant le pourcentage dans le champ de texte.
5. Cliquez sur **Enregistrer**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->