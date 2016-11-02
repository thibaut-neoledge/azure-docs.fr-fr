<properties
   pageTitle="Azure AD Connect : effectuer une mise à niveau depuis une version précédente | Microsoft Azure"
   description="Explique les différentes méthodes de mise à niveau vers la dernière version d’Azure Active Directory Connect, y compris la mise à niveau sur place et la migration « swing »."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="06/27/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect:-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect : effectuer une mise à niveau vers la dernière version
Cette rubrique décrit les différentes méthodes que vous pouvez utiliser pour mettre à niveau votre installation Azure AD Connect vers la dernière version. Nous vous recommandons d’utiliser systématiquement les dernières versions d’Azure AD Connect.

Si vous souhaitez effectuer une mise à niveau à partir de DirSync, consultez plutôt [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](./aad-connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Il existe plusieurs stratégies pour mettre à niveau Azure AD Connect.

Méthode | Description
--- | ---
[Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) | Pour les clients avec une installation rapide, il s’agit de la méthode la plus simple.
[Mise à niveau sur place](#in-place-upgrade) | Si vous avez un seul serveur, mettez à niveau l’installation sur place sur le même serveur.
[Migration « swing »](#swing-migration) | Avec deux serveurs, vous pouvez en préparer un avec la nouvelle version et changer de serveur actif quand vous êtes prêt.

Pour connaître les autorisations requises, consultez [Autorisations requises pour la mise à niveau](./aad-connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Mise à niveau sur place
Une mise à niveau sur place fonctionne à partir d’Azure AD Sync ou d’Azure AD Connect, mais pas pour DirSync ou une solution combinant FIM et Azure AD Connect.

Nous vous recommandons cette méthode si vous avez un seul serveur et moins de 100 000 objets environ. Après la mise à niveau, une importation et une synchronisation complètes se produisent si des modifications ont été apportées aux règles out-of-box de synchronisation. pour que la nouvelle configuration s’applique à tous les objets existants dans le système. Cette opération peut prendre plusieurs heures selon le nombre d’objets dans l’étendue du moteur de synchronisation. La synchronisation différentielle normale planifiée, par défaut toutes les 30 minutes, est suspendue, mais la synchronisation des mots de passe continue. Vous pouvez envisager une mise à niveau sur place pendant le week-end. S’il n’y a aucune modification de la configuration out-of-box avec la nouvelle version d’Azure AD Connect, une importation/synchronisation différentielle normale démarre à la place.  

![Mise à niveau sur place](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si vous avez apporté des modifications aux règles de synchronisation par défaut, celles-ci retrouvent leur configuration par défaut au moment de la mise à niveau. Pour conserver votre configuration d’une mise à niveau à l’autre, vérifiez que les modifications sont effectuées conformément aux [Meilleures pratiques pour modifier la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migration « swing »
Si vous avez un déploiement complexe ou un nombre d’objets très élevé, il peut être difficile d’effectuer une mise à niveau sur place sur le système réel. Pour certains clients, cette opération pourrait prendre plusieurs jours pendant lesquels aucune modification différentielle ne serait traitée.

Nous vous recommandons plutôt d’utiliser une migration « swing ». Pour cette méthode, vous devez avoir (au moins) deux serveurs : un serveur actif et un serveur de test. Le serveur actif (lignes bleues unies dans l’image ci-dessous) est responsable de la charge active. Le serveur de test (lignes en pointillés violettes dans l’image ci-dessous) est préparé avec la nouvelle version ; une fois prêt, il devient le serveur actif. Le serveur actif précédent, sur lequel est désormais installée l’ancienne version, devient le serveur de test et est mis à niveau.

Les deux serveurs peuvent utiliser différentes versions, par exemple le serveur actif que vous projetez de désactiver peut utiliser Azure AD Sync et le nouveau serveur intermédiaire peut utiliser Azure AD Connect.

![Serveur de test](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Remarque : Certains clients préfèrent avoir trois ou quatre serveurs pour cette opération. Pendant la mise à niveau du serveur intermédiaire, aucun serveur de sauvegarde n’est disponible en cas de [récupération d’urgence](active-directory-aadconnectsync-operations.md#disaster-recovery). Avec un maximum de quatre serveurs, un nouvel ensemble de serveurs principaux/de secours avec la nouvelle version peut être préparé ; ainsi, un serveur de test est toujours prêt à prendre le relais.

Ces étapes fonctionnent également à partir d’Azure AD Sync ou d’une solution combinant FIM et Azure AD Connect. En revanche, elles ne fonctionnent pas pour DirSync, mais on trouve cette même méthode de migration « swing » (également appelée déploiement parallèle) pour DirSync dans [Mise à niveau de la synchronisation Azure Active Directory (DirSync)](./aad-connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Étapes de la migration « swing »

1. Si vous utilisez Azure AD Connect sur les deux serveurs, assurez-vous que votre serveur actif et le serveur intermédiaire utilisent tous deux la même version avant de commencer la mise à niveau. Ceci facilitera une comparaison ultérieure des différences. Si vous mettez à niveau à partir d'Azure AD Sync, ces serveurs auront des versions différentes.
2. Si vous avez effectué une configuration personnalisée et que votre serveur intermédiaire ne l’a pas, suivez les étapes décrites sous [Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire](#move-custom-configuration-from-active-to-staging-server).
3. Si vous mettez à niveau depuis une version antérieure d'Azure AD Connect, mettez à niveau le serveur intermédiaire vers la dernière version. Si vous migrez à partir d'Azure AD Sync, installez Azure AD Connect sur votre serveur intermédiaire.
4. Laissez le moteur de synchronisation effectuer une importation et une synchronisation complètes sur votre serveur intermédiaire.
5. Vérifiez que la nouvelle configuration n’a pas entraîné de modifications inattendues à l’aide de la procédure indiquée sous **Vérifier** dans [Vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). En cas d’anomalie, apportez la correction nécessaire, exécutez l’importation, effectuez une synchronisation, puis vérifiez que les données sont correctes. Au besoin, renouvelez la procédure. Vous trouverez ces étapes dans la rubrique liée.
6. Convertissez le serveur de test en serveur actif. C’est la dernière étape, **Changer de serveur actif** , de la procédure [Vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si vous mettez à niveau Azure AD Connect, mettez à niveau le serveur actuellement en mode intermédiaire vers la dernière version. Suivez la même procédure pour mettre à niveau les données et la configuration. Si vous migrez à partir d'Azure AD Sync, vous pouvez maintenant désactiver et retirer votre ancien serveur.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire
Si vous avez apporté des modifications à la configuration du serveur actif, vous devez vérifier que les mêmes modifications sont appliquées au serveur de test.

Les règles de synchronisation personnalisée que vous avez créées peuvent être déplacées avec PowerShell. Les autres modifications doivent être appliquées de la même façon sur les deux systèmes et ne peuvent pas faire l’objet d’une migration.

Voici les éléments qui doivent être configurés de la même façon sur les deux serveurs :

- Connexion aux mêmes forêts
- Tout filtrage de domaine et d’unité organisationnelle
- Fonctionnalités facultatives identiques, telles que la synchronisation de mot de passe et l’écriture différée du mot de passe

**Déplacer les règles de synchronisation**  
 Pour déplacer une règle de synchronisation personnalisée, procédez comme suit :

1. Ouvrez l’ **Éditeur de règles de synchronisation** sur votre serveur actif.
2. Sélectionnez votre règle personnalisée. Cliquez sur **Exporter**. Une fenêtre Bloc-notes apparaît. Enregistrez le fichier temporaire avec l’extension PS1 pour le convertir en script PowerShell. Copiez le fichier ps1 sur le serveur intermédiaire.
![Exportation des règles de synchronisation](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Le GUID du connecteur est différent sur le serveur de test. Pour obtenir le GUID, démarrez **l’Éditeur de règles de synchronisation**, sélectionnez une des règles par défaut représentant le même système connecté, puis cliquez sur **Exporter**. Remplacez le GUID dans votre fichier PS1 par le GUID se trouvant sur le serveur de test.
4. Dans une invite de PowerShell, exécutez le fichier PS1. Cette action crée la règle de synchronisation personnalisée sur le serveur de test.
5. Si vous avez plusieurs règles personnalisées, répétez la procédure pour chacune d’elles.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Oct16_HO2-->


