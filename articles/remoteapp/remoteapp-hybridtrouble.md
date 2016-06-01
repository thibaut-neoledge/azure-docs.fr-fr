
<properties
    pageTitle="Résoudre les problèmes de création de collections hybrides RemoteApp | Microsoft Azure"
    description="Apprenez comment dépanner les échecs de création de collections hybrides RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="elizapo" />



# Résolution des problèmes de création de collections hybrides Azure RemoteApp

Une collection hybride est hébergée dans le cloud Azure et y stocke également les données, mais elle permet aussi aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise synchronisées ou fédérées avec Azure Active Directory. Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure existant ou vous pouvez créer un réseau virtuel. Nous vous recommandons d’utiliser une plage CIDR suffisamment étendue lorsque vous créez ou utilisez un sous-réseau de réseau virtuel afin de pouvoir prendre en compte la croissance future d’Azure RemoteApp.

Vous n’avez pas encore créé votre collection ? Pour plus d’informations, consultez la page [Création d’une collection hybride](remoteapp-create-hybrid-deployment.md).

Si vous rencontrez des problèmes lors de la création de votre collection, ou si la collection ne fonctionne pas comme elle devrait, vérifiez les informations suivantes.

## Votre image n'est pas valide ##
Si vous voyez un message similaire à « GoldImageInvalid » lorsque vous attendez qu'Azure configure votre collection, cela signifie que votre image de modèle ne répond pas aux [exigences définies pour l'image](remoteapp-imagereqs.md). Donc, lisez ces [exigences](remoteapp-imagereqs.md), corrigez votre image et essayez de créer à nouveau votre collection.



## Votre réseau virtuel comporte-t-il des groupes de sécurité réseau ? ##
Si vous avez défini des groupes de sécurité réseau sur le sous-réseau que vous utilisez pour votre collection, assurez-vous que les [URL et les ports](remoteapp-ports.md) suivants sont accessibles à partir de votre sous-réseau.

Vous pouvez ajouter d’autres groupes de sécurité réseau pour les machines virtuelles que vous avez déployées dans le sous-réseau pour un contrôle plus strict.

## Utilisez-vous vos propres serveurs DNS ? Et sont-ils accessibles à partir de votre sous-réseau de réseau virtuel ? ##
>[AZURE.NOTE] Vous devez vous assurer que les serveurs DNS de votre réseau sont toujours opérationnels et toujours capables de résoudre les machines virtuelles hébergées sur le réseau virtuel. N'utilisez pas Google DNS pour cela.


Pour les collections hybrides, vous utilisez vos propres serveurs DNS. Vous les spécifiez dans votre schéma de configuration réseau ou via le portail de gestion lorsque vous créez votre réseau virtuel. Les serveurs DNS sont utilisés dans l’ordre dans lequel ils sont spécifiés par basculement, et non par tourniquet (round robin). Reportez-vous à l’article [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) afin de vérifier que vos serveurs DNS sont correctement configurés.

Assurez-vous que les serveurs DNS de votre collection sont accessibles et disponibles à partir du sous-réseau de réseau virtuel spécifié pour cette collection.

Par exemple :

	<VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
	</VirtualNetworkConfiguration>

![Définition de votre serveur DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## Utilisez-vous un contrôleur de domaine Active Directory dans votre collection ? ##
Actuellement, un seul domaine Active Directory peut être associé à Azure RemoteApp. La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés à l’aide de l’outil DirSync à partir d’un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l’option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée. Vous devez créer un domaine personnalisé qui correspond au suffixe de votre domaine local et configurer l'intégration d'annuaire.

Consultez la page [Configuration d’Active Directory pour Azure RemoteApp](remoteapp-ad.md) pour plus d’informations.

Vérifiez que les détails du domaine fournis sont valides et que le contrôleur de domaine est accessible à partir de la machine virtuelle créée dans le sous-réseau utilisé pour Azure RemoteApp. Assurez-vous également que les informations d’identification du compte de service fournies disposent des autorisations nécessaires pour ajouter des ordinateurs au domaine fourni et que le nom Active Directory fourni peut être résolu à partir du DNS fourni dans le réseau virtuel.

## Quel nom de domaine avez-vous spécifié lorsque vous avez créé votre collection ? ##

Le nom de domaine que vous avez créé ou ajouté doit être un nom de domaine interne (et non pas votre nom de domaine Azure AD) et doit être au format DNS pouvant être résolu (contoso.local). Par exemple, vous avez un nom interne Active Directory (contoso.local) et un UPN Active Directory (contoso.com) : vous devez utiliser le nom interne lorsque vous créez votre collection.

<!---HONumber=AcomDC_0518_2016-->