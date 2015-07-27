
<properties 
    pageTitle="Résolution des problèmes de création de collections hybrides RemoteApp"
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
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# Résolution des problèmes de création de collections hybrides Azure RemoteApp

Une collection hybride est hébergée dans le cloud Azure et y stocke également les données, mais elle permet aussi aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise synchronisées ou fédérées avec Azure Active Directory. Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure existant ou vous pouvez créer un réseau virtuel. Nous vous recommandons d’utiliser une plage CIDR suffisamment étendue lorsque vous créez ou utilisez un sous-réseau de réseau virtuel afin de pouvoir prendre en compte la croissance future d’Azure RemoteApp.

Vous n’avez pas encore créé votre collection ? Pour plus d’informations, consultez la page [Création d’une collection hybride](remoteapp-create-hybrid-deployment.md).

Si vous rencontrez des problèmes lors de la création de votre collection, ou si la collection ne fonctionne pas comme elle devrait, vérifiez les informations suivantes.

## Votre réseau virtuel utilise-t-il le tunneling forcé ? ##
RemoteApp ne prend actuellement pas en charge les réseaux virtuels sur lesquels le tunneling forcé est activé. Si vous avez besoin de cette fonction, contactez l’équipe RemoteApp.

Une fois votre demande approuvée, assurez-vous que les ports suivants sont ouverts sur le sous-réseau que vous avez choisi pour Azure RemoteApp et les machines virtuelles du sous-réseau. Les machines virtuelles sur vos sous-réseaux doivent également pouvoir accéder aux URL mentionnées dans la section sur les groupes de sécurité réseau.

Sortant : TCP : 443, TCP : 10101-10175

## Votre réseau virtuel comporte-t-il des groupes de sécurité réseau ? ##
Si vous avez défini des groupes de sécurité réseau sur le sous-réseau que vous utilisez pour votre collection, assurez-vous que les URL suivantes sont accessibles à partir de votre sous-réseau :

	https://management.remoteapp.windowsazure.com  
	https://opsapi.mohoro.com  
	https://telemetry.remoteapp.windowsazure.com  
	https://*.remoteapp.windowsazure.com  
	https://login.windows.net (if you have Active Directory)  
	https://login.microsoftonline.com  
	Azure storage *.remoteapp.windowsazure.com  
	*.core.windows.net  
	https://www.remoteapp.windowsazure.com  
	https://www.remoteapp.windowsazure.com  

Ouvrez les ports suivants sur le sous-réseau de réseau virtuel :

Entrant - TCP : 3030, TCP : 443 Sortant - TCP : 443

Vous pouvez ajouter d’autres groupes de sécurité réseau pour les machines virtuelles que vous avez déployées dans le sous-réseau pour un contrôle plus strict.

## Utilisez-vous vos propres serveurs DNS ? Et sont-ils accessibles à partir de votre sous-réseau de réseau virtuel ? ##
Pour les collections hybrides, vous utilisez vos propres serveurs DNS. Vous les spécifiez dans votre schéma de configuration réseau ou via le portail de gestion lorsque vous créez votre réseau virtuel. Les serveurs DNS sont utilisés dans l’ordre dans lequel ils sont spécifiés par basculement, et non par tourniquet (round robin).

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

Pour plus d’informations, consultez la page [Résolution de noms à l’aide de votre propre serveur DNS](https://msdn.microsoft.com/library/azure/jj156088.aspx#bkmk_BYODNS).

## Utilisez-vous un contrôleur de domaine Active Directory dans votre collection ? ##
Actuellement, un seul domaine Active Directory peut être associé à Azure RemoteApp. La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés à l’aide de l’outil DirSync à partir d’un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l’option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée. Vous devez créer un domaine personnalisé qui correspond au suffixe de votre domaine local et configurer l'intégration d'annuaire.

Consultez la page [Configuration d’Active Directory pour Azure RemoteApp](remoteapp-ad.md) pour plus d’informations.

Vérifiez que les détails du domaine fournis sont valides et que le contrôleur de domaine est accessible à partir de la machine virtuelle créée dans le sous-réseau utilisé pour Azure RemoteApp. Assurez-vous également que les informations d’identification du compte de service fournies disposent des autorisations nécessaires pour ajouter des ordinateurs au domaine fourni et que le nom Active Directory fourni peut être résolu à partir du DNS fourni dans le réseau virtuel.

## Quel nom de domaine avez-vous spécifié lorsque vous avez créé votre collection ? ##

Le nom de domaine que vous avez créé ou ajouté doit être un nom de domaine interne (et non pas votre nom de domaine Azure AD) et doit être au format DNS pouvant être résolu (contoso.local). Par exemple, vous avez un nom interne Active Directory (contoso.local) et un UPN Active Directory (contoso.com) : vous devez utiliser le nom interne lorsque vous créez votre collection.

<!---HONumber=July15_HO3-->