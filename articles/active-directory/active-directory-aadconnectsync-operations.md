---
title: 'Azure AD Connect sync : tâches et examen opérationnels | Microsoft Docs'
description: Cette rubrique décrit les tâches opérationnelles de la synchronisation d’Azure AD Connect et explique comment se préparer pour utiliser ce composant.
services: active-directory
documentationcenter: ''
author: AndKjell
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2016
ms.author: andkjell

---
# Azure Connect AD sync : tâches opérationnelles et examen
L’objectif de cette rubrique consiste à décrire les tâches opérationnelles de la synchronisation d’Azure AD Connect.

## Mode intermédiaire
Le mode intermédiaire peut être utilisé dans le cadre de plusieurs scénarios, notamment :

* Haute disponibilité :
* Tester et déployer de nouvelles modifications de configuration.
* Introduire un nouveau serveur et retirer l’ancien.

Avec un serveur en mode intermédiaire, vous pouvez apporter des modifications à la configuration et visualiser les modifications avant de rendre le serveur actif. Il permet également d’exécuter une importation et la synchronisation complètes afin de vérifier que toutes les modifications sont attendues avant de les appliquer dans un environnement de production.

Lors de l’installation, vous pouvez sélectionner le serveur en **mode intermédiaire**. Cette action rend le serveur actif pour l’importation et la synchronisation, mais n’exécute aucune exportation. Un serveur en mode intermédiaire n’exécute pas la synchronisation de mot de passe et l’écriture différée de mot de passe même si vous avez sélectionné ces fonctions au cours de l’installation. Lorsque vous désactivez le mode intermédiaire, le serveur lance l’exportation et active la synchronisation de mot de passe et l’écriture différée de mot de passe.

Vous pouvez toujours forcer une exportation en utilisant le gestionnaire de services de synchronisation.

Un serveur en mode intermédiaire continue de recevoir des modifications d’Active Directory et d’Azure AD. Il dispose toujours d’une copie des modifications les plus récentes et peut très rapidement reprendre les responsabilités d’un autre serveur. Si vous apportez des modifications de configuration à votre serveur principal, la responsabilité d’apporter les mêmes modifications au serveur en mode intermédiaire vous incombe.

Pour ceux qui connaissant les technologies de synchronisation plus anciennes, le mode intermédiaire est différent, dans la mesure où le serveur a sa propre base de données SQL. Cette architecture permet au serveur en mode intermédiaire d’être situé dans un autre centre de données.

### Vérifiez la configuration d’un serveur
Pour appliquer cette méthode, procédez comme suit :

1. [Préparation](#prepare)
2. [Importer et synchroniser](#import-and-synchronize)
3. [Verify](#verify)
4. [Changer de serveur actif](#switch-active-server)

#### Préparation
1. Installez Azure AD Connect, sélectionnez **mode intermédiaire**, puis désélectionnez **Démarrer la synchronisation** sur la dernière page de l’Assistant Installation. Ce mode vous permet d’exécuter manuellement le moteur de synchronisation. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Déconnectez-vous puis connectez-vous de nouveau et, dans le menu Démarrer, sélectionnez **Service de synchronisation**.

#### Importer et synchroniser
1. Sélectionnez **Connecteurs**, puis sélectionnez le premier connecteur de type **Services de domaine Active Directory**. Cliquez sur **Exécuter**, sélectionnez **Importation intégrale**, puis **OK**. Répétez cette procédure pour tous les connecteurs de ce type.
2. Sélectionnez le connecteur de type **Azure Active Directory (Microsoft)**. Cliquez sur **Exécuter**, sélectionnez **Importation intégrale**, puis **OK**.
3. Vérifiez que l’onglet Connecteurs est toujours sélectionné. Pour chaque connecteur de type **Services de domaine Active Directory**, cliquez sur **Exécuter**, sélectionnez **Synchronisation Delta**, puis **OK**.
4. Sélectionnez le connecteur de type **Azure Active Directory (Microsoft)**. Cliquez sur **Exécuter**, sélectionnez **Synchronisation Delta**, puis **OK**.

Vous avez maintenant effectué une exportation intermédiaire vers Azure AD et Active Directory local (si vous utilisez un déploiement Exchange hybride). Les prochaines étapes vous permettront d’inspecter les changements avant de commencer effectivement l’exportation vers les répertoires.

#### Verify
1. Démarrez une invite de commande et accédez à `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Exécution : `csexport "Name of Connector" %temp%\export.xml /f:x` le nom du connecteur se trouve dans le service de synchronisation. Le nom est similaire à « contoso.com – AAD » pour Azure AD.
3. Exécuter : `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous disposez d’un fichier dans %temp% nommé export.csv qui peut être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications sur le point d’être exportées.
5. Apportez les modifications nécessaires aux données ou à la configuration et réexécutez ces opérations (importer, synchroniser et vérifier) jusqu’à ce que les modifications sur le point d’être exportées soient attendues.

**Connaître le fichier export.csv**

La majeure partie du fichier est explicite. Certaines abréviations permettant de comprendre le contenu :

* OMODT – Type de modification d’objet. Indique si l’opération au niveau de l’objet est un ajout, une mise à jour ou une suppression.
* AMODT – Type de modification d’attribut. Indique si l’opération au niveau de l’attribut est un ajout, une mise à jour ou une suppression.

Si la valeur d’attribut a plusieurs valeurs, toutes les modifications ne s’affichent pas. Seul le nombre de valeurs ajoutées et supprimées est visible.

#### Changer de serveur actif
1. Sur le serveur actif, éteignez le serveur (DirSync/FIM/Azure AD Sync) pour qu’il ne soit pas exporté vers Azure AD ou définissez-le en mode intermédiaire (Azure AD Connect).
2. Exécutez l'Assistant Installation sur le serveur en **mode intermédiaire**, puis désactivez le **mode intermédiaire**. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Récupération d'urgence
Une partie de la conception de l’implémentation consiste à planifier les procédures à suivre si un sinistre occasionne la perte du serveur de synchronisation. Il existe différents modèles et le choix de celui que vous devez utiliser dépend de plusieurs facteurs, notamment :

* Dans quelle mesure pouvez-vous tolérer de ne pas pouvoir apporter des modifications aux objets dans Azure AD pendant les temps d’indisponibilité ?
* Si vous utilisez la synchronisation de mot de passe, les utilisateurs acceptent-ils de devoir utiliser l’ancien mot de passe dans Azure AD dans le cas où il serait modifié en local ?
* Avez-vous une dépendance par rapport aux opérations en temps réel, notamment l’écriture différée de mot de passe ?

Selon les réponses à ces questions et la stratégie de votre organisation, une des stratégies suivantes peut être mise en œuvre :

* Régénérer si nécessaire.
* Disposer d'un serveur de secours en attente, appelé **mode intermédiaire**.
* Utiliser les machines virtuelles.

Si vous n’utilisez pas la base de données SQL Express intégrée, vous devez vous reporter à la section [Haute disponibilité SQL](#sql-high-availability).

### Régénérer lorsque nécessaire
Une stratégie viable consiste à planifier une régénération du serveur si nécessaire. Généralement, l’installation du moteur de synchronisation et l’exécution de l’importation et de la synchronisation initiales peuvent être effectuées en quelques heures. Si aucun serveur n’est libre, il est possible d’utiliser provisoirement un contrôleur de domaine pour héberger le moteur de synchronisation.

Le serveur de moteur de synchronisation ne stocke aucun état relatif aux objets de sorte que la base de données peut être recréée à partir des données présentes dans Active Directory et Azure AD. L’attribut **sourceAnchor** est utilisé pour associer les objets à partir du site et du cloud. Si vous régénérez le serveur avec les objets existants en local et sur le cloud, le moteur de synchronisation les remet en correspondance de nouveau au cours de la réinstallation. Vous devez documenter et enregistrer les modifications de configuration apportées au serveur, notamment aux règles de filtrage et de synchronisation. Ces configurations personnalisées doivent être réappliquées avant de commencer la synchronisation.

### Disposer d’un serveur de secours en attente, connu sous le nom de mode intermédiaire.
Si vous disposez d’un environnement plus complexe, il est recommandé d’avoir un ou plusieurs serveurs de secours. Lors de l’installation, vous pouvez activer un serveur en **mode intermédiaire**.

Pour plus d'informations, consultez la section [mode intermédiaire](#staging-mode).

### Utiliser les machines virtuelles
Une méthode courante et prise en charge consiste à exécuter le moteur de synchronisation sur une machine virtuelle. Si l’hôte rencontre un problème, l’image contenant le serveur de moteur de synchronisation peut être migrée vers un autre serveur.

### Haute disponibilité SQL
Si vous n’utilisez pas SQL Server Express livré avec Azure AD Connect, la haute disponibilité pour SQL Server doit alors être prise en compte. La seule solution haute disponibilité prise en charge est SQL clustering. Les solutions non prises en charge incluent la mise en miroir et Always On.

## Étapes suivantes
**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->