<properties
   pageTitle="Créer un équilibreur de charge interne dans Resource Manager à l’aide du portail Azure | Microsoft Azure"
   description="Découvrez comment créer un équilibreur de charge interne dans Resource Manager à l’aide du portail Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Prise en main de la création d’un équilibreur de charge interne dans le portail Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Prise en main de la création d’un équilibreur de charge interne avec le portail Azure

Pour créer un équilibreur de charge interne à partir du portail Azure, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Nouveau** > **Réseau** > **Équilibreur de charge**.
3. Dans le panneau **Créer un équilibreur de charge**, tapez le **Nom** de votre équilibreur de charge.
4. Sous **Schéma**, cliquez sur **Interne**.
5. Cliquez sur **Réseau virtuel**, puis sélectionnez le réseau virtuel dans lequel vous souhaitez créer l’équilibreur de charge.

    >[AZURE.NOTE] Si vous ne voyez pas le réseau virtuel que vous souhaitez utiliser, vérifiez l’**Emplacement** que vous utilisez pour l’équilibreur de charge et modifiez-le en conséquence.

6. Cliquez sur **Sous-réseau**, puis sélectionnez le sous-réseau sur lequel vous souhaitez créer l’équilibreur de charge.
7. Sous **Attribution d’adresses IP**, cliquez sur **Dynamique** ou **Statique**, selon que vous souhaitez une adresse IP statique ou non pour l’équilibreur de charge.

    >[AZURE.NOTE] Si vous choisissez d’utiliser une adresse IP statique, vous devez fournir une adresse pour l’équilibreur de charge.

8. Sous **Groupe de ressources**, spécifiez le nom d’un nouveau groupe de ressources pour l’équilibreur de charge ou cliquez sur **Sélectionner** et choisissez un groupe de ressources existant.
9. Cliquez sur **Create**.

## Configuration des règles d’équilibrage de la charge

Après la création de l’équilibreur de charge, accédez à la ressource d’équilibreur de charge pour la configurer. Vous devez tout d’abord configurer un pool d’adresses de serveur principal, ainsi qu’une sonde avant de configurer une règle d’équilibrage de charge.

### Étape 1

Configurer un pool principal :

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Pools principaux**.
3. Dans le panneau **Pools d’adresses principaux**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter un pool principal**, tapez le **nom** du pool principal, puis cliquez sur **OK**.

### Étape 2

Configurer une sonde :

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Sondes**.
3. Dans le panneau **Sondes**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter une sonde**, tapez le **nom** de la sonde.
5. Sous **Protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications basées sur TCP).
6. Sous **Port**, spécifiez le port à utiliser pour accéder à la sonde.
7. Sous **Chemin d’accès** (pour les sondes HTTP uniquement), spécifiez le chemin d’accès à utiliser comme sonde.
8. Sous **Intervalle**, spécifiez la fréquence de sondage de l’application.
9. Sous **Seuil défectueux**, spécifiez le nombre de tentatives devant échouer avant que la machine virtuelle principale ne soit marquée comme défectueuse.
10. Cliquez sur **OK** pour créer la sonde.

### Étape 3

Configurer les règles d’équilibrage de la charge :

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Règles d’équilibrage de charge**.
3. Dans le panneau **Règles d’équilibrage de charge**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter une règle d’équilibrage de charge**, tapez le **nom** de la règle.
5. Sous **Protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications basées sur TCP).
6. Sous **Port**, spécifiez le port auquel les clients se connectent dans l’équilibreur de charge.
7. Sous **Port principal**, spécifiez le port à utiliser dans le pool principal (en règle générale, le port de l’équilibreur de charge et le port principal sont identiques).
8. Sous **Pool principal**, sélectionnez le pool principal créé ci-dessus.
9. Sous **Persistance de session**, sélectionnez la façon dont vous souhaitez que les sessions soient conservées.
10. Sous **Délai d’inactivité (minutes)**, spécifiez le délai d’inactivité.
11. Sous **Adresse IP flottante (retour serveur direct)**, cliquez sur **Désactivé** ou **Activé**.
12. Cliquez sur **OK**.

## Étapes suivantes

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->