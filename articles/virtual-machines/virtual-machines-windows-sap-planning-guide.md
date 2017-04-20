<properties
   pageTitle="SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação do | Microsoft Azure"
   description="SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-windows-virtual-machines-vms--planning-and-implementation-guide"></a>SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação do DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (colocação em cache para VMs e VHDs) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (armazenamento do Windows Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (estrutura de uma implementação RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (elevada disponibilidade e recuperação de falhas com Azure VMs) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e lançamentos anteriores) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (utilizando uma imagens do SQL Server fora do Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (geral do SQL Server para SAP no Azure resumo) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (especificidades para SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configuração de armazenamento) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (fazer cópia de segurança e restaurar) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (considerações de desempenho para cópia de segurança e restauro) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (outro) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP recursos) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (implementar uma VM com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (cenário 1: implementar uma VM terminar o Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (cenário 2: implementar uma VM com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md# a9a60133-a763-4de8-8986-ac0fa33aa8c1 (cenário 3: mover uma VM no local utilizando um VHD de Azure não generalized com SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (implementação cenários de VMs para SAP no Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlets de implementação do Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (transferir e pela SAP, importar relevantes os cmdlets do PowerShell) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (participar VM para o domínio no local - apenas para o Windows) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md# 6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (transferir, instalar e ativar o Azure VM agente) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (configurar Azure avançada monitorização extensão para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (verificação de preparação para Azure avançada monitorização para SAP) [5.2 de guia de implementação]: virtual-Machines-Windows-SAP-Deployment-Guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (verificação de integridade de configuração de infraestrutura de monitorização Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação do) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11.4]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidade (HA) e falhas recuperação (DR) para o SAP NetWeaver em execução no máquinas virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (elevada disponibilidade para os servidores de aplicações SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (utilizar início automático para instâncias de SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md# 1625df66-4CC6-4d60-9202-de8a0b77f803 (apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regiões) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (falhas de domínios) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (atualizar domínios) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088- F9BE - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (do Microsoft Azure Máquina Virtual conceito) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (mover uma VM nos locais para o Azure com um disco não generalized) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (implementar uma VM com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparação para mover uma VM nos locais para o Azure com um não generalized disco) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparação para implementar uma VM com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (preparar VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferença entre um Azure disco e imagem do Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (carregar um VHD nos locais para Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiar discos entre contas de armazenamento do Azure) [5.5.1 de planeamento-guia]: virtual-Machines-Windows-SAP-Planning-Guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (estrutura VM/VHD para implementações do SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montagem definição automática para discos ligados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (única VM com SAP NetWeaver demonstração/formação cenário) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implementação conceitos de Cloud-Only de instâncias do SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitorização solução para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md# ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (armazenamento de Azure Premium)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

Microsoft Azure permite às empresas adquirir recursos cluster e armazenamento altura mínimas sem ciclos de contratos comprida. Azure máquinas virtuais permitem às empresas implementar clássica aplicações, como SAP NetWeaver com base aplicações para o Azure e expandir as respetivas fiabilidade e disponibilidade sem ter ainda mais recursos disponíveis no local. Serviços de Máquina Virtual Azure também suporta a conectividade de publicação em local, que permite aos empresas ativamente entregar máquinas virtuais do Azure nos seus domínios no local, os respetivos nuvens privado e os respetivos horizontal de sistema do SAP.
Este documento técnico descreve os conceitos básicos de Máquina Virtual da Microsoft Azure e fornece uma guia passo a passo de considerações de planeamento e implementação para SAP NetWeaver instalações no Azure e como tal deve ser o documento para ler antes do início reais implementações do SAP NetWeaver no Azure.
Papel completa a documentação de instalação do SAP e SAP notas que representam os recursos principais para implementações do software SAP e instalações em determinadas plataformas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="summary"></a>Resumo
É um termo amplamente utilizado que está a ganhar mais importância dentro da indústria IT, de pequenas empresas até empresas de grandes e multinacionais de informática em nuvem.

Microsoft Azure é a plataforma de serviços na nuvem da Microsoft que disponibiliza um grande espectro de novas possibilidades. Agora clientes conseguem rapidamente disposição e a disposição das aplicações do como um serviço na nuvem, para que não estão limitados a restrições técnicas ou orçamentação. Em vez de investir tempo e o orçamento para infraestrutura de hardware, empresas podem concentrar-na aplicação, processos de negócio e os benefícios para clientes e utilizadores.

Com os serviços de Máquina Virtual do Microsoft Azure, a Microsoft oferece uma infraestrutura abrangente como uma plataforma de serviço (IaaS). As aplicações SAP NetWeaver com base são suportadas no Azure máquinas virtuais (IaaS). Este documento técnico irá descrevem como planear e implementar aplicações de SAP NetWeaver com base no Microsoft Azure como a plataforma de escolha.

Papel propriamente dito irá focar-se em dois aspectos principais:

* A primeira parte irá descrevem duas padrões de implementação suportados para as aplicações SAP NetWeaver com base no Azure. -Lo também irá descrever processamento geral do Azure com implementações SAP deve ter em conta.
* A segunda parte será de detalhe implementar os dois cenários diferentes descritos na primeira parte.

Para obter recursos adicionais consulte capítulo [recursos] [1.2 de planeamento-guia] neste documento.

### <a name="definitions-upfront"></a>Definições de upfront
Em todo o documento utilizamos os termos seguintes:

* IaaS: Infraestrutura como um serviço.
* PaaS: Plataforma como um serviço.
* SaaS: Software como um serviço.
* PROCESSADOR: Gestor de recursos do Azure
* Componente do SAP: uma SAP aplicação individual, como ECC, p/b, Gestor de solução ou EP.  Componentes SAP podem ser baseadas no tecnologias ABAP ou Java tradicionais ou uma aplicação que não sejam com base NetWeaver como objetos de negócio.
* Ambiente do SAP: um ou mais componentes SAP agrupados de forma lógica execute uma função de negócio como desenvolvimento, QAS, formação, DR ou de produção.
* SAP horizontal: Isto refere-se para os ativos SAP inteiros de um cliente horizontal IT. Paisagem SAP inclui todas produção e não produção ambientes.
* Sistema SAP: A combinação de camada DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento do SAP ERP, p/b do SAP teste sistema, sistema de produção SAP CRM, etc... Em implementações Azure-não é suportada para dividir estas duas camadas entre no local e Azure. Isto significa que um sistema SAP é seja implementado no local ou for implementado no Azure. No entanto, pode implementar os diferentes sistemas de um horizontal SAP para Azure ou no local. Por exemplo, pode implementar o desenvolvimento de SAP CRM e sistemas de teste no Azure, mas o SAP CRM produção sistema no local.
* Implementação apenas na nuvem: uma implementação onde a subscrição Azure não está ligada através de um site para o site ou ligação ExpressRoute para a infraestrutura de rede no local. Em comum documentação Azure estes tipos de implementações são também descritos como 'Só de nuvem' híbridas. Máquinas virtuais implementadas com este método são acedidas através da internet e um endereço ip público e/ou um nome de DNS público atribuído VMs no Azure. Para o Microsoft Windows no local Active Directory (AD) e DNS não é expandido para Azure nestes tipos de implementações. Por conseguinte, as VMs não fazem parte do Active Directory no local. Mesmo se aplica para implementações Linux utilizando, por exemplo, OpenLDAP + Kerberos.

> [AZURE.NOTE] Apenas na nuvem implementações neste documento é definida como concluídas SAP paisagens estiver a executar o exclusivamente no Azure sem extensão do Active Directory / OpenLDAP ou resolução do nome no local para o público na nuvem. Configurações apenas na nuvem não são suportadas para sistemas SAP de produção ou configurações onde SAP stm ou outros recursos no local necessite de ser utilizada entre os sistemas SAP alojados no Azure e recursos que residem no local.

* Publicação em local: Descreve um cenário em que VMs são implementadas para uma subscrição do Azure que tenha o site para o site, múltiplos sites ou ExpressRoute conectividade entre o datacenter(s) no local e o Azure. Documentação em comum Azure, estes tipos de implementações também são descritos como cenários de publicação em local. O motivo para a ligação é alargar domínios no local, no local do Active Directory / OpenLDAP e no local DNS para o Azure. Paisagem no local é expandida para os ativos Azure da subscrição. Ter esta extensão, os VMs podem fazer parte do domínio no local. Utilizadores do domínio do domínio no local podem aceder aos servidores e podem executar serviços nesses VMs (como DBMS serviços). Resolução de comunicação e o nome entre VMs implementados no local e Azure VMs implementados é possível. Este é o cenário que poderemos esperar a maior parte dos elementos do SAP para ser implementada no.  Consulte o artigo [Este] [ vpn-gateway-cross-premises-options] artigo e [Este] [ vpn-gateway-site-to-site-create] para obter mais informações.

> [AZURE.NOTE] Implementações cruzada local do sistemas SAP onde máquinas virtuais do Azure com sistemas SAP estão membros de um domínio no local são suportadas para sistemas SAP de produção. Configurações de publicação em local são suportadas para implementar peças ou concluir paisagens SAP para Azure. Mesmo a execução de paisagem SAP concluída no Azure requer tendo aqueles VMs a ser parte do domínio no local e anúncios / OpenLDAP. Em versões anteriores da documentação falámos sobre cenários de híbrido-IT, onde o termo 'Híbrido' com raiz no facto de que existe uma ligação de publicação em local entre no local e Azure. Para além disso, o facto de que o VMs no Azure fazem parte do Active Directory no local / OpenLDAP.

Algumas documentação da Microsoft descreve cenários de publicação em local de forma um pouco diferente, especialmente para configurações DBMS HA. No caso do SAP documentos relacionados, o cenário de publicação em local apenas é ter uma site para o site ou privada conectividade de (ExpressRoute) e o facto de que a paisagem SAP é distribuída entre no local e Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
Os seguintes guias adicionais estão disponíveis para o tópico de implementações do SAP no Azure:

* [SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação do (este documento)] [-guia de planeamento]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação] [guia de implementação]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação do DBMS] [dbms guia]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação de elevada disponibilidade][ha-guide]

> [AZURE.IMPORTANT] Sempre que é utilizado possível uma ligação para o guia de instalação do SAP referência (referência InstGuide-01, consulte o artigo <http://service.sap.com/instguides>). Quando chegar para a pré-requisitos e o processo de instalação, as guias de instalação do SAP NetWeaver sempre deverá ler cuidadosamente, tal como este documento apenas abrange tarefas específicas para sistemas SAP NetWeaver instalados numa máquina de Virtual do Microsoft Azure.

As seguintes notas SAP estão relacionados com o tópico da SAP no Azure:

| Número da nota | Título |
|--------------|-------|
| [1928533] | As aplicações SAP no Azure: suportadas produtos e redimensionamento |
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] | Resolução de problemas avançada Azure de monitorização para SAP |
| [2178632] | Tecla de métricas de monitorização para SAP no Microsoft Azure |
| [1409604] | Virtualização no Windows: as funcionalidades de monitorização |
| [2191498] | SAP no Linux com Azure: as funcionalidades de monitorização
| [2243692] | Linux no Microsoft Azure (IaaS) VM: problemas de licença do SAP
| [1984787] | SUSE LINUX Enterprise Server 12: Notas de instalação
| [2002167] | Chapéu vermelho Enterprise Linux 7. x: instalação e atualização

Leia também [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas de SAP para Linux.

Limitações de predefinido gerais e limitações máximas de subscrições Azure podem ser encontradas [neste] artigo[azure-subscription-service-limits-subscription] 

## <a name="possible-scenarios"></a>Cenários possíveis
SAP é frequentemente visualizada como uma das aplicações do mais críticos dentro de empresas. A arquitetura e operações destas aplicações é principalmente muito complexo e garantir que cumpre os requisitos da disponibilidade e desempenho é importante.

Assim empresas têm a ter em conta cuidadosamente relativos às aplicações podem ser executadas num ambiente do público na nuvem, independentemente do fornecedor na nuvem que selecionou.

Tipos de sistema possíveis para implementar o SAP NetWeaver baseados aplicações dentro de nuvem pública ambientes estão indicadas abaixo:

1. Sistemas de produção tamanho médio
1. Sistemas de desenvolvimento
1. Sistemas de testes
1. Sistemas de protótipo
1. Formação / sistemas de demonstração

Para implementar com êxito o sistemas SAP para Azure IaaS ou IaaS em geral, é importante compreender as diferenças entre as ofertas de outsourcers tradicionais ou responsáveis e ofertas IaaS significativas. Enquanto o depare tradicional ou outsourcer irá adaptar infraestrutura (tipo de rede, armazenamento e server) para um cliente pretende alojar a carga de trabalho, em vez disso, é responsabilidade do cliente para escolher a carga de trabalho à direita para implementações IaaS.

Como primeiro passo, clientes precisa de verificar os seguintes itens:

* O SAP suportados tipos VM do Azure
* O SAP produtos/edições suportadas no Azure
* O sistema operativo e DBMS suportados liberta para as versões específicas do SAP no Azure
* Débito SAP fornecido pela diferentes SKUs do Azure

As respostas a estas questões podem ser lidos na nota SAP [1928533]. 

Como um segundo passo, o Azure limitações de recurso e largura de banda precisa de ser comparado com consumo de recursos reais de sistemas no local. Por conseguinte, clientes tem de estar familiarizado com as capacidades de diferentes dos tipos de Azure suportados com SAP na área de:

* Recursos de memória e CPU de diferentes tipos VM e
* Largura de banda IOPS de diferentes tipos VM e 
* Capacidades de rede de diferentes tipos VM.

A maior parte desses dados pode ser encontradas [aqui][virtual-machines-sizes]

Lembre-se de que os limites de listado na hiperligação acima são limites superiores. Não significa que os limites para qualquer um dos recursos, por exemplo, IOPS podem ser fornecidos em todas as circunstâncias. As exceções consistem embora os recursos de memória e CPU de um tipo VM que selecionou. Para os tipos VM suportados pela SAP, os recursos de memória e CPU estão reservadas e como tal disponíveis em qualquer ponto no tempo para consumo dentro da VM.

A plataforma Microsoft Azure como noutras plataformas IaaS é uma plataforma do inquilino com várias. Isto significa que o armazenamento, rede e outros recursos são partilhados entre inquilinos. Lógica de limitação e quota inteligente é utilizada para impedir que um inquilino que afetam o desempenho do outro inquilino (vizinho ruído) de uma forma importante. Apesar de lógica no Azure tenta manter variâncias em plataformas de largura de banda ocorreu ao pequeno, mas altamente partilhada tendem para apresentar variâncias maiores na disponibilidade de recursos/largura de banda que muitas clientes são utilizadas para nas suas implementações no local. Como resultado, que se poderá deparar diferentes níveis de largura de banda no que respeita a rede ou armazenamento e/s (o volume, bem como latência) a partir de um minuto para minutos. A probabilidade que um sistema SAP no Azure poderia experiência variâncias maiores do que num sistema no local tem de ser tidos em conta.

Um último passo é avaliar os requisitos de disponibilidade. Pode acontecer, que a infraestrutura de Azure subjacente tem de ser atualizadas e necessita de anfitriões executar VMs ser reiniciado. Nestes casos, VMs em execução nesses anfitriões seriam encerrar e reiniciados também. A temporização dessa manutenção é feita durante o horário de empresas que não sejam core para uma região específico, mas a janela potencial de algumas horas durante o qual irá ocorrer um reinício está relativamente a largura. Existem vários tecnologias dentro da plataforma Azure que pode ser configurado para mitigar algumas ou todas as o impacto dessas atualizações. Melhoramentos futuros da plataforma do Azure, aplicação DBMS e pela SAP, foram concebidos para minimizar o impacto dessas ser reiniciado. 

Para implementar com êxito um sistema SAP Azure, no local SAP sistemas sistema operativo, a base de dados e as aplicações SAP tem de aparecer na matriz de suporte SAP Azure, ajustar dentro os recursos pode fornecer infraestrutura do Azure e que pode trabalhar com as ofertas de disponibilidade SLA Microsoft Azure. Como são identificados esses sistemas, tem de decidir das seguintes cenários de implementação de duas.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local
 
![Única VM com cenário de formação implementado no Azure ou demonstração do SAP][planning-guide-figure-100]

Este cenário é típico para formações ou sistemas de demonstração, onde todos os componentes de SAP e o software não SAP estão instalados dentro de uma única VM. Sistemas SAP de produção não são suportados neste cenário de implementação. Em geral, este cenário cumpre os seguintes requisitos:

* Os VMs próprios são acessíveis através da rede pública. A conectividade da rede direta para as aplicações em execução no interior de VMs à rede no local da empresa ou se o conteúdo demos ou formações ou o cliente não é necessária. 
* Em caso de vários VMs que representa o cenário de demonstração ou formações, resolução de comunicações e o nome de rede tem de trabalhar entre os VMs. Mas as comunicações entre o conjunto de VMs tem de ser isoladas para que a vários conjuntos de VMs podem ser implementados lado a lado sem interferência.  
* Ligação à Internet é necessária para o utilizador final remoto início de sessão para VMs alojado no Azure. Consoante o convidado SO, serviços de Terminal/RDS ou VNC/ssh é utilizada para aceder a VM para satisfazer as tarefas de formação ou executar as demonstrações do. Se SAP portas como 3200, 3300 e 3600 podem também ser expostos a instância da aplicação SAP pode ser acedida a partir de qualquer ambiente de trabalho ligada do Internet.
* Os sistemas SAP (e VM(s)) representam um cenário de autónomo no Azure que apenas necessita de conectividade de internet público para o acesso do utilizador final e não requer uma ligação a outras VMs no Azure.
* SAPGUI e de um browser instalados e executar diretamente na VM. 
* É necessária uma rápida reposição de uma VM ao estado original e a nova implementação desse estado original novamente. 
* No caso de cenários de formação que são realizados em vários VMs, um Active Directory e demonstração / serviço OpenLDAP e/ou DNS é necessário para cada conjunto de VMs.


![Grupo de VM que representa uma demonstração ou cenário de formação num serviço de nuvem do Azure][planning-guide-figure-200]

É importante ter em conta que VM(s) em cada um dos conjuntos precisa de ser implementada em paralelo, onde os nomes VM em cada uma do conjunto são os mesmos.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Publicação em local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local
 
![VPN com a conectividade de Site para o Site (por comparação local)][planning-guide-figure-300]

Este cenário é um cenário de publicação em local com muitas padrões de implementação possíveis. Pode ser descrito simplesmente como executar algumas partes do SAP horizontal no local e outras partes do SAP horizontal no Azure. Todos os aspetos do facto de parte dos componentes do SAP estiverem em execução no Azure devem ser transparentes para os utilizadores finais. Por conseguinte, o sistema de correção de transporte do SAP (STM), comunicação RFC, impressão, segurança (por exemplo, SSO), etc. irá funcionar totalmente para os sistemas SAP em execução no Azure. Mas o cenário de publicação em local também descreve um cenário onde paisagem SAP concluída é executado no Azure com o domínio de cliente e DNS expandido para o Azure. 

> [AZURE.NOTE] Este é o cenário de implementação que é suportado para executar o produtivos sistemas SAP.

Leia [Este artigo] [ vpn-gateway-create-site-to-site-rm-powershell] para mais informações sobre como ligar à rede no local ao Microsoft Azure

> [AZURE.IMPORTANT] Quando podemos estiver a falar sobre cenários de publicação em local entre implementações de cliente do Azure e no local, podemos esteja a visualizar a granularidade da totalidade sistemas SAP. Cenários que são _não suportadas_ de cenários de publicação em local são:
> 
> * Em execução dos diferentes níveis das aplicações SAP no diferentes métodos de implementação. Por exemplo a executar o DBMS camada no local, mas a camada de aplicação SAP no VMs implementados como Azure VMs ou vice versa.
> * Alguns componentes de uma camada SAP no Azure e algumas no local. Por exemplo Dividir instâncias da camada de aplicação do SAP entre no local e Azure VMs. 
> * Distribuição dos VMs instâncias de SAP de um sistema de em execução através de vários Azure regiões não é suportada.
> 
> O motivo para estas restrições é o requisito de uma rede de alto desempenho latência muito baixo dentro de um sistema SAP, especialmente entre as instâncias da aplicação e a camada DBMS de um sistema SAP.



### <a name="supported-os-and-database-releases"></a>Sistema operativo e versões de base de dados suportadas

* Software de servidor do Microsoft suportada para serviços de Máquina Virtual Azure é listados neste artigo: <http://support.microsoft.com/kb/2721672>. 
* Sistema operativo suportado lançamentos lançamentos do sistema de base de dados suportados nos serviços de Máquina Virtual Azure em conjunto com um software de SAP estão documentados na nota SAP [1928533]. 
* As aplicações SAP e lançamentos suportados nos serviços de Máquina Virtual Azure estão documentados na nota de SAP [1928533].
* Imagens de 64 bits só são suportadas para executar como convidado VMs no Azure para cenários de SAP. Isto significa também que são suportados apenas 64-bit as aplicações SAP e bases de dados.

## <a name="microsoft-azure-virtual-machine-services"></a>Serviços de Máquina Virtual do Microsoft Azure
A plataforma do Microsoft Azure é uma plataforma de serviços internet escala nuvem alojado e operado nos centros de dados do Microsoft. A plataforma inclui os serviços de Máquina Virtual do Microsoft Azure (infraestrutura como um serviço ou IaaS) e um conjunto de plataforma formatado como uma capacidades de serviço (PaaS).

A plataforma Azure reduz a necessidade de tecnologia inicial e infraestrutura de compras. Simplifica a manutenção e funcionamento aplicações ao fornecer a pedido cluster e armazenamento alojar, dimensionar e gerir a aplicação web e aplicações ligadas. Gestão de infraestrutura é automatizado com uma plataforma que foi concebida para elevada disponibilidade e dinâmicos dimensionamento para que correspondam às necessidades em termos de utilização com a opção de um modelo de preços repartição.


 
![Posicionamento dos serviços de Máquina Virtual do Microsoft Azure][planning-guide-figure-400]

Com o Azure Máquina Virtual Services, Microsoft é permitindo-lhe implementar imagens de servidor personalizados para Azure como instâncias IaaS (consulte figura 4). As máquinas virtuais no Azure são baseados nas unidades de disco rígido Hyper-V virtuais (VHD) e podem executar diferentes sistemas operativos como SO convidado.

A partir de uma perspetiva operacionais avançada, o serviço de Máquina Virtual Azure oferece experiências semelhantes como máquinas virtuais implementadas no local. No entanto, tem a vantagem significativa que não precisa de adquirir, administrar e gerir a infraestrutura. Os programadores e administradores têm controlo total de imagem dentro estes máquinas virtuais do sistema operativo. Os administradores de início de sessão podem remotamente em máquinas virtuais para efetuar manutenção e resolução de problemas de tarefas, bem como tarefas de implementação de software. No que diz respeito implementação, as restrições só são os tamanhos e capacidades do Azure VMs. Estes podem não ser tão finos granular configuração Isto poderá ser feito no local. Existe uma opção de tipos VM que representam uma combinação de:

* Número de vCPUs,
* Memória,
* Número de VHDs que pode ser anexado
* Larguras de banda de rede e de armazenamento.

O tamanho e limitações das várias máquinas virtuais diferentes tamanhos oferecidos podem ser vistos numa tabela [neste] artigo[virtual-machines-sizes]

Como será apercebeu-se existem famílias de tipos de diferentes ou série de máquinas virtuais. Partir dez de 2015, é possível distinguir seguintes famílias de VMs:

* Tipos de VM a0 A7: não de todas as tenham certificação para SAP. Série VM primeiro que Azure IaaS tem introduzida com.
* Tipos de VM a8 A11: instâncias de computação de alto desempenho. Em execução sobre como efectuar uma melhor diferentes calcular anfitriões que outros VMs A série.
* Tipos de série D VM: melhor efetuar que A0 A7. Todos os tipos VM não são certificados com SAP.
* Tipos de série DS VM: utilizar o mesmo anfitriões como D série, mas são possível ligar ao armazenamento de Premium do Azure (consulte capítulo [Azure Premium armazenamento] [3.3.2 de planeamento-guia] deste documento). Novamente nem todos os tipos VM tenham certificação com SAP.
* Tipos de série G VM: tipos VM memória alta. 
* Tipos de série s VM: gostar série G mas incluindo a opção para utilizar o armazenamento do Windows Azure Premium (consulte capítulo [Azure Premium armazenamento] [3.3.2 de planeamento-guia] deste documento). Ao utilizar VMs da série s como os servidores de base de dados é obrigatória para utilizar Premium armazenamento para ficheiros de registo de dados e da transação DB


Que poderá encontrar as configurações de memória e CPU mesmo na série VM diferente. No entanto, quando procurar o desempenho destes VMs terminar a série de diferentes débito estes podem ser diferentes significativamente. Apesar de ter a mesma configuração de memória e CPU. Motivo é que o hardware do servidor anfitrião subjacente na introdução dos diferentes tipos VM tinha características diferentes débito.  Normalmente, a diferença apresentada no desempenho débito também é reflectida no preço das VMs diferentes.

Tenha em atenção que diferentes não todas as séries de VM poderão ser-lhe fornecidos em cada uma das regiões Azure (para ver do Azure regiões capítulo seguinte). Tenha também em atenção de que não em todos os VMs ou série de VM tenham certificação para SAP.

> [AZURE.IMPORTANT] Para a utilização de aplicações do SAP NetWeaver com base, apenas o subconjunto de tipos de VM e configurações listadas na nota SAP [1928533] são suportados.

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões Azure
Microsoft permite para implementar máquinas virtuais para o chamado 'Azure regiões'. Uma região Azure pode ser um ou vários centros de dados estão localizados no proximidade. Para a maioria das regiões geopolíticas no mundo Microsoft tem, pelo menos, duas áreas de Azure. Por exemplo na Europa existe uma região Azure da 'Europa Norte' e uma das «Europa Ocidental». Dessas regiões Azure duas dentro de uma região geopolítica separadas por distância significativa suficiente para que catástrofes naturais ou técnicos não afetam ambas as regiões Azure na mesma geopolítica região. Uma vez que a Microsoft está constantemente a criar saída novas regiões de Azure em diferentes regiões geopolíticas globalmente, o número destas regiões constantemente em crescimento e partir dez de 2015 atingiu o número de 20 Azure regiões com as regiões adicionais anunciadas já. Como um cliente pode implementar os sistemas SAP para todas as seguintes regiões, incluindo as duas regiões de Azure na China. Para atual até Data informações sobre regiões Azure consulte este Web site: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>O conceito de Máquina Virtual do Microsoft Azure
Microsoft Azure oferece uma infraestrutura como uma solução de serviço (IaaS) para o anfitrião máquinas virtuais com funcionalidades semelhantes como uma solução de virtualização no local. É possível criar máquinas virtuais a partir do Portal do Azure, PowerShell ou clip, que também oferecem implementação e as capacidades da gestão.

Gestor de recursos do Azure permite-lhe aprovisionar as suas aplicações utilizando um modelo de declarativa. Num único modelo, pode implementar os vários serviços juntamente com as respectivas dependências. Utilize o mesmo modelo para implementar repetidamente a sua aplicação durante todas as fases do ciclo de vida de aplicação.

Obter mais informações sobre como utilizar modelos de processador podem ser encontradas aqui:

* [Implementar e gerir máquinas virtuais através da utilização de modelos de Gestor de recursos do Azure e o clip do Azure][virtual-machines-linux-cli-deploy-templates]
* [Gerir máquinas virtuais utilizando o Gestor de recursos do Azure e PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://Azure.microsoft.com/Documentation/Templates/>

Outra função interessante é a capacidade de criar imagens a partir de máquinas virtuais, que permite-lhe preparar determinados repositórios a partir do qual for capaz de rapidamente implementar instâncias de Máquina Virtual que correspondam às suas necessidades.

Podem encontrar mais informações sobre a criação de imagens a partir de máquinas virtuais [neste] artigo (Windows)[ virtual-machines-windows-capture-image] ou [neste]artigo (Linux)[virtual-machines-linux-capture-image].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de falhas
Falhas domínios representam uma unidade física de falha, muito estreitamente relacionados com a infraestrutura física contida nos centros de dados e, enquanto uma pá física ou bastidor pode ser considerado um domínio de falhas, não existe nenhuma mapeamento de a um direto entre os dois. 

Quando implementar várias máquinas virtuais como parte de um sistema SAP no Microsoft Azure Máquina Virtual Services, pode influenciar o controlador de ferro Azure para implementar a sua aplicação para domínios falhas diferentes, portanto satisfaz os requisitos do SLA de Azure a Microsoft. No entanto, a distribuição dos domínios falhas através de uma unidade de escala Azure (colecção de centenas de nós de cluster ou nós de armazenamento e redes) ou a atribuição de VMs a um domínio de falhas específico é algo através da qual não tiver controlo direto. Para direcionar o controlador de ferro Azure implementar um conjunto de VMs através de domínios falhas diferentes, é necessário atribuir um conjunto de disponibilidade de Azure aos VMs no momento da implementação. Para mais informações sobre conjuntos de disponibilidade do Azure, consulte o artigo capítulo [Azure disponibilidade conjuntos] [3.2.3 de planeamento-guia] neste documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Actualizar domínios
Domínios atualização representam uma unidade lógica que ajuda a determinar como uma VM dentro de um sistema SAP, que consiste em instâncias do SAP a ser executada em vários VMs, será atualizada. Quando ocorre uma atualização, Microsoft Azure vai durante o processo de atualização estes domínios atualização um a um. Por propagação VMs no momento da implementação sobre domínios atualizar diferentes pode proteger o seu sistema SAP parcialmente a partir de tempo de inatividade potencial. Para forçar o Azure para implementar VMs de um sistema SAP escalonado domínios atualizar diferentes, tem de definir um atributo específico no momento da implementação de cada VM. Semelhante de falhas domínios, uma unidade de escala Azure é dividida em vários domínios atualizar. Para direcionar o controlador de ferro Azure implementar um conjunto de VMs através de domínios diferente de atualização, é necessário atribuir um conjunto de disponibilidade de Azure aos VMs no momento da implementação. Para mais informações sobre conjuntos de disponibilidade do Azure, consulte capítulo [Azure disponibilidade conjuntos] [3.2.3 de planeamento-guia] abaixo.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade Azure
Azure máquinas virtuais dentro de um conjunto de disponibilidade de Azure será distribuído pelo controlador de ferro Azure através de diferentes falhas e atualizar domínios. É o objetivo da distribuição sobre diferentes falhas e atualizar domínios impedir que todos os VMs de um sistema SAP encerrar no caso de manutenção de infraestrutura ou uma falha de dentro de uma falha de domínio. Por predefinição, VMs não fazem parte de um conjunto de disponibilidade. A participação de uma VM num conjunto de disponibilidade é definida no momento da implementação ou posterior por um reconfiguração e re implementação de uma VM.

Para compreender o conceito de conjuntos de disponibilidade do Azure e a forma como os conjuntos de disponibilidade relacionar a falhas e atualizar domínios, leia [Este artigo][virtual-machines-manage-availability]

Para definir a disponibilidade do conjuntos para processador através de um modelo de json consulte o artigo [especificações de rest api](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e procure "disponibilidade".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento do Microsoft Azure e discos de dados
Máquinas virtuais do Microsoft Azure utilizam tipos de armazenamento diferente. Quando a execução do SAP no Azure Máquina Virtual serviços é importante compreender as diferenças entre estes dois tipos principais de armazenamento:

* Armazenamento não persistente, voláteis.
* Armazenamento persistente.

O armazenamento de que não sejam persistente diretamente é anexado às máquinas virtuais em execução e encontra-se em nós cluster próprios – o armazenamento de instância local (armazenamento temporário). O tamanho depende do tamanho da Máquina Virtual escolhido quando a implementação iniciado. Este tipo de armazenamento é voláteis e, consequentemente, o disco está inicializado quando uma instância de Máquina Virtual é reiniciada. Normalmente, o ficheiro de paginação para o sistema operativo está localizado no disco temporário.

___

> ![Windows][Logo_Windows] Windows
>
> No Windows VMs a unidade temp é fixada como unidade D:\ numa VM implementada.
>
> ![Linux][Logo_Linux] Linux
> 
> No Linux VMs é fixado como /mnt/resource ou /mnt. Ver mais detalhes aqui:
> 
> * [Como anexar um disco de dados a uma máquina de Virtual Linux][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/Archive/2013/12/07/Understanding-the-Temporary-drive-on-Windows-Azure-virtual-Machines.aspx>

___

A unidade real é voláteis porque está a obter armazenada no servidor anfitrião própria. Se a VM movida na nova uma implementação (por exemplo, devido a manutenção no anfitrião ou encerramento e reiniciar) o conteúdo da unidade será perdido. Por conseguinte, não é uma opção para armazenar quaisquer dados importantes nesta unidade. O tipo de multimédia utilizado para este tipo de armazenamento difere entre diferentes séries de VM com características de desempenho muito diferente que partir Junho de 2015 aspeto:

* A5 a A7: Desempenho muito limitado. Não é recomendado para tudo para além do ficheiro de página
* A8 A11: Características de muito bom desempenho com algumas IOPS dez mil e > débito/seg. 1GB.
* Série de D: Características muito bom desempenho com alguns, em seguida, milhares IOPS e > débito de 1GB/seg.
* Série de DS: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.
* Série de G: Características de muito bom desempenho com algumas IOPS dez mil e > débito/seg. 1GB.
* Série de s: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.

Declarações acima estão a aplicar aos tipos de VM que tenham certificação com SAP. A série de VM com excelente IOPS e débito elegíveis para aproveitar por algumas funcionalidades DBMS. Consulte o artigo [DBMS guia de implementação do] [dbms guia] para obter mais detalhes.

Armazenamento do Windows Azure fornece armazenamento persistente e os níveis típicos de proteção e redundância visto sobre o armazenamento de SAN. Discos com base em armazenamento do Windows Azure são disco rígido virtual (VHDs) localizado nos serviços de armazenamento do Azure. O sistema operativo do disco local (c Windows:\, Linux / (/ Dev Center/sda1)) está armazenado no armazenamento Azure, e Volumes/discos adicionais instalados para a VM obter aí armazenados, demasiado.

É possível carregar um VHD existente a partir no local ou criar ficheiros vazios, a partir do Azure e anexar aos VMs implementados. Esses VHDs são referenciados como discos Azure. 

Depois de criar ou carregar um VHD para o armazenamento do Windows Azure, é possível para montagem e anexe os para uma Máquina Virtual existente e copiar existente VHD (das).

Como são permanentes esses VHDs, dados e alterações dentro aqueles são seguras quando reiniciar o computador e recriar uma instância de Máquina Virtual. Mesmo se uma instância é eliminada, estes VHDs permanecem seguros e podem ser novamente implementados em caso de discos não SO podem ser instalados ou para outros VMs.

Dentro da rede de armazenamento do Windows Azure podem ser configurados níveis redundância diferente:

* Nível mínimo que pode ser selecionado é 'redundância local', o que é equivalente a três-réplica dos dados no mesmo centro de dados de uma região Azure do (consulte o artigo capítulo [Azure Regions][planning-guide-3.1]). 
* Armazenamento de redundante zona que irá propagar-se as três imagens através de dados diferentes centra dentro da mesma região do Azure.
* Nível de redundância de predefinido é redundância geográfica que modo assíncrono replica o conteúdo para outro imagens 3 dos dados para outra região do Azure que está alojado na mesma geopolítica região.

Consulte também a tabela na parte superior deste artigo no que respeita as opções de redundância diferente: <https://azure.microsoft.com/pricing/details/storage/> 

Obter mais informações relativamente ao armazenamento do Windows Azure podem ser encontradas aqui: 

* <https://Azure.microsoft.com/Documentation/Services/Storage/>
* <https://Azure.microsoft.com/Services/site-Recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/Archive/2015/11/17/Azure-Disk-Encryption-for-Linux-and-Windows-virtual-Machines-Public-Preview.aspx>


#### <a name="azure-standard-storage"></a>Azure armazenamento padrão
Armazenamento de BLOBS padrão do Azure era o tipo de armazenamento disponível quando o Azure IaaS foi lançado. Ocorreram quotas IOPS impostas por única VHD. Latência ocorreu ao não era na mesma classe como os dispositivos de SAN/NAS normalmente implementados de alto nível sistemas SAP alojado no local. No entanto, o armazenamento de padrão Azure provar suficiente para muitos centenas sistemas SAP entretanto implementados no Azure.

Azure armazenamento padrão é cobrado com base dos dados reais que estão armazenados, o volume de transações do armazenamento, as transferências de dados de saída e opção redundância escolhido. Muitos VHDs podem ser criados no máximo 1TB de tamanho, mas como aqueles permanecem vazias é gratuito. Se, em seguida, preencher um VHD com 100GB, será cobrado para armazenar 100GB e não para o tamanho nominal que VHD tem criado com.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento de Azure Premium
De Abril de 2015 Microsoft introduzida armazenamento do Windows Azure Premium. Armazenamento de Premium tem introduzido com o objetivo para fornecer:

* Latência e/s melhor.
* Melhor débito.
* Menos variabilidade da latência e/s.

Para o efeito, muitas alterações foram introduzidas das quais são os dois mais significativos:

* Utilização da discos SSD em nós do armazenamento do Windows Azure
* Ler uma nova cache que é suportado pela SSD local de um nó cluster Azure

No oposto armazenamento padrão, onde capacidades não alterou depende do tamanho do disco (ou VHD), armazenamento de Premium atualmente com 3 disco diferentes categorias que são apresentadas no final deste artigo antes da secção FAQ: <https://azure.microsoft.com/pricing/details/storage/>

Ver o que são dependentes a categoria de tamanho de discos débito/VHD IOPS/VHD e disco

Custo base, no caso de armazenamento de Premium não é o volume de dados reais armazenado nessas VHDs, mas a categoria de tamanho dessa um VHD, independentemente da quantidade dos dados que estão armazenadas no VHD.

Também pode criar VHDs armazenamento Premium que não são mapeamento diretamente para as categorias de tamanho apresentadas. Isto pode ser as maiúsculas/minúsculas, especialmente quando copiar VHDs a partir do armazenamento padrão para armazenamento Premium. Nestes casos, é executado um mapeamento para a opção de disco Premium armazenamento maior seguinte. 

Tenha em atenção que apenas determinada série VM pode beneficiar a partir do armazenamento Azure Premium. Partir dez de 2015, estas são as DS-s-Séries e. A série de DS é, essencialmente, igual D-série com a exceção que DS série tem a capacidade de armazenamento de montagem Premium com base VMs para além para VHDs estão alojados em armazenamento padrão do Windows Azure. Mesma coisa seja válida para Série G em comparação comparada a série s.

Se está a dar saída a parte da VMs a série de DS [neste] artigo[ virtual-machines-sizes] apercebeu-também irá se de que não existem dados volume limitações Premium armazenamento VHDs a granularidade do nível de VM. Série de DS diferente ou série s VMs também têm limitações diferentes no que respeita para o número de VHDs que podem ser instalados. Estes limites encontram-se documentados no artigo mencionado acima, assim. Mas na sua essência significa que, se, por exemplo, de montagem 32 x P30 discos/VHDs para uma única VM de DS14 não pode obter 32 x o débito máximo de um disco P30. Em vez disso, o débito máximo no nível VM conforme é documentado no artigo irá limitar débito de dados. 

Mais informações sobre o armazenamento de Premium podem ser encontradas aqui: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Contas do Azure armazenamento
Quando implementar serviços ou VMs no Azure, implementação de VHDs e imagens de VM deve ser organizada em unidades chamadas Azure armazenamento contas. Quando planear uma implementação do Azure, tem de considerar cuidadosamente as restrições do Azure. No um lado, existe um número limitado de contas de armazenamento por subscrição Azure. Apesar de cada conta de armazenamento do Azure podem conter um grande número de ficheiros VHD, existe um limite fixo no total IOPS por conta de armazenamento. Quando implementar centenas de VMs do SAP com sistemas DBMS criar significativas IO chamadas, recomenda-se para distribuir alta VMs de DBMS IOPS entre várias contas de armazenamento do Azure. Tem cuidado para não excede o limite atual de contas do Azure armazenamento por subscrição. Uma vez que o armazenamento é uma parte essencial da implementação da base de dados para um sistema SAP, este conceito é abordado mais detalhe já referenciada [DBMS guia de implementação do] [guia de dbms].

Podem encontrar mais informações sobre contas de armazenamento do Azure [neste]artigo[storage-scalability-targets]. Ao ler este artigo, apercebeu-será se de que não existem diferenças nas limitações entre contas de armazenamento padrão do Azure e contas de armazenamento de Premium. Diferenças principais são o volume de dados que podem estar armazenados dentro de uma conta de armazenamento. No armazenamento padrão o volume é uma magnitude maior do que com o armazenamento de Premium. No outro lado a conta de armazenamento padrão é extremamente limitada IOPS (consulte a coluna 'Taxa Total de pedir'), enquanto a conta de armazenamento do Azure Premium tem sem limitação em causa. Abordaremos detalhes e os resultados destas diferenças quando debater implementações dos sistemas SAP, especialmente os servidores de DBMS.

Dentro de uma conta de armazenamento, tem a possibilidade de criar contentores diferentes para organizar e categorizar VHDs diferentes. Nestes contentores são normalmente utilizadas para, por exemplo, separados VHDs de VMs diferentes. Não existem sem implicações no desempenho ao utilizar apenas uma ou nos vários contentores por baixo de uma única conta de armazenamento do Azure.

Dentro do Azure um nome VHD segue a seguinte ligação de nomenclatura que necessita para fornecer um nome exclusivo para o VHD dentro Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Tal como mencionado a cadeia acima tem de identificar exclusivamente VHD que está armazenado no armazenamento do Windows Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Redes do Microsoft Azure
Microsoft Azure irá fornecer uma infraestrutura de rede que permite que o mapeamento de todos os cenários que pretendemos aperceba com um software de SAP. As funcionalidades são:

* Acesso do exterior, diretamente a VMs através de serviços de Terminal Windows ou ssh/VNC
* Acesso aos serviços e portas específicas utilizadas por aplicações dentro de VMs
* Internas comunicação e resolução de nomes entre um grupo de VMs implementado como Azure VMs
* Publicação em local conectividade entre rede no local de um cliente e a rede Azure
* Conectividade cruzada de centro de região do Azure ou os dados entre Azure sites 

Obter mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existem muitas diferentes possibilidades para configurar o nome e resolução de IP no Azure. Neste documento, apenas na nuvem cenários dependem a predefinição de através de DNS de Azure (contrariamente ao que define um serviço DNS próprio). Também existe um novo serviço Azure DNS que pode ser utilizado em vez da configurar o seu servidor de DNS. Podem encontrar mais informações [neste] artigo[ virtual-networks-manage-dns-in-vnet] e [nesta](https://azure.microsoft.com/services/dns/)página.

Para obter cenários cruzada local podemos são depender do facto de que no local OpenLDAP/AD/DNS foi expandida via VPN ou ligação privada para Azure. Para determinados cenários conforme é documentado aqui, poderá ser necessário ter uma réplica do AD/OpenLDAP instalada no Azure.

Uma vez que o funcionamento em rede e resolução do nome é uma parte essencial da implementação de base de dados para um sistema SAP, este conceito é abordado no mais detalhe [DBMS guia de implementação do] [guia de dbms].


##### <a name="azure-virtual-networks"></a>Redes virtuais Azure

Pode definir o intervalo de endereço de endereços IP privados atribuído pela funcionalidade do Azure DHCP através da criação de uma rede Virtual Azure. Em cenários de publicação em local, o intervalo de endereços IP definido serão ainda atribuído utilizando DHCP pela Azure. No entanto, resolução do nome de domínio será realizada no local (partindo do princípio de que o VMs são uma parte de um domínio no local) e, consequentemente, pode resolver endereços para além dos serviços em nuvem Azure diferente.

[comentário]: <>  (MSSedusch ainda necessário? TODO originalmente uma rede Virtual Azure estava ligado a um grupo de afinidade. Com que uma rede Virtual no Azure tem restrita para a unidade de escala Azure que o grupo afinidade tem atribuído a. No final, isto destina que a rede Virtual foi restrita para os recursos disponíveis na unidade de escala Azure. Esta coluna uma vez que foi alterada e agora redes virtuais Azure pode esticar através de mais do que uma unidade de escala Azure. No entanto, que requer que estão redes virtuais Azure * * não * * associada afinidade grupos já na hora de criação. Já mencionado anteriormente que no oposto recomendações por ano atrás, deverá * * não tirar partido do Azure afinidade grupos deixem * *. Para obter detalhes, consulte o artigo < https://azure.microsoft.com/blog/regional-virtual-networks/>)

Cada máquina Virtual no Azure tem de estar ligado à rede Virtual.

Podem encontrar mais detalhes [neste] artigo[ resource-groups-networking] e [nesta](https://azure.microsoft.com/documentation/services/virtual-network/)página.

[comentário]: <>  (MShermannd TODO não foi possível um artigo que inclui o tópico OpenLDAP + processador;)
[comentário]: <>  (MSSedusch < https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [AZURE.NOTE] Por predefinição, assim que um VM é implementada não é possível alterar a configuração de rede Virtual. As definições de TCP/IP tem de ser da esquerda para o servidor Azure DHCP. Comportamento predefinido é atribuição de IP dinâmico.

O endereço de MAC do cartão de rede virtual podem ser alteradas, por exemplo, depois de voltar dimensionar e o Windows ou Linux convidado SO vai selecionar novo cartão de rede e utilizará automaticamente DHCP para atribuir os endereços IP e DNS neste caso.

##### <a name="static-ip-assignment"></a>Atribuição de IP estático
É possível atribuir fixos ou reservados endereços IP a VMs dentro de uma rede Virtual Azure. A executar os VMs numa rede Virtual Azure é aberta uma excelente possibilidade tirar partido esta funcionalidade, se for necessário ou necessários para alguns cenários. A atribuição de IP permanece válida em toda a existência de VM, independentemente de se a VM está em execução ou encerramento. Como resultado, tem de ter o número geral de VMs (VMS em execução e parado) em consideração quando definir o intervalo de endereços IP para a rede Virtual. O endereço IP permanece atribuído até a VM e a sua Interface de rede é eliminada ou até que o endereço IP retirar obtém atribuído novamente. Consulte o artigo obter informações detalhadas [neste]artigo[virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>NIC vários por VM
Pode definir vários cartões de interface de rede virtual (vNIC) para uma máquina de Virtual do Azure. A capacidade de vários vNICs que pode começar a configurar o tráfego de rede é encaminhada separação onde, por exemplo, o tráfego de cliente é encaminhado através de um tráfego vNIC e back-end através de uma segunda vNIC. Dependentes o tipo da VM são diferentes limitações no que respeita para o número de vNICs. Detalhes exatas, a funcionalidade e restrições podem ser encontradas nestes artigos:
 
* [Criar uma VM com vários NIC][virtual-networks-multiple-nics]
* [Implementar multi NIC VMs através de um modelo][virtual-network-deploy-multinic-arm-template]
* [Implementar multi NIC VMs através do PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implementar multi NIC VMs utilizando o clip do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade de site para o Site
Publicação em local é Azure VMs e no local ligado com uma ligação VPN transparente e permanente. É esperado para se tornar o padrão de implementação do SAP mais comuns no Azure. Partem do princípio de é procedimentos operacionais e processos com instâncias do SAP no Azure deverão transparente funcionar. Este significa que deverá conseguir imprimir partido destes sistemas, bem como os utilizar sistema de gestão de transporte (TMS) o SAP para transporte altera a partir de um sistema de desenvolvimento no Azure para um sistema de teste que é implementado no local. Documentação mais à volta do site para o site pode ser encontrada [neste] artigo[vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN
Para criar uma ligação de site para o site (Centro de dados no local para o Centro de dados Azure), terá de obter e configurar um dispositivo VPN ou utilizar o encaminhamento e Remote Access Service (RRAS) que foi introduzida como um componente de software com o Windows Server 2012. 

* [Criar uma rede virtual com uma ligação VPN de site para o site através do PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Sobre os dispositivos VPN para ligações de Site para o Site VPN Gateway][vpn-gateway-about-vpn-devices]
* [VPN Gateway perguntas mais frequentes][vpn-gateway-vpn-faq]

![Ligação de site para o site entre no local e o Azure][planning-guide-figure-600]

A figura acima mostra duas subscrições Azure tem subintervalos de endereço IP reservadas para utilização no redes virtuais no Azure. A conectividade da rede no local para Azure é estabelecida através de VPN.

#### <a name="point-to-site-vpn"></a>Site de ponto VPN
Site de ponto VPN requer cada computador cliente para se ligar a sua própria VPN para Azure. Para os cenários SAP que vamos olhar, ponto-para-site não está prático. Por conseguinte, sem as futuras referências serão dada a análise do ponto-para-site.

[comentário]: <>  (MSSedusch – obter mais informações podem ser encontradas aqui)
[comentário]: <>  (MShermannd TODO ligação já não é válida; Mas processador não mesmo assim é suportada - pode consultar a hiperligação seguinte abaixo)
[comentário]: <>  (MSSedusch – < http://msdn.microsoft.com/library/azure/dn133798.aspx>.)
[comentário]: <>  (Ponto de TODO MShermannd para o Site não é suportada ainda com processador)
[comentário]: <>  (MSSedusch – < https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multi-site-vpn"></a>Múltiplos Site VPN
Azure oferece hoje em dia também a possibilidade de criar o Site com várias grupo análise para uma subscrição Azure. Anteriormente uma subscrição única foi limitada a uma ligação VPN de site para o site. Esta limitação ausente correu com ligações de VPN de múltiplos sites para uma subscrição única. Isto torna possível tirar partido mais do que uma região Azure para uma subscrição através de configurações de publicação em local específica.

Para obter mais documentação consulte [Este artigo][vpn-gateway-create-site-to-site-rm-powershell]
[comentário]: <> (MShermannd TODO encontrados sem ligação de docu processador)

#### <a name="vnet-to-vnet-connection"></a>VNet VNet ligação
Utilizar múltiplos sites VPN, tem de configurar um rede de Virtual do Azure separada em cada uma das regiões. No entanto muito frequentemente tem o requisito de que os componentes de software nas diferentes regiões devem comunicar com os outros. Idealmente esta comunicação não deve ser encaminhada a partir de uma região Azure para no local e a partir desse local para outros região do Azure. Atalho de Azure proporciona a possibilidade de configurar uma ligação a partir de uma rede Virtual do Azure uma região para outra rede Virtual do Azure alojado noutro região. Esta funcionalidade é denominada VNet para VNet ligação. Obter mais detalhes sobre esta funcionalidade só podem ser encontradas aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure--expressroute"></a>Ligação privada para Azure – ExpressRoute
Microsoft Azure ExpressRoute permite a criação de ligações privadas entre centros de dados Azure e infraestrutura do cliente no local ou num ambiente localização cocriação. ExpressRoute é disponibilizado pelos MPLS vários fornecedores VPN (pacote mudado) ou outros fornecedores de serviço de rede. Ligações de ExpressRoute não aceda através da Internet pública. ExpressRoute ligações fornecem uma maior segurança, fiabilidade mais através de vários circuitos paralelos, velocidades mais rápidas e inferiores latências que as ligações típicas através da Internet. 

Localize mais detalhes sobre Azure ExpressRoute e ofertas aqui:

* <https://Azure.microsoft.com/Documentation/Services/expressroute/>
* <https://Azure.microsoft.com/pricing/details/expressroute/>
* <https://Azure.microsoft.com/Documentation/articles/expressroute-FAQs/>

Encaminhar Express permite múltiplas subscrições Azure através de um ExpressRoute circuito conforme é documentado aqui 

* <https://Azure.microsoft.com/Documentation/articles/expressroute-howto-linkvnet-Arm/> 
* <https://Azure.microsoft.com/Documentation/articles/expressroute-howto-circuit-Arm/>


#### <a name="forced-tunneling-in-case-of-cross-premise"></a>Forçada túnel em caso de publicação em local
VMs aderir a domínios no local através de site para o site, ponto-para-site ou ExpressRoute, terá para se certificar de que as definições de proxy de Internet são introdução implementadas para todos os utilizadores também esses VMs. Por predefinição, o software a ser executada nesses VMs ou utilizadores utilizando um browser para aceder à internet não apareceriam através do proxy de empresa, mas seria ligar diretamente através de Azure à internet. Mas ainda a definição de proxy não é uma solução de 100% para direcionar tráfego através do proxy de empresa, uma vez que é responsabilidade do software e serviços para verificar se o proxy. Se o software a ser executado na VM não está a fazer que ou um administrador manipula as definições, o tráfego para a Internet pode ser detoured novamente diretamente através da Azure à Internet. 

Para evitar esta situação, pode configurar forçada túnel com a conectividade de site para o site entre no local e Azure. A descrição detalhada da funcionalidade forçada túnel é publicado aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/> 

Túnel forçada com ExpressRoute é activado por clientes publicidade uma rota predefinida através das sessões peering ExpressRoute BGP.

#### <a name="summary-of-azure-networking"></a>Resumo das redes Azure
Este capítulo contidas muitos pontos importantes sobre Azure de rede. Eis um resumo dos pontos principais:


* Azure redes virtuais permite-lhe para configurar a rede de acordo com as suas necessidades
* Azure redes virtuais podem ser utilizadas para atribuir intervalos de endereços IP para VMs ou atribuir endereços IP fixos a VMs
* Para configurar uma ligação de Site para o Site ou Site de ponto tem de criar uma rede Virtual Azure pela primeira vez
* Depois de ter sido implementada uma máquina virtual já não é possível alterar a rede Virtual atribuído a VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Quotas de nos serviços do Azure Máquina Virtual
Precisamos de seja claro sobre o facto de que a infraestrutura de armazenamento e de rede é partilhada entre VMs execução de uma variedade de serviços no infraestrutura do Azure. E apenas vistos centros de dados do cliente, indevidamente aprovisionamento de alguns dos recursos infraestrutura realizar para um ângulo. A plataforma do Microsoft Azure utiliza disco, CPU, rede e outros quotas para limitar o consumo de recursos e preservar consistente e determinista desempenho.  Os diferentes tipos VM (A5, A6, etc) tenham quotas diferentes para o número de discos, CPU, RAM e de rede.

> [AZURE.NOTE] Recursos de memória e CPU dos tipos de VM suportados pelo SAP são previamente atribuídos em nós do anfitrião. Isto significa que assim que é implementada a VM, os recursos no anfitrião do estarão disponíveis, tal como foi definido pelo tipo VM.

Quando planear e SAP de redimensionamento no Azure soluções devem ser consideradas as quotas de cada tamanho de máquina virtual.  As quotas VM descritas [aqui][virtual-machines-sizes].

As quotas de descrito representam os valores máximos teóricos.  O limite de IOPS por VHD pode ser obtido com IOs pequenas (8kb), mas possivelmente não pode ser obtido com IOs grandes (1Mb).  O limite IOPS é aplicado na granularidade da VHDs únicos.

Como uma árvore de decisões em bruto para decidir se um sistema SAP adequa dos serviços de Máquina Virtual do Azure e as suas capacidades ou se o sistema existente tem de ser configurado de forma diferente para implementar o sistema no Azure, pode ser utilizada a árvore de decisões abaixo:
 
![Árvore de decisões de decidir capacidade para implementar o SAP no Azure][planning-guide-figure-700]

**Passo 1**: as informações mais importantes para começar com são o requisito de SAP para um determinado sistema do SAP. Os requisitos de SAP tem de ser separados para a parte DBMS e peça de aplicação SAP, mesmo se o sistema SAP já está a ser implementado no local numa configuração camada de 2. Para sistemas existentes, os SAP relacionados com o hardware utilizado com frequência podem ser determinados ou calculados com base em avaliações existentes do SAP. Os resultados podem ser encontrados aqui: <http://global.sap.com/campaigns/benchmark/index.epx>. Para sistemas SAP recentemente implementados, deverá foram trocadas através de um exercício redimensionamento qual deve determinar os requisitos de SAP do sistema.
Consulte também neste blogue e documento anexado para o dimensionamento SAP no Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Passo 2**: para sistemas existentes, o volume de e/s e operações de e/s por segundo no servidor DBMS devem ser medidas. Para sistemas recentemente planeados, o exercício redimensionamento para o novo sistema também deve dar-ideias em bruto dos requisitos e/s no lado DBMS. Se não souber, eventualmente terá conduzir uma prova de conceito.

**Passo 3**: comparar o requisito de SAP para o servidor DBMS com os SAP podem fornecer os diferentes tipos VM do Azure. As informações na SAP dos diferentes tipos de Azure VM são documentadas na nota SAP [1928533]. O foco deve estar na VM DBMS primeiro uma vez que a camada de base de dados a camada de um sistema de SAP NetWeaver Dimensionar não na maioria dos híbridas. Em contrapartida, a camada de aplicação SAP pode ser dimensionada saída. Se nenhum do SAP suportados tipos de Azure VM podem entregar os SAP necessários, não é possível executar a carga de trabalho do sistema SAP planeada Azure. Precisa de um para implementar o sistema no local ou precisar de alterar o volume de carga de trabalho para o sistema.

**Passo 4**: como documentado [aqui][virtual-machines-sizes], Azure impõe uma quota IOPS por VHD independente, se utilizar o armazenamento padrão ou armazenamento Premium. Depende do tipo VM, o número de VHDs que podem ser instalados varia. Como resultado, pode calcular um número máximo de IOPS que pode ser obtido com cada um dos diferentes tipos VM. Dependentes no esquema de ficheiro de base de dados, pode faixas VHDs para se tornar um volume no sistema operativo convidado. No entanto, se o volume IOPS atual de um sistema SAP implementado ultrapassa os limites calculados maior tipo VM do Azure e se não existe nenhuma oportunidade do Adquirente na íntegra com mais memória, a carga de trabalho do sistema SAP pode extremamente afetada. Nestes casos, pode clicar num ponto onde não deve implementar o sistema no Azure.

**Passo 5**: especialmente no SAP sistemas que são implementado no local em configurações de 2-níveis, o é muito provável que o sistema poderá ter de ser configurado no Azure numa configuração 3 camadas. Neste passo, tem de verificar se existe um componente na camada de aplicação SAP que não pode ser redimensionado saída e qual seria não se ajustam os recursos de memória e CPU que os diferentes tipos de Azure VM oferecem. Se facto existir um componente dessa, o sistema SAP e o respetiva carga de trabalho não podem ser implementadas no Azure. Mas se que pode escala de saída dos componentes da aplicação SAP para vários Azure VMs, o sistema pode ser implementado no Azure. 

**Passo 6**: se os componentes de camada de aplicação DBMS e pela SAP, podem ser executados em Azure VMs, a configuração tem de ser definidos com para:

* Número de Azure VMs
* Tipos de VM para os componentes individuais
* Número de VHDs na DBMS VM para fornecer suficiente IOPS

## <a name="managing-azure-assets"></a>Gestão de recursos do Azure

### <a name="azure-portal"></a>Portal do Azure
Portal do Azure é um dos três interfaces para gerir implementações Azure VM. As tarefas de gestão básicas, como implementar VMs a partir do imagens, podem ser feitas através do Portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes Azure também são tarefas Portal do Azure pode consegue processar muito. No entanto, a funcionalidade como carregar VHDs a partir no local para Azure ou copiar um VHD dentro Azure é tarefas que requerem ferramentas de terceiros ou administração através do PowerShell ou clip.
 
![Portal do Microsoft Azure - descrição geral de Máquina Virtual][planning-guide-figure-800]

[comentário]: <>  (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comentário]: <>  (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Tarefas de administração e configuração para a instância de Máquina Virtual são possíveis a partir do Portal do Azure. 

Para além de reiniciar e encerrar uma Máquina Virtual também pode anexar, desanexar e criar discos de dados para a instância de Máquina Virtual, para capturar a instância da preparação de imagem e configure o tamanho da instância Máquina Virtual.

Portal do Azure fornece funcionalidade básica para implementar e configurar VMs e vários outros serviços Azure. No entanto não todas as funcionalidade disponível é abrangida pelo Portal do Azure. No Portal do Azure, não é possível executar tarefas como:

* Carregar VHDs para Azure
* Copiar VMs

[comentário]: <>  (MShermannd TODO o que sobre a automatização do serviço para SAP VMs?)
[comentário]: <>  (Implementação de MSSedusch do vários SO VMs entretanto possíveis)
[comentário]: <>  (MSSedusch também qualquer tipo de automatização de implementação não é possível fazer com o portal do Azure. Tarefas como script de implementação do VMs vários não é possível através do Portal do Azure.) 

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestão de via os cmdlets do PowerShell do Microsoft Azure
O Windows PowerShell é um quadro extensible e avançado que foi aprovado amplamente por clientes implementar números maiores de sistemas no Azure. Após a instalação do cmdlets do PowerShell num ambiente de trabalho, portátil ou estação de gestão dedicada, os cmdlets do PowerShell podem ser executados remotamente.

O processo para ativar um ambiente de trabalho/portátil local para uma utilização dos cmdlets do PowerShell do Azure e saber como configurar os para utilização com as suas subscrições Azure é descrita [neste]artigo[powershell-install-configure]. 

Também é possível encontrar passos mais detalhados sobre como instalar, atualizar e configurar os cmdlets do Azure PowerShell [neste capítulo do guia de implementação do] [4.1 de guia de implementação].

Experiência do cliente até ao momento foi que é a mais poderosa ferramenta para implementar VMs e para criar passos personalizados na implementação de VMs certamente PowerShell (PS). Todos os clientes executar instâncias do SAP no Azure estiver a utilizar PS cmdlets para completar tarefas de gestão de serem fazer no Portal do Azure ou ainda estão a utilizar PS cmdlets exclusivamente para gerir os respetivos implementações no Azure. Uma vez que os cmdlets específicos Azure partilhar a mesma Convenção de nomenclatura como os cmdlets relacionados do Windows mais de 2000, é uma tarefa fácil para os administradores do Windows tirar partido esses cmdlets.

Consulte o artigo exemplo aqui: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comentário]: <>  (MShermannd TODO descrevem comando clip novo durante o ensaio)
Implementação da extensão do Azure monitorização de SAP (consulte capítulo [Azure monitorização solução para SAP] [planeamento-guia-9.1] neste documento) só é possível através do PowerShell ou clip. Por conseguinte, é obrigatório do programa de configuração e configurar o PowerShell ou clip quando implementar ou administrar um sistema de SAP NetWeaver no Azure.  

Como Azure fornece mais funcionalidade, novos cmdlets PS dos irão ser adicionada que necessita de uma atualização dos cmdlets. Por conseguinte, faz sentido para verificar o site de transferência do Azure pelo menos uma vez a mês <https://azure.microsoft.com/downloads/> para uma nova versão dos cmdlets. A nova versão será instalada apenas na parte superior a versão mais antiga.

Para uma lista geral de Azure relacionadas com comandos do PowerShell Marque aqui: <https://msdn.microsoft.com/library/azure/dn708514.aspx>. 

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestão através de comandos do Microsoft Azure clip

Para clientes que utilizam Linux e quiser gerir Azure recursos Powershell não poderão ser uma opção. A Microsoft oferece Azure clip como alternativa.
O clip do Azure fornece um conjunto de abrir origem, em diferentes plataformas comandos para trabalhar com a plataforma Azure. O clip de Azure fornece muitas das mesmas funcionalidades que se encontram no portal do Azure.

Para obter informações sobre a instalação, configuração e como utilizar o clip Consulte comandos para efetuar tarefas Azure

* [Instalar o clip Azure][xplat-cli]
* [Implementar e gerir máquinas virtuais através da utilização de modelos de Gestor de recursos do Azure e o clip do Azure][virtual-machines-linux-cli-deploy-templates]
* [Utilizar o clip Azure para Mac, Linux e Windows com o Gestor de recursos Azure][xplat-cli-azure-resource-manager]

Leia também capítulo [Azure clip para Linux VMs] [implementação-guia-4.5.2] na [guia de implementação do] [-guia de planeamento] sobre como utilizar o clip do Azure para implementar a extensão de monitorização do Azure para SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Formas diferentes para implementar VMs para SAP no Azure
Neste capítulo irá aprender as várias formas de implementar uma VM no Azure. Procedimentos preparação adicionais, bem como processamento de VHDs e VMs no Azure estejam tapados neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implementação do VMs para SAP
Microsoft Azure oferece várias formas para implementar VMs e discos associados. Assim, é muito importante compreender as diferenças desde preparações dos VMs podem ser diferentes dependendo o método de implementação. Em geral, podemos irá demorar um olhar sobre os cenários seguintes:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Mover uma VM no local para o Azure com um disco não generalized
Planear mover um sistema SAP específico no local para Azure. Isto pode ser feito ao carregá VHD que contém os binários SO, SAP binários e DBMS plus os VHDs com os ficheiros de dados e de registo ao DBMS Azure. Em contraste para [cenário #2 abaixo] [planeamento-guia-5.1.2], que manter o nome do anfitrião, SAP SID e as contas de utilizador do SAP na Azure VM como estavam configuradas no ambiente no local. Por conseguinte, generalizar a imagem não é necessário. Consulte o artigo capítulos [preparação para mover uma VM nos locais para o Azure com um disco não generalized] [planeamento-guia-5.2.1] deste documento para obter passos de preparação no local e o carregamento de que não sejam generalized VMs ou VHDs para Azure. Leia capítulo [cenário 3: mover uma VM no local utilizando um VHD do Azure não generalized com SAP] [implementação-guia-3.4] na [guia de implementação do] [guia de implementação] para obter passos detalhados de implementação como uma imagem no Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implementar uma VM com uma imagem específica do cliente
Devido a requisitos de patches específico da sua versão do SO ou DBMS, as imagens fornecidas no Azure Marketplace não poderão corresponder às suas necessidades. Por conseguinte, poderá ter de criar uma VM utilizando a sua própria imagem SO/DBMS VM 'private' que pode ser implementada posteriormente várias vezes. Para preparar como uma imagem de 'private' para duplicação, os itens seguintes tem de ser consideradas:

___

> ![Windows][Logo_Windows] Windows
>
> As definições do Windows (como SID do Windows e o nome do anfitrião) tem de ser captadas/generalized na VM no local através do comando sysprep.
[comentário]: <>  (MSSedusch > ver mais detalhes aqui:)
[comentário]: <>  (MShermannd TODO primeira hiperligação é sobre o modelo clássico. Encontrámos um artigo Azure docu)
[comentário]: <>  (MSSedusch >< https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[comentário]: <>  (MSSedusch >< http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
>
> ![Linux][Logo_Linux] Linux
>
> Siga os passos descritos nestes artigos para [SUSE] [ virtual-machines-linux-create-upload-vhd-suse] ou [Chapéu vermelho] [ virtual-machines-linux-redhat-create-upload-vhd] para preparar um VHD ser carregado para Azure.

___

Se já tiver instalado o conteúdo do SAP na sua VM no local (especialmente para sistemas de camada 2), pode adaptá as definições do sistema SAP depois de mudar o nome de implementação da VM Azure através da instância procedimento suportado pelo Gestor de aprovisionamento de Software SAP (nota SAP [1619720]). Consulte capítulos [preparação para implementar uma VM com uma imagem específica do cliente para SAP] [5.2.2 de planeamento-guia] e [carregar um VHD nos locais para Azure] [planeamento-guia-5.3.2] deste documento para obter passos de preparação no local e o carregamento de um VM GRG para Azure. Leia capítulo [cenário 2: implementar uma VM com uma imagem personalizada para SAP] [implementação-guia-3.3] na [guia de implementação do] [guia de implementação] para obter passos detalhados de implementação como uma imagem no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementar uma VM fora do Azure Marketplace
Gostaria de utilizar uma conta Microsofthttp ou festa 3º fornecidos imagem VM do Azure Marketplace para implementar o seu VM. Depois de implementado a VM no Azure, siga o mesmo diretrizes e ferramentas para instalar o software do SAP e/ou DBMS dentro da sua VM, tal como o faria num ambiente no local. Para mais detalhadas descrição de implementação, consulte o artigo capítulo [cenário 1: implementar uma VM terminar o Azure Marketplace para SAP] [implementação-guia-3,2] na [guia de implementação do] [guia de implementação].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparar VMs com SAP Azure
Antes de carregar VMs para Azure para se certificar de que precisa da VMs e VHDs cumpram certos requisitos. Existem diferenças pequenas consoante o método de implementação utilizado. 

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma VM nos locais para o Azure com um disco não generalized
É um método de implementação comum mover um VM existente que executa um sistema SAP a partir de no local para Azure. Que VM e o sistema SAP na VM apenas deverão ser executadas no Azure utilizando o mesmo nome de anfitrião e muito provável que os mesmos SID SAP. Neste caso o convidado SO de VM não deve ser generalized para implementações múltiplos. Se a rede no local tem expandida para o Azure (consulte capítulo [cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local] [planeamento-guia-2.2] neste documento), em seguida, mesmo nas mesmas contas de domínio podem ser utilizadas dentro da VM aqueles foram utilizadas anteriormente no local. 

Requisitos de quando preparar o seu próprio disco de VM Azure são:

* Originalmente VHD que contém o sistema operativo poderia ter um tamanho máximo de 127GB apenas. Esta limitação tem eliminada no final de Março de 2015. Agora o VHD que contém o sistema operativo pode ser até 1TB de tamanho como qualquer outro armazenamento do Windows Azure alojado VHD também.
[comentário]: <>  (MShermannd TODO tem de verificar se o clip também converte estáticos)
* -Tem de estar no formato VHD fixo. Dinâmico VHDs ou VHDs no formato de VHDx ainda não são suportados no Azure. VHDs dinâmicos serão convertido em VHDs estáticos quando carregar o VHD com commandlets do PowerShell ou clip
* VHDs que são instalados para a VM e devem ser colocados novamente de Azure para ser num formato de VHD fixo a necessidade de VM. O mesmo limite de tamanho do disco SO aplica-se ao também discos de dados. VHDs podem ter um tamanho máximo de 1TB. VHDs dinâmicos serão convertido em VHDs estáticos quando carregar o VHD com commandlets do PowerShell ou clip
* Adicione outra conta local com privilégios de administrador que podem ser utilizadas pelo suporte da Microsoft ou que podem ser atribuído como contexto para serviços e aplicações para executar até a VM é implementada e os utilizadores adequados mais pode ser utilizada.
* Para o caso de utilização de um cenário de implementação apenas na nuvem (consulte capítulo [apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local] [planeamento-guia-2.1] deste documento) em combinação com este método de implementação, contas de domínio poderão não funcionar depois do disco Azure é implementado no Azure. Este é especialmente verdadeiro para contas que são utilizadas para executar a serviços como as aplicações DBMS ou SAP. Por isso, tem de substituir essas contas de domínio com contas locais VM e elimine as contas de domínio no local na VM. Manter os utilizadores do domínio no local na imagem VM não é um problema quando a VM é implementada num cenário de publicação em local conforme descrito capítulo [cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local] [planeamento-guia-2.2] neste documento.
* Se contas de domínio foram utilizadas como inícios de sessão de DBMS ou utilizadores quando a executar o sistema no local e essas VMs é suposto existirem implementado em cenários apenas na nuvem, os utilizadores do domínio tem de ser eliminada. É necessário para se certificar de que o administrador local plus outro utilizador do local VM é adicionado como um utilizador/início de sessão para o DBMS como administradores.
* Adicione outras contas locais como aqueles poderão ser necessárias para o cenário de implementação específico.

___

> ![Windows][Logo_Windows] Windows
>
> Neste cenário, sem generalização (sysprep) da VM é necessário para carregar e implementar a VM no Azure.
> Certifique-se que unidade D:\ não é utilizado montagem automática no disco do conjunto de discos ligados conforme descrito capítulo [montagem definição automática para discos ligados] [planeamento-guia-5.5.3] neste documento.
> 
> ![Linux][Logo_Linux] Linux
>
> Neste cenário sem generalização (waagent-deprovision) da VM é necessário para carregar e implementar a VM no Azure.
> Certifique-se de que não é utilizado/mnt/recurso e de que todos os discos são instalados através do uuid. Para o disco SO Certifique-se de que a entrada de bootloader reflete também a montagem com base em uuid.

___

#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implementar uma VM com uma imagem específica do cliente para SAP
Ficheiros VHD que contêm um SO GRG também são armazenados em contentores de contas de armazenamento do Azure. Pode implementar uma nova VM a essas imagens VHD ao referenciar o VHD como uma origem de VHD nos seus ficheiros de modelo de implementação, conforme descrito capítulo [cenário 2: implementar uma VM com uma imagem personalizada para SAP] [implementação-guia-3.3] [implementação do guia de] [guia de implementação]. 

Requisitos de quando preparar a sua própria imagem de VM Azure são:

* Originalmente VHD que contém o sistema operativo poderia ter um tamanho máximo de 127GB apenas. Esta limitação tem eliminada no final de Março de 2015. Agora o VHD que contém o sistema operativo pode ser até 1TB de tamanho como qualquer outro armazenamento do Windows Azure alojado VHD também.
[comentário]: <>  (MShermannd TODO tem de verificar se o clip também converte estáticos)
* -Tem de estar no formato VHD fixo. Dinâmico VHDs ou VHDs no formato de VHDx ainda não são suportados no Azure. VHDs dinâmicos serão convertido em VHDs estáticos quando carregar o VHD com commandlets do PowerShell ou clip
* VHDs que são instalados para a VM e devem ser colocados novamente de Azure para ser num formato de VHD fixo a necessidade de VM. O mesmo limite de tamanho do disco SO aplica-se aos discos em dados também. VHDs podem ter um tamanho máximo de 1TB. VHDs dinâmicos serão convertido em VHDs estáticos quando carregar o VHD com commandlets do PowerShell ou clip
* Uma vez que todos os utilizadores de domínio registado como os utilizadores na VM não existirão num cenário de apenas na nuvem (consulte capítulo [apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local] [planeamento-guia-2.1] deste documento), serviços de utilizar essa domínio contas poderão não funcionar assim que a imagem é implementada no Azure. Este é especialmente verdadeiro para contas que são utilizadas para executar a serviços como o DBMS ou SAP aplicações. Por isso, tem de substituir essas contas de domínio com contas locais VM e elimine as contas de domínio no local na VM. Manter os utilizadores do domínio no local na imagem VM poderão não estar um problema à VM é implementada no cenário de publicação em local conforme descrito capítulo [cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local] [planeamento-guia-2.2] neste documento.
* Adicione outra conta local com privilégios de administrador que podem ser utilizadas pelo suporte da Microsoft no investigações problema ou que podem ser atribuído como contexto para serviços e aplicações para executar até a VM é implementada e utilizadores mais adequados pode ser utilizada.
* Em implementações apenas na nuvem e onde as contas de domínio foram utilizadas como inícios de sessão de DBMS ou utilizadores ao executar o sistema no local, devem ser eliminados os utilizadores do domínio. É necessário para se certificar de que o administrador local plus outro utilizador do local VM é adicionado como um início de sessão do utilizador do DBMS como administradores.
* Adicione outras contas locais como aqueles poderão ser necessárias para o cenário de implementação específico.
* Se a imagem que contém uma instalação do SAP NetWeaver e mudar o nome do nome do anfitrião do nome original no ponto de implementação do Azure é provável, que recomenda-se para copiar as versões mais recentes do DVD do Gestor de SAP de aprovisionamento de Software para o modelo. Isto permite-lhe utilizar facilmente a funcionalidade de mudar o nome do SAP fornecido para adaptar o nome do anfitrião alterada e/ou alterar o SID do sistema SAP dentro da imagem VM implementado assim que uma nova cópia é iniciada.

___

> ![Windows][Logo_Windows] Windows
>
> Certifique-se que unidade D:\ não é utilizado montagem automática no disco do conjunto de discos ligados conforme descrito capítulo [montagem definição automática para discos ligados] [planeamento-guia-5.5.3] neste documento.
> 
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que não é utilizado/mnt/recurso e de que todos os discos são instalados através do uuid. Para o sistema operativo disco certificar a entrada de bootloader reflete também a montagem com base em uuid.

___

* Interface gráfica do SAP (para administrativo e configuração efeitos) pode ser instalado previamente num modelo.
* Outro software necessário para executar os VMs com êxito em cenários de publicação em local pode ser instalado, desde que este software pode trabalhar com mudar o nome da VM.

Se a VM está suficientemente preparada para ser genérico e eventualmente independentes de contas/utilizadores não está disponíveis no cenário de implementação Azure alvo, o último passo de preparação do generalizar como uma imagem é realizado.

##### <a name="generalizing-a-vm"></a>Generalizar uma VM 

___

[comentário]: <>  (MShermannd TODO tem que localizar artigos melhor / docu sobre generalizar VMs para processador)
> ![Windows][Logo_Windows] Windows
>
> O último passo é iniciar sessão numa VM com uma conta de administrador. Abra uma janela de comando do Windows como 'Administrador'. Aceda à ...\windows\system32\sysprep e executar sysprep.exe.
> Será apresentada uma pequena janela. É importante verificar a opção 'Generalize' (a predefinição é anulado dada) e alterar a opção de encerramento a partir do seu valor predefinido de 'Reiniciar' para 'Encerramento'. Este procedimento assume que o processo de sysprep é executado no local no SO convidado de uma VM.
> Se pretender efetuar o procedimento com uma VM já em execução no Azure, siga os passos descritos [neste]artigo[virtual-machines-windows-capture-image].
> 
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina de virtual Linux para utilizar como um modelo de Gestor de recursos][virtual-machines-linux-capture-image]

___

### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferir VMs e VHDs no local para Azure
Uma vez que não é possível através do Portal do Azure carregar imagens VM e discos para Azure, tem de utilizar os cmdlets do Azure PowerShell ou clip. Outra possibilidade é a utilização da ferramenta de 'AzCopy'. A ferramenta pode copiar VHDs entre no local e Azure (em ambas as direções). Também pode copiar VHDs entre regiões Azure. Consulte [Este documentação] [ storage-use-azcopy] para transferir e a utilização da AzCopy.

Uma terceira alternativa seria utilizar várias ferramentas de interface gráfica orientados para terceiros. No entanto, certifique-se de que estas ferramentas são suporte Blobs do Azure página. Para as nossas finalidades precisamos de utilizar armazenamento de Blobs do Azure página (as diferenças descritas aqui: <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>). Além disso, as ferramentas de fornecida pela Azure são muito eficientes comprimindo a VMs e VHDs que necessitam de ser carregado. Isto é importante porque este eficiência na compressão reduz o tempo de carregamento (que varia mesmo assim consoante a ligação de carregamento à internet a partir da Facilidade de acesso no local e a região de implementação do Azure direccionado). É da feira da ciência partem do princípio de que carregar um VM ou VHD a partir da localização Europeu para EUA dados baseados no Azure centros de irão demorar mais tempo a carregar os mesmo VMs/VHDs para os centros de dados do Azure Europeu. 

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregar um VHD nos locais para Azure
Para carregar um VM existente ou VHD da rede no local VM ou VHD tem de cumprir os requisitos, tal como indicado na capítulo [preparação para mover uma VM nos locais para o Azure com um disco não generalized] [planeamento-guia-5.2.1] deste documento.

Como uma VM não precisa de ser generalized e pode ser carregada no Estado e a forma tiver após o encerramento no lado no local. O mesmo se VERDADEIRO para VHDs adicionais que não contêm qualquer sistema operativo. 

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregar um VHD e tornando um disco do Azure
Neste caso queremos carregar um VHD, com ou sem um sistema operativo e montagem-lo para uma VM como um disco de dados ou utilizá-la como disco SO. Este é um processo com várias passo 

__PowerShell__

* Início de sessão para a sua subscrição do _AzureRmAccount de início de sessão_
* Definir a subscrição do seu contexto com _Conjunto AzureRmContext_ e parâmetro SubscriptionId ou SubscriptionName - ver <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregar o VHD com _Adicionar AzureRmVhd_ para uma conta de armazenamento do Azure - ver <https://msdn.microsoft.com/library/mt603554.aspx>
* Definir o disco de sistema operativo de uma nova config VM VHD com _Conjunto AzureRmVMOSDisk_ - ver <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM a partir do config VM com _Novo AzureRmVM_ - ver <https://msdn.microsoft.com/library/mt603754.aspx>
* Adicionar um disco de dados para uma nova VM com _Adicionar AzureRmVMDataDisk_ - ver <https://msdn.microsoft.com/library/mt603673.aspx>

__Clip Azure__

* Mudar para o modo de Gestor de recursos do Azure com _braço de modo de azure config_
* Início de sessão para a sua subscrição do _início de sessão do azure_
* Selecione a sua subscrição do _conta azure conjunto `<subscription name or id` _
* Carregar o VHD com _BLOBs do azure armazenamento carregar_ - consulte o artigo [utilizar o clip do Azure com armazenamento do Windows Azure][storage-azure-cli]
* Criar uma nova VM especificando o VHD carregado como disco do SO com _Criar azure vm_ e parâmetro -d
* Adicionar um disco de dados para uma nova VM com _vm disco anexar-novo_

__Modelo__

* Carregar o VHD com clip Powershell ou do Azure
* Implemente a VM com um modelo JSON referenciar o VHD como é mostrado [neste](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json)modelo de JSON de exemplo.

#### <a name="deployment-of-a-vm-image"></a>Implementação de uma imagem VM
Para carregar um VM existente ou VHD da rede no local para poder utilizá-la como uma imagem de Azure VM VM ou VHD tem de cumprir os requisitos listados na capítulo [preparação para implementar uma VM com uma imagem específica do cliente para SAP] [planeamento-guia-5.2.2] deste documento.

* Utilizar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar a VM - veja [como capturar uma máquina de virtual do Windows no modelo de implementação de Gestor de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina de virtual Linux para utilizar como um modelo de Gestor de recursos][virtual-machines-linux-capture-image-capture]
* Início de sessão para a sua subscrição do _AzureRmAccount de início de sessão_
* Definir a subscrição do seu contexto com _Conjunto AzureRmContext_ e parâmetro SubscriptionId ou SubscriptionName - ver <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregar o VHD com _Adicionar AzureRmVhd_ para uma conta de armazenamento do Azure - ver <https://msdn.microsoft.com/library/mt603554.aspx>
* Configurar o disco de sistema operativo de uma configuração VM novo para o VHD com _conjunto AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage_ -consulte <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM a partir do config VM com _Novo AzureRmVM_ - ver <https://msdn.microsoft.com/library/mt603754.aspx>

__Clip Azure__

* Utilizar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar a VM - veja [como capturar uma máquina de virtual do Windows no modelo de implementação de Gestor de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina de virtual Linux para utilizar como um modelo de Gestor de recursos] [ virtual-machines-linux-capture-image-capture] para Linux
* Mudar para o modo de Gestor de recursos do Azure com _braço de modo de azure config_
* Início de sessão para a sua subscrição do _início de sessão do azure_
* Selecione a sua subscrição do _conta azure conjunto `<subscription name or id` _
* Carregar o VHD com _BLOBs do azure armazenamento carregar_ - consulte o artigo [utilizar o clip do Azure com armazenamento do Windows Azure][storage-azure-cli]
* Criar uma nova VM especificando o VHD carregado como disco SO com _Criar azure vm_ e parâmetro -Q

__Modelo__

* Utilizar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar a VM - veja [como capturar uma máquina de virtual do Windows no modelo de implementação de Gestor de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina de virtual Linux para utilizar como um modelo de Gestor de recursos] [ virtual-machines-linux-capture-image-capture] para Linux
* Carregar o VHD com clip Powershell ou do Azure
* Implemente a VM com um modelo JSON referenciar a imagem VHD como é mostrado [neste](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)modelo de JSON de exemplo.

#### <a name="downloading-vhds-to-on-premises"></a>Transferir VHDs para no local
Azure infraestrutura como um serviço não é uma rua unidirecional de apenas ser capaz de carregar VHDs e pela SAP, sistemas. Pode mover SAP sistemas a partir do Azure de volta para o mundo no local.

Durante o período de tempo de transferência de VHDs não podem ser ativos. Mesmo quando a transferir VHDs que são instalados para VMs, a VM tem de estar encerramento. Se apenas pretende transferir o conteúdo da base de dados que, em seguida, deverá ser utilizado para configurar um novo sistema no local e se for aceitável que durante o tempo de transferência e a configuração do novo sistema que o sistema no Azure pode ainda ser operacionais avançado, poderia Evite um tempo de inatividade longo ao efetuar uma cópia de segurança da base de dados comprimidos para um VHD e apenas transfira esse VHD em vez de transferir também a VM base SO.

#### <a name="powershell"></a>PowerShell

Assim que o sistema SAP está parado e a VM é encerramento, pode utilizar o cmdlet do PowerShell AzureRmVhd guardar no destino no local para transferir os discos VHD novamente para o mundo no local. Para poder fazer, que precisa do URL do VHD que pode encontrar na 'Armazenamento secção' do Portal do Azure (necessidade para navegar para a conta de armazenamento e o contentor de armazenamento onde foi criado o VHD) e precisa de saber onde o VHD deve ser copiado.

Em seguida, pode tirar partido o comando definindo simplesmente o parâmetro SourceUri como o URL do VHD para transferir e o LocalFilePath como a localização física do VHD (incluindo o seu nome). O comando foi o seguinte aspeto:

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Para obter mais detalhes do cmdlet AzureRmVhd guardar, verifique aqui <https://msdn.microsoft.com/library/mt622705.aspx>. 

#### <a name="cli"></a>CLIP

Assim que o sistema SAP está parado e a VM é encerramento, pode utilizar a transferência de Blobs do Azure clip comando azure armazenamento no destino no local para transferir os discos VHD novamente para o mundo no local. Para fazê-lo, necessita do nome e o contentor do VHD que pode encontrar na secção especiais de armazenamento do Portal do Azure (necessidade para navegar para a conta de armazenamento e o contentor de armazenamento onde foi criado o VHD) e precisa de saber onde o VHD deve ser copiado.

Em seguida, pode tirar partido o comando definindo simplesmente a blob parâmetros e contentor do VHD para transferir e o destino como a localização de destino física do VHD (incluindo o seu nome). O comando foi o seguinte aspeto:

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download> 
```

### <a name="transferring-vms-and-vhds-within-azure"></a>Transferir VMs e VHDs dentro do Azure

#### <a name="copying-sap-systems-within-azure"></a>Copiar sistemas SAP dentro do Azure

Um sistema SAP ou até mesmo um DBMS servidor dedicado suporte uma camada de aplicação do SAP irá provavelmente consistir VHDs várias que contêm o sistema operativo utilizado com os binários ou os ficheiros de dados e de registo da base de dados SAP. Nem a funcionalidade de copiar VHDs Azure nem a funcionalidade Azure da guardando VHDs disco tem um mecanismo de sincronização que iria instantâneo vários VHDs modo síncrono. Por conseguinte, o estado dos VHDs copiados ou guardados, mesmo se aqueles são instalados contra a mesma VM seria diferente. Isto significa que o betão caso de ter dados diferentes e logfile(s) contidos os VHDs diferentes, a base de dados no final seria inconsistente. 

**Conclusão: Para poder copie ou guarde VHDs que fazem parte da configuração de um sistema SAP tem de parar o sistema SAP e também precisa de encerrar a VM implementada. Apenas, em seguida, pode copiar ou transferir o conjunto de VHDs quer criar uma cópia do sistema SAP no Azure ou no local.**

Discos de dados são armazenados como ficheiros VHD numa conta de armazenamento do Azure e podem ser diretamente anexar uma máquina virtual ou ser utilizado como uma imagem. Neste caso, o VHD é copiado para outro local antes de beeing anexado à máquina virtual. O nome completo do ficheiro VHD no Azure tem de ser exclusivo Azure. Tal como mencionado anteriormente já, o nome é tipo de um nome de três partes que tem a seguinte apresentação: 

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>PowerShell
Pode utilizar os cmdlets do Azure PowerShell para copiar um VHD como é mostrado [neste]artigo[storage-powershell-guide-full-copy-vhd].

##### <a name="cli"></a>CLIP
Pode utilizar o clip do Azure para copiar um VHD como é mostrado [neste] artigo[storage-azure-cli-copy-blobs]

##### <a name="azure-storage-tools"></a>Ferramentas de armazenamento Azure

* <http://azurestorageexplorer.Codeplex.com/releases/View/125870>

Existem também profissionais edições do Explorador de armazenamento do Azure que podem ser encontradas aqui:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer> 


A cópia de um VHD propriamente dito dentro de uma conta de armazenamento é um processo que entra em apenas alguns segundos (semelhante ao hardware de SAN criar instantâneos com preguiça cópia e cópia na escrita). Depois de ter uma cópia do ficheiro VHD pode anexá-lo a uma máquina virtual ou utilizá-la como uma imagem para anexar cópias do VHD a máquinas virtuais.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLIP
```
azure config mode arm 

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiar discos entre contas de armazenamento do Azure
Não é possível executar esta tarefa no Portal do Azure. Pode ise Azure PowerShell cmdlets, Azure clip ou uma terceira parte armazenamento browser. Os comandos de clip ou os cmdlets do PowerShell, podem criar e gerir blobs, que incluem a capacidade de forma assíncrona copiar blobs através de contas de armazenamento e regiões dentro da subscrição Azure.

##### <a name="powershell"></a>PowerShell 

Também é possível copiar VHDs entre subscrições. Para obter mais informações, leia [Este artigo][storage-powershell-guide-full-copy-vhd]. 

O fluxo básico da lógica do cmdlet PS tem o seguinte aspeto:

* Criar um contexto de conta de armazenamento para a conta de armazenamento de origem com _Novo AzureStorageContext_ - ver <https://msdn.microsoft.com/library/dn806380.aspx>
* Criar um contexto de conta de armazenamento para a conta de armazenamento de destino com _Novo AzureStorageContext_ - ver <https://msdn.microsoft.com/library/dn806380.aspx>
* Iniciar a cópia com

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verificar o estado da cópia de um ciclo com
 
```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o novo VHD para uma máquina virtual, tal como descrito acima.

Para obter exemplos consulte [Este artigo][storage-powershell-guide-full-copy-vhd]

##### <a name="cli"></a>CLIP
* Iniciar a cópia com

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* Verificar o estado, se a cópia de um ciclo com

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```
  
* Anexe o novo VHD para uma máquina virtual, tal como descrito acima.

Para obter exemplos consulte [Este artigo][storage-azure-cli-copy-blobs]

### <a name="disk-handling"></a>Processamento do disco
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura VM/VHD para implementações do SAP
Idealmente o processamento da estrutura de uma VM e os VHDs associados deve ser muito simple. Em instalações no local, clientes desenvolvido várias formas de estruturar uma instalação de servidor. 

* VHD base uma que contém o sistema operativo e todos os binários do DBMS e/ou do SAP. Desde de Março de 2015, este VHD pode ser até 1TB de tamanho em vez de restrições anteriores que limitado-lo a 127 GB. 
* Um ou vários VHDs que contém o ficheiro de registo DBMS da base de dados SAP e o ficheiro de registo da área de armazenamento temp DBMS (se o DBMS suporta esta). Se os requisitos de IOPS de registo de base de dados são alto, terá de faixas vários VHDs para alcançar o volume IOPS necessário. 
* Um número de VHDs que contém um ou dois ficheiros de base de dados da base de dados SAP e também os ficheiros de dados temp DBMS (se o DBMS suporta esta).

![Referência de configuração da Azure IaaS VM para SAP][planning-guide-figure-1300]

[comentário]: <>  (MShermannd TODO descrever Linux estrutura)

___

> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes podemos viu configurações que, por exemplo, SAP e DBMS binários não foram instalados na unidade de c:\ onde foi instalado o SO. Que ocorreram vários motivos para isto, mas quando podemos correu novamente para a raiz, normalmente estava que foram pequenas as unidades e SO actualizações necessário espaço adicional 10 a 15 anos atrás. Ambas as condições que não são aplicadas demasiado muitas vezes já estes dias. Hoje em dia na unidade de c:\ pode ser mapeada em grande volume de discos ou em VMs. Para manter implementações simples na sua estrutura, recomenda-se para seguir o padrão de implementação seguintes para sistemas SAP NetWeaver no Azure
>
> O ficheiro de paginação do sistema operativo Windows deve ser na unidade d: (que não sejam persistentes disco) 
> 
> ![Linux][Logo_Linux] Linux
>
> Colocar o ficheiro de Linux comutaçao em /mnt/mnt/recurso num Linux, tal como é descrito [neste artigo][virtual-machines-linux-agent-user-guide]. O ficheiro de trocar pode ser configurado no ficheiro de configuração de /etc/waagent.conf o agente de Linux. Adicionar ou alterar as seguintes definições:

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, tem de reiniciar o agente Linux com

```
sudo service waagent restart
```

Leia a nota de SAP [1597355] para obter mais detalhes sobre o tamanho do ficheiro trocar recomendadas

___

O número de VHDs utilizadas para os ficheiros de dados DBMS e o tipo de armazenamento do Windows Azure estes VHDs estão alojados no deve ser determinado aos requisitos de IOPS e a latência necessário. As quotas de exatas são descritas [neste] artigo[virtual-machines-sizes]

Experiência de implementações do SAP ao longo dos anos de 2 últimas ensinadas-nos algumas lições que podem ser resumidas como:

* IOPS tráfego aos ficheiros de dados diferentes não é sempre o mesmo uma vez que os sistemas de cliente existentes poderão ter forma diferente com tamanhos ficheiros de dados que representa as bases de dados do SAP. Como resultado que instalado melhor utilizar uma configuração de RAID através de vários VHDs para colocar os ficheiros de dados que LUNs gravados terminar aqueles. Ocorreram situações, especialmente com armazenamento padrão do Windows Azure onde uma taxa de IOPS visitas a quota de um único VHD contra o registo de transações do DBMS. Destes cenários recomenda a utilização de armazenamento de Premium ou em alternativa agregar várias VHDs armazenamento padrão com um software de RAID.

___

> ![Windows][Logo_Windows] Windows
>
> * [Práticas recomendadas do desempenho para SQL Server em máquinas virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
> 
> ![Linux][Logo_Linux] Linux
>
> * [Configurar o Software RAID em Linux][virtual-machines-linux-configure-raid]
> * [Configurar o LVM numa VM Linux no Azure][virtual-machines-linux-configure-lvm]
> * [Azure segredos de armazenamento e otimizações e de Linux/s](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)

___

* Armazenamento de Premium apresenta significativo um melhor desempenho, especialmente para escritas de registo da transação crítica. Cenários do SAP que espera para a prestação produção como o desempenho, recomenda-se vivamente utilizar VM séries que podem tirar partido de armazenamento do Windows Azure Premium.

Tenha em conta que o VHD que contém o sistema operativo, e como recomendamos, os binários do SAP e a base de dados (base VM), assim, não está já limitado a 127GB. Agora pode ter até 1TB de tamanho. Isto deve ser espaço suficiente para manter o ficheiro de necessário, por exemplo, incluindo os registos de tarefa do lote SAP.

Para obter mais sugestões e obter mais detalhes, especificamente para DBMS VMs, consulte [DBMS guia de implementação do] [dbms guia]


#### <a name="disk-handling"></a>Processamento do disco
Na maioria dos cenários tem de criar discos adicionais para implementar a base de dados do SAP para a VM. Falámos sobre as considerações num número de VHDs capítulo [estrutura VM/VHD para implementações do SAP] [5.5.1 de planeamento-guia] deste documento. Portal do Azure permite anexar e desanexar discos assim que um VM base é implementada. Os discos podem ser anexado/desligado quando está a VM para cima e em execução, assim como quando está parado. Quando a anexar um disco, o Portal do Azure oferece anexar um disco vazio ou um disco existente que neste ponto específico não está associado a outra VM. 

**Nota**: só podem ser anexados VHDs VM um dado momento.
 
![Anexar / desanexar discos com armazenamento padrão do Windows Azure][planning-guide-figure-1400]

Tem de decidir se pretende criar um VHD novo e vazio (que deverá ser criado na mesma conta de armazenamento como base que VM está no) ou se quiser selecionar um VHD existente que foi carregado anterior e deve ser anexado agora a VM. 

**Importante**: **Não** pretende utilizar anfitrião colocação em cache com armazenamento padrão do Windows Azure. Deve deixar a preferência de Cache de anfitrião a predefinição de nenhum. Com o Azure Premium armazenamento deve ativar colocação em cache de leitura se característica e/s é principalmente leitura como típico e/s tráfego contra ficheiros de dados da base de dados. Em caso de ficheiro de registo da transação da base de dados sem colocação em cache é recomendada.

___

> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal do Azure][virtual-machines-windows-attach-disk-portal]
>
> Se estiverem anexados discos, tem de iniciar sessão para a VM para abrir o Gestor de disco do Windows. Se montagem automática não estiver ativada, tal como recomendado capítulo [montagem definição automática para discos ligados] [planeamento-guia-5.5.3], o volume recentemente anexado tem de ser tomadas online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Se discos estão anexados, terá de iniciar sessão para a VM e inicialização discos conforme descrito [neste] artigo[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]

___

Se o novo disco for um disco vazio, é necessário formatar o disco também. Para a formatação, especialmente para ficheiros de dados e de registo DBMS as recomendações mesmo que para implementações do base de DBMS aplicam-se.

Tal como já é mencionado capítulo [do Microsoft Azure Máquina Virtual conceito] [planeamento-guia-3,2], uma conta de armazenamento do Windows Azure não fornece recursos infinitos em termos de volume e/s, volume IOPS e os dados. Normalmente DBMS VMs mais são afetados por este. Poderá ser melhor utilizar uma conta de armazenamento separada para cada VM se tiver alguns elevado e/s volume VMs para implementar o para se manter dentro do limite do volume de conta de armazenamento do Azure. Caso contrário, é necessário ver como pode equilibrar estes VMs entre diferentes contas de armazenamento sem atingir o limite de cada única conta de armazenamento. Obter mais detalhes são explicadas [DBMS guia de implementação do] [dbms guia]. Também deve manter estas limitações Lembre-se para a aplicação de SAP pura VMs do servidor ou outros VMs que desejar, poderá ser necessário VHDs adicionais.

Outro tópico que é relevante para contas de armazenamento é se VHDs numa conta armazenamento estão a obter replicadas Geo. Replicação geo está activada ou desactivada no nível de conta de armazenamento e não no nível VM. Se estiver ativada geo replicação, VHDs dentro da conta de armazenamento seriam ser replicados para outro centro de dados Azure dentro da mesma região. Antes de decidir nisto, deverá consideração a restrição seguinte:

Replicação de Geo Azure funciona localmente cada VHD numa VM e não replica IOs por ordem cronológica através de vários VHDs num VM. Por conseguinte, que representa a VM base VHD, bem como qualquer VHDs adicionais anexados a VM são replicados independentes uns dos outros. Isto significa que não existe nenhuma sincronização entre as alterações nas VHDs diferentes. O facto de que o IOs são replicadas independentemente dos existentes da ordem na qual são escritas significa que geo-a replicação não é do valor para os servidores de base de dados que tenham suas bases de dados distribuídos ao longo de vários VHDs. Para além de DBMS, também poderá haver outras aplicações, onde os processos de escrita ou manipulam os dados na VHDs diferentes e onde é importante manter a ordem das alterações. Se o que é um requisito, a replicação geo no Azure não deve ser activada. Depende de se precisa ou pretende geo replicação para um conjunto de VMs, mas não para outro conjunto, pode já categorizar VMs e os respetivos VHDs relacionados para diferentes contas de armazenamento que tenham replicação geo ativado ou desativado.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Definir montagem automática para discos ligados

___


> ![Windows][Logo_Windows] Windows
> 
> Para VMs que são criados a partir do próprio imagens ou discos, é necessário verificar e possivelmente definir o parâmetro montagem automática. A definição deste parâmetro permitirá a VM após um reinício ou nova implementação no Azure para montagem novamente automaticamente as unidades anexado/instalados. 
> O parâmetro estiver definido para as imagens fornecidas pela Microsoft Azure Marketplace.
>
> Para definir a montagem automática, consulte a documentação da linha de comandos executável diskpart.exe aqui: 
> 
> * [Opções da linha de comandos DiskPart](https://technet.microsoft.com/library/cc766465.aspx)
> * [Montagem automática](http://technet.microsoft.com/library/cc753703.aspx)
> 
> A janela de linha de comandos do Windows deve ser aberta como administrador.
> 
> Se estiverem anexados discos, tem de iniciar sessão para a VM para abrir o Gestor de disco do Windows. Se montagem automática não estiver ativada, tal como recomendado capítulo [montagem definição automática para discos ligados] [planeamento-guia-5.5.3], o volume recentemente anexado > tem de ser tomadas online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Terá de iniciar um disco vazio recentemente anexado conforme descrito [neste]artigo[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Também precisa de adicionar novos discos para o /etc/fstab.

___


### <a name="final-deployment"></a>Implementação final
Para a implementação final e os passos exatos, sobretudo no que diz respeito a implementação do SAP expandido monitorização, consulte [guia de implementação do] [guia de implementação].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Aceder aos sistemas SAP em execução no interior do Azure VMs
Para obter cenários apenas na nuvem, poderá pretender ligar a esses sistemas SAP através da internet público utilizando a interface gráfica do SAP. Para nestes casos, os seguintes procedimentos tem de ser aplicada.

Posteriormente no documento Abordaremos o outro cenário principal, ligar-se aos sistemas SAP no implementações de publicação em local que tenham uma ligação de site para o site (túnel VPN) ou ligação do Azure ExpressRoute entre os sistemas de Azure e sistemas de no local.


### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos sistemas SAP

Com o Gestor de recursos do Azure não existem predefinido pontos finais deixem como no modelo clássico de antigo. Todas as portas de uma VM de processador Azure estão abertas desde que:

1. Nenhum grupo de segurança de rede está definido para a sub-rede ou a interface de rede. O tráfego de rede para o Azure VMs pode ser protegido através de chamados "grupos de segurança de rede". Para obter mais informações consulte o artigo [o que é um grupo de segurança de rede (NSG)?][virtual-networks-nsg]
1. Sem Balanceador de carga Azure está definido para a interface de rede   
 
Ver a diferença de arquitectura entre modelo clássico e processador conforme descrito [neste]artigo[virtual-machines-azure-resource-manager-architecture].
 
#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuração da conectividade de sistema do SAP e interface gráfica do SAP para cenário apenas na nuvem
Consulte este artigo que descreve detalhes este tópico: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx> 

#### <a name="changing-firewall-settings-within-vm"></a>Alterar as definições da Firewall dentro VM
Poderá ser necessário configurar a firewall no seu máquinas virtuais para permitir o tráfego de entrada ao seu sistema de SAP. 

___

> ![Windows][Logo_Windows] Windows
>
> Por predefinição, a Firewall do Windows dentro de uma VM implementado Azure acende. Tem agora permitir a porta SAP para serem abertos, caso contrário, a interface gráfica SAP não será possível ligar.
> Para fazer isto:
>
>  * Controlo abrir Panel\System e Security\Windows Firewall para 'Definições Avançadas'.
>  * Agora com o botão direito no regras de entrada e escolha regra de novo.
>  * No seguinte escolha de Assistente para criar uma nova regra de 'Porta'.
>  * No próximo passo do assistente, deixe a definição em TCP e escreva o número de porta que pretende abrir. Uma vez que os nossos ID da instância SAP 00, podemos demorou 3200. Se a sua instância tiver um número diferente de instância, a porta que definiu anteriormente baseada no número instância deve ser aberta.
>  * Na parte seguinte do assistente, tem de deixar o item 'Permitir a ligação à' selecionada.
>  * No passo seguinte do assistente tem de definir se a regra aplica-se para a rede de domínio, privado e público. Fórum ajuste-se for necessário para as suas necessidades. No entanto, ligar-se com a interface gráfica do SAP do exterior através da rede público, tem de ter a regra aplicada à rede pública.
>  * No último passo do assistente, tem de dar um nome à regra e, em seguida, guardar a regra ao premir 'Concluir'
>
>  A regra entra em vigor imediatamente.
>
> ![Definição de regra de porta][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens Linux no Azure Marketplace não ativar a firewall iptables por predefinição e a ligação ao seu sistema de SAP deverá funcionar. Se ativou iptables ou qualquer outro firewall, consulte a documentação de iptables ou a firewall utilizada para permitir o tráfego de entrada tcp para porta 32xx (onde xx é o número de sistema do seu sistema de SAP). 

___

#### <a name="security-recommendations"></a>Recomendações de segurança

A interface gráfica SAP não ligar imediatamente a qualquer uma das instâncias SAP (porta 32xx) que estiver a executar, mas primeiro liga-se através da porta que foi aberta para o processo de servidor do SAP mensagem (porta 36xx). No passado a mesma muito porta foi utilizada pelo servidor de mensagem para a comunicação interna para as instâncias da aplicação. Para impedir que no local servidores de aplicações de inadvertidamente comunicar com um servidor de mensagem no Azure as portas de comunicação interna podem ser alteradas. Recomenda-se vivamente para alterar a comunicação interna entre o servidor de mensagem SAP e o respetivas instâncias da aplicação para um número de porta diferente nos sistemas que tenham sido clonar de sistemas no local, tal como um clonar de desenvolvimento para etc testes de projeto. Isto pode ser feito com o parâmetro de perfil predefinido:

>   rdisp/msserv_internal

conforme é documentado: <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm> 

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Conceitos de implementação apenas na nuvem de instâncias do SAP

### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Única VM com SAP NetWeaver demonstração/formação cenário
 
![Sistemas de VM SAP demonstração única em execução com os mesmos nomes VM, isolados em serviços em nuvem Azure][planning-guide-figure-1700]

Neste cenário (ver capítulo [apenas na nuvem] [planeamento-guia-2.1] deste documento) estamos a implementar um cenário de sistema típica formação/demonstração onde o cenário de formação concluída/demonstração encontra-se uma única VM. Vamos partem do princípio de que a implementação é feita através de modelos de imagens VM. Recomendamos também partem do princípio que múltiplo destes demonstração/formações VMs precisa de ser implementada com VMs ter o mesmo nome.

É de partem do princípio de que criou uma imagem VM, tal como descrito em algumas secções do capítulo [preparar VMs com SAP para Azure] [planeamento-guia-5.2] neste documento.

A sequência de eventos para implementar o cenário tem o seguinte aspeto:

[comentário]: <>  (MShermannd TODO tem de fornecer processador amostra descrição com o modelo de json + esclarecer relativamente ao nome VM exclusivo na rede virtual processador)   
##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recursos para cada horizontal formação/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* Criar uma nova conta de armazenamento

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada horizontal formação/demonstração ativar a utilização da mesma hostname e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permite que apenas o tráfego porta 3389 de ativar o acesso de ambiente de trabalho remoto e porta 22 para SSH. 

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser utilizado para aceder a máquina virtual a partir da internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell 
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip 
```

* Crie uma máquina virtual. O cenário apenas na nuvem cada VM terá o mesmo nome. O SID SAP das instâncias SAP NetWeaver nesses VMs serão os mesmos também. Dentro do grupo de recursos do Azure, o nome da VM precisa de ser exclusivos, mas no diferentes grupos de recursos do Azure pode ser executadas VMs com o mesmo nome. A conta de 'Administrador' predefinido do Windows ou raiz para Linux não são válidas. Por conseguinte, um novo nome de utilizador do administrador tem de ser definidas juntamente com uma palavra-passe. O tamanho da VM também tem de ser definidos.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione discos adicionais e restaurar conteúdo for necessário. Tenha em atenção que todos os nomes de BLOBs (URLs para os blobs) tem de ser exclusivos Azure.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLIP

O seguinte código de exemplo pode ser utilizado em Linux. Para o Windows, consulte Utilizar PowerShell, conforme descrito acima ou adaptar o exemplo para utilizar % rgName % em vez de $rgName e definir a variável de ambiente utilizando o comando Windows _Definir_.

* Criar um novo grupo de recursos para cada horizontal formação/demonstração

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* Criar uma nova conta de armazenamento

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* Crie uma nova rede virtual para cada horizontal formação/demonstração ativar a utilização da mesma hostname e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permite que apenas o tráfego porta 3389 de ativar o acesso de ambiente de trabalho remoto e porta 22 para SSH. 

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser utilizado para aceder a máquina virtual a partir da internet

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet 
```

* Crie uma máquina virtual. O cenário apenas na nuvem cada VM terá o mesmo nome. O SID SAP das instâncias SAP NetWeaver nesses VMs serão os mesmos também. Dentro do grupo de recursos do Azure, o nome da VM precisa de ser exclusivos, mas no diferentes grupos de recursos do Azure pode ser executadas VMs com o mesmo nome. A conta de 'Administrador' predefinido do Windows ou raiz para Linux não são válidas. Por conseguinte, um novo nome de utilizador do administrador tem de ser definidas juntamente com uma palavra-passe. O tamanho da VM também tem de ser definidos.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* Opcionalmente, adicione discos adicionais e restaurar conteúdo for necessário. Tenha em atenção que todos os nomes de BLOBs (URLs para os blobs) tem de ser exclusivos Azure.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>Modelo
Pode utilizar os modelos de exemplo do repositório de modelos de guia de introdução de azure no github.

* [Simples Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Windows simples VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementar um conjunto de VMs que precisa de comunicar dentro do Azure
Este cenário apenas na nuvem é um cenário típico de formação e demonstração fins onde o software que representa o demonstração/formação cenário for distribuído através de vários VMs. Os diferentes componentes instalados nas VMs diferentes precisam de comunicar com os outros. Novamente, neste cenário no local a comunicação de rede ou cenário de publicação em local é necessária.

Este cenário é uma extensão da instalação do descrito capítulo [VM única com SAP NetWeaver demonstração/formação cenário] [planeamento-guia-7.1] deste documento. Neste caso mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo seguinte paisagem formação é composta por um VM ASCS/SCS do SAP, uma VM executar um DBMS e uma instância de servidor de aplicações SAP VM.

Antes de criar este cenário tem refletir sobre as definições básicas, tal como já exercidas cenário antes.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupo de recursos e atribuir nomes a Máquina Virtual
Todos os nomes de grupo de recursos tem de ser exclusivos. Desenvolver o seu próprio esquema de nomes dos seus recursos, tais como `<rg-name`>-sufixo. 

O nome de máquina virtual tem de ser exclusivos dentro do grupo de recursos. 

#### <a name="setup-network-for-communication-between-the-different-vms"></a>Configurar a rede para a comunicação entre os VMs diferentes
 
![Conjunto de VMs dentro de uma rede Virtual Azure][planning-guide-figure-1900]

Para evitar conflitos com clones dos mesmo paisagens formação/demonstração de atribuição de nomes, tem de criar uma rede Virtual Azure para cada horizontal. Resolução de nomes DNS será fornecida pelo Azure ou pode configurar o seu servidor DNS fora do Azure (não a ainda mais discutir aqui). Neste cenário podemos não configure a nossa própria DNS. Para todas as máquinas virtuais dentro de uma rede Virtual do Azure, comunicação via nomes de anfitriões irão estar ativada. 

As razões para separar paisagens formação ou demonstração por redes virtuais e não apenas os grupos do recurso podem ser:

* Precisa de paisagem SAP como configurar o seu próprio AD/OpenLDAP e um servidor de domínio tem de fazer parte de cada um das paisagens.  
* Paisagem SAP como configurar o tem componentes necessitar de trabalhar com endereços IP fixos.

Podem encontrar mais detalhes sobre redes virtuais do Azure e como defini-las [neste]artigo[virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implementar VMs do SAP com a conectividade da rede da empresa (cruzada local)

Executar uma horizontal SAP e dividir a implementação entre base para os servidores de DBMS de alto nível, ambientes no local foi virtualizado para camadas de aplicação e camadas de 2 mais pequena configurado sistemas SAP e o Azure IaaS. Partem do princípio de base é que os sistemas SAP dentro de uma horizontal SAP necessitam comunicar com os outros e com muitos outros componentes de software implementados na empresa, independentemente da sua forma de implementação. Também deverá haver sem diferenças introduzidas pelo formulário de implementação para estabelecer ligação com a interface gráfica do SAP ou outras interfaces do utilizador final. Apenas podem ser cumpridas estas condições quando temos os serviços DNS expandidos para os sistemas Azure através de conectividade de site-para-site/multi inter-locais ligações ou privada como Azure ExpressRoute e no local Active Directory/OpenLDAP.

Para obter mais fundo nos detalhes de implementação do SAP no Azure, aconselhamos a ler capítulo [implementação conceitos de Cloud-Only de instâncias do SAP] [planeamento-guia-7] deste documento que explica algumas das construções de noções básicas do Azure e como estas devem ser utilizadas com as aplicações SAP no Azure.

### <a name="scenario-of-a-sap-landscape"></a>Cenário de um horizontal do SAP

O cenário de publicação em local pode ser aproximadamente descrito como nos gráficos abaixo:
 
![Conectividade de site para o Site entre no local e recursos do Azure][planning-guide-figure-2100]

O cenário mostrado acima descreve um cenário onde no local AD/OpenLDAP e DNS é expandido para Azure. No lado no local, um determinado intervalo de endereços IP está reservado por subscrição Azure. O intervalo de endereços IP forem atribuído a uma rede Virtual Azure no lado Azure.

#### <a name="security-considerations"></a>Considerações de segurança

O requisito mínimo é a utilização de protocolos de comunicação segura, tal como SSL/TLS acesso ao browser ou ligações de baseado em VPN para um acesso aos serviços do Azure sistema. Partem do princípio de é empresas alça da ligação VPN entre o respetivo rede de empresa e Azure muito diferente. Algumas empresas blankly poderão abrir todas as portas. Algumas outras empresas poderão pretender ser muito preciso nos quais são as portas que necessitam para abrir, etc. 

Na tabela abaixo SAP típica portas de comunicação estão listadas. Que mostra basicamente é suficiente para abrir a porta de gateway SAP.

| Serviço | Nome da porta | Exemplo `<nn`> = 01 | Intervalo predefinido (max mínimo) | Comentário |
|---------|-----------|-------------------|-------------------------|---------|
| Distribuidor | sapdp`<nn>` consulte * | 3201 | 3200 – 3299 | SAP distribuidor, utilizado pelo SAP interface gráfica para Windows e Java |
| Servidor de mensagens | sapms`<sid`> Ver * * | 3600 | sapms gratuito`<anySID`> | SID = ID do sistema do SAP |
| Gateway | sapgw`<nn`> Ver * | 3301 | libertar | Gateway SAP, utilizado para comunicação CPIC e RFC |
| Router do SAP | sapdp99 | 3299 | libertar | Apenas nomes de serviço CI (instância central) podem ser reatribuídos no /etc/services para um valor arbitrário após a instalação. |

*) nn = número de instância do SAP

*) sid = ID do sistema do SAP

Informações mais detalhadas sobre portas necessárias para diferentes SAP produtos ou serviços por produtos SAP podem ser encontrados aqui <http://scn.sap.com/docs/DOC-17124>. Com este documento deverá conseguir abrir portas dedicadas no dispositivo VPN necessário para produtos SAP específicos e cenários.

Medidas de outros segurança quando implementar VMs num cenário tal pode dever criar um [Grupo de segurança de rede] [ virtual-networks-nsg] para definir regras de acesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Trabalhar com diferentes séries de Máquina Virtual

Durante a últimas primeiros 12 meses a Microsoft adicionou vários tipos VM mais que diferem quer em número de vCPUs, memória ou mais importante no hardware está a ser alimentado. Nem todas essas VMs são suportadas com SAP (consulte o artigo suportado tipos de VM na nota SAP [1928533]). Alguns desses VMs ser executados em gerações de hardware do anfitrião diferente. Estes gerações hardware do anfitrião são introdução implementadas na granularidade de uma Azure-unidade de escala. Meios casos podem surgir onde os diferentes tamanhos VM optar por não não possível executar a unidade de escala mesmo. Um conjunto de disponibilidade é limitado a capacidade de unidades de escala com a base de hardware diferentes do intervalo.  Por exemplo Se pretender executar o DBMS em A5 A11 VMs e a camada de aplicação SAP no VMs série G, teria de ser forçada para implementar um único sistema SAP ou diferentes sistemas SAP dentro de diferentes conjuntos de disponibilidade.


#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimir uma impressora a rede local instância do SAP no Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impressão através de TCP/IP num cenário de publicação em local


Configurar impressoras de rede no local em TCP/IP com base numa VM Azure global é o mesmo, tal como a sua rede empresarial, partindo do princípio de que possui uma ligação de ExpressRoute estabelecida ou túnel VPN Site para Site. 

___

> ![Windows][Logo_Windows] Windows
>
> Para fazer isto:
> - Algumas impressoras de rede são fornecidos com um Assistente de configuração que torna mais fácil configurar a sua impressora numa VM Azure. Se nenhum software do assistente foi distribuído com a impressora a forma "manual" para configurar a impressora é criar uma nova porta de impressora TCP/IP.
> - Abra o painel de controlo -> dispositivos e impressoras -> Adicionar uma impressora 
> - Selecione adicionar uma impressora utilizando um endereço de TCP/IP ou o nome do anfitrião
> - Escreva o endereço IP da impressora
> - Porta de impressora 9100 padrão
> - Se for necessário instale manualmente o controlador da impressora adequado. 
> 
> ![Linux][Logo_Linux] Linux
>
> - como para seguimento basta Windows o procedimento padrão para instalar uma impressora de rede
> - Basta seguir os guias Linux públicos para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Chapéu vermelho](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.

___

 
![Impressão em rede][planning-guide-figure-2200]



##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora de anfitrião sobre SMB (impressora partilhada) num cenário de publicação em local

Baseados no sistema anfitrião impressoras não são rede compatível com o por predefinição. Mas uma impressora baseados no sistema anfitrião pode ser partilhada entre computadores numa rede, desde que a impressora estiver ligada a um powered no computador. Ligar a sua empresa Site para o Site ou ExpressRoute de rede e partilhar a sua impressora local. O protocolo SMB utiliza NetBIOS em vez de DNS como serviço de nomes. O nome do anfitrião NetBIOS pode ser diferente a partir do nome do anfitrião DNS. As maiúsculas/minúsculas padrão são que o nome do anfitrião NetBIOS e o nome de anfitrião DNS são idênticos. O domínio de DNS não faz sentido no espaço de nomes NetBIOS. Por conseguinte, o nome do anfitrião DNS completamente qualificado que consiste o nome do anfitrião DNS e o domínio DNS não deve ser utilizado no espaço de nomes NetBIOS.

A partilha de impressora está identificada por um nome exclusivo na rede:

* Nome do anfitrião do anfitrião do SMB (sempre seja necessário). 
* Nome da partilha (sempre seja necessária). 
* Nome do domínio se impressora partilhar não está no mesmo domínio como sistema SAP. 
* Para além disso, um nome de utilizador e uma palavra-passe poderão ser necessário para aceder à partilha de impressora.

Como:

___

> ![Windows][Logo_Windows] Windows
>
> Partilhe a sua impressora local.
> Na Azure VM abra o Explorador do Windows e escreva o nome da partilha da impressora.
> Assistente de instalação uma impressora irá guiá-lo durante o processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre como configurar impressoras de rede no Linux ou incluindo um capítulo respeito impressão no Linux. Funcionar da mesma forma numa VM de Linux Azure desde a VM faz parte de uma VPN:
>
> * SLES <_Share_or_Windows_Share https://en.opensuse.org/SDB:Printing_via_SMB_ (Samba)>
> * RHEL <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>

___


##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (reencaminhamento de chamadas impressora) 

No Azure a capacidade dos serviços de ambiente de trabalho remoto para fornecer aos utilizadores acesso aos seus dispositivos de impressora local numa sessão remota não está disponível.

___

> ![Windows][Logo_Windows] Windows
>
> Obter mais detalhes sobre a impressão com Windows podem ser encontradas aqui: <http://technet.microsoft.com/library/jj590748.aspx>.

___

 
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração do SAP sistemas Azure numa correção e o sistema de transporte (TMS) cruzada local

A alteração do SAP e o sistema de transporte (TMS) tem de ser configurado para exportar e importar pedido transporte nos sistemas de paisagem. Vamos partem do princípio de que as instâncias de desenvolvimento de um sistema SAP (Dev Center) estão localizadas no Azure, Considerando que os sistemas de produtivos (PRD) e qualidade (das perguntas e respostas) estão no local. Além disso, podemos partem do princípio de que existe um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurar o domínio de transporte
Configure o seu domínio de transporte do sistema designado o controlador de transporte do domínio, tal como descrito na [configuração do controlador de domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Um utilizador de sistema que TMSADM será criado e o destino de RFC necessário serão gerados. Pode verificar estas ligações RFC utilizando a transação SM59. Resolução do nome do anfitrião tem de estar ativada ao longo do seu domínio de transporte. 

Como:

* No nosso cenário podemos decidiu que o sistema QAS no local será o controlador de domínio etc. Chamar da transação stm. É apresentada a caixa de diálogo TMS. É apresentada uma caixa de diálogo Configurar o domínio de transporte. (Esta caixa de diálogo apenas aparece se ainda não tiver configurado um domínio de transporte.)
* Certifique-se de que o utilizador criado automaticamente TMSADM está autorizado (SM59 -> ABAP ligação -> TMSADM@E61.DOMAIN_E61 -> detalhes -> Utilities(M) -> autorização de teste). O ecrã inicial da transação stm deve mostrar a que este sistema SAP está agora a funcionar como o controlador do domínio de transporte como é mostrado aqui:
 
![Ecrã inicial da transação stm no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte

A sequência de incluindo um sistema SAP num domínio de transporte será apresentada da seguinte forma:

* No sistema Dev Center no Azure vá para o sistema de transporte (cliente 000) e da transação stm de chamadas. Selecione configuração de outros na caixa de diálogo e continue com incluir sistema de domínio. Especifique o controlador de domínio como anfitrião de destino ([Incluindo sistemas SAP no domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema agora está à espera de serão incluídas no domínio de transporte.
* Por motivos de segurança, em seguida, tem de voltar para o controlador de domínio para confirmar o seu pedido. Selecione descrição geral do sistema e aprovar do sistema em espera. Em seguida, confirme o pedido e a configuração serão distribuídos.

Este sistema SAP contém agora a informação necessária sobre todos os outros sistemas SAP domínio transporte. Ao mesmo tempo, os dados do endereço do novo sistema SAP são enviados para todos os outros sistemas SAP e o sistema SAP é introduzido no perfil de transporte do programa de controlo de transporte. Verifique se trabalhar RFCs e acesso ao directório de transporte do domínio.

Continue com a configuração do seu sistema de transporte como habitualmente, tal como descrito na documentação do [sistema de transporte e para alterar](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Como:

* Certifique-se a que sua stm no local está configurado corretamente.
* Certifique-se de que o nome do anfitrião do controlador de domínio de transporte pode ser resolvida pelo seu máquina virtual no visto Azure e vice-versa.
* Chamada transação stm -> configuração outras -> sistema incluir no domínio.
* Confirme a ligação no sistema TMS de no local.
* Configure transporte rotas, grupos e camadas de como costuma fazer.

No site para o site ligados cenários de publicação em local, a latência entre no local e Azure ainda pode ser substancial. Se vamos seguir a sequência de transporte objectos através de desenvolvimento e teste sistemas de produção pensar sobre como aplicar transportes ou pacotes diferentes sistemas de suporte, apercebeu-se de que, dependentes na localização do directório de transporte central, alguns dos sistemas vai encontrar uma latência alta leitura ou escrita dados no directório de transporte central. A situação é semelhante a configurações do SAP horizontal onde os diferentes sistemas são propagados através de centros de dados diferentes com substancial distância entre os centros de dados.

Para contornar essa latência e ter os sistemas de trabalhar rapidamente na leitura ou escrita para ou do diretório transporte, pode configurar duas stm transporte de domínios (um para no local e uma com os sistemas no Azure e ligar os domínios transporte. Verifique se existem esta documentação que explica os princípios atrás este conceito em TMS o SAP: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>. 

Como:

* Configurar um domínio de transporte em cada localização (no local e Azure) através da transação stm <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Ligar os domínios com uma ligação de domínio e confirme a ligação entre os dois domínios. 
  <http://help.SAP.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/Content.htm>
* Distribua a configuração para o sistema ligada.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego de RFC entre instâncias SAP localizada no Azure e no local (cruzada local)

Necessita de tráfego RFC entre os sistemas que são no local e no Azure trabalhar. Configurar uma transação da chamada de ligação SM59 num sistema de origem, onde precisa de definir uma ligação de RFC no sistema de destino. A configuração é semelhante à configuração do padrão de uma ligação de RFC.

Vamos partem do princípio de que o cenário de publicação em local, os VMs que são executar sistemas SAP que precisam de comunicar com os outros no mesmo domínio. Por conseguinte, a configuração de uma ligação de RFC entre os sistemas SAP não diferir os passos de configuração e entradas em cenários no local.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Aceder a fileshares 'local' a partir do SAP instâncias localizadas no Azure ou vice-versa

Instâncias SAP localizadas no Azure necessitam de aceder partilhas de ficheiros que estão dentro da empresa instalações. Além disso, instâncias SAP no local tem de aceder a partilhas de ficheiros que estão localizadas no Azure. Para ativar as partilhas de ficheiros, tem de configurar as permissões e as opções de partilha no sistema local. Certifique-se abrir as portas na ligação VPN ou ExpressRoute entre o Azure e o seu centro de dados.

## <a name="supportability"></a>Compatibilidade
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure monitorização solução para SAP
Para poder ativar a monitorização de sistemas SAP críticos missão no Azure SAP ferramentas SAPOSCOL ou agente de anfitrião do SAP obter dados de desativar o anfitrião do serviço de Máquina Virtual do Azure através de uma extensão de monitorização do Azure para SAP de monitorização. Uma vez que foram muito específicas para as aplicações de SAP exigências pela SAP, Microsoft decidiu não forma genérica implementar a funcionalidade necessária para Azure, mas deixá-la para clientes implementar as configurações de componentes de monitorização necessários e para os respetivos máquinas virtuais em execução no Azure. No entanto, gestão de implementação e ciclo de vida dos componentes monitorização será principalmente automatizada por Azure.

#### <a name="solution-design"></a>Estrutura de solução

A solução desenvolvida para ativar a monitorização de SAP baseia-se a arquitetura do agente de VM do Azure e framework extensão. A ideia do quadro Azure VM agente e extensão é permitir a instalação de software aplicações disponíveis na Galeria de Azure VM extensão dentro de uma VM. A ideia princípio atrás este conceito é permitir (em casos como a extensão de monitorização do Azure para SAP), a implementação de funcionalidades especiais para uma VM e a configuração deste tipo de software no momento da implementação. 

Desde de Fevereiro de 2014, o 'Azure VM Agent' que permite o processamento de extensões de VM Azure específicas dentro da VM é inserido para o Windows VMs por predefinição na criação de VM no Portal do Azure. Em caso de SUSE ou Red chapéu Linux a VM agente já faz parte da imagem Azure Marketplace. No caso de um seria carregar uma VM Linux nos locais para Azure o agente VM tem de ser instalado manualmente.


Blocos Modulares básicos da solução de monitorização no Azure para o seguinte aspeto SAP da seguinte forma:
 
![Componentes da extensão do Microsoft Azure][planning-guide-figure-2400]

Como é mostrado no diagrama bloco acima, uma parte da solução de monitorização para SAP está alojada na galeria do Azure extensão que é um repositório de replicadas globalmente é gerido pelo operações de Azure e Azure VM imagem. É a responsabilidade da equipa do SAP/MS mista trabalhar sobre a aplicação Azure do SAP para trabalhar com as operações do Azure para publicar novas versões da extensão de monitorização do Azure para SAP. Esta extensão Azure de monitorização para SAP irá utilizar a extensão do Microsoft Azure diagnósticos (WAD) ou Linux espertalhão (qualquer Azure diagnósticos) para obter as informações necessárias. 

Quando implementar uma nova VM do Windows, «Agente de VM Azure» é adicionado automaticamente para a VM. A função deste agente está coordenar o carregamento e configuração das extensões do Azure monitorização de sistemas de NetWeaver SAP. Para Linux VMs o agente de VM Azure já é parte da imagem Azure Marketplace SO.

No entanto, existe um passo que ainda tem de ser executada pelo cliente. Este é o pré iniciação à ativação e a configuração da coleção de desempenho. O processo relacionadas com a configuração é automatizado por um PowerShell script ou comando clip. Pode ser transferido o script do PowerShell no Microsoft Azure Script Center conforme descrito no [guia de implementação do] [guia de implementação].

A arquitetura geral da solução Azure monitorização para SAP tem a seguinte apresentação:
 
![Solução de monitorização para SAP NetWeaver do Azure][planning-guide-figure-2500]

**Para as instruções exata e para obter passos detalhados de utilizar estes cmdlets do PowerShell ou comando clip durante implementações, siga as instruções indicadas no [guia de implementação do] [guia de implementação].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração do Azure localizado instância do SAP para SAProuter

Instâncias SAP em execução no Azure precisam de ser acessíveis a partir do SAProuter também.
 
![Ligação de rede SAP Router][planning-guide-figure-2600]

Um SAProuter permite comunicação TCP/IP entre os sistemas de participantes se não existe IP ligação direta. Este procedimento fornece a vantagem que sem ligação ponto a ponto entre os parceiros de comunicação é necessária num nível de rede. O SAProuter está a escutar porta 3299 por predefinição.
Para ligar instâncias do SAP através de um SAProuter tem de dar o nome de anfitrião e cadeia SAProuter com qualquer tentativa de ligação.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver como Java

Até ao momento o foco do documento tiver sido SAP NetWeaver em geral ou empilhar a ABAP NetWeaver SAP. Nesta secção pequenas, são listadas considerações específicas para a pilha de SAP Java. Uma das aplicações do SAP NetWeaver Java exclusivamente com base mais importantes é o Portal de empresa do SAP. Outras SAP NetWeaver aplicações baseadas em como SAP PI e o Gestor de solução SAP utilizar SAP NetWeaver ABAP tanto pilhas Java. Por conseguinte, certamente existir uma necessidade de considerar aspectos específicos relacionados com a pilha SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>Portal de empresarial do SAP

A configuração de um Portal de SAP numa máquina de Virtual do Azure não diferir uma instalação local no se estiver a implementar em cenários de publicação em local. Uma vez que o DNS é executado por no local, podem ser feitas as definições da porta das instâncias individuais como configurado no local. As recomendações e as restrições descritos neste documento até ao momento aplicam-se para uma aplicação como o Portal do SAP Enterprise ou na pilha de SAP NetWeaver Java em geral. 

![SAP exposta Portal][planning-guide-figure-2700]

Um cenário de implementação especiais por alguns clientes é a exposição direta do Portal da empresa SAP à Internet, enquanto o anfitrião de máquina virtual está ligado à rede da empresa através de túnel VPN do site para o site ou ExpressRoute. Para um cenário, tem de Certifique-se de que as portas específicas estejam abertos e não bloqueados por grupo de segurança da firewall ou rede. A mesmo mecânica seria necessário para serem aplicadas quando o que pretende ligar a uma instância do SAP Java de no local num cenário de apenas na nuvem.

O portal inicial URI é http:`<Portalserver`>: 5XX00/irj onde a porta é constituída por 50000 sinal de adição (Systemnumber × 100). O sistema de URI de SAP portal predefinido 00 é `<dns name`>. `<azure region`>.Cloudapp.azure.com:PublicPort/irj. Para obter mais detalhes, consulte as um <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>. 
 
![Configuração de ponto final][planning-guide-figure-2800]

Se pretende personalizar o URL e/ou portas do seu Portal da empresa de SAP, verifique a documentação:

* [Alterar o URL do Portal](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL) 
* [Alterar números de porta predefinidos, números de porta de Portal](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers) 


## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Alto disponibilidade (HA) e falhas recuperação (DR) para o SAP NetWeaver em execução no máquinas virtuais do Azure
### <a name="definition-of-terminologies"></a>Definição de terminologias

A termos **elevada disponibilidade (HA)** geralmente está relacionada com a um conjunto de tecnologias minimiza as IT interrupções, fornecendo continuidade do negócio de serviços de TI através de tolerância, redundantes ou activação pós-falha protegidos componentes no interior do Centro de dados do **mesmo** . No nosso caso, dentro de uma região do Azure.

**Recuperação de falhas (DR)** também é filtragem de interrupção de serviços redução do IT e os respetivos recuperação mas através de dados **diferentes** centros, que estão normalmente localizados centenas de quilómetros fora do escritório. No nosso caso normalmente entre diferentes regiões do Azure dentro da mesma região geopolítica ou como estabelecida por si, como um cliente.

### <a name="overview-of-high-availability"></a>Descrição geral de elevada disponibilidade
Vamos pode separar o debate sobre SAP elevada disponibilidade no Azure em duas partes:

* **Disponibilidade elevada infraestrutura azure**, por exemplo, HA de cluster (VMs), rede, etc. de armazenamento e seus benefícios para aumentar a disponibilidade de aplicação do SAP.
* **Disponibilidade de elevada de aplicação do SAP**, por exemplo, componentes de software de HA do SAP:
    * Servidores de aplicações do SAP
    * Instância do SAP ASCS/SCS 
    * Servidor de BD

e como pode ser combinado com infraestrutura Azure HA.

SAP elevada disponibilidade no Azure tem algumas diferenças em comparação com elevada disponibilidade do SAP num ambiente no local físico ou virtual. A seguinte documentação do SAP descreve padrão SAP elevada disponibilidade configurações nos ambientes virtualizados no Windows: <http://scn.sap.com/docs/DOC-44415>. Não existe nenhuma integrada sapinst SAP-HA configuração para Linux como existir para Windows. Em relação à HA do SAP no local para Linux Saiba mais aqui: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Infraestrutura Azure elevada disponibilidade
Não existe nenhuma SLA único VM disponível em máquinas virtuais do Azure neste momento. Para obter uma ideia de como a disponibilidade de uma única VM poderá sensação de criar o produto da SLA Azure disponíveis diferentes: <https://azure.microsoft.com/support/legal/sla/>.

A base para o cálculo é 30 dias por mês, ou minutos 43200. Por conseguinte, tempo de inatividade da % de 0,05 corresponde ao 21,6 minutos. Como habitualmente, a disponibilidade de diferentes serviços irá multiplicar da seguinte forma:

(Serviço de disponibilidade #1/100) *(Serviço de disponibilidade #2/100)* (Serviço de disponibilidade #3/100) *...

Como:

(99.95/100) *(99,9/100)* (99,9/100) = 0.9975 ou uma disponibilidade geral de 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Disponibilidade de alta máquina virtual (VM)

Existem dois tipos de plataforma Azure eventos que podem afetar a disponibilidade de máquinas virtuais: planeado manutenção e manutenção não planeada.

* Eventos de manutenção planeada são periódicas feitas pela Microsoft para a plataforma do Azure subjacente para melhorar a fiabilidade, desempenho e segurança infraestrutura do plataforma que executam o seu máquinas virtuais no geral.
* Eventos de manutenção imprevistas ocorrem quando o hardware ou infraestrutura física subjacentes máquina virtual tiver havido uma falha de algum modo. Isto pode incluir outros falhas de nível de bastidor, falhas de disco local ou falhas de rede local. Quando uma falha na é detectada, a plataforma Azure migrar automaticamente máquina virtual do servidor físico danificado que aloja a sua máquina virtual para um servidor físico saudável. Tais eventos são raros, mas também podem fazer com que o máquina de virtual para reiniciar o computador.

Obter mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância de armazenamento Azure

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para se certificar de durabilidade e elevada disponibilidade, o SLA de armazenamento do Azure mesmo face a falhas de hardware breves da reunião

Uma vez que o armazenamento do Windows Azure é manter 3 imagens dos dados por predefinição, RAID5 ou RAID1 por vários discos Azure não são necessárias.

Podem encontrar mais detalhes neste artigo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/> 

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Recorrendo infraestrutura Azure VM reiniciar para alcançar "Maior disponibilidade" das aplicações do SAP

Se decidir não utilizar funcionalidades como o Windows Server activação pós-falha clusters (WSFC) ou um Linux equivalente (o um não é suportado ainda no Azure em combinação com um software de SAP último), reinicie o Azure VM é utilizado para proteger o sistema SAP um tempo de inatividade planeado e não da infraestrutura de servidor físico Azure e a plataforma de Azure subjacente geral. 
 
> [AZURE.NOTE] É importante no Office Accounting que principalmente reiniciar o Azure VM protege VMs e não a aplicações. Reinicie o VM não oferecer elevada disponibilidade para as aplicações SAP, mas este oferecer um determinado nível de disponibilidade de infraestrutura e, consequentemente, indiretamente "maior disponibilidade" dos sistemas SAP. Também não existe nenhuma SLA para o tempo irá demorar até para reiniciar uma VM após uma falha de sistema anfitrião planeado ou não planeado. Por conseguinte, este método de disponibilidade de alta não é adequado para críticos componentes de um sistema SAP como () SCS ou DBMS.

Outro elemento de infraestrutura importantes para elevada disponibilidade é armazenamento. Por exemplo Azure armazenamento SLA é disponibilidade do 99,9%. Se um implementa VMs todos os seus discos para uma conta de armazenamento de Azure única, potenciais armazenamento do Windows Azure a indisponibilidade causará a indisponibilidade de VMs todos os que são colocados nessa conta de armazenamento do Azure e executar dentro desses VMs também componentes SAP todas as.  

Em vez de colocar em todos os VMs para uma única conta de armazenamento de Azure, também pode utilizar o armazenamento dedicado contas para cada VM e, desta forma aumentar disponibilidade geral de VM e pela SAP, da aplicação através da utilização de várias contas de armazenamento do Azure independentes. 

Uma arquitetura de exemplo de um sistema de SAP NetWeaver que utiliza infraestrutura Azure HA poderia o seguinte aspeto:
 
![Infraestrutura Azure recorrendo HA para alcançar a disponibilidade de "mais elevada" de aplicação do SAP][planning-guide-figure-2900]

Para componentes SAP críticos podemos obtida a seguinte até ao momento:

* Disponibilidade elevada SAP de servidores de aplicação (AS)

Instâncias da aplicação de SAP server são redundantes componentes. Cada SAP tal como a instância é implementada no seu próprio VM, que está a ser executado numa diferente falhas do Azure e atualizar domínio (consulte capítulos [falhas domínios] [3.2.1 de planeamento-guia] e [Domains][planning-guide-3.2.2]) atualizar. Isto é assegurado utilizando Azure conjuntos de disponibilidade (consulte o artigo capítulo [Azure disponibilidade Sets][planning-guide-3.2.3]). Potencial indisponibilidade planeada ou não planeada de um Azure falhas ou atualizar domínio originará indisponibilidade de um número limitado de VMs com os respetivos como SAP instâncias. Cada SAP como instância é colocada na sua própria conta de armazenamento do Windows Azure – potencial indisponibilidade de uma conta de armazenamento do Azure causará a indisponibilidade da VM apenas uma com o seu como SAP instância. No entanto, tenha em atenção de que existe um limite de contas de armazenamento do Azure dentro de uma subscrição Azure. Para se certificar de início automático da instância () SCS após o reinício VM, certifique-se definir o parâmetro de início automático num () SCS instância iniciar perfil descrito capítulo [utilizando iniciar instâncias do SAP] [11.5 de planeamento-guia].
Leia também capítulo [elevada disponibilidade para os servidores de aplicações SAP] [planeamento-guia-11.4.1] para obter mais detalhes.

* _Mais elevados_ Disponibilidade da instância SCS SAP (A)
 
Aqui vamos utilizam os mesmos Azure VM reiniciar para proteger a VM com instalado instância SCS SAP (A). No caso de tempo de inatividade planeado ou não planeado do Azure servidores, VMs vai ser reiniciado noutro servidor disponíveis. Como mencionado anteriormente, reinicie o Azure VM principalmente protege VMs e não a aplicações, nesta instância de SCS de maiúsculas/minúsculas (A). Através de reiniciar o VM podemos irá atingir indiretamente "maior disponibilidade" da instância SCS SAP (A). Para garantir início automático da instância () SCS após o reinício VM, certifique-se definir o parâmetro de início automático num () SCS instância iniciar perfil descrito capítulo [utilizando iniciar instâncias do SAP] [11.5 de planeamento-guia]. Isto significa que o SCS (A) da instância como uma única ponto de falha (SPOF) a ser executada numa única VM será o factor determinante para a disponibilidade de toda paisagem SAP. 

* _Mais elevados_ Disponibilidade de servidor DBMS

Aqui, semelhante para o caso de utilização de instância SCS SAP (A), vamos utilizar Azure VM reiniciar para proteger a VM com software DBMS instalado e podemos atingir "uma maior disponibilidade" de software DBMS através de reiniciar o VM. DBMS a ser executada numa única VM também é um SPOF e é o factor determinante para a disponibilidade de toda paisagem SAP. 

### <a name="sap-application-high-availability-on-azure-iaas"></a>Disponibilidade de elevada aplicação SAP no Azure IaaS
Para alcançar completa SAP sistema elevada disponibilidade, precisamos de proteger todas as críticos SAP componentes do sistema, por exemplo, redundantes SAP servidores de aplicações, e componentes exclusivos (por exemplo, único ponto de falha) como instância do SAP (A) SCS e DBMS. 

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Elevada disponibilidade para servidores de aplicações do SAP
Para as instâncias de servidores/caixa de diálogo do SAP aplicação não é necessário refletir sobre uma solução de elevada disponibilidade específico. Disponibilidade elevada simplesmente é obtida por redundância e assim ter suficiente-las em máquinas virtuais do diferentes. Estes devem todas ser colocados no mesmo Azure disponibilidade conjunto para evitar que o VMs poderão ser atualizados ao mesmo tempo durante o tempo de inatividade da manutenção planeada. As funcionalidades básicas que cria no diferentes atualização e domínios falhas dentro de uma unidade de escala Azure já foram introduzida numa capítulo [atualizar domínios] [3.2.2 de planeamento-guia]. Conjuntos de disponibilidade Azure foram apresentados em capítulo [Azure disponibilidade conjuntos] [3.2.3 de planeamento-guia] deste documento. 

Não existe nenhuma número infinito de falhas e atualizar domínios que podem ser utilizados por um conjunto de disponibilidade de Azure dentro de uma unidade de escala Azure. Isto significa que a colocação de um número de VMs para um conjunto de disponibilidade mais cedo ou mais tarde no facto do que mais do que uma VM termina para cima na mesma actualização do domínio ou falhas

Alguns SAP server instâncias da aplicação no seus VMs dedicadas a implementar e partindo do princípio de que tem podemos 5 domínios atualizar, a imagem seguinte conclui no final. O número máximo real de domínios falhas e atualização dentro de um conjunto de disponibilidade poderá mudar no futuro:
 
![HA SAP servidores de aplicação no Azure][planning-guide-figure-3000]

Obter mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>


#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Elevada disponibilidade para a instância SCS SAP (A) no Windows

Cluster de activação pós-falha de servidor do Windows (WSFC) é uma solução utilizada com frequência para proteger a instância SCS SAP (A). -Lo também está integrado no sapinst no formulário de uma instalação"HA". Neste ponto no momento infraestrutura do Azure não é possível fornecer a funcionalidade para configurar o necessários Cluster de activação pós-falha do Windows Server da mesma forma como terminar, no local.

A partir de Janeiro de 2016 a plataforma de nuvem Azure a executar o sistema operativo Windows não fornece a possibilidade de utilizar um volume cluster partilhado num disco partilhado entre duas Azure VMs.

Uma solução válida é embora a utilização de software de terceiros 3 que fornece um volume partilhado por replicação de disco síncrono e transparente que pode ser integrada WSFC. Esta abordagem implica que apenas o nó do active cluster é possível aceder das cópias do disco num ponto no tempo. A partir de Janeiro de 2016 este HA configuração é suportada para proteger a instância SCS SAP (A) no Windows convidado SO no Azure VMs combinação com um software de terceiros 3 SIOS DataKeeper.

A solução SIOS DataKeeper fornece um recurso de cluster de disco partilhado a Windows activação pós-falha Clusters por ter:

* Um VHD Azure adicionais anexados a cada uma das máquinas virtuais (VMs) que estão numa configuração Cluster do Windows
* SIOS DataKeeper Cluster Edition em execução em ambos os nós VM
* Está com dificuldades SIOS DataKeeper Cluster Edition configurado de forma a que modo síncrono reflete o conteúdo do VHD adicional volume anexado a partir de origem VMs VHD adicionais anexados volume de destino VM.
* SIOS DataKeeper é abstracting volumes locais origem e destino e apresentá-los para Cluster de activação pós-falha do Windows como um único disco partilhado.
 
Pode encontrar todos os detalhes sobre como instalar um Cluster de activação pós-falha do Windows com SIOS Datakeeper e SAP no [clusters instância do SAP ASCS utilizando Cluster de activação pós-falha do Windows Server no Azure com SIOS DataKeeper] [ ha-guide-classic] técnico. 

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Elevada disponibilidade para a instância do SAP (A) SCS em Linux
 
Partir dez de 2015 também não existe nenhuma equivalente a disco partilhado WSFC para Linux VMs no Azure. Soluções alternativas utilizando o software de terceiros 3 como SIOS para Windows não são validadas ainda para executar SAP num Linux no Azure.



#### <a name="high-availability-for-the-sap-database-instance"></a>Elevada disponibilidade para a instância de base de dados do SAP
A SAP DBMS HA configuração típica é baseada duas DBMS VMs onde a funcionalidade de alta disponibilidade DBMS é utilizada para criar uma réplica dados da instância DBMS ativa para a segunda VM para uma instância DBMS passiva.

Disponibilidade de alta e falhas a funcionalidade de recuperação para DBMS em geral, bem como específicos DBMS são descritos no [DBMS guia de implementação do] [dbms guia].


#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Ponto a ponto elevada disponibilidade para o sistema SAP concluída

Aqui estão dois exemplos de uma concluída SAP NetWeaver HA arquitetura no Azure - um para Windows e outro para Linux.
Os conceitos conforme explicado abaixo poderão ter de ser um pouco comprometida quando implementar muitos sistemas SAP e o número de VMs implementado é excedem o limite máximo de contas de armazenamento por subscrição. Nestes casos, VHDs de VMs tem de ser combinados dentro de uma conta de armazenamento. Normalmente faria fazê-lo através da combinação de camada de aplicação VHDs do SAP VMs de diferentes sistemas SAP.  Recomendamos também combinadas VHDs diferentes de diferentes DBMS VMs de diferentes sistemas SAP uma conta de armazenamento do Azure. Portanto tendo os limites IOPS das contas do Azure armazenamento em consideração ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA no Windows

![SAP NetWeaver aplicação HA arquitetura com SQL Server em máquinas Azure IaaS][planning-guide-figure-3200]

As seguintes construções Azure são utilizadas para o sistema de SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e aplicação de patches do anfitrião:

* O sistema concluído é implementado no Azure (obrigatório - camada DBMS, (um) instância do SCS e a camada de aplicação completo precisa para executar na mesma localização).
* Executa o sistema concluído dentro de uma subscrição Azure (obrigatório).
* O sistema concluído executa dentro de uma Azure Virtual rede (obrigatório).
* É possível a separação de VMs de um sistema SAP para três conjuntos de disponibilidade mesmo com todos os VMs que pertencem à mesma rede Virtual.
* VMs todas as instâncias de DBMS de um sistema SAP em execução estão um conjunto de disponibilidade. Vamos partem do princípio de que existe mais do que uma VM executar instâncias DBMS por sistema desde nativa DBMS elevada disponibilidade funcionalidades são utilizadas, como AlwaysOn do SQL Server ou Oracle atento de dados.
* VMs todas as instâncias de DBMS em execução utilizam os seus próprios conta de armazenamento. Ficheiros de dados e de registo DBMS são replicados a partir de uma conta de armazenamento para outra conta de armazenamento utilizar funções de elevada disponibilidade DBMS sincronizar os dados. A indisponibilidade de uma conta de armazenamento irão provocar indisponibilidade de um nó de cluster de SQL Windows, mas não o serviço do SQL Server todo. 
* Todos os VMs executar (A) instância SCS de um sistema SAP estão um conjunto de disponibilidade. Parte de dentro desses VMs é configurar o Windows Server activação pós-falha Cluster (WSFC) para proteger (uma) instância SCS.
* VMs todas as instâncias (um) SCS em execução utilizam os seus próprios conta de armazenamento. (A) Ficheiros de SCS da instância e uma pasta de global SAP são replicadas a partir de uma conta de armazenamento para outra conta de armazenamento utilizando a replicação de SIOS DataKeeper. A indisponibilidade de uma conta de armazenamento irão provocar indisponibilidade de um (um) nó de cluster SCS Windows, mas não a totalidade (um) serviço SCS. 
* Todos os VMs que representa a camada de servidor do SAP aplicação está num terceiro disponibilidade definida.
* Todos os VMs executar os servidores de aplicações SAP utilizam os seus próprios conta de armazenamento. A indisponibilidade de uma conta de armazenamento irão provocar indisponibilidade de um servidor de aplicação do SAP, onde outros como SAP continuar a ser executado.

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA em Linux

A arquitetura para de SAP HA no Linux no Azure é basicamente o mesmo que para Windows, tal como descrito acima. Partir Jan de 2016 não existem duas restrições apesar:

* apenas o SAP Auxiliar 16 são atualmente suportadas no Linux no Azure sem qualquer funcionalidades de replicação Auxiliar. 
* não existe nenhuma solução SAP (A) SCS HA suportada ainda em Linux no Azure

Como consequência partir Janeiro de 2016 um sistema de SAP-Linux-Azure não é possível alcançar a disponibilidade do mesmo como um sistema do SAP-Windows-Azure devido à falta HA para o SCS (A) instância e a base de dados de SAP Auxiliar instância individual.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Utilizar início automático para instâncias do SAP

SAP oferecida a funcionalidade para iniciar instâncias do SAP imediatamente após o início do SO dentro da VM. Os passos exatos documentados no artigo de Base de dados de conhecimento do SAP [1909114] - como iniciar SAP instâncias automaticamente, utilizando o parâmetro iniciar. No entanto, SAP não é recomendar para utilizar a definição deixem porque não existe nenhum controlo pela ordem instância reinicia, partindo do princípio de mais do que uma VM tem afectado ou várias instâncias executou por VM. A iniciar partindo do princípio de um cenário Azure típico de uma instância de servidor de aplicação SAP numa VM e para maiúsculas/minúsculas uma única VM eventualmente introdução reiniciada, não é realmente crítica e pode ser ativado ao adicionar este parâmetro:

    Autostart = 1

Para o perfil de início da instância SAP ABAP e/ou Java.

> [AZURE.NOTE] 
> O parâmetro de início automático pode ter alguns downfalls também. Mais detalhadamente, o parâmetro accionadores o início de uma instância de Java ou SAP ABAP quando o serviço Windows/Linux relacionado da instância é iniciado. Certamente que é o caso quando os sistemas operativos arrancar. No entanto, é reiniciado dos serviços SAP também é uma coisa comuns para a funcionalidade de gestão de ciclo de vida de Software de SAP como soma ou outros atualiza ou actualiza. Estas funcionalidades não estão à espera de uma instância de ser reiniciado automaticamente sequer. Por conseguinte, o parâmetro iniciar deve ser desactivado antes de executar essas tarefas. O parâmetro iniciar também deve não ser utilizado para instâncias SAP, que estão agrupadas, como SCS/ASCS/CI.

Ver informações adicionais relativas ao iniciar para SAP instâncias aqui:

* [Início/fim SAP juntamente com o seu servidor Unix iniciar/parar](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciar e parar agentes de gestão de NetWeaver do SAP](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como ativar automática iniciar de HANA da base de dados](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)


### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP 3 camadas maiores
Aspetos de alta disponibilidade das configurações de SAP 3 camadas já tem abordados nas secções anteriores. Mas e quanto sistemas onde os requisitos do servidor DBMS são demasiado grandes para ter localizado no Azure, mas a camada de aplicação do SAP podem ser implementados no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização das configurações de SAP 3 camadas

Não é suportada para dividir a camada de aplicação próprio ou a aplicação e DBMS camada entre no local e Azure. Um sistema SAP é um dos completamente implementado no local ou no Azure. Não é suportada também ter alguns dos servidores aplicação executar no local e algumas outras pessoas no Azure. Que é o ponto de partida do debate. Recomendamos também são não suporte para que os componentes DBMS de um sistema SAP e a SAP server a camada de aplicação implementado em dois diferentes regiões do Azure. Por exemplo DBMS na camada de aplicação EUA oeste e pela SAP no Central (EUA). Razão para não suporte estas configurações é a sensibilidade latência da arquitetura do SAP NetWeaver.

No entanto, ao longo de ano passado Centro parceiros desenvolvidos cocriação localizações dos dados a regiões do Azure. Estas localizações cocriação são frequentemente muito proximidade aos dados Azure físicas centros de dentro de uma região Azure. A distância curta e ligação de activos na localização cocriação através de ExpressRoute para Azure podem resultar numa latência que é menor que 2ms. Nestes casos, para localizar a camada DBMS (incluindo armazenamento SAN/NAS) no como uma localização de cocriação e o SAP camada de aplicação no Azure é possível. Partir dez de 2015, não temos qualquer implementações desta forma. Mas diferentes clientes com implementações da aplicação que não sejam SAP estiver a utilizar o essas abordagens já. 

### <a name="offline-backup-of-sap-systems"></a>Sistemas de cópia de segurança do SAP offline

Depende da configuração de SAP escolhida (2 camada ou camadas 3) existem poderá ser uma necessidade de cópia de segurança. O conteúdo da VM propriamente dito sinal de adição de ter uma cópia de segurança da base de dados. O DBMS relacionado com cópias de segurança são esperadas de ser executadas com métodos de base de dados. Uma descrição detalhada para as bases de dados diferentes, pode encontrar [DBMS guia] [dbms guia]. Por outro lado, os dados SAP podem ser cópia de segurança de uma forma offline (incluindo o conteúdo da base de dados), tal como descrito nesta secção ou online, tal como descrito na secção seguinte.

A cópia de segurança offline que mostra basicamente iria necessitar de um encerramento da VM através do Portal do Azure e uma cópia da base disco VM juntamente com todas as anexado VHDs para a VM. Isto preservar a um ponto na imagem de tempo da VM e o seu disco associada. Recomenda-se para copiar as 'cópias de segurança' para uma conta de armazenamento do Azure diferente. Por conseguinte, o procedimento descrito no capítulo [copiar discos entre contas de armazenamento do Azure] [planeamento-guia-5.4.2] deste documento seria aplicável.
Para além do encerramento utilizando o Portal do Azure um pode também fazê-lo através do Powershell ou clip conforme é aqui descrito: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Um restauro desse Estado seria consistem eliminar a base VM, bem como os discos originais da base VM e instalados VHDs, copiando novamente os VHDs guardados para a conta de armazenamento original e, em seguida, Reimplementar o sistema.
Este artigo mostra um exemplo de como o script deste processo no Powershell: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se instalar uma nova licença SAP, uma vez que restoing uma cópia de segurança VM conforme descrito acima cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Cópia de segurança online de um sistema SAP

Cópia de segurança do DBMS é executada com métodos específicos DBMS conforme descrito no [DBMS guia] [dbms guia]. 

Outros VMs dentro do sistema do SAP podem ser cópias de segurança utilizando a funcionalidade de cópia de segurança do Azure Máquina Virtual. Cópia de segurança de Máquina Virtual Azure tem introduzida no início 2015 e entretanto é um método padrão uma VM concluída no Azure uma cópia de segurança. Cópia de segurança do Azure armazena as cópias de segurança no Azure e permite uma operação de restauro de uma VM novamente. 

> [AZURE.NOTE] 
> Partir dez de 2015 utilizando VM cópia de segurança não manter o ID exclusivo da VM que é utilizado para SAP licenciamento. Isto significa que um restaurar a partir de uma cópia de segurança VM requer a instalação de uma nova chave de licença do SAP tal como a VM restaurada é considerado como uma VM nova e não substituição antigo aquele que foi guardado. Partir Jan de 2016 cópia de segurança do Azure VM não suporta a VMs que são implementadas com o Gestor de Resourc Azure ainda.

> ![Windows][Logo_Windows] Windows
>
> Teoricamente VMs executar bases de dados podem ser cópia de segurança de uma forma consistente também se os sistemas DBMS suporta o Windows VSS (serviço de cópia sombra <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> ) como, por exemplo, SQL Server faz.
> No entanto, tenha em atenção de que baseada no Azure VM as cópias de segurança no momento restaura das bases de dados não são possíveis. Por conseguinte, a recomendação é para efetuar cópias de segurança das bases de dados com a funcionalidade de DBMS em vez de depender de cópia de segurança do Azure VM
>
> Para se familiarizar com cópia de segurança do Azure Máquina Virtual comece aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>.
>
> Outras possibilidades estão a utilizar uma combinação do Microsoft dados proteção Manager instalada num Azure VM e uma cópia de segurança do Azure a cópia de segurança/restauro bases de dados. Obter mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>.  


> ![Linux][Logo_Linux] Linux
> 
> Não existe nenhuma equivalente ao Windows VSS no Linux. Por isso apenas as cópias de segurança ficheiro consistentes são possíveis, mas não aplicação consistentes cópias de segurança. A cópia de segurança do SAP DBMS deve ser feita utilizando a funcionalidade DBMS. O sistema de ficheiros que inclui o SAP relacionados com dados podem ser guardados, por exemplo, utilizando alcatrão, tal como descrito aqui: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>


### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como DR site para paisagens SAP de produção

Desde 2014 seg. texto, o extensões de vários componentes à volta Hyper-V, centro do sistema e Azure Active a utilização do Azure conforme site DR VMs em execução no local com base nos Hyper-V. 

Um blogue que explicam detalhadamente como implementar esta solução é documentado aqui: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>Resumo
Os pontos chave do elevada disponibilidade para sistemas SAP no Azure são:

* Neste ponto específico, o ponto de falha único SAP não pode ser protegido exatamente da mesma forma como pode ser feito implementações no local. O motivo é que ainda não podem ser criados clusters no Azure sem a utilização do software de terceiros 3º o disco partilhado.
* A camada DBMS terá de utilizar a funcionalidade DBMS que não dependem de tecnologia de cluster de disco partilhado. Detalhes encontram-se documentados no [DBMS guia] [dbms guia].
* Para minimizar o impacto de problemas dentro Domains falhas na manutenção infraestrutura ou anfitrião Azure, deve utilizar conjuntos de disponibilidade do Azure:
    * Recomenda-se de ter um conjunto de disponibilidade para a camada de aplicação do SAP.
    * Recomenda-se de ter um conjunto de disponibilidade separada para a camada SAP DBMS.
    * Não é recomendado para aplicar a mesma disponibilidade configurar para VMs de diferentes sistemas SAP.
* Para fins de cópia de segurança da camada SAP DBMS, verifique o [dbms guia] [DBMS guia].
* Criar cópias de segurança de instâncias de caixa de diálogo do SAP sentido pequeno uma vez que é normalmente mais rápido se implementar instâncias de caixa de diálogo simples.
* Fazer cópia de segurança a VM que contém o directório global do sistema SAP e com o mesmo todos os perfis de instâncias diferentes, fazer sentido e deve ser realizada com cópia de segurança do Windows ou, por exemplo, alcatrão no Linux. Uma vez que existem diferenças entre o Windows Server 2008 (R2) e Windows Server 2012 (R2), que tornam mais fácil de utilizar o Windows Server mais recente de cópia de segurança liberta, recomendamos que para executar o Windows Server 2012 (R2) como o sistema operativo Windows convidado. 
