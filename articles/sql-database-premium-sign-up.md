<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

Inscription à la version préliminaire de l'édition Premium de la base de données SQL Azure
==========================================================================================

Dans ce didacticiel, vous allez apprendre la procédure à suivre pour accéder à la version préliminaire de l'édition Premium de la base de données SQL.

La base de données SQL Azure a publié une version préliminaire limitée d'un nouveau service : l'édition Premium pour la base de données SQL. Les bases de données de l'édition Premium fournissent des ressources réservées permettant de bénéficier de performances plus prévisibles pour les applications cloud.

[Les fonctionnalités décrites dans cette rubrique sont disponibles dans leur version préliminaire uniquement. Cette rubrique est une documentation préliminaire et peut faire l'objet de modifications dans les versions à venir.]

Sommaire
--------

-   [Étape 1 : inscription à la version préliminaire de l'édition Premium de la base de données SQL](#SignUp)
-   [Étape 2 : demande d'un quota de base de données Premium](#Quota)
-   [Étape 3 : création d'une base de données Premium](#Upgrade)

Étape 1 : inscription à la version préliminaire de l'édition Premium de la base de données SQL
----------------------------------------------------------------------------------------------

Pour tirer parti des avantages de cette fonctionnalité, inscrivez votre abonnement à la version préliminaire de l'édition Premium de la base de données SQL.

1.  Connectez-vous à la [page consacrée aux versions préliminaires des fonctionnalités Azure](http://account.windowsazure.com/PreviewFeatures) à l'aide de votre compte Microsoft.

    **Remarque** : seuls les administrateurs de comptes Azure peuvent accéder au portail des comptes. Si vous n'êtes pas l'administrateur de compte de votre abonnement, demandez à ce dernier de s'occuper du processus d'inscription pour votre abonnement. Pour plus d'informations sur les comptes et abonnements Azure, consultez la page [Options d'achat](http://account.windowsazure.com/PreviewFeatures).

    ![Image1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  Recherchez l'élément **Édition Premium pour Base de données SQL** dans la liste des fonctionnalités en version préliminaire, puis cliquez sur le bouton **try it now** associé à l'élément.

    ![Image2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  Sélectionnez l'abonnement que vous souhaitez inscrire à la version préliminaire.

    ![Image3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    Seuls les abonnements actifs que vous avez payés peuvent accéder à la version préliminaire. Vous pouvez inscrire plusieurs abonnements, mais chacun d'eux ne peut être inscrit qu'une seule fois.

    L'inscription d'un abonnement à la version préliminaire n'engendre pas de frais supplémentaires, mais une fois l'inscription activée et les quotas de l'édition Premium octroyés, la création ou la mise à niveau vers une base de données Premium est soumise à la tarification indiquée à la [page sur la tarification de la base de données SQL](http://www.windowsazure.com/en-us/pricing/details/sql-database/).

    Le statut actuel de la demande d'inscription est répercuté dans la liste des versions préliminaires des fonctionnalités.

    ![Image4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  Les demandes sont approuvées en fonction de la capacité et de la demande en cours. L'activation de l'abonnement peut prendre jusqu'à 2 jours, voire plus si la demande est élevée ou si la capacité maximale a été atteinte.

5.  Vous recevrez une notification par courrier électronique une fois que votre abonnement sera activé et pourra accéder à la version préliminaire.

Étape 2 : demande d'un quota de base de données Premium
-------------------------------------------------------

Une fois l'abonnement à la version préliminaire activé, vous devez demander un quota de base de données Premium pour chaque serveur sur lequel vous envisagez de créer une base de données Premium. La capacité étant limitée, demandez uniquement un quota pour les serveurs sur lesquels vous envisagez de créer une base de données Premium et annulez les demandes en attente inutiles.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com) en utilisant votre compte Azure.

    **Remarque** : les administrateurs de compte et de service et les co-administrateurs de l'abonnement peuvent demander un quota une fois que l'abonnement est inscrit à la version préliminaire.

2.  Accédez à la liste **Serveurs** dans l'extension **Bases de données SQL**.
3.  Sélectionnez le serveur pour lequel vous envisagez de demander un quota de base de données Premium.
4.  Accédez à l'option de **démarrage rapide** correspondant au serveur sélectionné en cliquant sur l'icône en forme d'éclair située dans la barre de navigation supérieure.
5.  Cliquez sur **Demander un quota de base de données Premium** dans la section **Base de données Premium**.

    ![Image6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    Une fois la demande envoyée, le quota vous sera octroyé dans un délai de 5 jours. Un délai plus long peut indiquer une demande élevée ou une capacité maximale atteinte.

    Voici quelques remarques supplémentaires sur les demandes de quota de base de données Premium :

    -   Les clients bénéficiant d'un abonnement à la version d'évaluation gratuite ne peuvent pas bénéficier d'un quota.
    -   Dans la mesure où le quota est limité, les demandes seront honorées en fonction de la demande en cours et de la capacité disponible.
    -   Microsoft se réserve le droit de réclamer un quota inutilisé au bout de 15 jours.
    -   Une seule demande de quota peut être envoyée pour chaque serveur logique de l'abonnement.
    -   Le quota est initialement limité à une seule base de données par serveur logique.
    -   Bien que la demande de quota de base de données soit gratuite, la création d'une base de données Premium ou la mise à niveau d'une base de données existante au sein de l'édition Web ou Entreprise augmentera le coût de la base de données.

6.  Pour afficher le statut de votre demande de quota, consultez la page de **démarrage rapide** du serveur.

    ![Image7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  Vous recevrez une notification par courrier électronique lorsque la demande de quota de base de données Premium aura été acceptée et que le quota sera prêt à être utilisé.
8.  Une fois le quota octroyé, le quota de base de données Premium restant pour un serveur s'affiche sous l'onglet **Démarrage rapide** ou **Tableau de bord** du serveur.

    ![Image8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

Étape 3 : création d'une base de données Premium
------------------------------------------------

Une fois que le quota vous a été octroyé, vous pouvez créer une base de données Premium ou mettre à niveau une base de données Web ou Entreprise existante vers l'édition Premium pour tirer parti des avantages de la capacité réservée et de performances plus prévisibles.

![Image9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Image10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

Pour plus d'informations, consultez la page [Gestion d'une base de données Premium](http://go.microsoft.com/fwlink/p/?LinkID=311927).

**Remarque :** vous ne pouvez modifier le statut Premium ou la taille de réservation de votre base de données qu'une fois toutes les 24 heures.

Étapes suivantes
----------------

Pour plus d'informations sur les bases de données Premium, consultez les ressources suivantes :

-   [Gestion d'une base de données Premium](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [Recommandations relatives à l'édition Premium de la base de données SQL](http://go.microsoft.com/fwlink/p/?LinkId=313650)

