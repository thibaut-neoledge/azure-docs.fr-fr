---
title: Configuration de CHAP pour votre appareil StorSimple | Microsoft Docs
description: Décrit comment configurer le protocole Challenge Handshake Authentication Protocol (CHAP) sur un appareil StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# Configuration de CHAP pour votre appareil StorSimple
Ce didacticiel explique comment configurer CHAP pour votre appareil StorSimple. La procédure détaillée dans cet article s’applique aux appareils StorSimple série 8000, ainsi qu’aux appareils StorSimple 1200.

CHAP est l’abréviation de Challenge Handshake Authentication Protocol. Il s’agit d’un schéma d’authentification utilisé par les serveurs pour valider l’identité des clients distants. La vérification repose sur un mot de passe partagé ou un secret. Le protocole CHAP peut être à sens unique (unidirectionnel) ou mutuel (bidirectionnel). Le protocole CHAP est unidirectionnel lorsque la cible authentifie un initiateur. Par opposition, l’authentification CHAP mutuelle ou inverse nécessite que la cible authentifie l’initiateur, puis que l’initiateur authentifie la cible. L’authentification de l’initiateur peut être implémentée sans authentification cible. Toutefois, l’authentification cible peut être implémentée uniquement si l’authentification de l’initiateur est également implémentée.

Nous vous recommandons d’utiliser CHAP pour améliorer la sécurité iSCSI.

> [!NOTE]
> N’oubliez pas que IPSEC n’est pas actuellement pris en charge sur les appareils StorSimple.
> 
> 

Les paramètres CHAP sur l’appareil StorSimple peuvent être configurés comme suit :

* Authentification unidirectionnelle ou unidirectionnelle
* Authentification bidirectionnelle, mutuelle ou inverse

Dans chacun de ces cas, le portail pour l’appareil et le logiciel de l’initiateur iSCSI du serveur doit être configuré. Les étapes détaillées de cette configuration sont décrites dans ce didacticiel.

## Authentification unidirectionnelle ou unidirectionnelle
Dans l’authentification unidirectionnelle, la cible authentifie l’initiateur. Cette authentification nécessite de configurer les paramètres d’initiateur CHAP sur l’appareil StorSimple et le logiciel iSCSI Initiator sur l’ordinateur hôte. Les procédures détaillées pour votre appareil StorSimple et un hôte Windows sont décrites ci-après.

#### Configuration de votre appareil pour l’authentification unidirectionnelle
1. Dans le portail Azure Classic, sur la page **Appareils**, cliquez sur l’onglet **Configurer**.
   
    ![Initiateur CHAP](./media/storsimple-configure-chap/IC740943.png)
2. Faites défiler cette page vers le bas, puis dans la section **CHAP Initiator** :
   
   1. Indiquez un nom d’utilisateur pour votre initiateur CHAP.
   2. Spécifiez un mot de passe pour votre initiateur CHAP.
      
           > [AZURE.IMPORTANT] Le nom d’utilisateur CHAP doit contenir moins de 233 caractères. Le mot de passe CHAP doit comprendre entre 12 et 16 caractères. L’utilisation d’un nom d’utilisateur ou d’un mot de passe plus long entraîne un échec d’authentification sur l’hôte Windows.
   3. Confirmez le mot de passe.
3. Cliquez sur **Save**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### Configuration de l’authentification unidirectionnelle sur le serveur hôte Windows
1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.
2. Dans la fenêtre **iSCSI Initiator Properties**, procédez comme suit :
   
   1. Cliquez sur l’onglet **Discovery**.
      
       ![Propriétés de l’initiateur iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Cliquez sur **Discover Portal**.
3. Dans la boîte de dialogue **Découvrir le portail cible** :
   
   1. Spécifiez l’adresse IP de votre appareil.
   2. Cliquez sur **Avancé**.
      
       ![Découvrir le portail cible](./media/storsimple-configure-chap/IC740945.png)
4. Dans la boîte de dialogue **Paramètres avancés** :
   
   1. Cochez la case **Enable CHAP log on**.
   2. Dans le champ **Nom**, tapez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail Azure Classic.
   3. Dans le champ **Secret de la cible**, fournissez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail Azure Classic.
   4. Cliquez sur **OK**.
      
       ![Paramètres avancés - Généraux](./media/storsimple-configure-chap/IC740946.png)
5. Dans l’onglet **Targets** de la fenêtre **iSCSI Initiator Properties**, l’état de l’appareil devrait indiquer **Connected**. Si vous utilisez un appareil StorSimple 1200, chaque volume est monté en tant que cible iSCSI comme indiqué ci-dessous. Les étapes 3 et 4 doivent donc être répétées pour chaque volume.
   
    ![Volumes montés en tant que cibles distinctes](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Si vous modifiez le nom iSCSI, le nouveau nom sera utilisé pour les nouvelles sessions iSCSI. Les nouveaux paramètres ne sont pas appliqués aux sessions existantes tant que vous n’avez pas quitté puis relancé la session.
   > 
   > 

Pour plus d’informations sur la configuration du protocole CHAP sur le serveur hôte Windows, voir la rubrique [Considérations supplémentaires](#additional-considerations).

## Authentification bidirectionnelle ou mutuelle
Dans l’authentification bidirectionnelle, la cible authentifie l’initiateur, puis l’initiateur authentifie la cible. Cela oblige l’utilisateur à configurer les paramètres d’initiateur CHAP ainsi que les paramètres CHAP inverses sur l’appareil et le logiciel iSCSI Initiator sur l’ordinateur hôte. Les procédures suivantes décrivent les étapes pour configurer l’authentification mutuelle sur l’appareil et sur l’hôte Windows.

#### Configuration de votre appareil pour l’authentification mutuelle
1. Dans le portail Azure Classic, sur la page **Appareils**, cliquez sur l’onglet **Configurer**.
   
    ![Cible CHAP](./media/storsimple-configure-chap/IC740948.png)
2. Faites défiler cette page vers le bas, puis dans la section **CHAP Target** :
   
   1. Indiquez le **nom d’utilisateur CHAP inverse** de votre appareil.
   2. Indiquez le **mot de passe CHAP inverse** de votre appareil.
   3. Confirmez le mot de passe.
3. Dans la section **CHAP Initiator** :
   
   1. Indiquez un **nom d’utilisateur** pour votre appareil.
   2. Indiquez un **mot de passe** pour votre appareil.
   3. Confirmez le mot de passe.
4. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### Configuration de l’authentification bidirectionnelle sur le serveur hôte Windows
1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.
2. Dans la fenêtre **iSCSI Initiator Properties**, cliquez sur l’onglet **Configuration**.
3. Cliquez sur **CHAP**.
4. Dans la boîte de dialogue **Secret CHAP mutuel de l’initiateur iSCSI** :
   
   1. Entrez le **mot de passe CHAP inverse** que vous avez configuré dans le portail Azure Classic.
   2. Cliquez sur **OK**.
      
       ![Secret CHAP mutuel de l’initiateur iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Cliquez sur l’onglet **Targets**.
6. Cliquez sur le bouton **Connect**.
7. Dans la boîte de dialogue **Se connecter à la cible**, cliquez sur **Avancé**.
8. Dans la boîte de dialogue **Advanced Properties** :
   
   1. Cochez la case **Enable CHAP log on**.
   2. Dans le champ **Nom**, tapez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail Azure Classic.
   3. Dans le champ **Secret de la cible**, fournissez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail Azure Classic.
   4. Cochez la case **Perform mutual authentication**.
      
       ![Paramètres avancés - Authentification mutuelle](./media/storsimple-configure-chap/IC740950.png)
   5. Cliquez sur **OK** pour terminer la configuration CHAP

Pour plus d’informations sur la configuration du protocole CHAP sur le serveur hôte Windows, voir la rubrique [Considérations supplémentaires](#additional-considerations).

## Considérations supplémentaires
La fonctionnalité **Quick Connect** ne prend pas en charge les connexions pour lesquelles le protocole CHAP est activé. Lorsque le protocole CHAP est activé, assurez-vous d’utiliser le bouton **Connect** disponible dans l’onglet **Targets** pour vous connecter à une cible.

![Se connecter à la cible](./media/storsimple-configure-chap/IC740947.png)

Dans la boîte de dialogue **Se connecter à la cible** qui s’affiche, cochez la case **Add this connection to the list of Favorite Targets**. Cela garantit que chaque fois que l’ordinateur redémarre, une tentative est effectuée pour rétablir la connexion aux cibles iSCSI favorites.

## Erreurs lors de la configuration
Si votre configuration CHAP est incorrecte, un message d’erreur d’**échec de l’authentification** risque d’apparaître.

## Vérification de la configuration CHAP
Vous pouvez vérifier que le protocole CHAP est utilisé en procédant comme suit.

#### Vérification de votre configuration CHAP
1. Cliquez sur **Favorite Targets**.
2. Sélectionnez la cible pour laquelle vous avez activé l’authentification.
3. Cliquez sur **Details**.
   
    ![Cibles favorites des propriétés de l’initiateur iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. Dans la boîte de dialogue **Favorite Target Details**, notez l’entrée figurant dans le champ **Authentication**. Si la configuration a réussi, le champ devrait afficher **CHAP**.
   
    ![Détails sur les cibles favorites](./media/storsimple-configure-chap/IC740952.png)

## Étapes suivantes
* En savoir plus sur la [sécurité StorSimple](storsimple-security.md).
* En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0824_2016-->