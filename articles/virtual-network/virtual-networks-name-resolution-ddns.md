<properties 
   pageTitle="Utilisation de DNS dynamique pour inscrire les noms d’hôte"
   description="Cette page fournit des détails sur la configuration de DNS dynamiques pour inscrire les noms d’hôtes dans vos propres serveurs DNS."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Utilisation de DNS dynamique pour inscrire les noms d’hôte
[Azure fournit la résolution de noms](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour les machines virtuelles et les instances de rôle. Lorsque votre résolution de noms doit aller au-delà de ceux fournis par Azure, vous pouvez fournir vos propres serveurs DNS. Cela vous permet de personnaliser votre solution DNS pour l’adapter à vos propres besoins. Par exemple, l’accès à votre contrôleur de domaine Active Directory local.

Comme Azure n’a pas la possibilité (informations d’identification, par exemple) d’inscrire directement les enregistrements dans vos serveurs DNS, d’autres mécanismes sont souvent nécessaires. Ci-dessous, vous trouverez certains détails de haut niveau de quelques-uns des scénarios les plus courants.

## Clients Windows ##
Les clients Windows non joints à un domaine tentent des mises à jour DNS dynamiques non sécurisées lors du démarrage ou de la modifications de leurs adresses IP. Le nom DNS se compose du nom d’hôte et du suffixe DNS principal. Azure laisse le suffixe DNS principal vide, mais ce dernier peut être remplacé dans la machine virtuelle, via l’[interface utilisateur](https://technet.microsoft.com/library/cc794784.aspx) ou [avec Automation](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Les clients Windows joints à un domaine inscrivent leurs adresses IP auprès du contrôleur de domaine à l’aide d’un DDNS sécurisé. Le processus de jonction de domaine définit le suffixe DNS principal sur le client et gère la relation d’approbation.

## Clients Linux ##
Généralement, les clients Linux ne s’inscrivent pas eux-mêmes auprès du serveur DNS au démarrage et présument que le serveur DHCP le fait à leur place. Le package Bind est fourni avec un outil appelé *nsupdate* qui peut être utilisé pour envoyer les mises à jour DDNS. Comme le protocole DDNS est normalisé, *nsupdate* peut être utilisé même si vous n’utilisez pas Bind sur le serveur DNS.

Le client DHCP Linux fournit les raccordements qui vous permettent d’exécuter des scripts lorsqu’une adresse IP est affectée ou modifiée. Par exemple, dans */etc/dhcp/dhclient-exit-hooks.d/*. Cela permet d’inscrire le nom d’hôte dans DNS. Par exemple :
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

La commande *nsupdate* permet aussi d’effectuer des mises à jour DDNS sécurisées. Par exemple, lorsque vous utilisez un serveur DNS Bind, une paire de clés publique/privée est [générée](http://linux.yyz.us/nsupdate/) et le serveur DNS est [configuré](http://linux.yyz.us/dns/ddns-server.html) avec la partie publique de la clé, ce qui lui permet de vérifier la signature de la demande. Pour signer la demande de mise à jour DDNS, la paire de clés doit être fournie à *nsupdate* à l’aide de son option *-k*.

Lorsque vous utilisez un serveur DNS Windows, l’authentification de Kerberos peut être utilisée avec le commutateur *-g* de nsupdate (non disponible dans la version Windows de *nsupdate*). Pour ce faire, utilisez *kinit* pour charger les informations d’identification (depuis un [fichier keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html), par exemple), puis *nsupdate -g* sélectionnera les informations d’identification à partir du cache.

Si nécessaire, un suffixe de recherche DNS peut être ajouté à vos machines virtuelles. Le suffixe DNS est spécifié dans le fichier */etc/resolv.conf*. Comme la plupart des distributions Linux gèrent automatiquement le contenu de ce fichier, il ne peut généralement pas être modifié. Cependant, le suffixe peut être substitué à l’aide de la commande *supersede* du client DHCP ; dans */etc/dhcp/dhclient.conf*, ajoutez :

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->