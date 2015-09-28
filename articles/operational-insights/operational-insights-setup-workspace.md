<properties
    pageTitle="Configuration de votre espace de travail et gestion des paramètres"
    description="Découvrez comment configurer votre espace de travail et gérer les paramètres de Microsoft Azure Operational Insights"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/10/2015"
    ms.author="banders"/>

# Configuration de votre espace de travail et gestion des paramètres

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Pour créer un nouvel espace de travail Microsoft Azure Operational Insights, vous choisissez un nom d’espace de travail et l’associez à votre compte, puis vous choisissez un emplacement géographique. Un espace de travail Operational Insights est en fait un conteneur qui inclut des informations de compte et des informations de configuration simple pour le compte. Vous ou d’autres membres de votre organisation peuvent utiliser plusieurs espaces de travail Operational Insights pour gérer différents ensembles de données provenant de l’ensemble ou de certaines parties de votre infrastructure informatique.

Après la création de l’espace de travail, vous pouvez effectuer d’autres tâches à l’aide de l’espace de travail, par exemple : gérer Operational Insights, ajouter une solution, connecter des sources de données, ajouter des journaux, sélectionner des comptes de stockage, afficher vos données d’utilisation sur le tableau de bord et gérer les paramètres de chaque espace de travail.

L’article [Embarquez en quelques minutes](./operational-insights-onboard-in-minutes.md) vous explique comment procéder rapidement à l’installation et l’exécution. Le reste de l’article décrit de manière détaillée les actions nécessaires pour terminer l’installation et gérer votre espace de travail.

Nous aborderons toutes les tâches courantes que vous utilisez dans les sections suivantes :

1. Ajouter des solutions
2. Connecter des sources de données
3. Ajouter et gérer des journaux
4. Gérer les comptes et les utilisateurs

![étapes](./media/operational-insights-setup-workspace/steps.png)
## 1 Ajouter des solutions

Microsoft Azure Operational Insights inclut une fonctionnalité d’évaluation de la configuration. Ainsi, il n’est pas nécessaire d’installer une solution à ces fins. Toutefois, vous pouvez obtenir des fonctionnalités supplémentaires en y ajoutant des solutions à partir des pages Paramètres ou Galerie de solutions.

Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service Operational Insights. Le traitement des données par le service Operational Insights peut prendre de quelques minutes à plusieurs heures. Une fois les données traitées par le service, vous pouvez les consulter dans Operational Insights.

Vous pouvez facilement supprimer une solution quand vous n'en avez plus besoin. Quand vous supprimez une solution, ses données ne sont pas envoyées au service Operational Insights, ce qui réduit la quantité de données utilisées par rapport à votre quota quotidien.

### Solutions prises en charge par Microsoft Monitoring Agent

Pour le moment, les serveurs qui sont connectés directement à Microsoft Azure Operational Insights à l'aide de Microsoft Monitoring Agent peuvent utiliser la plupart des solutions disponibles, notamment :

- [Mises à jour du système](operational-insights-updates.md)
- [Logiciel anti-programme malveillant](operational-insights-antimalware.md)
- [Suivi des modifications](operational-insights-change-tracking.md)
- [Évaluation de SQL et d'Active Directory](operational-insights-assessment.md)

Les solutions suivantes ne sont toutefois *pas* prises en charge par Microsoft Monitoring Agent et requièrent System Center Operations Manager (SCOM).

- [Gestion de la capacité](operational-insights-capacity.md)
- [Gestion des alertes](operational-insights-alerts.md)
- [Évaluation de la configuration](operational-insights-solutions.md#configuration-assessment)

Reportez-vous à [Considérations relatives à Operations Manager avec Operational Insights](operational-insights-operations-manager.md) pour obtenir des conseils sur l’utilisation de ces solutions avec Operations Manager.

La collecte de journaux IIS est prise en charge sur les ordinateurs équipés de :

- Windows Server 2012
- Windows Server 2012 R2

### Pour ajouter des solutions à l’aide de la page des paramètres

- Sélectionnez les solutions que vous souhaitez ajouter et cliquez sur **Ajouter les solutions sélectionnées**. Certaines solutions disponibles ne sont pas affichées ici. Si vous souhaitez ajouter des solutions qui ne sont pas répertoriées, utilisez la procédure suivante.![ajouter des solutions](./media/operational-insights-setup-workspace/settings-add-sol.png)

### Pour ajouter une solution à l’aide de la galerie de solutions

1. Dans la page d’aperçu d’Operational Insights, cliquez sur la vignette **Galerie des solutions**.![image de l'icône de solutions](./media/operational-insights-setup-workspace/sol-gallery.png)
2. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'Operational Insights. Cliquez sur le nom de la solution que vous souhaitez ajouter à Operational Insights.
3. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Ajouter**.
4. Dans la page de confirmation, cliquez sur **Accepter** pour accepter les conditions d'utilisation et la déclaration de confidentialité.
5. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d'aperçu dans Operational Insights. Vous pouvez commencer à l'utiliser une fois que le service Operational Insights a traité vos données.

### Pour supprimer une solution à l’aide de la galerie de solutions

1. Dans la page d'aperçu d'Operational Insights, cliquez sur la vignette **Galerie des solutions**.
2. Dans la page Galerie des solutions Operational Insights, cliquez sur **Supprimer** sous la solution que vous souhaitez supprimer.
3. Dans la page de confirmation, cliquez sur **Oui** pour supprimer la solution.

## 2 Connecter des sources de données

Il existe trois manières de connecter des sources de données :

- Relier directement des ordinateurs à Operational Insights. Voir [Connexion directe des ordinateurs à Operational Insights](./operational-insights-direct-agent.md) pour plus d’informations.![relier directement](./media/operational-insights-setup-workspace/attach-directly.png)
- Associer des groupes d’administration Operations Manager. Voir [Connexion à Operational Insights à partir de System Center Operations Manager](./operational-insights-connect-scom.md) pour plus d’informations.![relier Operations Manager](./media/operational-insights-setup-workspace/attach-om.png)
- Associer un compte de stockage Azure. Voir [Analyse des données des serveurs dans Microsoft Azure](./operational-insights-analyze-data-azure.md) pour plus d’informations.![relier Azure](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 Ajouter et gérer des journaux

Avant d’ajouter des journaux, vous devez disposer d’une solution qui utilise les données de journaux installées. Vous pouvez ensuite ajouter de nouveaux journaux pour collecter des événements et choisir les niveaux ou la gravité des événements que vous souhaitez collecter dans les journaux. Vous pouvez collecter :

- Journaux d’événements Windows
- Journaux IIS
- Autres journaux que vous avez ajoutés

![ajouter des journaux](./media/operational-insights-setup-workspace/collect-logs.png)

### Format de fichier journal IIS

Le seul format de journal IIS actuellement pris en charge est W3C. Ne vous inquiétez pas, c’est le format le plus courant et le format par défaut dans IIS 7 et IIS 8. Par conséquent, si vous vous connectez au format natif NCSA ou IIS, Operational Insights n’acceptera pas ces journaux. Même pour le format W3C, vous verrez que tous les champs ne sont pas journalisés par défaut. Pour en savoir plus sur le format, consultez la page [Sélection des champs W3C à consigner (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx).


> [AZURE.TIP]Pour une expérience de recherche de journaux optimale, nous vous recommandons de sélectionner tous les champs de journalisation pour chaque site web à l’aide de la **Journalisation** dans IIS. Nous vous recommandons également de modifier le calendrier de **Substitution de fichier journal** pour les nouveaux journaux sur **Toutes les heures**, afin de télécharger de plus petits fichiers sur le cloud et économiser ainsi de la bande passante.


### Pour collecter des journaux des événements Windows à partir d’Operations Manager ou d’agents directement connectés

1. Sur la page **Aperçu**, cliquez sur la vignette **Paramètres**, puis cliquez sur l’onglet **Journaux**.
2. Tapez le nom du journal des événements à partir duquel vous souhaitez collecter des informations. Si vous n’êtes pas sûr du nom à utiliser, sélectionnez les propriétés du journal des événements Windows dans l’**Observateur d’événements**, copiez le nom du champ **FullName**, puis collez-le dans la zone **Collecter les événements depuis les journaux d’événements suivants**.
3. Cliquez sur **+** pour ajouter le journal.
4. Sélectionnez les niveaux ou la gravité d’événements que vous souhaitez collecter dans le journal. Les événements de **Succès de l’audit** et d’**Échec de l’audit** ne sont pas pris en charge dans cette version.
5. Répétez les étapes précédentes pour chaque journal à partir duquel vous souhaitez collecter des informations, puis cliquez sur **Enregistrer**.
6. Les événements apparaissent dans Operational Insights en quelques minutes, et vous pouvez ensuite rechercher les données.

### Collection des journaux IIS à partir d’Operations Manager ou d’agents directement connectés

1. Sur la page **Aperçu**, cliquez sur la vignette **Paramètres**, puis cliquez sur l’onglet **Journaux**.
2. Dans l’onglet **Journaux**, sous **Journaux des événements**, sélectionnez ** Collecter les journaux depuis Operations Manager**.


### Pour collecter des journaux IIS et/ou des événements Windows à partir d'Azure Diagnostics
Vous pouvez configurer ce paramètre à partir du portail de gestion Azure et non du portail Operational Insights. Dans votre espace de travail, accédez à l’onglet **Stockage**. Vous pouvez activer la collecte de journaux à partir de ce compte de stockage.

### Après avoir configuré la collecte de journaux
Une fois la collecte de journaux configurée, votre stratégie de collecte des journaux est envoyée aux agents, directement ou via des groupes de gestion, et le service lance la collecte des événements.

Vous pouvez accéder à l’analyse initiale des événements de journaux collecté à partir des serveurs surveillés depuis la page **Utilisation**.

![image de la vignette de la page d’utilisation](./media/operational-insights-setup-workspace/usage.png)


## 4 Gérer les comptes et les utilisateurs
Gérez les comptes et les utilisateurs depuis l’onglet **Comptes** sur la page Paramètres. Vous pouvez alors exécuter les tâches suivantes.

![onglet Comptes](./media/operational-insights-setup-workspace/manage-users.png)

## Ajout d’un utilisateur à un espace de travail existant


Procédez comme suit pour ajouter un utilisateur ou un groupe à un espace de travail Operational Insights. L’utilisateur ou le groupe sera en mesure d’afficher et d’agir sur toutes les alertes associées à cet espace de travail.

>[AZURE.NOTE]Si vous souhaitez ajouter un utilisateur ou un groupe à partir de votre compte professionnel Azure Active Directory, vous devez vous assurer que vous avez associé votre compte Operational Insights à votre domaine Active Directory. Voir [Ajout d’une organisation Azure Active Directory à un espace de travail existant](#).

### Pour ajouter un utilisateur à un espace de travail existant
1. Dans Operational Insights, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**.
3. Dans la section **Gérer les utilisateurs**, sélectionnez le type de compte à ajouter : **Compte d’organisation** ou **Compte Microsoft**.
    - Si vous choisissez Compte Microsoft, saisissez l’adresse électronique de l’utilisateur associé au compte Microsoft.
    - Si vous choisissez Compte d’organisation, saisissez une partie du nom de l’utilisateur ou du groupe ou l’alias de messagerie électronique. Une liste d’utilisateurs et de groupes s’affiche alors. Sélectionnez un utilisateur ou un groupe.
        >[AZURE.NOTE]Pour de meilleurs résultats, limitez à deux le nombre de groupes Active Directory associés à un seul compte Operational Insights : un pour les administrateurs et un pour les utilisateurs. L’utilisation de plusieurs groupes peut avoir une incidence sur les performances d’Operational Insights.
7. Choisissez le type d’utilisateur ou de groupe à ajouter : **Administrateur** ou **Utilisateur**.  
8. Cliquez sur **Ajouter**.

  Si vous ajoutez un compte Microsoft, une invitation à joindre l’espace de travail est envoyée à l’adresse électronique que vous avez fournie. Après avoir suivi les instructions pour rejoindre Operational Insights, l’utilisateur peut visualiser les alertes et les informations du compte Operational Insights et vous pourrez afficher les informations de l’utilisateur dans l’onglet **Comptes**, sur la page **Paramètres**. Si vous ajoutez un compte professionnel, l’utilisateur pourra accéder à Operational Insights immédiatement. ![invitation](./media/operational-insights-setup-workspace/manage-users04.png)


### De combien d’espaces de travail ai-je besoin ?
Un espace de travail est considéré comme une ressource Azure dans le portail de gestion Azure.

Vous pouvez créer un espace de travail ou créer un lien vers un espace de travail existant, ouvert précédemment pour une utilisation avec System Center Operations Manager, mais pas encore associé à un abonnement Azure (nécessaire pour la facturation). Un espace de travail représente le niveau auquel les données sont collectées, agrégées, analysées et présentées dans le portail Operational Insights. Vous pouvez choisir plusieurs espaces de travail pour séparer les données de différents environnements et systèmes ; chaque groupe d’administration Operations Manager (et tous ses agents) ou des machines virtuelles/agents ne peuvent être connectés qu’avec un seul espace de travail.

Chaque espace de travail peut être associé à plusieurs comptes utilisateur et chaque compte utilisateur (compte Microsoft ou compte professionnel) peut également avoir accès à plusieurs espaces de travail Operational Insights. Par défaut, le compte Microsoft ou le compte professionnel utilisé pour créer l’espace de travail devient l’administrateur de l’espace de travail. L’administrateur peut ensuite inviter d’autres comptes Microsoft ou choisir les utilisateurs à partir de son Azure Active Directory.

## Liaison d’un espace de travail existant à un abonnement Azure

Il est possible de créer un espace de travail à partir de [microsoft.com/oms](https://microsoft.com/oms). Toutefois, ces espaces de travail disposent de certaines limites, la plus notable étant celle de 500 Mo de téléchargement de données par jour si vous utilisez un compte gratuit. Pour apporter des modifications à cet espace de travail, vous devez **lier votre espace de travail existant à un abonnement Azure**.

>[AZURE.IMPORTANT]Pour lier un espace de travail, votre compte Azure doit déjà avoir accès à l’espace de travail que vous souhaitez lier. En d’autres termes, le compte que vous utilisez pour accéder au portail Azure doit être **le même** que le compte que vous utilisez pour accéder à votre espace de travail Operational Insights. Si ce n’est pas le cas, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Connectez-vous au portail de gestion Azure.
2. Cliquez sur **+ Nouveau** dans le coin inférieur gauche du portail.
3. Cliquez sur **App Services**, accédez à **Operational Insights** et sélectionnez-le.
4. Cliquez sur **Create**.
5. Sur la liste des **Comptes**, vous devez voir une liste de vos espaces de travail existants qui n’ont *pas encore* été liés à votre abonnement Azure. Sélectionnez un compte.

  >[AZURE.NOTE]Si l’espace de travail que vous souhaitez lier ne s’affiche pas ici, cela signifie que votre abonnement Azure n’a pas accès à votre espace de travail Operational Insights. Vous devez accorder l’accès à ce compte à partir de votre espace de travail Operational Insights. Pour ce faire, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).

  ![link account](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Renseignez les champs restants, puis sélectionnez **Créer un espace de travail**.

## Mise à niveau de l’espace de travail vers un plan de données payant

Il existe trois types de plans de données d’espace de travail pour Operational Insights : **Gratuit**, **Standard** et **Premium**. Si vous utilisez un plan *gratuit*, vous avez peut-être atteint votre plafond de données de 500 Mo. Vous devez mettre à niveau votre espace de travail vers un « **plan avec paiement à l’utilisation** » afin de collecter des données au-delà de cette limite. Vous pouvez convertir votre type de plan à tout moment. Pour plus d’informations sur la tarification d’Operational Insights, consultez la page [Tarification](https://azure.microsoft.com/fr-FR/pricing/details/operational-insights/).

>[AZURE.IMPORTANT]Les plans d’espace de travail peuvent uniquement être modifiés s’ils sont *liés* à un abonnement Azure. Si vous avez créé votre espace de travail dans Azure ou si vous avez *déjà* lié votre espace de travail, vous pouvez ignorer ce message. Si vous avez créé votre espace de travail à partir de [opinsights.azure.com](http://opinsights.azure.com), vous devez suivre les étapes de [Liaison d’un espace de travail existant à un abonnement Azure](#link-an-existing-workspace-to-an-Azure-subscription).

### Modification du type de plan

Dans le portail de gestion Azure, accédez à l’espace de travail Operational Insights vers lequel vous souhaitez effectuer une mise à niveau niveau :

![scale](./media/operational-insights-setup-workspace/find-workspace.png)

Sélectionnez cet espace de travail et sélectionnez **MISE À L’ÉCHELLE** dans les onglets en haut de l’écran

![select scale](./media/operational-insights-setup-workspace/select-scale.png)

Enfin, sélectionnez le plan que vous souhaitez mettre à niveau, puis cliquez sur **ENREGISTRER**. Les modifications sont reflétées dans le portail et vous pouvez désormais collecter des données au-delà du plafond de données du plan « gratuit ».

![select plan](./media/operational-insights-setup-workspace/plan-select.png)

## Ajout d’une organisation Azure Active Directory à un espace de travail existant

Vous pouvez associer votre espace de travail Operational Insights à un domaine Azure Active Directory. Vous pouvez ainsi ajouter des utilisateurs à partir d’Active Directory directement à votre espace de travail Operational Insights, sans compte Microsoft distinct.

### Pour ajouter une organisation Azure Active Directory à un espace de travail existant

1. Sur la page Paramètres d’Operational Insights, cliquez sur **Comptes**, puis sur **Ajouter une organisation**.![invitation](./media/operational-insights-setup-workspace/add-org.png)
2. Consultez les informations sur les comptes d’organisation, puis cliquez sur **Suivant**.
3. Entrez les informations d’identité de l’administrateur de votre domaine Azure Active Directory, puis cliquez sur **Se connecter**.
4. Cliquez sur **Autoriser l’accès** pour permettre à Operational Insights d’utiliser les informations d’identité de votre domaine Active Directory. ![linked](./media/operational-insights-setup-workspace/ad-existing01.png)


## Modification d’un type d’utilisateur existant

Vous pouvez modifier le rôle de compte pour un utilisateur associé à votre compte Operational Insights. Vous pouvez choisir parmi les rôles suivants :

 - *Administrateur* : peut gérer les utilisateurs, afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs

 - *Utilisateur* : peut afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs

### Pour modifier un compte
1. Sur la page **Paramètres**, dans l’onglet **Comptes** dans Operational Insights, sélectionnez le rôle de l’utilisateur que vous souhaitez modifier.
2. Cliquez sur **OK**.

## Suppression d’un utilisateur d’un espace de travail Operational Insights

Procédez comme suit pour supprimer un utilisateur d’un espace de travail Operational Insights. Notez que cela ne ferme pas l’espace de travail de l’utilisateur. Au lieu de cela, cela supprime l’association entre cet utilisateur et l’espace de travail. Si un utilisateur est associé à plusieurs espaces de travail, il reste en mesure de se connecter à Operational Insights.

### Pour supprimer un utilisateur d’un espace de travail

1. Sur la page **Paramètres**, dans l’onglet **Comptes** d’Operational Insights, cliquez sur Supprimer, en regard du nom d’utilisateur que vous souhaitez supprimer.
2. Cliquez sur **OK** pour confirmer que vous souhaitez supprimer l’utilisateur.

## Fermeture de votre espace de travail Operational Insights

Lorsque vous fermez un espace de travail Operational Insights, toutes les données relatives à votre espace de travail sont supprimées du service Operational Insights dans les 30 jours suivant la fermeture de l’espace de travail.

Si vous êtes administrateur et que plusieurs utilisateurs sont associés à l’espace de travail, l’association entre les utilisateurs et l’espace de travail est rompue. Si les utilisateurs sont associés à d’autres espaces de travail, ils peuvent continuer à utiliser Operational Insights avec ces autres espaces de travail. Toutefois, s’ils ne sont pas associés à des espaces de travail, ils devront créer un espace de travail pour utiliser Operational Insights.

### Pour fermer un espace de travail Operational Insights

1. Sur la page **Paramètres**, dans l’onglet **Comptes** d’Operational Insights, cliquez sur **Fermer l’espace de travail**.

2. Sélectionnez un des motifs de fermeture de votre espace de travail ou saisissez une autre raison dans la zone de texte.

3. Cliquez sur **Fermer l’espace de travail**.

## Ressources supplémentaires
- [Exigences de format de journal IIS dans Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Consultez les autres sources de données et types de journaux que la communauté nous demande de mettre en œuvre dans notre [Forum de commentaires](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

<!---HONumber=Sept15_HO3-->