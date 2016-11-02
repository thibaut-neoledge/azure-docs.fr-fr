<properties
  pageTitle="Utiliser Azure DNS avec d’autres services Azure | Microsoft Azure"
  description="Comprendre comment utiliser Azure DNS pour résoudre le nom d’autres services Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>


# <a name="using-azure-dns-with-other-azure-services"></a>Utiliser Azure DNS avec d’autres services Azure

Azure DNS est un service hébergé de gestion de DNS et de résolution de noms. Il vous permet de créer des noms DNS publics pour les autres applications et services que vous avez déployés dans Azure. La création d’un nom pour un service Azure dans votre domaine personnalisé est aussi simple que l’ajout d’un enregistrement du type correct pour votre service.

* Pour les adresses IP allouées de manière dynamique, vous devez créer un enregistrement DNS CNAME qui mappe au nom DNS qu’Azure a créé pour votre service. Les normes DNS vous empêchent d’utiliser un enregistrement CNAME à l’extrémité de la zone.
* Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A utilisant n’importe quel nom, notamment un nom de _domaine nu_ à l’extrémité de la zone.

Le tableau suivant décrit les types d’enregistrement pris en charge qui peuvent être utilisés pour les différents services Azure. Comme vous pouvez le voir à partir de cette table, Azure DNS prend uniquement en charge les enregistrements DNS pour les ressources réseau accessibles sur Internet. Azure DNS ne peut pas être utilisé pour la résolution de nom d’adresses privées internes.

| Service Azure | Interface réseau | Description |
|---------------|-------------------|-------------|
| Application Gateway | Adresse IP publique frontale | Vous pouvez créer un enregistrement DNS A ou CNAME. |
| Load Balancer | Adresse IP publique frontale | Vous pouvez créer un enregistrement DNS A ou CNAME. L’équilibreur de charge peut disposer d’une adresse IP publique IPv6 affectée de manière dynamique. Par conséquent, vous devez créer un enregistrement CNAME pour une adresse IPv6. |
| Traffic Manager | Nom public | Vous pouvez uniquement créer un CNAME qui mappe au nom trafficmanager.net affecté à votre profil Traffic Manager. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Service cloud | Adresse IP publique | Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A. Pour les adresses IP allouées de manière dynamique, vous devez créer un enregistrement CNAME qui mappe au nom _cloudapp.net_ . Cette règle s’applique aux machines virtuelles créées dans le portail classique, car elles sont déployées sous la forme d’un service cloud. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Services cloud](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| App Service | Adresse IP externe | Pour les adresses IP externes, vous pouvez créer un enregistrement DNS A. Dans le cas contraire, vous devez créer un enregistrement CNAME qui mappe au nom azurewebsites.net. Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Machines virtuelles Resource Manager | Adresse IP publique | Les machines virtuelles Resource Manager peuvent avoir des adresses IP publiques. Une machine virtuelle avec une adresse IP publique peut également être derrière un équilibreur de charge. Vous pouvez créer un enregistrement DNS A ou CNAME pour l’adresse publique. Ce nom personnalisé peut être utilisé pour contourner l’adresse IP virtuelle de l’équilibreur de charge. |
| les machines virtuelles Classic, | Adresse IP publique | Les machines virtuelles classiques créées à l’aide de PowerShell ou de l’interface de ligne de commande peuvent être configurées avec une adresse virtuelle (réservée) dynamique ou statique. Vous pouvez créer un enregistrement DNS CNAME ou A, respectivement. |



<!--HONumber=Oct16_HO2-->


