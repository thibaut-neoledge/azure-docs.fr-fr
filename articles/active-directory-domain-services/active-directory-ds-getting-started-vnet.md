<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : prise en main | Microsoft Azure"
	description="Prise en main des services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/11/2016"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(version préliminaire)* : prise en main

## Instructions pour sélectionner un réseau virtuel Azure
Quand vous sélectionnez un réseau virtuel en vue de l’utiliser avec les services de domaine Azure AD, gardez à l’esprit les instructions suivantes :

- Veillez à sélectionner un réseau virtuel dans une région qui est prise en charge par les services de domaine Azure AD. Consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles.
- Si vous prévoyez d’utiliser un réseau virtuel existant, assurez-vous qu’il s’agit d’un réseau virtuel régional. Les réseaux virtuels qui recourent au mécanisme des groupes d’affinités hérité ne peuvent pas être utilisés avec les services de domaine Azure AD. Dans ce cas de figure, vous devez [migrer les réseaux virtuels hérités vers des réseaux virtuels régionaux](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
- Si vous envisagez d’utiliser un réseau virtuel existant, assurez-vous qu’aucun serveur DNS personnalisé n’est configuré pour le réseau virtuel. Les services de domaine Azure AD ne prennent pas en charge les serveurs DNS personnalisés.
- Si vous envisagez d’utiliser un réseau virtuel existant, vérifiez qu’aucun domaine existant sous le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Vous essayez ensuite d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.
- Sélectionnez le réseau virtuel qui héberge actuellement, ou qui hébergera, des machines virtuelles ayant besoin d’accéder aux services de domaine Azure AD. Vous ne pouvez pas déplacer les services de domaine vers un autre réseau virtuel une fois que vous avez activé le service.
- Les services de domaine Azure AD ne sont pas pris en charge avec les réseaux virtuels créés à l’aide d’Azure Resource Manager. Vous pouvez [connecter un réseau virtuel classique à un réseau virtuel ARM](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) pour utiliser les services de domaine Azure AD dans un réseau virtuel créé à l'aide d’Azure Resource Manager.


## Étape 2 : créer un réseau virtuel Azure
L’étape suivante de la configuration consiste à créer un réseau virtuel Azure dans lequel vous souhaitez activer les services de domaine Azure AD. Si vous souhaitez utiliser un réseau virtuel existant, vous pouvez ignorer cette étape.

> [AZURE.NOTE] Assurez-vous que le réseau virtuel Azure que vous créez ou que vous choisissez d’utiliser avec les services de domaine Azure AD appartient à une région Azure qui est prise en charge par les services de domaine Azure AD. Consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles.

Notez le nom du réseau virtuel, car vous en aurez besoin au cours d’une prochaine étape de configuration pour sélectionner le réseau virtuel adéquat au moment de l’activation des services de domaine Azure AD.

Effectuez les étapes de configuration suivantes pour créer un réseau virtuel Azure dans lequel vous souhaitez activer les services de domaine Azure AD.

1. Accédez au **portail de gestion Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Sélectionnez le nœud **Réseaux** dans le volet gauche.
3. Cliquez sur **NOUVEAU** dans le volet des tâches en bas de la page.

    ![Nœud Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Dans le nœud **Services réseau**, sélectionnez **Réseau virtuel**.
5. Cliquez sur **Création rapide** pour créer un réseau virtuel.

    ![Réseau virtuel : création rapide](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Spécifiez un **Nom** pour votre réseau virtuel. Vous pouvez également choisir de configurer l’**Espace d’adressage** ou le **Nombre maximal de machines virtuelles** pour ce réseau. Vous pouvez laisser le paramètre du serveur DNS sur « Aucun » pour l’instant. Ce paramètre sera actualisé une fois que vous aurez activé les services de domaine Azure AD.
7. Vérifiez que vous sélectionnez une région Azure prise en charge dans la liste déroulante **Emplacement**. Consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles. Cette étape est essentielle. Si vous sélectionnez un réseau virtuel dans une région Azure qui n’est pas prise en charge par les services de domaine Azure AD, vous ne pouvez pas activer le service dans ce réseau virtuel.
8. Cliquez sur le bouton **Créer un réseau virtuel** pour créer votre réseau virtuel.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**Étape suivante : activer les services de domaine Azure AD.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=AcomDC_0413_2016-->