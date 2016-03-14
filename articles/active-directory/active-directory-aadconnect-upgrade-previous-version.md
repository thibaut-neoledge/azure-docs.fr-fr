<properties
   pageTitle="Azure AD Connect : effectuer une mise à niveau depuis une version précédente | Microsoft Azure"
   description="Explique les différentes méthodes de mise à niveau vers la dernière version d’Azure Active Directory Connect, y compris la mise à niveau sur place et la migration « swing »."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="02/29/2016"
   ms.author="andkjell"/>

# Azure AD Connect : effectuer une mise à niveau d’une version précédente vers la dernière version
Cette rubrique décrit les différentes méthodes que vous pouvez utiliser pour mettre à niveau votre installation Azure AD Connect vers la dernière version. Nous vous recommandons d’utiliser systématiquement les dernières versions d’Azure AD Connect.

Si vous souhaitez effectuer une mise à niveau à partir de DirSync, consultez plutôt [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Il existe plusieurs stratégies pour mettre à niveau Azure AD Connect.

| Méthode | Description |
| --- | --- |
| [Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) | Pour les clients avec une installation rapide, il s’agit de la méthode la plus simple. |
| [Mise à niveau sur place](#in-place-upgrade) | Si vous avez un seul serveur, mettez à niveau l’installation sur place sur le même serveur. |
| [Migration « swing »](#swing-migration) | Avec deux serveurs, vous pouvez en préparer un avec la nouvelle version et changer de serveur actif quand vous êtes prêt.

Pour connaître les autorisations requises, consultez [Autorisations requises pour la mise à niveau](active-directory-aadconnect-accounts-permissions.md#upgrade).

## Mise à niveau sur place
Une mise à niveau sur place fonctionne à partir d’Azure AD Sync ou d’Azure AD Connect, mais pas pour DirSync ou une solution combinant FIM et Azure AD Connect.

Nous vous recommandons cette méthode si vous avez un seul serveur et moins de 100 000 objets environ. Après la mise à niveau, une importation et une synchronisation complètes se produisent pour que la nouvelle configuration s’applique à tous les objets existants dans le système. Cette opération peut prendre plusieurs heures selon le nombre d’objets dans l’étendue du moteur de synchronisation. La synchronisation différentielle normale planifiée, par défaut toutes les 30 minutes, est suspendue, mais la synchronisation des mots de passe continue. Vous pouvez envisager une mise à niveau sur place pendant le week-end.

![Mise à niveau sur place](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si vous avez apporté des modifications aux règles de synchronisation par défaut, celles-ci retrouvent leur configuration par défaut au moment de la mise à niveau. Pour conserver votre configuration d’une mise à niveau à l’autre, vérifiez que les modifications sont effectuées conformément aux [Meilleures pratiques pour modifier la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## Migration « swing »
Si vous avez un déploiement complexe ou un nombre d’objets très élevé, il peut être difficile d’effectuer une mise à niveau sur place sur le système réel. Pour certains clients, cette opération pourrait prendre plusieurs jours pendant lesquels aucune modification différentielle ne serait traitée.

Nous vous recommandons plutôt d’utiliser une migration « swing ». Pour cette méthode, vous devez avoir (au moins) deux serveurs : un serveur actif et un serveur intermédiaire. Le serveur actif (lignes bleues unies dans l’image ci-dessous) est responsable de la charge active. Le serveur intermédiaire (lignes en pointillés violettes dans l’image ci-dessous) est préparé avec la nouvelle version ; une fois prêt, il devient le serveur actif. Le serveur actif précédent, sur lequel est désormais installée l’ancienne version, devient le serveur intermédiaire et est mis à niveau.

![Serveur intermédiaire](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Remarque : Certains clients préfèrent avoir trois ou quatre serveurs pour cette opération. Pendant la mise à niveau du serveur intermédiaire, aucun serveur de sauvegarde n’est disponible en cas de [récupération d’urgence](active-directory-aadconnectsync-operations.md#disaster-recovery). Avec un maximum de quatre serveurs, un nouvel ensemble de serveurs principaux/de secours avec la nouvelle version peut être préparé ; ainsi, un serveur intermédiaire est toujours prêt à prendre le relais.

Ces étapes fonctionnent également à partir d’Azure AD Sync ou d’une solution combinant FIM et Azure AD Connect. Par contre, pour appliquer la même méthode de migration « swing » (également appelée déploiement en parallèle) dans le cas de DirSync, consultez [Mise à niveau d’Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### Étapes de la migration « swing »

1. Vérifiez que le serveur actif et le serveur intermédiaire utilisent la même version.
2. Si vous avez effectué une configuration personnalisée et que votre serveur intermédiaire ne l’a pas, suivez les étapes décrites sous [Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire](#move-custom-configuration-from-active-to-staging-server).
3. Mettez à niveau le serveur intermédiaire vers la dernière version.
4. Laissez le moteur de synchronisation effectuer une importation et une synchronisation complètes.
5. Vérifiez que la nouvelle configuration n’a pas entraîné de modifications inattendues à l’aide de la procédure indiquée sous **Vérifier** dans [Vérifiez la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). En cas d’anomalie, apportez la correction nécessaire, exécutez l’importation, effectuez une synchronisation, puis vérifiez que les données sont correctes. Au besoin, renouvelez la procédure. Vous trouverez ces étapes dans la rubrique liée.
6. Convertissez le serveur intermédiaire en serveur actif. C’est la dernière étape, **Changer de serveur actif**, de la procédure [Vérifiez la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Mettez à niveau le serveur actuellement en mode intermédiaire vers la dernière version. Suivez la même procédure pour mettre à niveau les données et la configuration.

### Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire
Si vous avez apporté des modifications à la configuration du serveur actif, vous devez vérifier que les mêmes modifications sont appliquées au serveur intermédiaire.

Les règles de synchronisation personnalisée que vous avez créées peuvent être déplacées avec PowerShell. Les autres modifications doivent être appliquées de la même façon sur les deux systèmes.

Voici les éléments qui doivent être configurés de la même façon sur les deux serveurs :

- Connexion aux mêmes forêts
- Tout filtrage de domaine et d’unité organisationnelle
- Fonctionnalités facultatives identiques, telles que la synchronisation de mot de passe et l’écriture différée du mot de passe

**Déplacer les règles de synchronisation** Pour déplacer une règle de synchronisation personnalisée, procédez comme suit :

1. Ouvrez l’**Éditeur de règles de synchronisation** sur votre serveur actif.
2. Sélectionnez votre règle personnalisée. Cliquez sur **Exporter**. Une fenêtre Bloc-notes apparaît. Enregistrez le fichier temporaire avec l’extension PS1 pour le convertir en script PowerShell. Copiez le fichier ps1 sur le serveur intermédiaire. ![Exportation des règles de synchronisation](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Le GUID du connecteur est différent sur le serveur intermédiaire. Pour obtenir le GUID, démarrez l’**Éditeur de règles de synchronisation**, sélectionnez une des règles par défaut représentant le même système connecté, puis cliquez sur **Exporter**. Remplacez le GUID dans votre fichier PS1 par le GUID se trouvant sur le serveur intermédiaire.
4. Dans une invite de PowerShell, exécutez le fichier PS1. Cette action crée la règle de synchronisation personnalisée sur le serveur intermédiaire.
5. Si vous avez plusieurs règles personnalisées, répétez la procédure pour chacune d’elles.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->