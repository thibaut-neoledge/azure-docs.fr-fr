<properties 
   pageTitle="Gérer votre compte de stockage StorSimple | Microsoft Azure"
   description="Explique comment utiliser la page Configurer de StorSimple Manager pour ajouter, modifier, supprimer ou régénérer les clés de sécurité d’un compte de stockage associé à StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="02/05/2016"
   ms.author="alkohli" />

# Utiliser le service StorSimple Manager pour gérer votre compte de stockage (version préliminaire)

## Vue d'ensemble

La page **Configurer** présente les paramètres de service globaux qui peuvent être créés dans le service StorSimple Manager. Ces paramètres peuvent être appliqués à tous les appareils connectés au service et incluent les éléments suivants :

- Comptes de stockage 
- Enregistrements de contrôle d’accès 

Ce didacticiel explique comment utiliser la page **Configurer** pour ajouter, modifier ou supprimer des comptes de stockage pour StorSimple Virtual Array. Les informations contenues dans ce didacticiel s’appliquent uniquement à la version préliminaire publique de StorSimple Virtual Array.

 ![Page Configurer](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)

Les comptes de stockage contiennent les informations d’identification que l’appareil utilise pour accéder au compte de stockage que vous procure votre fournisseur de services cloud. Pour les comptes de stockage Microsoft Azure, il s’agit d’informations d’identification telles que le nom du compte et la clé d’accès primaire.

Dans la page **Configurer**, tous les comptes de stockage créés pour l’abonnement à la facturation sont affichés sous forme de tableau et contiennent les informations suivantes :

- **Nom** : nom unique attribué au compte lors de sa création.
- **SSL activé** : permet d’indiquer si le protocole SSL est activé et si la communication appareil-cloud s’effectue sur le canal sécurisé.

Les tâches les plus courantes que vous pouvez effectuer sur les comptes de stockage dans la page **Configurer** sont les suivantes :

- Ajout d’un compte de stockage 
- Modification d’un compte de stockage 
- Suppression d'un compte de stockage 


## Types de compte de stockage

Vous pouvez utiliser trois types de compte de stockage avec votre appareil StorSimple.

- **Comptes de stockage générés automatiquement** : comme son nom l’indique, ce type de compte de stockage est généré automatiquement à la création du service. Pour plus d’informations sur la création des comptes de stockage, consultez [Création d’un nouveau service](storsimple-ova-manage-service.md#create-a-service). 
- **Comptes de stockage de l’abonnement au service** : il s’agit des comptes de stockage Azure associés à l’abonnement au service. Pour en savoir plus sur la création de ces comptes de stockage, consultez la rubrique [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). 
- **Comptes de stockage hors abonnement au service** : il s’agit des comptes de stockage Azure qui ne sont pas associés à votre service et existaient probablement avant sa création.

## Ajout d’un compte de stockage

Vous pouvez ajouter un compte de stockage à votre configuration du service StorSimple Manager en fournissant un nom convivial unique et les informations d’accès associées au compte de stockage. Vous avez également la possibilité d’activer le mode SSL (Secure Sockets Layer) afin de créer un canal sécurisé pour la communication réseau entre l’appareil et le cloud.

Vous pouvez créer plusieurs comptes pour un fournisseur de services cloud donné. Pendant l’enregistrement du compte de stockage, le service tente de communiquer avec votre fournisseur de services cloud. Les informations d’identification et les données d’accès que vous avez fournies sont alors authentifiées. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affiche.

La procédure d’ajout d’un compte de stockage est détaillée ci-dessous.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## Modification d’un compte de stockage

Vous pouvez modifier un compte de stockage utilisé par votre appareil. Si vous modifiez un compte de stockage actuellement utilisé, les champs modifiables seront celui de la clé d’accès et celui du mode SSL du compte de stockage. Vous pouvez saisir la nouvelle clé d’accès de stockage ou modifier la sélection **Activer le mode SSL**, puis enregistrer les modifications.

#### Pour modifier un compte de stockage

1. Dans la page d’accueil du service, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur **Configurer**.

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue **Ajouter/modifier des comptes de stockage**, procédez comme suit :

  1. Dans la liste déroulante **Comptes de stockage**, choisissez un compte existant à modifier.
  2. Si nécessaire, vous pouvez modifier le réglage de l’option **Activer le mode SSL**.
  3. Vous pouvez choisir de régénérer les clés d’accès de votre compte de stockage. Pour plus d’informations, consultez [Régénération des clés d’accès de stockage](storage-create-storage-account.md#manage-your-storage-access-keys). Fournissez la nouvelle clé de compte de stockage. Pour un compte de stockage Azure, il s’agit de la clé d’accès primaire. 
  4. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-manage-storage-accounts/checkicon.png) pour enregistrer les paramètres. Les paramètres de la page **Configurer** sont mis à jour. 
  5. Pour enregistrer les modifications apportées aux paramètres, cliquez sur **Enregistrer** au bas de la page. 

     ![Modification d’un compte de stockage](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## Suppression d'un compte de stockage

> [AZURE.IMPORTANT] Vous ne pouvez supprimer un compte de stockage que s’il n’est pas utilisé. Si un compte de stockage est actuellement utilisé, vous en serez informé.

#### Pour supprimer un compte de stockage

1. Dans la page d’accueil du service StorSimple Manager, sélectionnez votre service, double-cliquez sur son nom, puis cliquez sur **Configurer**.

2. Dans le tableau de comptes de stockage, placez le pointeur sur le compte à supprimer.

3. Une icône de suppression (**x**) s’affiche dans la colonne de droite en regard de ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.

4. Lorsque vous êtes invité à confirmer la suppression, cliquez sur **Oui**. La liste du tableau est mise à jour pour refléter les modifications.

5. Pour enregistrer les modifications apportées aux paramètres, cliquez sur **Enregistrer** au bas de la page.


## Étapes suivantes

- Découvrez comment [gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0211_2016-->