# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Questions fréquemment posées sur la migration du modèle classique vers le modèle Azure Resource Manager

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Ce plan de migration affecte-t-il l’un de mes services ou applications existants qui s’exécutent sur des machines virtuelles Azure ? 

Non. Les machines virtuelles (Classic) sont des services entièrement pris en charge des services à disponibilité générale. Vous pouvez continuer à utiliser ces ressources pour développer votre empreinte sur Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Que se passera-t-il pour mes machines virtuelles si je n’envisage pas de procéder à cette migration dans un avenir proche ? 

Nous ne déconseillons pas l’utilisation des API et du modèle de ressource Classic existants. Compte tenu des fonctionnalités avancées offertes par le modèle de déploiement Resource Manager, notre objectif est de faciliter la migration. Nous vous recommandons donc vivement de passer en revue [certaines des avancées réalisées](../articles/azure-resource-manager/resource-manager-deployment-model.md) en matière d’IaaS dans Resource Manager.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Quelles sont les implications de ce plan de migration pour mes outils existants ? 

La mise à jour de vos outils vers le modèle de déploiement Resource Manager constitue l’un des plus importants changements à prendre en compte dans vos plans de migration.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Quelle sera la durée de l’arrêt du plan de gestion ? 

Cette durée dépend du nombre de ressources dont vous effectuez la migration. Pour les déploiements de plus petite taille (quelques dizaines de machines virtuelles), le processus de migration complet devrait prendre moins d’une heure. Pour les déploiements à grande échelle (plusieurs centaines de machines virtuelles), la migration peut prendre quelques heures.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Puis-je procéder à une restauration après avoir validé la migration de mes ressources dans Resource Manager ? 

Vous pouvez abandonner la migration tant que vos ressources se trouvent à l’état Préparé. La restauration n’est plus prise en charge une fois que les ressources ont fait l’objet d’une migration par le biais de l’opération de validation.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Puis-je restaurer ma migration en cas d’échec de l’opération de validation ? 

Vous ne pouvez pas abandonner la migration si l’opération de validation échoue. Toutes les opérations de migration, notamment l’opération de validation, sont idempotentes. Nous vous recommandons donc de retenter l’opération après une courte période. Si l’erreur persiste, créez un ticket de support ou publiez un billet avec la balise ClassicIaaSMigration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Dois-je acheter un autre circuit ExpressRoute si je dois utiliser la ressource IaaS sous Resource Manager ? 

Non. Nous avons récemment activé la [migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](../articles/expressroute/expressroute-move.md). Vous n’avez pas besoin d’acheter de nouveau circuit ExpressRoute si vous en possédez déjà un.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Que se passera-t-il si j’ai configuré des stratégies de contrôle d’accès en fonction du rôle (RBAC) pour mes ressources IaaS Classic ? 

Pendant la migration, les ressources Classic sont converties en ressources Resource Manager. Nous vous recommandons donc de planifier les mises à jour de stratégies RBAC qui seront nécessaires après la migration.

## <a name="what-if-im-using-azure-site-recovery-or-azure-backup-today"></a>Que se passera-t-il si j’utilise actuellement des services Azure Site Recovery ou Azure Backup ? 

Pour migrer vos machines virtuelles avec sauvegarde activée, consultez [J’ai sauvegardé mes machines virtuelles classiques dans le coffre de sauvegarde. Maintenant, je souhaite les migrer du mode Classic vers le mode Resource Manager. Comment faire pour les sauvegarder dans le coffre Recovery Services ?](../articles/backup/backup-azure-backup-ibiza-faq.md)J’ai sauvegardé mes machines virtuelles classiques dans le coffre de sauvegarde. Maintenant, je souhaite les migrer du mode Classic vers le mode Resource Manager.  Comment faire pour les sauvegarder dans le coffre Recovery Services ?

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une migration ? 

Oui. Dans l’option de migration prise en charge par la plateforme, la première étape de préparation de la migration consiste à s’assurer que les ressources peuvent faire l’objet d’une migration. En cas d’échec de l’opération de validation, vous recevez des messages avec toutes les raisons pour lesquelles la migration ne peut pas aboutir.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Que se passe-t-il si je rencontre une erreur de quota lors de la préparation des ressources IaaS pour la migration ? 

Nous vous recommandons d’abandonner votre migration et de créer ensuite une demande de support en vue d’augmenter les quotas pour la région dans laquelle vous effectuez la migration des machines virtuelles. Une fois votre demande de quota approuvée, vous pouvez réexécuter la procédure de migration.

## <a name="how-do-i-report-an-issue"></a>Comment signaler un problème ? 

Publiez vos problèmes et questions concernant la migration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)en utilisant le mot clé ClassicIaaSMigration. Nous vous recommandons de poster toutes vos questions sur ce forum. Si vous disposez d’un contrat de support, vous pouvez également créer un ticket de support.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Comment procéder si je n’apprécie pas les noms que la plateforme a choisis pour mes ressources lors de la migration ? 

Les noms de toutes les ressources que vous avez explicitement fournis dans le modèle de déploiement sont conservés pendant la migration. Dans certains cas, de nouvelles ressources seront créées. Par exemple, une interface réseau est créée pour chaque machine virtuelle. Actuellement, nous ne prenons pas en charge la possibilité de contrôler les noms de ces ressources créées pendant la migration. Vous pouvez voter en facteur de cette fonctionnalité via le [forum des commentaires sur Azure](http://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Puis-je migrer des circuits ExpressRoute utilisés dans les abonnements avec des liens d’autorisation ? 

Les circuits ExpressRoute utilisant des liens d’autorisation entre abonnements ne peuvent pas être migrés automatiquement sans temps d’arrêt. Il existe des étapes manuelles permettant de migrer ces circuits. Consultez [Migrer des circuits ExpressRoute et les réseaux virtuels associés du modèle de déploiement classique au modèle de déploiement Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md) pour voir la procédure et obtenir plus d’informations.

## <a name="i-got-a-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated-"></a>J’ai reçu un message *« La machine virtuelle signale que l’agent est dans l’état général Pas prêt. Par conséquent, la machine virtuelle ne peut pas être migrée. Assurez-vous que l’état général de l’agent signalé par l’agent de machine virtuelle est « Prêt »* ou *« La machine virtuelle contient une extension dont l’état n’est pas signalé par la machine virtuelle. Par conséquent, elle ne peut pas faire l’objet d’une migration. ». *

Ce message est affiché lorsque la machine virtuelle n’a pas de connectivité sortante à Internet. L’agent de machine virtuelle utilise la connectivité sortante pour atteindre le compte de stockage Azure afin de mettre à jour l’état de l’agent toutes les 5 minutes.
