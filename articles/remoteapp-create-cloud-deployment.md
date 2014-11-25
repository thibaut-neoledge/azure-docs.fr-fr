<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo" />

# Création d'un déploiement cloud de RemoteApp

Il existe deux types de déploiement RemoteApp :

-   Cloud : réside complètement dans Azure et est créé à l'aide de l'option **Création rapide** du portail de gestion Azure.
-   Hybride : comprend un réseau virtuel pour un accès local et est créé à l'aide de l'option **Créer avec VPN** du portail de gestion.

Ce didacticiel vous familiarise avec la procédure de création d'un déploiement cloud. Il se compose de quatre étapes :

1.  Création d'un service RemoteApp
2.  Vous pouvez éventuellement configurer une synchronisation d'annuaires. RemoteApp en a besoin pour synchroniser les utilisateurs, groupes, contacts et mots de passe de votre annuaire Active Directory local avec votre client Azure Active Directory.
3.  Publication de programmes RemoteApp
4.  Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer le service, vous devez effectuer les étapes suivantes :

-   Vous inscrire pour bénéficier de la version préliminaire de RemoteApp. Pour ce faire, rendez-vous à l'adresse suivante [][]http://azure.microsoft.com/fr-fr/services/remoteapp/</a>.
-   Collecter des informations sur les utilisateurs et groupes auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs ou les groupes.
-   Cette procédure part du principe que vous allez utiliser l'image de modèle fournie dans le cadre de votre abonnement ou que vous avez déjà téléchargé l'image de modèle que vous souhaitez utiliser. Si vous devez télécharger une autre image de modèle, rendez-vous sur la page Images de modèle. Il vous suffit de cliquer sur **télécharger une image de modèle** et de suivre les étapes de l'Assistant.

## **Étape 1 : Création d'un service RemoteApp**

1.  Accédez à la page RemoteApp du [portail de gestion Windows Azure][portail de gestion Windows Azure].
2.  Cliquez sur **Nouveau \> Création rapide**.

3.  Entrez le nom de votre service et sélectionnez ensuite votre région.
4.  Sélectionnez l'abonnement à utiliser pour créer ce service.
5.  Sélectionnez le modèle à utiliser pour ce service.

    **Conseil :** votre abonnement à RemoteApp s'accompagne d'une image de modèle qui contient des programmes Office 2013, certains étant publiés (tels que Word) et d'autres étant prêts pour la publication.

6.  Cliquez sur **Créer un service RemoteApp**.

    **Important :** l'approvisionnement de votre service peut prendre jusqu'à 30 minutes.

Après avoir créé votre service RemoteApp, accédez à la page **Démarrage rapide** de RemoteApp pour passer aux étapes de configuration.

## **Étape 2 : Configuration de la synchronisation d'annuaires Active Directory (facultatif)**

Si vous souhaitez utiliser Active Directory, RemoteApp nécessite une synchronisation d'annuaires entre Azure Active Directory et votre annuaire Active Directory local afin de synchroniser les utilisateurs, groupes, contacts et mots de passe avec votre client Azure Active Directory. Pour plus d'informations sur la planification, consultez la rubrique [Programme de synchronisation d'annuaires][Programme de synchronisation d'annuaires].

## **Étape 3 : Publication de programmes RemoteApp**

Un programme RemoteApp est l'application ou le programme mis à la disposition de vos utilisateurs. Il se situe dans l'image de modèle que vous avez téléchargée pour le service. Lorsqu'un utilisateur accède à un programme RemoteApp, celui-ci semble s'exécuter dans son environnement local. En réalité, il s'exécute dans Azure.

Avant que vos utilisateurs puissent accéder à des programmes RemoteApp, vous devez les publier sur le flux de l'utilisateur final ; il s'agit d'une liste des programmes disponibles auxquels vos utilisateurs peuvent accéder via le portail Azure.

Vous pouvez publier plusieurs programmes sur votre service RemoteApp. Sur la page des programmes RemoteApp, cliquez sur **Publier** afin d'ajouter un programme. Vous pouvez publier le programme à partir du menu Démarrer de l'image de modèle ou en indiquant le chemin sur l'image de modèle du programme. Si vous choisissez la première option, sélectionnez le programme à publier. Si vous optez pour la deuxième solution, indiquez le nom du programme, ainsi que le chemin d'accès au répertoire d'installation sur l'image de modèle.

## **Étape 4 : Configuration de l'accès utilisateur**

Maintenant que vous avez créé votre service RemoteApp, vous devez ajouter les utilisateurs et groupes qui seront autorisés à utiliser vos ressources distantes. Si vous utilisez Active Directory, les utilisateurs ou groupes auxquels vous accordez l'accès doivent se trouver dans le client Active Directory associé à l'abonnement utilisé pour créer ce service RemoteApp.

1.  Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**.
2.  Entrez le nom du groupe ou du compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.

    Dans le cas des utilisateurs, veillez à utiliser le format <user@domain.com>. Dans le cas des groupes, entrez le nom.

3.  Une fois les utilisateurs ou groupes validés, cliquez sur **Enregistrer**.

## Étapes suivantes

Félicitations ! Vous avez créé et déployé correctement votre déploiement cloud RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L'URL du client est disponible sur la page Démarrage rapide de RemoteApp. Les utilisateurs devront ensuite se connecter à Azure et accéder aux programmes RemoteApp que vous avez publiés.

  []: http://azure.microsoft.com/fr-fr/services/remoteapp/
  [portail de gestion Windows Azure]: http://manage.windowsazure.com
  [Programme de synchronisation d'annuaires]: http://msdn.microsoft.com/fr-fr/library/azure/hh967642.aspx
