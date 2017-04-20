<properties
   pageTitle="SAP NetWeaver em Linux máquinas virtuais (VMs) – guia de implementação do DBMS | Microsoft Azure"
   description="SAP NetWeaver em Linux máquinas virtuais (VMs) – guia de implementação do DBMS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-azure-virtual-machines-vms--dbms-deployment-guide"></a>SAP NetWeaver em máquinas virtuais Azure (VMs) – guia de implementação do DBMS

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

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (SAP NetWeaver em Linux máquinas virtuais (VMs) – guia de implementação do DBMS) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (colocação em cache para VMs e VHDs) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (armazenamento do Windows Azure) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (estrutura de uma implementação RDBMS) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (elevada disponibilidade e recuperação de falhas com Azure VMs) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e lançamentos anteriores) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (utilizando uma imagens do SQL Server fora do Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (geral do SQL Server para SAP no Azure resumo) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (especificidades para SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configuração de armazenamento) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (fazer cópia de segurança e restaurar) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (considerações de desempenho para cópia de segurança e restauro) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (outro) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (SAP NetWeaver em Linux máquinas virtuais (VMs) – guia de implementação) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP recursos) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (implementar uma VM com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (cenário 1: implementar uma VM terminar o Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (cenário 2: implementar uma VM com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 ( Cenário 3: Mover uma VM no local utilizando um VHD de Azure não generalized com SAP) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (implementação cenários de VMs para SAP no Microsoft Azure) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlets de implementação do Azure PowerShell) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (transferir e pela SAP, importar relevantes os cmdlets do PowerShell) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (participar VM para o domínio no local - apenas para o Windows) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [4.4 de guia de implementação]: virtual-Machines-Linux-SAP-Deployment-Guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (transferir, instalar e ativar o Azure VM agente) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (configurar Azure avançada monitorização extensão para SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (verificação de preparação para Azure as funcionalidades de monitorização para SAP) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (verificação de integridade para Azure Monitorização de configuração de infraestrutura) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (SAP NetWeaver em Linux máquinas virtuais (VMs) – planeamento e guia de implementação do) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidade (HA) e falhas recuperação (DR) para o SAP NetWeaver em execução no máquinas virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (elevada disponibilidade para os servidores de aplicações SAP) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (utilizando iniciar instâncias do SAP) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md# 1625df66-4CC6-4d60-9202-de8a0b77f803 (apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regiões) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (falhas de domínios) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (atualizar domínios) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088- F9BE - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (do Microsoft Azure Máquina Virtual conceito) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (mover uma VM nos locais para o Azure com um disco não generalized) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (implementar uma VM com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparação para mover uma VM nos locais para o Azure com um disco não generalized) [ Planning-Guide-5.2.2]:virtual-Machines-Linux-SAP-Planning-Guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparação para implementar uma VM com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (preparar VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferença entre um Azure disco e imagem do Azure) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (carregar um VHD nos locais para Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiar discos entre contas de armazenamento do Azure) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md# 4efec401-91e0-40c0-8e64-f2dceadff646 (estrutura VM/VHD para implementações do SAP) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montagem definição automática para discos ligados) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (única VM com SAP NetWeaver demonstração/formação cenário) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implementação conceitos de Cloud-Only de instâncias do SAP) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitorização solução para SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

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
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

Este guia é parte da documentação em execução e implementar o software SAP no Microsoft Azure. Antes de a ler neste guia, leia [planeamento e guia de implementação do] [-guia de planeamento]. Este documento abrange a implementação de vários sistemas de gestão de base de dados relacionais (RDBMS) e produtos relacionados em conjunto com SAP no Microsoft máquinas virtuais do Azure (VMs) utilizando a infraestrutura do Azure como capacidades de serviço (IaaS).

Papel completa a documentação de instalação do SAP e SAP notas que representam os recursos principais para implementações do software SAP e instalações em determinadas plataformas

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="general-considerations"></a>Considerações gerais
Neste capítulo considerações da execução do SAP relacionado com sistemas DBMS no Azure VMs são introduzidos. Existem alguns referências para sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são processados dentro deste documento depois deste capítulo.

### <a name="definitions-upfront"></a>Definições de upfront
Em todo o documento utilizamos os termos seguintes:

* IaaS: Infraestrutura como um serviço.
* PaaS: Plataforma como um serviço.
* SaaS: Software como um serviço.
* Componente do SAP: uma SAP aplicação individual, como ECC, p/b, Gestor de solução ou EP.  Componentes SAP podem ser baseadas no tecnologias ABAP ou Java tradicionais ou uma aplicação que não sejam com base NetWeaver como objetos de negócio.
* Ambiente do SAP: um ou mais componentes SAP agrupados de forma lógica execute uma função de negócio como desenvolvimento, QAS, formação, DR ou de produção.
* SAP horizontal: Isto refere-se para os ativos SAP inteiros de um cliente horizontal IT. Paisagem SAP inclui todas produção e não produção ambientes.
* Sistema SAP: A combinação de camada DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento do SAP ERP, p/b do SAP teste sistema, sistema de produção SAP CRM, etc. Em implementações Azure-não é suportada para dividir estas duas camadas entre no local e Azure. Isto significa que um sistema SAP é seja implementado no local ou for implementado no Azure. No entanto, pode implementar os diferentes sistemas de um horizontal SAP no Azure ou no local. Por exemplo, pode implementar o desenvolvimento de SAP CRM e sistemas de teste no Azure, mas o SAP CRM produção sistema no local.
* Implementação apenas na nuvem: uma implementação onde a subscrição Azure não está ligada através de um site para o site ou ligação ExpressRoute para a infraestrutura de rede no local. Em comum documentação Azure estes tipos de implementações são também descritos como 'Só de nuvem' híbridas. Máquinas virtuais implementadas com este método são acedidas através da Internet e públicos na Internet os pontos finais atribuídos a VMs no Azure. No local do Active Directory (AD) e DNS não é expandido para Azure nestes tipos de implementações. Por conseguinte, as VMs não fazem parte do Active Directory no local. Nota: Apenas na nuvem implementações neste documento são definidas como concluídas paisagens SAP que estiver a executar exclusivamente no Azure sem extensão do Active Directory ou resolução do nome no local para o pública na nuvem. Configurações apenas na nuvem não são suportadas para sistemas SAP de produção ou configurações onde SAP stm ou outros recursos no local necessite de ser utilizada entre os sistemas SAP alojados no Azure e recursos que residem no local.
* Publicação em local: Descreve um cenário em que VMs são implementadas para uma subscrição do Azure que tenha o site para o site, múltiplos sites ou ExpressRoute conectividade entre o datacenter(s) no local e o Azure. Documentação em comum Azure, estes tipos de implementações também são descritos como cenários de publicação em local. O motivo para a ligação é alargar domínios no local no local do Active Directory e no local DNS para o Azure. Paisagem no local é expandida para os ativos Azure da subscrição. Ter esta extensão, os VMs podem fazer parte do domínio no local. Utilizadores do domínio do domínio no local podem aceder aos servidores e podem executar serviços nesses VMs (como DBMS serviços). Resolução de comunicação e o nome entre VMs implementado no local e é possível VMs implementados no Azure. Recomendamos que esta opção para ser o cenário mais comum para implementar activos SAP no Azure. Consulte o artigo [Este] [ vpn-gateway-cross-premises-options] artigo e [Este] [ vpn-gateway-site-to-site-create] para obter mais informações.

> [AZURE.NOTE] Implementações cruzada local do sistemas SAP onde máquinas virtuais do Azure com sistemas SAP estão membros de um domínio no local são suportadas para sistemas SAP de produção. Configurações de publicação em local são suportadas para implementar peças ou concluir paisagens SAP para Azure. Mesmo a execução da paisagem SAP concluída no Azure requer tendo essas VMs a ser parte do domínio no local e anúncios. Em versões anteriores da documentação falámos sobre cenários de híbrido-IT, onde o termo 'Híbrido' com raiz no facto de que existe uma ligação de publicação em local entre no local e Azure. Neste caso 'Híbrido' também significa que o VMs no Azure fazem parte do Active Directory no local.

Algumas documentação da Microsoft descreve cenários de publicação em local de forma um pouco diferente, especialmente para configurações DBMS HA. No caso do SAP documentos, a publicação em local cenário basta destila para baixo até ter uma site para o site, ou privada (ExpressRoute) conectividade de e para o facto de que a paisagem SAP é distribuída entre no local e Azure relacionados.

### <a name="resources"></a>Recursos
Os seguintes guias estão disponíveis para o tópico de implementações do SAP no Azure:

* [SAP NetWeaver no Azure máquinas virtuais (VMs) – planeamento e guia de implementação do] [-guia de planeamento]
* [SAP NetWeaver em máquinas virtuais Azure (VMs) – guia de implementação] [guia de implementação]
* [SAP NetWeaver em máquinas virtuais Azure (VMs) – guia de implementação do DBMS (este documento)] [dbms guia]

As seguintes notas SAP estão relacionados com o tópico da SAP no Azure:

| Número da nota   | Título
|------------|--------
| [1928533] | As aplicações SAP no Azure: tipos de produtos suportados e Azure VM
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte
| [1999351] | Resolução de problemas avançada Azure de monitorização para SAP
| [2178632] | Tecla de métricas de monitorização para SAP no Microsoft Azure
| [1409604] | Virtualização no Windows: as funcionalidades de monitorização
| [2191498] | SAP no Linux com Azure: as funcionalidades de monitorização
| [2039619] | As aplicações SAP no Microsoft Azure utilizando a base de dados Oracle: produtos suportados e versões
| [2233094] | DB6: As aplicações SAP no Azure utilizando IBM DB2 para Linux, UNIX e Windows - informações adicionais
| [2243692] | Linux no Microsoft Azure (IaaS) VM: problemas de licença do SAP
| [1984787] | SUSE LINUX Enterprise Server 12: Notas de instalação
| [2002167] | Chapéu vermelho Enterprise Linux 7. x: instalação e atualização

Leia também [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas de SAP para Linux.

Que deve ter conhecimento prático sobre a arquitetura do Microsoft Azure e como máquinas virtuais do Microsoft Azure são implementados e operado. Pode encontrar mais informações aqui <https://azure.microsoft.com/documentation/>
 
> [AZURE.NOTE] Vamos **se a discutir plataforma do Microsoft Azure como um ofertas de serviço (PaaS) da plataforma Microsoft Azure** . Este papel é sobre a execução de um sistema de gestão de base de dados (DBMS) no Microsoft máquinas virtuais do Azure (IaaS) tal como seria executado DBMS no seu ambiente no local. Capacidades de base de dados e funcionalidades entre estas duas ofertas são muito diferentes e não devem ser misturadas com os outros. Consulte também: <https://azure.microsoft.com/services/sql-database/>

Uma vez que estamos são tratar IaaS, em geral a instalação do Windows, Linux e DBMS e configuração são, essencialmente, igual a qualquer máquina virtual ou máquina de metal simples, terá de instalar no local. No entanto, existem algumas arquitetura e o sistema de gestão decisões de implementação que serão diferentes quando recorrendo IaaS. É o objetivo deste documento explicar a arquitetura específicos e as diferenças de gestão de sistema que tem de estar preparado para quando utilizar IaaS.

Em geral, as áreas gerais de diferença deste documento irá abordar são:

* Planear o esquema VM/VHD inicial de sistemas SAP para se certificar de que tem os dados adequados esquema de ficheiros e possível alcançar suficiente IOPS para sua carga de trabalho.
* Considerações de funcionamento em rede ao utilizar IaaS.
* Funcionalidades de base de dados específica para utilizar para otimizar o esquema da base de dados.
* Considerações de cópia de segurança e restauro no IaaS.
* Recorrendo diferentes tipos de imagens para implementação.
* Disponibilidade de alta no Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de uma implementação RDBMS
Ordem siga este capítulo, é necessário compreender o que foi apresentado [neste] capítulo [3 de guia de implementação] [implementação do guia de] [guia de implementação]. Dados de conhecimento sobre a série de VM diferente e as respetivas diferenças e diferenças do Azure e não padrão Premium armazenamento devem ser compreendidos e conhecidos antes de ler este capítulo.

Até de Março de 2015, VHDs Azure que contêm um sistema operativo foram limitados a 127 GB de tamanho. Esta limitação tem levantada de Março de 2015 (para obter mais informações verificação <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/> ). A partir desse local no VHDs que contém o sistema operativo pode ter o mesmo tamanho que quaisquer outros VHD. No entanto, ainda estamos prefere uma estrutura de implementação onde o sistema operativo, DBMS e eventual binários do SAP estão separados a partir dos ficheiros de base de dados. Por conseguinte, podemos esperar sistemas SAP em execução no máquinas virtuais do Azure terão a base VM (ou VHD) instalado com o sistema operativo, base de dados gestão sistema executáveis e executáveis SAP. Os ficheiros de dados e de registo DBMS serão armazenados no armazenamento de Azure (padrão ou Premium armazenamento) em ficheiros VHD separados e anexados como discos lógicos para a imagem original do sistema operativo Azure VM. 

Depende tirar partido da Azure padrão ou armazenamento de prémio (por exemplo, ao utilizar a série de DS ou série s VMs) existem são outras quotas no Azure que se encontram-se documentados [aqui][virtual-machines-sizes]. Quando planear a sua VHDs Azure, terá de localizar o melhor equilíbrio das quotas para as seguintes opções:

* O número de ficheiros de dados.
* O número de VHDs que contêm os ficheiros.
* As quotas IOPS de um único VHD.
* O débito de dados por VHD.
* O número de adicional VHDs possível por tamanho da memória virtual.
* O débito de armazenamento geral pode fornecer uma VM.
 
Azure irá impor uma quota IOPS por unidade VHD. Estas quotas são diferentes para VHDs alojados no armazenamento padrão do Azure e armazenamento de Premium. Também serão muito diferentes entre os dois tipos de armazenamento com armazenamento Premium faz fatores melhor latências de e/s latências e/s. Cada um dos diferentes tipos VM têm um número limitado de VHDs conseguir anexar. Restrição outra é que apenas determinados tipos VM podem tirar partido armazenamento do Windows Azure Premium. Isto significa que a decisão para um determinado tipo VM poderá não apenas ser condicionada pelos requisitos de memória e CPU, mas também por IOPS, requisitos de débito latência e disco normalmente dimensionados com o número de VHDs ou o tipo de discos de armazenamento de Premium. Especialmente com armazenamento Premium o tamanho de um VHD poderá de ser ditado também pelo número de IOPS e débito que tem de ser realizado pelos cada VHD.

O facto da taxa IOPS global, o número de VHDs instalados e o tamanho da VM estão todas associados juntas, pode causar uma configuração Azure de um sistema SAP para ser diferente da sua implementação no local. Os limites IOPS por LUN são normalmente configuráveis no implementações no local. Considerando que com armazenamento do Windows Azure esses limites são fixo ou vistos Premium armazenamento depende do tipo de disco. Por isso, com implementações no local, podemos ver configurações de cliente de servidores de base de dados que estiver a utilizar vários volumes diferentes para executáveis especiais como SAP e o DBMS ou volumes especiais para bases de dados temporários ou espaços de tabela. Quando um sistema de no local é movido para Azure-pode conduzir a um desperdício de largura de banda IOPS potencial por desperdício um VHD para executáveis ou bases de dados que não efetua qualquer ou não muitas IOPS. Por conseguinte, no Azure VMs Recomendamos que os executáveis DBMS e pela SAP, ser instalado no disco SO, se possível.

A colocação de ficheiros de base de dados e ficheiros de registo e o tipo de armazenamento do Windows Azure utilizado, deverá ser definido por IOPS, latência e requisitos de débito. Para poder tem suficiente IOPS para o registo de transações, poderá ser forçada para tirar partido de vários VHDs para o ficheiro de registo da transação ou utilizar um disco Premium armazenamento maior. Nesse caso um simplesmente construir um software de RAID (por exemplo, Windows armazenamento agrupamento para Windows ou MDADM e LVM (Gestor de Volume lógico) para Linux) com os VHDs que irão conter o registo de transações.

___

> ![Windows][Logo_Windows] Windows
>
> Unidade D:\ numa VM Azure é uma unidade que não sejam persistentes que é suportada por alguns discos locais no nó cluster Azure. Porque é que não sejam persistentes, isto significa que as alterações efetuadas ao conteúdo na unidade de D:\ são perdidos quando a VM for reiniciada. "Quaisquer alterações", podemos dizer ficheiros guardados, directórios criados, aplicações instaladas, etc.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VMs montagem automaticamente uma unidade de /mnt/resource que é uma unidade que não sejam persistentes cópia por discos locais no nó cluster Azure. Porque é que não sejam persistentes, isto significa que as alterações efetuadas ao conteúdo no /mnt/resource são perdidos quando a VM é reiniciada. Quaisquer alterações, podemos dizer ficheiros guardados, directórios criados, aplicações instaladas, etc.

___

Depende da série de VM Azure, os discos locais no nó cluster mostram o desempenho diferentes que pode ser categorizado como:

* A0 A7: Desempenho muito limitado. Não podem ser utilizadas por nada para além do ficheiro de página do windows
* A8 A11: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.
* Série de D: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.
* Série de DS: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.
* Série de G: Características de muito bom desempenho com algumas IOPS dez mil e > débito 1GB/seg.
* Série de s: Características muito bom desempenho com algumas IOPS dez mil e > débito de 1GB/seg.

Declarações acima estão a aplicar aos tipos de VM que tenham certificação com SAP. A série de VM com excelente IOPS e débito elegíveis para aproveitar por algumas funcionalidades DBMS, como o tempdb ou o espaço temporário de tabela.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Colocação em cache para VMs e VHDs
Quando criamos esses discos/VHDs através do portal do ou quando podemos montagem VHDs carregados para VMs, podemos pode escolher se o tráfego de e/s entre a VM e essas VHDs localizados no Azure armazenamento são armazenadas em cache. Azure padrão e armazenamento de Premium utilizar duas tecnologias diferentes para este tipo de cache. Em ambas as casos a cache propriamente dito seria ser disco de segurança nas unidades mesmo utilizadas pelo disco temporário (D:\ no Windows) ou /mnt/resource no Linux da VM.
 
Para o armazenamento padrão do Azure os tipos de cache possíveis são:

* Sem colocação em cache
* Leia a colocação em cache
* Ler e escrever colocação em cache

Para obter consistente e determinista desempenho, deverá definir a colocação em cache no armazenamento do Windows Azure padrão para todos os VHDs que contém **DBMS relacionados com ficheiros de dados, ficheiros de registo e espaço de tabela para 'NONE'**. A colocação em cache da VM pode permanecem com as predefinições.

Para o armazenamento do Azure Premium existem as seguintes opções de colocação em cache:

* Sem colocação em cache
* Leia a colocação em cache

Recomendação de armazenamento do Windows Azure Premium é tirar partido de **ler a colocação em cache para ficheiros de dados** da base de dados SAP e optar por **não colocação em cache para VHD(s) dos ficheiros de registo**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Software RAID
Tal como já indicado acima, é necessário calcular o número de IOPS necessária para os ficheiros de base de dados através do número de VHDs pode configurar o saldo e escreva os máximos IOPS irá fornecer uma VM Azure por disco VHD ou armazenamento Premium. A forma mais fácil lidar com a carga IOPS sobre VHDs é construir um software de RAID sobre os VHDs diferentes. Em seguida, coloque um número de ficheiros de dados de SAP DBMS no LUNS gravados terminar o software RAID. Dependentes sobre os requisitos de que poderá considerar a utilização de armazenamento de Premium também desde duas das três diferentes Premium armazenamento discos fornecem a quota IOPS mais elevado que VHDs com base em armazenamento padrão. Para além de significativa latência e/s melhor fornecida pelo armazenamento do Windows Azure Premium. 

Mesmo aplica-se para o registo de transações dos diferentes sistemas DBMS. Com muitas-las apenas adicionar mais Tlog ficheiros não ajudar a vez que os sistemas DBMS escrever numa só dos ficheiros, uma vez só. Se forem necessárias taxas IOPS mais elevadas que um único padrão com base de armazenamento VHD pode entregar, pode faixas através de vários VHDs armazenamento padrão ou pode utilizar um tipo de disco de armazenamento de Premium maior que, para além de taxas IOPS mais elevadas fornece também fatores latência inferior para a escrita/s para o registo de transações.
 
Situações com experiência em implementações Azure que iria preferir utilizar um software RAID são:

* Registo de transações do registo/Refazer requerem IOPS mais do que Azure fornece para um único VHD. Tal como mencionado acima Isto pode ser resolvido através da criação de um LUN através de vários VHDs utilizando um software de RAID.
* Irregular e/s carga de trabalho distribuição sobre os ficheiros de dados diferente da base de dados SAP. Nestes casos um pode ocorrer atingir preferir frequentemente a quota de um ficheiro de dados. Considerando que outros ficheiros de dados não são mesmo obtém perto a quota do IOPS de um único VHD. Nestes casos a solução mais fácil é criar uma LUN através de vários VHDs utilizando um software de RAID. 
* Não sabe o que a carga de trabalho e/s exata por ficheiro de dados é e apenas aproximadamente saber o que é a carga de trabalho IOPS geral contra DBMS. É mais fácil de fazer criar uma LUN com a ajuda de um software RAID. A soma de quotas de vários VHDs atrás este LUN, em seguida, deve cumprir a taxa IOPS conhecida.

___

> ![Windows][Logo_Windows] Windows
>
> A utilização da Windows Server 2012 ou superior espaços de armazenamento é preferível uma vez que é mais eficiente do que Windows repartição de versões anteriores do Windows. Tenha em atenção que poderá ter de criar a agrupamentos de armazenamento do Windows e espaços de armazenamento por comandos do PowerShell quando utilizar o Windows Server 2012 como sistema operativo. Os comandos do PowerShell podem ser encontrados aqui <https://technet.microsoft.com/library/jj851254.aspx>

> 
> ![Linux][Logo_Linux] Linux
>
> Só MDADM LVM (Gestor de Volume lógico) são suportadas e para construir um software RAID no Linux. Para obter mais informações, leia os seguintes artigos:
>
> * [Configurar o Software RAID no Linux] [ virtual-machines-linux-configure-raid] (para MDADM)
> * [Configurar o LVM numa VM Linux no Azure][virtual-machines-linux-configure-lvm]


___

Considerações para tirar partido da série VM podem trabalhar com o armazenamento do Windows Azure Premium normalmente são:

* Pedidos de latências e/s que estão perto o que fazer a dispositivos SAN/NAS.
* Pedido para a latência de e/s melhor fatores que podem fornecer armazenamento padrão do Windows Azure.
* Superior IOPS por VM que o que pode ser realizado com vários VHDs armazenamento padrão relativamente a um determinado tipo VM.

Uma vez que o armazenamento do Windows Azure subjacente replica cada VHD para pelo menos três nós de armazenamento, simple RAID 0 repartição pode ser utilizada. Não é necessário para implementar RAID5 ou RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Armazenamento do Microsoft Azure
Armazenamento do Windows Azure irá guardar a base VM (com o SO) e VHDs ou BLOBs para nós de armazenamento em separado, pelo menos, 3. Ao criar uma conta de armazenamento, existe uma opção de proteção conforme mostrado aqui:

![Replicação de geo ativada para a conta de armazenamento do Windows Azure][dbms-guide-figure-100]

Azure armazenamento Local a replicação (localmente redundantes) fornece níveis de proteção contra a perda de dados devido a falha de infraestrutura alguns clientes podem perder de forma alguma implementar. Como é mostrado acima existem 4 diferentes opções com uma quinta a ser uma variação de um dos três primeiros. Está à procura mais perto-las podemos possível distinguir:

* **Premium localmente redundantes armazenamento (LRS)**: Azure Premium armazenamento fornece suporte do disco de alto desempenho, baixa latência para máquinas virtuais executadas posso/the-com um grau elevado cargas de trabalho. Existem 3 réplicas dos dados dentro o Centro de dados Azure mesmo de uma região Azure. As cópias serão diferentes falhas e atualizar os domínios (para capítulo de [planeamento-guia-3,2] consulte [isto] conceitos no [Guide][planning-guide]) planeamento. Em caso de uma réplica dos dados aceder fora do serviço devido a uma falha de nó de armazenamento ou falha do disco, uma nova réplica é gerada automaticamente.
* **Localmente redundantes armazenamento (LRS)**: neste caso, existem 3 réplicas dos dados dentro o Centro de dados de Azure mesmo de uma região Azure. As cópias serão diferentes falhas e atualizar os domínios (para capítulo de [planeamento-guia-3,2] consulte [isto] conceitos no [Guide][planning-guide]) planeamento. Em caso de uma réplica dos dados aceder fora do serviço devido a uma falha de nó de armazenamento ou falha do disco, uma nova réplica é gerada automaticamente. 
* **Armazenamento redundantes geo (GRS)**: neste caso, existe uma replicação assíncrona que irá feed uma réplicas 3 adicionais dos dados noutra região Azure que se encontra na maioria dos casos na mesma região geográfica (como Norte e Europa Ocidental). Isto resultará em 3 réplicas adicionais, para que existam 6 réplicas na soma. Uma variação de isto é uma adição onde os dados na região Azure replicada geo podem ser utilizados para fins de leitura (acesso de leitura Geo-redundantes).
* **Zona redundantes armazenamento (ZRS)**: neste caso as 3 réplicas dos dados permanecem na mesma região Azure. Como é explicado neste [] [3.1 de planeamento-guia] capítulo de [guia de planeamento] [-guia de planeamento] uma região Azure pode ser um número de centros de dados na proximidade. No caso de LRS réplicas seriam distribuídas ao longo do centros de dados diferentes que efetuar uma região Azure.

Mais informações podem ser encontradas [aqui][storage-redundancy].
 
> [AZURE.NOTE] Para implementações DBMS, a utilização de armazenamento redundante Geo não é recomendada
>
> Azure armazenamento Geo replicação é assíncrona. Replicação de VHDs individuais instalados para uma única VM não são sincronizados no passo de bloqueio. Por conseguinte, não é adequado criar uma réplica ficheiros DBMS que são distribuídos ao longo do VHDs diferentes ou implementados contra um software RAID com base em vários VHDs. Software DBMS necessita que o armazenamento de disco persistente com precisão é sincronizado através de LUNs diferentes e subjacentes discos/VHDs/eixos accionadores. DBMS software utiliza vários mecanismos e atividades de escrita sequência IO e um DBMS apresentará um relatório que o armazenamento de disco orientado pela replicação está danificado se estes variar mesmo através de alguns milissegundos. Por conseguinte, se se pretender na verdade uma configuração de base de dados com uma base de dados for esticada através de vários VHDs replicadas geo, essa uma replicação necessita de ser executadas com meios de base de dados e a funcionalidade. Um não deve confiar no Azure armazenamento Geo replicação para executar esta tarefa. 
>
> O problema é mais simples explicar com um sistema de exemplo. Imaginemos que tem um sistema SAP carregado no Azure que possui 8 VHDs que contém os ficheiros de dados da DBMS juntamente com um VHD que contém o ficheiro de registo da transação. Cada um dos seguintes 9 VHDs terá os dados escritos num consistente método de acordo com o DBMS, se os dados está a ser escritos para os ficheiros de registo de dados ou da transação.
>
> Fim corretamente geo replicação dos dados e manter uma imagem de base de dados consistentes, o conteúdo de todos os nove VHDs teria de ser replicadas geo pela ordem exata as operações e/s foram executadas em relação os nove VHDs diferentes. No entanto, armazenamento do Windows Azure geo-replicação não permite para declarar dependências entre VHDs. Isto significa que o armazenamento do Windows Azure geo-replicação não sabe sobre o facto de que o conteúdo nestes VHDs diferentes nove estão relacionadas entre si e que as alterações de dados são consistentes apenas quando replicação na ordem de operações de e/s aconteceu através de todas as 9 VHDs.
>
> Para além de hipóteses a ser alta que as imagens de replicadas geo no cenário não fornecem uma imagem de base de dados consistentes, também existe uma sanções de desempenho que aparece com armazenamento redundante geo que extremamente pode afetar o desempenho. Num resumo não utilize este tipo de redundância de armazenamento para cargas de trabalho de tipo DBMS.
 
#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapear VHDs para contas de armazenamento do serviço de Máquina Virtual Azure
Uma conta de armazenamento do Azure é não apenas uma construção administrativa, mas também um assunto das limitações. Considerando que as limitações variam de falarmos sobre como uma conta de armazenamento padrão do Azure ou uma conta de armazenamento do Azure Premium. As capacidades de exatas e limitações estão listadas [aqui][storage-scalability-targets]
 
Por isso para armazenamento padrão do Windows Azure, é importante ter em atenção que existe um limite no IOPS por conta de armazenamento (linha que contém «Total pedir taxa» no [artigo][storage-scalability-targets]). Além disso, existe um limite inicial de 100 contas de armazenamento por subscrição Azure (a partir de Julho de 2015). Por conseguinte, recomenda-se para equilibrar IOPS de VMs entre várias contas do armazenamento quando utilizar o armazenamento padrão do Windows Azure. Considerando que um única VM utiliza Idealmente uma conta de armazenamento se possível. Por isso, se falarmos sobre implementações DBMS onde cada VHD que está alojado num armazenamento padrão do Windows Azure poderia atingir o seu limite de quota, deve implementar apenas 30 40 VHDs por conta de armazenamento do Azure que utiliza armazenamento padrão do Windows Azure. Por outro lado, se tirar partido de armazenamento do Windows Azure Premium e pretende armazenar da base de dados grandes volumes, poderá ser fino em termos de IOPS. Mas uma conta de armazenamento do Azure Premium é a forma mais restritiva em volume de dados do que uma conta de armazenamento padrão do Azure. Como resultado, só pode implementar um número limitado de VHDs dentro de uma conta de armazenamento do Azure Premium antes de atingir o limite de volumes de dados. A pensar o fim de uma conta de armazenamento do Azure como "Virtual SAN" que tem capacidades limitadas no IOPS e/ou a capacidade. Como resultado, a tarefa permanece, tal como implementações no local, para definir o esquema de VHDs dos diferentes sistemas SAP sobre o 'imaginários SAN dispositivos diferentes' ou contas de armazenamento do Azure.
 
Para o armazenamento padrão do Azure-não é recomendado para apresentar o armazenamento de contas de armazenamento diferente para uma única VM se possível.

Considerando que utilizando o DS ou s-série de Azure VMs é possível montagem VHDs partido contas armazenamento padrão do Azure e contas de armazenamento de Premium. Casos de utilização, como escrever cópias de segurança para o armazenamento padrão cópias VHDs, Considerando que está a ter dados DBMS e ficheiros de registo Premium armazenamento ao sítio ideias onde essa armazenamento heterogéneo pode ser utilizado. 

Com base no implementações do cliente e testes cerca de 30 a 40 VHDs que contém os ficheiros de dados da base de dados e ficheiros de registo podem ser aprovisionados numa única Azure padrão armazenamento conta com um desempenho aceitável. Como mencionado anteriormente, a limitação de uma conta de armazenamento do Azure Premium é provável que seja a capacidade de dados podem conter e não IOPS.

Como com SAN dispositivos no local, a partilha requer alguns monitorização para poder para eventualmente detectar congestionamentos numa conta de armazenamento do Azure. Extensão de monitorização da Azure para SAP e o Portal do Azure são ferramentas que podem ser utilizadas para detetar ocupado contas de armazenamento de Azure que podem ser fornecer um desempenho IO reduzido.  Se esta situação é detectada que recomenda-se para mover VMs ocupados para outra conta de armazenamento do Azure. Consulte [guia de implementação do] [guia de implementação] para obter detalhes sobre como ativar o anfitrião do SAP capacidades de monitorização.

Outro artigo resumir práticas recomendadas para armazenamento padrão do Windows Azure e contas do Azure padrão armazenamento pode ser encontrado aqui <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>
 
#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Mover implementado VMs DBMS a partir do armazenamento padrão Azure ao armazenamento de Premium do Azure
Vamos encontrar bastante alguns cenários de onde pretende que como cliente para mover uma VM implementada a partir do armazenamento padrão Azure para armazenamento do Windows Azure Premium. Isto não é possível sem física mover os dados. Existem várias formas de atingir objetivo:

* Pode simplesmente copiar todos os VHDs, base VHD, bem como VHDs de dados para uma nova conta de armazenamento de Premium Azure. Muito frequentemente optou o número de VHDs no armazenamento padrão do Windows Azure não devido ao facto que seja necessário o volume de dados. Mas necessário VHDs que muitas devido a IOPS. Agora que deslocar-se ao armazenamento de Premium do Azure pode ir com forma menos VHDs para alcançar o algumas débito IOPS. Dado que no Azure armazenamento padrão pagar para os dados utilizados e não o tamanho do disco nominal, o número de VHDs não realmente importante em termos de custos. No entanto, com o armazenamento do Azure Premium, teria de pagar para o tamanho do disco nominal. Por conseguinte, a maior parte do cliente tente manter o número de Azure VHDs no Premium armazenamento no número necessário para alcançar o débito IOPS necessárias. Por isso, a maioria dos clientes decidir contra a forma de um simples 1:1 cópia.
* Se ainda não foram instalados, é montagem um único VHD que pode conter uma cópia de segurança da base de dados da base de dados SAP. Após a cópia de segurança, desmontar VHDs todos, incluindo o VHD que contém a cópia de segurança e copie o VHD base e o VHD com a cópia de segurança para uma conta de armazenamento do Windows Azure Premium. Em seguida, teria implementar a VM com base no base VHD e montagem VHD com a cópia de segurança. Agora criar discos de armazenamento Premium vazios adicionais para a VM que são utilizadas para restaurar a base de dados para. Este comando assume que o DBMS permite-lhe alterar caminhos de ficheiros de dados e inicie sessão como parte do processo de restaurar.
* Outra possibilidade é uma variação do processo de antigo, onde apenas copie a cópia de segurança VHD para armazenamento do Windows Azure Premium e anexá-lo contra uma VM que implementado recentemente e instalou.
* A possibilidade de quarta seria escolha quando se encontra necessitados alterar o número de ficheiros de dados da base de dados. Nesse caso são executadas uma cópia do sistema homogénea SAP utilizando Importar/exportar. Colocar aqueles exporte os ficheiros para um VHD que é copiado para uma conta de armazenamento do Azure Premium e anexá-lo a uma VM que utilizar para executar os processos de importação. Os clientes utilizam esta possibilidade sobretudo quando pretendem diminuir o número de ficheiros de dados.

### <a name="deployment-of-vms-for-sap-in-azure"></a>Implementação do VMs para SAP no Azure
Microsoft Azure oferece várias formas para implementar VMs e discos associados. Portanto é muito importante compreender as diferenças desde preparações dos VMs podem ser diferentes depende da forma de implementação. Em geral, vamos analisar os cenários descritos nos seguintes capítulos.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Implementar uma VM a partir do Azure Marketplace
Como tirar da Microsoft ou festa 3º fornecidos imagem a partir do Azure Marketplace para implementar o seu VM. Depois de implementado a VM no Azure, siga o mesmo diretrizes e ferramentas para instalar o software do SAP dentro da sua VM, tal como o faria num ambiente no local. Para instalar o software SAP dentro da VM Azure, SAP e o Microsoft recomendado para carregar e armazenar o suporte de instalação do SAP no Azure VHDs ou para criar um VM Azure trabalhar como um 'servidor do ficheiro' que contém todos os necessário SAP suporte de instalação.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Implementar uma VM com uma imagem GRG específica do cliente
Devido a requisitos de patches específico no que respeita à sua versão do SO ou DBMS, as imagens fornecidas no Azure Marketplace não poderão corresponder às suas necessidades. Por conseguinte, poderá ter de criar uma VM utilizando a sua própria imagem SO/DBMS VM 'private' que pode ser implementada posteriormente várias vezes. Para preparar como uma imagem de 'private' para duplicação, o SO tem de ser generalized na VM no local. Consulte [guia de implementação do] [guia de implementação] para obter detalhes sobre como generalizar uma VM.

Se já tiver instalado o conteúdo do SAP na sua VM no local (especialmente para sistemas de camada 2), pode adaptá as definições do sistema SAP depois de mudar o nome de implementação da VM Azure através da instância procedimento suportado pelo Gestor de aprovisionamento de Software SAP (nota SAP [1619720]). Caso contrário, pode instalar o software do SAP mais tarde depois da implementação da Azure VM.
 
A partir da base de dados conteúdo utilizado pela aplicação SAP, pode gerar o conteúdo recentemente por uma instalação do SAP ou pode importar o conteúdo para Azure utilizando um VHD com uma cópia de segurança da base de dados DBMS ou tirar partido da capacidades do DBMS diretamente fazer cópia de segurança para o armazenamento do Windows Azure. Neste caso, que também preparar VHDs com os DBMS dados e de registo ficheiros no local e, em seguida, importá aqueles como discos Azure. Mas a transferência de dados DBMS que está a obter carregados nos locais para Azure funcionarão sobre discos VHD que precisam de ser preparados no local.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Mover uma VM no local para o Azure com um disco não generalized
Planear mover um sistema SAP específico no local para Azure (elevação e shift). Isto pode ser feito ao carregá VHD que contém o sistema operativo, binários SAP e eventual binários do DBMS plus VHDs com os ficheiros de dados e de registo ao DBMS Azure. No oposto cenário #2 acima, que manter o nome do anfitrião, SAP SID e as contas de utilizador do SAP na Azure VM como estavam configuradas no ambiente no local. Por conseguinte, generalizar a imagem não é necessário. Neste caso, principalmente serão aplicadas para cenários de publicação em local em que uma parte da paisagem SAP é executada no local e peças Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Disponibilidade de alta e recuperação de falhas com Azure VMs
Azure oferece as seguintes funcionalidades de disponibilidade (HA alta) e falhas recuperação (DR) que se aplicam a diferentes componentes que utilizamos para implementações SAP e DBMS

### <a name="vms-deployed-on-azure-nodes"></a>VMs implementados em nós do Azure
A plataforma Azure não oferecer funcionalidades como Live migração para VMs implementadas. Isto significa que se existir manutenção necessárias num cluster de servidor em que é implementada uma VM, a VM precisa de obter parado e reiniciado. Manutenção no Azure é executada utilizando designado por isso, atualizar domínios dentro clusters dos servidores. Está a ser mantido apenas um domínio atualizar uma vez. Durante um reinício essa haverá uma interrupção do serviço enquanto a VM é encerrar, manutenção é executada e VM reiniciado. No entanto, a maior parte dos fornecedores DBMS fornecem elevada disponibilidade e recuperação de falhas funcionalidades que irão reiniciar rapidamente os serviços DBMS noutro nó se o nó principal não estiver disponível. A plataforma de Azure oferece uma funcionalidade para distribuir VMs, armazenamento e outros serviços do Azure por atualizar domínios para se certificar de que planeada falhas de manutenção ou infraestrutura teria apenas um impacto um pequeno subconjunto de VMs ou serviços.  Com um planeamento cuidado é possível alcançar níveis de disponibilidade comparáveis aos infra-estruturas no local.

Conjuntos de disponibilidade do Microsoft Azure são um agrupamento lógico de VMs ou serviços que garante VMs e outros serviços que são distribuídos para diferentes falhas e atualizar domínios dentro de um cluster assim que iria apenas ser um encerramento nó em qualquer uma ponto hora (ler [Esta] [ virtual-machines-manage-availability] artigo para obter mais detalhes).

Precisa de ser configurado por objetivo quando gradual saída VMs conforme visto aqui:

![Definição do conjunto de disponibilidade de configurações DBMS HA][dbms-guide-figure-200]

Se queremos criar configurações altamente disponíveis de implementações DBMS (independentemente da individual DBMS HA funcionalidade utilizada), o VMs DBMS seria necessário:

* Adicionar os VMs à mesma Azure Virtual rede (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* VMs da configuração do HA também devem estar na mesma sub-rede. Resolução de nomes entre diferentes sub-redes não é possível nas implementações apenas na nuvem, apenas a resolução de IP irá funcionar. Utilizar o site para o site ou conectividade ExpressRoute para implementações cruzada local, numa rede com pelo menos uma sub-rede será já estabelecida. Vai ser feita resolução do nome de acordo com no local infraestrutura de rede e políticas de AD. 
[comentário]: <>  (Teste de TODO MSSedusch se ainda verdadeiro no braço)

#### <a name="ip-addresses"></a>Endereços IP
Recomenda-se vivamente para VMs para HA configurações do programa de configuração de uma forma e são. Depender de endereços IP para o endereço de parceiros HA têm dentro da configuração HA não é fiável no Azure, a menos que os endereços IP estáticos são utilizados. Existem dois conceitos "Encerramento" no Azure:

* Encerrar através do Portal do Azure ou Azure PowerShell cmdlet parar AzureRmVM: neste caso a Máquina Virtual obtém encerramento e anular atribuídos. A conta Azure já não será cobrada por este VM por isso, os únicos encargos que irão implicam são para o armazenamento utilizado. No entanto, se o endereço IP privado da interface de rede não tiver sido estático, o endereço IP é disponibilizado e não é possível garantir que a interface de rede obtém o endereço IP antigo atribuído novamente após um reinício da VM. Efetuar a encerrar para baixo, através do Portal do Azure ou ao contactar o suporte parar AzureRmVM automaticamente causará a atribuição. Se não quiser deallocat a utilização de máquina parar AzureRmVM - StayProvisioned 
* Se encerrar a VM a partir de um nível de sistema operativo, a VM obtém encerrar e não retirar atribuída. No entanto, neste caso, a conta Azure será ainda cobrada pelo VM, apesar do que é encerramento. Neste caso, a atribuição do endereço IP a uma VM parado irá permanecer intacta. Encerrar a VM a partir de dentro irá não automaticamente forçar a atribuição.

Mesmo para cenários de publicação em local, por predefinição um encerramento e a alocação significa que a atribuição dos endereços IP do VM, mesmo se as políticas de no local nas definições de DHCP são diferentes. 

* A exceção é se um endereço IP estático um atribui uma interface de rede como descrito [aqui][virtual-networks-reserved-private-ip].
* Nesse caso o endereço IP permanece fixo, desde que a interface de rede não é eliminada.

> [AZURE.IMPORTANT] Para manter a implementação toda simples e fácil, a limpar recomendação é VMs um DBMS HA ou configuração de DR do Azure de uma forma que existe uma resolução do nome de funcionamento entre as diferentes VMs envolvidos a parceria de configuração.
 
## <a name="deployment-of-host-monitoring"></a>Implementação da monitorização de anfitrião
Para a mais produtiva utilização das aplicações SAP em máquinas virtuais do Azure, SAP requer a capacidade de obter dados a partir de anfitriões físicos a executar o máquinas virtuais do Azure de monitorização de anfitrião. Um nível de patches SAP HostAgent específico serão necessário que permite a esta funcionalidade no SAPOSCOL e SAP HostAgent. O nível de patches exata é documentado na nota SAP [1409604].

Para obter detalhes sobre implementação dos componentes entregar alojar dados SAPOSCOL e SAPHostAgent e a gestão de ciclo de vida dos componentes desses, consulte [guia de implementação do] [guia de implementação]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Detalhes do Microsoft SQL Server

### <a name="sql-server-iaas"></a>SQL Server IaaS
A partir do Microsoft Azure, pode migrar facilmente as aplicações do SQL Server existentes criada com base em plataforma Windows Server para máquinas virtuais do Azure. SQL Server em máquinas uma Máquina Virtual permite-lhe reduzir o custo total da propriedade de implementação, gestão e manutenção das aplicações de boca empresariais ao migrar facilmente estas aplicações para o Microsoft Azure. Com o SQL Server uma Máquina Virtual no Azure, os administradores e programadores ainda podem utilizar as ferramentas de administração que estão disponível no local e desenvolvimento do mesmo. 

> [AZURE.IMPORTANT] Tenha em atenção que se a discutir Microsoft Azure SQL da base de dados que é uma plataforma como uma oferta de serviço da plataforma Microsoft Azure. O debate deste documento é sobre como executar o produto do SQL Server, tal como é é conhecido para implementações no local em máquinas virtuais do Azure, tirar partido da infraestrutura do como uma funcionalidade de serviço do Azure. Capacidades de base de dados e funcionalidades entre estas duas ofertas são diferentes e não devem ser misturadas com os outros. Consulte também: <https://azure.microsoft.com/services/sql-database/>
 
É vivamente recomendado para rever [Este] [ virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas secções seguintes partes de elementos da documentação na hiperligação acima serão agregadas e mencionados. Detalhes à volta do SAP são mencionados também e alguns conceitos são descritos mais detalhadamente. No entanto, recomenda-se vivamente para trabalhar através de documentação acima primeiro antes de ler a documentação específica de SQL Server.

Existe algum do SQL Server no IaaS informações específicas que deve saber antes de continuar:

* **Máquina virtual SLA**: existe um SLA para máquinas virtuais em execução no Azure que pode ser encontrado aqui: <https://azure.microsoft.com/support/legal/sla/>  
* **Suporte de versão SQL**: para clientes do SAP, suportamos o SQL Server 2008 R2 e superior no Microsoft Azure Virtual Machine. Edições anteriores não são suportadas. Reveja esta gerais [Declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Tenha em atenção que no geral SQL Server 2008 é suportado pelo Microsoft também. No entanto, devido a funcionalidade significativa para SAP, que foi introduzida com SQL Server 2008 R2, SQL Server 2008 R2 é a versão mínima para SAP. Tenha em atenção que SQL Server 2012 e 2014 tem expandido com mais aprofundada integração no cenário de IaaS (como cópias de segurança directamente contra armazenamento do Windows Azure). Por conseguinte, podemos restringir deste documento para SQL Server 2012 e 2014 com o seu nível de patches mais recente para Azure.
* **Suporte de funcionalidades de SQL**: funcionalidades de mais do SQL Server são suportadas em máquinas virtuais do Microsoft Azure com algumas exceções. **SQL Server activação pós-falha clusters utilizando discos partilhada não é suportada**.  Distribuído tecnologias como espelhando as bases de dados, grupos de Disponibilidade AlwaysOn, replicação, registo de envio e de serviço corretor são suportadas numa única região Azure. SQL Server AlwaysOn também é suportada entre diferentes regiões do Azure conforme é documentado aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Reveja a [Declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Um exemplo sobre como implementar uma configuração AlwaysOn é apresentado neste [] [ virtual-machines-workload-template-sql-alwayson] artigo. Além disso, consulte as melhores práticas documentado [aqui][virtual-machines-sql-server-infrastructure-services] 
* **Desempenho de SQL**: estamos a certeza de que a Microsoft Azure alojado máquinas virtuais fará muito bem em comparação com outros ofertas de virtualização da nuvem público, mas resultados individuais podem variar. Veja [Este] [ virtual-machines-sql-server-performance-best-practices] artigo.
* **Utilizar imagens do Azure Marketplace**: A maneira mais para implementar uma nova Microsoft Azure VM é utilizar uma imagem a partir do Azure Marketplace. Existem imagens no Azure Marketplace que contêm do SQL Server. As imagens, onde do SQL Server já está instalado não podem ser utilizadas imediatamente para as aplicações SAP NetWeaver. O motivo pelo qual é que o agrupamento de SQL Server predefinido é instalado dentro dessas imagens e não o agrupamento de uma ferramenta necessária ao sistemas SAP NetWeaver. Para que possa utilizar essas imagens, consulte os passos indicados no capítulo [utilizando uma imagens do SQL Server fora do Microsoft Azure Marketplace] [dbms-guia 5.6]. 
* Consulte os [Detalhes do preços](https://azure.microsoft.com/pricing/) para obter mais informações. O [Guia de licenciamento do SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e [Guia de licenciamento do SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) também são um recurso importante.
 
### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Diretrizes de configuração do SQL Server para SAP relacionadas com as instalações do SQL Server no Azure VMs

#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura VM/VHD para SAP relacionados com implementações do SQL Server
Em conformidade com a descrição geral, SQL Server executáveis deverão ser localizado ou instalado na unidade de sistema do VHD base a VM (unidade c:\).  Normalmente, a maior parte das bases de dados do SQL Server sistema não é utilizadas de alto nível pela carga de trabalho SAP NetWeaver. Por conseguinte, as bases de dados de sistema do SQL Server (modelo global, msdb e modelo) podem permanecer na unidade de C:\. Uma exceção poderão ser tempdb, que, no caso de alguns ERP SAP e todas as cargas de trabalho de p/b, poderá ser necessário volume de dados mais elevado ou volume de operações de e/s que não se ajustam à VM original. Para tais sistemas, devem ser executados os seguintes passos:

* Mova os ficheiros de dados de tempdb principal para a mesma unidade lógica como os ficheiros de dados principal da base de dados SAP.
* Adicione quaisquer ficheiros de dados de tempdb adicionais para cada uma das outras unidades lógicas que contém um ficheiro de dados da base de dados de utilizador do SAP.
* Adicione o ficheiro de registo de tempdb para a unidade de lógica que contém o ficheiro de registo de utilizador da base de dados.
* **Exclusivamente para tipos VM que utilizam SSDs locais** nos cluster nó tempdb dados e ficheiros de registo podem ser colocados na unidade D:\. No entanto, poderá ser recomendada para utilizar vários ficheiros de dados tempdb. Tenha em atenção volumes de unidade D:\ diferem consoante o tipo VM.
 
Estas configurações ativar tempdb consumir mais espaço que forneçam a unidade de sistema. Para determinar o tamanho de tempdb inicial, um pode verificar os tamanhos tempdb nos sistemas existentes que se deslocam no local. Além disso, tal configuração seria ativar números IOPS contra tempdb que não pode ser fornecida com a unidade de sistema. Novamente, sistemas de que estão a executar o no local podem ser utilizados para monitorizar a carga de trabalho e/s contra tempdb para que pode deriva os números IOPS que esperava ver na sua tempdb.

Uma configuração de VM que executa o SQL Server com uma base de dados do SAP e onde os dados de tempdb e ficheiro de registo de tempdb são colocados na unidade de D:\ teria o seguinte aspeto:
 
![Referência de configuração da Azure IaaS VM para SAP][dbms-guide-figure-300]

Tenha em atenção que a unidade D:\ tem diferentes tamanhos de dependentes do tipo VM. Dependente o requisito de tamanho de tempdb lhe poderá ser forçada a par tempdb dados e ficheiros de registo com os ficheiros de dados e de registo da base de dados SAP nos casos em D:\ unidade é demasiado pequena.

#### <a name="formatting-the-vhds"></a>Formatação de VHDs
Para o SQL Server o NTFS do tamanho do bloco para VHDs que contém os dados do SQL Server e ficheiros de registo devem ser 64 mil. Não é necessário para formatar a unidade D:\. Esta unidade inclui pré-formatados.

Para se certificar de que a criação de bases de dados ou de restauro é não a inicialização os ficheiros de dados por colocando a zero o conteúdo dos ficheiros, um deve certificar-se de que o contexto de utilizador, que o serviço do SQL Server está em execução no tem uma determinada permissão. Normalmente, os utilizadores do grupo de administrador do Windows tem estas permissões. Se o serviço do SQL Server é executado no contexto de utilizador do utilizador que não sejam - administrador do Windows, terá de atribuir a esse utilizador à direita do utilizador 'Realizar tarefas de manutenção de volume'.  Ver os detalhes neste artigo de Base de dados de conhecimento da Microsoft: <https://support.microsoft.com/kb/2574695>
 
#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações onde largura de banda e/s pode tornar-se um fator de limitação, todas as medidas que reduz IOPS poderão ajudar a esticar a carga de trabalho um pode executar um cenário de IaaS como Azure. Por isso, se ainda não o fez, aplicar a compressão de página do SQL Server é vivamente recomendado pelo SAP e o Microsoft antes de carregar um SAP existente de bases de dados para Azure.
 
Recomendação ao executar a compressão de base de dados antes de carregar para Azure é dado terminar duas razões:

* A quantidade de dados ser carregado é inferior.
* A duração da execução compressão é mais curta partindo do princípio que podem ser utilizados mais forte do hardware com mais CPUs ou largura de banda e/s superior ou inferior e/s latência no local.
* Tamanho da base de dados mais pequeno pode conduzir a menos os custos de atribuição de disco

A compressão de base de dados também funciona num máquinas virtuais do Azure como o faz no local. Para obter mais detalhes sobre como comprimir um servidor de SQL SAP existente base de dados Verifique se existem aqui: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>
  
### <a name="sql-server-2014--storing-database-files-directly-on-azure-blog-storage"></a>SQL Server 2014 – armazenar da base de dados ficheiros diretamente no Azure blogue armazenamento
SQL Server 2014 abre-se a possibilidade de armazenar ficheiros de base de dados diretamente no arquivo de Blobs do Azure sem 'bombom' de um VHD à volta dos mesmos. Especialmente com a utilização de armazenamento do Windows Azure padrão ou tipos de VM mais pequenos permite que cenários onde pode ultrapassar dos limites da IOPS seria impostas por um número limitado de VHDs que podem ser instalados para alguns tipos VM mais pequenos. Isto funciona para bases de dados de utilizador no entanto, nem para bases de dados de sistema do SQL Server. Também funciona para dados e ficheiros de registo do SQL Server. Se pretender para implementar uma base de dados do SQL Server do SAP desta forma em vez de 'Moldagem'-lo para VHDs, consulte tenha o seguinte em mente:

* A conta de armazenamento utilizada necessidades para ser na mesma região Azure como aquele que é utilizado para implementar o SQL Server VM está em execução no.
* Considerações listadas anteriormente no que respeita para distribuir VHDs através de diferentes contas de armazenamento do Azure aplicam-se para este método de implementações também. Significa que a contagem de operações e/s contra os limites da conta de armazenamento Azure.
[comentário]: <>  (MSSedusch TODO, mas isto irá utilizar largura de banda máxima e não armazenamento bandwith, não é verdade?)

Obter mais detalhes sobre este tipo de implementação estão listadas aqui: <https://msdn.microsoft.com/library/dn385720.aspx>
 
Para poder armazenar ficheiros de dados do SQL Server diretamente no Azure Premium armazenamento, tem de ter uma versão de patches mínima de 2014 do SQL Server que é documentada aqui: <https://support.microsoft.com/kb/3063054>. Armazenar ficheiros de dados do SQL Server Azure padrão armazenamento funciona com o versão de lançamento do SQL Server 2014. No entanto, os patches muito mesmos contêm outra série de correções que tornam mais fiável a utilização de direta de armazenamento de Blobs do Azure para ficheiros de dados do SQL Server e cópias de segurança. Por conseguinte, recomendamos que utilize estes patches em geral.

### <a name="sql-server-2014-buffer-pool-extension"></a>Extensão de conjunto de dados do SQL Server 2014 memória intermédia
SQL Server 2014 introduzida uma nova funcionalidade denominada extensão do conjunto de memória intermédia. Esta funcionalidade expande do conjunto de memória intermédia do SQL Server que é mantido na memória com uma segunda cache nível que é suportada pelo SSDs locais de um servidor ou VM. Isto permite para manter um conjunto maior de trabalho de dados 'na memória'. Em comparação com a aceder ao armazenamento de padrão Azure o acesso para a extensão do conjunto de memória intermédia que está armazenado no locais SSDs de uma VM Azure é de vários fatores mais rápido.  Por conseguinte, pode tirar partido da unidade local D:\ dos tipos de VM que tenham excelente IOPS e débito dever uma forma muito razoável para reduzir a carga IOPS contra armazenamento do Windows Azure e melhorar significativamente tempos de resposta de consultas. Isto aplica-se principalmente quando não utilizar o armazenamento de Premium. Em caso de armazenamento de Premium e a utilização da Cache de leitura do Azure Premium no nó cluster, tal como recomendado para os ficheiros de dados, não existem diferenças grandes são esperadas. Motivo é que ambos os caches (SQL Server memória intermédia de agrupamento de extensão e Cache de leitura de armazenamento Premium) estão a utilizar os discos locais de nós cluster.
Para obter mais detalhes sobre esta funcionalidade, consulte a documentação: <https://msdn.microsoft.com/library/dn133176.aspx> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considerações de cópia de segurança/recuperação para SQL Server
Quando implementar o SQL Server para Azure a sua metodologia de cópia de segurança tem de ser revista. Mesmo se o sistema não é um sistema produtivo, a base de dados do SAP alojado do SQL Server deve ser cópia de segurança periodicamente. Uma vez que o armazenamento do Windows Azure mantém três imagens, uma cópia de segurança agora é menos importante em relação à compensadores uma falha de armazenamento. O motivo de prioridade de manutenção de um plano de cópia de segurança e recuperação adequado é mais do que pode compensar erros lógicos/manual, fornecendo ponto no capacidades de recuperação do tempo. Para que o objetivo é uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para outro sistema de propagar ao copiar a base de dados existente. Por exemplo, poderia transferir a partir de uma configuração de SAP 2 camada para a configuração do mesmo sistema de um sistema de níveis de 3 por restaurar uma cópia de segurança.

Existem três formas diferentes de cópia de segurança do SQL Server ao armazenamento do Azure:
 
1. SQL Server 2012 CU4 e superiores podem vierem cópia de segurança bases de dados para um URL. Isto é detalhado a [nova funcionalidade no SQL Server 2014 – parte 5 – melhoramentos de cópia de segurança/restauro](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)de blogue. Consulte o artigo capítulo [SQL Server 2012 SP1 CU4 e versões posteriores] [dbms-guia 5.5.1].
1. Edições do SQL Server antes de SQL 2012 CU4 podem utilizar uma funcionalidade de redirecionamento para cópia de segurança para um VHD e que mostra basicamente mover a sequência de escrita no sentido de uma localização de armazenamento do Windows Azure que tenha sido configurada. Consulte o artigo capítulo [SQL Server 2012 SP1 CU3 e lançamentos anteriores] [dbms-guia 5.5.2].
1. O método final é para efetuar uma cópia de segurança do SQL Server convencional ao comando do disco para um dispositivo de disco VHD.  Este é idêntico ao padrão de implementação no local e não é abordado detalhadamente neste documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e versões posteriores
Esta funcionalidade permite-lhe diretamente fazer cópia de segurança armazenamento de BLOBS do Azure. Sem este método, deve fazer cópia de segurança para outros VHDs Azure que iria consumir capacidade VHD e IOPS. A ideia é basicamente o seguinte:
 
 ![Utilizar o SQL Server 2012 cópia de segurança do Microsoft Azure armazenamento BLOB][dbms-guide-figure-400]

A vantagem neste caso é que um não necessite de passam VHDs para armazenar cópias de segurança do SQL Server no. Por isso, tem menos VHDs atribuídos e a largura de banda toda VHD IOPS pode ser utilizada para dados e ficheiros de registo. Tenha em atenção que o tamanho máximo de uma cópia de segurança é limitado a um máximo de 1 TB conforme é documentado na secção 'Limitações' neste artigo: <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Se o tamanho da cópia de segurança, apesar utilizando a compressão de cópia de segurança do SQL Server iria exceder 1 TB no tamanho, a funcionalidade descrita capítulo [SQL Server 2012 SP1 CU3 e lançamentos anteriores] [dbms-guia-5.5.2] neste documento tem de ser utilizado.

[Documentação relacionada](https://msdn.microsoft.com/library/dn449492.aspx) que descreve o restauro das bases de dados a partir de cópias de segurança relativamente ao armazenamento de Blobs do Azure recomendar não para restaurar diretamente a partir do armazenamento de BLOBS do Azure se as cópias de segurança > 25 GB. Recomendação neste artigo simplesmente é baseada no considerações sobre o desempenho e não devido a restrições funcionais. Por conseguinte, diferentes condições poderão aplicar no caso a caso.

Pode ser encontrada documentação sobre como este tipo de cópia de segurança é configurar e utilizado [neste](https://msdn.microsoft.com/library/dn466438.aspx) tutorial
 
Um exemplo da sequência de passos pode ser lidos [aqui](https://msdn.microsoft.com/library/dn435916.aspx).

Automatizar cópias de segurança, é de maior importância para se certificar de que BLOBs para cada cópia de segurança estão nomeadas de forma diferente. Caso contrário, serão substituídos e a cadeia de restaurar é interrompida.
 
Ordem para não misturar o coisas entre os 3 diferentes tipos de cópias de segurança, é aconselhável criar contentores diferentes sob a conta de armazenamento utilizada para cópias de segurança. Os contentores podem ser apenas por VM ou por tipo VM e cópia de segurança. Esquema de poderia o seguinte aspeto:
 
 ![Utilizar a cópia de segurança do SQL Server 2012 ao Microsoft Azure armazenamento BLOB – contentores diferentes em separado de armazenamento de conta][dbms-guide-figure-500]

No exemplo acima, as cópias de segurança não seriam ser executadas para a mesma conta de armazenamento, onde os VMs são implementadas. Seria uma nova conta de armazenamento especificamente para as cópias de segurança. Dentro de contas de armazenamento, teria ser contentores diferentes criados com uma matriz do tipo de cópia de segurança e o nome VM. Essa segmentação irá facilitar administrar as cópias de segurança as VMs diferentes.

Os BLOBs um escreve diretamente de cópias de segurança para não está a adicionar à contagem de VHDs de um VM. Por conseguinte, um poderia maximizar o número máximo de VHDs instalados do SKU VM específicos para os dados e transação o ficheiro de registo e ainda executar uma cópia de segurança contra um contentor de armazenamento. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versões anteriores
O primeiro passo para alcançar uma cópia de segurança diretamente contra armazenamento do Windows Azure, tem de executar seria transferir o msi que está associado a [Este](https://www.microsoft.com/download/details.aspx?id=40740) artigo KBA.
 
Ficheiro de instalação de transferir o x64 e a documentação. O ficheiro será instale um programa chamado: 'Microsoft SQL Server cópia de segurança para a ferramenta do Microsoft Azure'. Leia a documentação do produto cuidadosamente.  A ferramenta que mostra basicamente funciona da seguinte forma:

* A partir do lado do SQL Server, é definida uma localização do disco para a cópia de segurança do SQL Server (não utilizar a unidade D:\ para este).
* A ferramenta de permitirá definir regras que podem ser utilizadas para direcionar diferentes tipos de cópias de segurança para contentores de armazenamento do Windows Azure diferentes.
* Assim que as regras são no local, a ferramenta irá redirecioná-a sequência de escrita da cópia de segurança para um dos VHDs discos para a localização de armazenamento do Windows Azure que tenha sido definida anteriormente.
* A ferramenta de irão deixá-um ficheiro pequeno stub de alguns de KB de tamanho do VHD/disco qual tenha sido definida para o SQL Server cópia de segurança. **Este ficheiro deve ser deixado na localização de armazenamento, uma vez que é necessário para restaurar novamente a partir do armazenamento do Windows Azure.**
    * Se tiver perdido o ficheiro de stub (por exemplo, através de perda de suporte de armazenamento que continha o ficheiro de stub) e que escolheu a opção de cópias de segurança para uma conta de armazenamento do Windows Azure, pode recuperar o ficheiro de stub através do Microsoft Azure armazenamento ao transferi-lo a partir do contentor de armazenamento no qual foi colocada. Deve, em seguida, coloque o ficheiro de stub para uma pasta no computador local onde a ferramenta está configurada para detetar e carregar para o contentor mesmo com a mesma palavra-passe de encriptação se encriptação tiver sido utilizada com a regra original. 

Isto significa que o esquema, tal como descrito acima para as versões mais recentes do SQL Server pode ser colocado no local, bem como para edições do SQL Server que não estão a permitir endereço direto uma localização de armazenamento do Windows Azure.
 
Este método não deve ser utilizado com as versões mais recentes do SQL Server que suportam a segurança o vierem contra armazenamento do Windows Azure. Exceções estão onde limitações da cópia de segurança nativa para Azure estejam a impedir nativa execução cópia de segurança para o Azure.

#### <a name="other-possibilities-to-backup-sql-server-databases"></a>Outras possibilidades a cópia de segurança bases de dados do SQL Server
Outras possibilidades para bases de dados de cópia de segurança é anexar VHDs adicionais a uma VM que utiliza para armazenar as cópias de segurança no. Nesse caso seria necessário para se certificar de que o VHDs não estiver a executar completos. Se for esse o caso, que precisa para desmontar o VHD e por isso, ao speak 'arquivá'-lo e substituí-la por um novo VHD vazio. Se descer esse caminho, que pretende manter estas VHDs nas contas de armazenamento em separado do Azure a partir daqueles que VHDs com os ficheiros de base de dados.

Uma segunda possibilidade é utilizar uma grande VM que pode ter muitos VHDs anexados. Por exemplo D14 com 32VHDs. Utilize espaços de armazenamento para criar um ambiente flexível qual foi possível criar partilhas de utilizados, em seguida, como destinos cópia de segurança para os servidores de DBMS diferentes.
 
Alguns dos procedimentos recomendados tem documentados [aqui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) também. 

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para cópias de segurança/restaura
Tal como base implementações, desempenho de cópia de segurança/restauro é dependente volumes quantos podem ser lidos em paralelo e o que poderá ser débito desses volumes. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança poderá ser reproduzido um papel significativo no VMs com apenas até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dados de ficheiros, menor for o débito geral no modo de leitura.
* Quanto mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança.
* Os menos alvos (BLOBs ou VHDs) para escrever a cópia de segurança, o menor o débito.
* Menor for a VM o tamanho, mais pequena a débito quota de armazenamento de escrita e de leitura a partir do armazenamento do Windows Azure. Independentemente de se as cópias de segurança diretamente estão armazenadas num BLOBs do Azure ou se são armazenadas no VHDs novamente são armazenados no Blobs do Azure.

Ao utilizar um BLOB do Microsoft Azure armazenamento como o destino da cópia de segurança versões mais recentes, está restringido a designar apenas um alvo de URL para cada cópia de segurança específica.
 
Mas ao utilizar a 'Microsoft SQL Server cópia de segurança para a ferramenta do Microsoft Azure' versões mais antigas, pode definir mais do que um alvo de ficheiro. Com mais do que um destino, pode dimensionar a cópia de segurança e o débito da cópia de segurança é superior. Isto resultará em seguida, em vários ficheiros, assim na conta de armazenamento do Windows Azure. Nos nossos testes, utilizar vários destinos de ficheiro altamente uma possível alcançar o caudal de um poderia atingir com as extensões de cópia de segurança implementada a partir do SQL Server 2012 SP1 CU4 no. Pode também não é bloqueadas pelo limite de 1TB tal como a cópia de segurança nativo para Azure.

No entanto, lembre-se, o débito também é dependente a localização da conta de armazenamento do Azure que utiliza para a cópia de segurança. Poderá ser uma ideia localizar a conta de armazenamento numa região diferente do que o VMs estão a ser executada em. Por exemplo seria executar a configuração de VM na Europa Oeste, mas coloque a conta de armazenamento que utiliza para trás para cima contra na América do Norte Europa. Certamente que terá impacto nas débito de cópia de segurança e não é provável gerar um débito de 150MB/seg como parecer que seja possível em casos onde o armazenamento de destino e as VMs estiverem em execução no Centro de dados regional mesmo.

#### <a name="managing-backup-blobs"></a>Gerir BLOBs cópia de segurança
Existe um requisito para gerir as cópias de segurança no seu próprio. Uma vez que o expetativa que blobs muitos serão criados através da execução da transação frequente registo das cópias de segurança, administração desses blobs facilmente pode sobrecarregar Portal do Azure. Por conseguinte, é recommendable para aproveitar o Explorador de armazenamento de Azure. Existem várias bons disponíveis que podem ajudar a gerir uma conta de armazenamento Azure

* Microsoft Visual Studio com Azure SDK instalado (<https://azure.microsoft.com/downloads/>)
* Explorador de armazenamento do Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* 3º ferramentas de terceiros

[comentário]: <>  (Ainda não é suportada no braço)
[comentário]: <>  (# # # Cópia de segurança do azure VM)
[comentário]: <> É possível fazer cópias  (VMs dentro do sistema SAP utilizando a funcionalidade de cópia de segurança do Azure Máquina Virtual. Cópia de segurança de Máquina Virtual Azure tem introduzida no início do ano 2015 e entretanto é um método padrão uma VM concluída no Azure uma cópia de segurança. Cópia de segurança do Azure armazena as cópias de segurança no Azure e permite um restauro de uma VM.) 
[comentário]: <> (VMs executar bases de dados podem ser cópia de segurança de uma forma consistente também se os sistemas DBMS suporta o VSS do Windows (serviço de cópia sombra - <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>), tal como é que, por exemplo, o SQL Server. Para que utilizar a cópia de segurança do Azure VM pode ser uma forma para aceder a uma cópia de segurança restaurável de uma base de dados do SAP. No entanto, tenha em atenção de que baseada no Azure VM as cópias de segurança no momento restaura das bases de dados não é possível. Por conseguinte, a recomendação é para efetuar cópias de segurança das bases de dados com a funcionalidade de DBMS em vez de depender de cópia de segurança do Azure VM.) [comentário]: <> (para se familiarizar com cópia de segurança do Azure Máquina Virtual comece aqui <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Utilizar um imagens do SQL Server fora do Microsoft Azure Marketplace
A Microsoft oferece VMs no Azure Marketplace que já contêm versões do SQL Server. Para clientes do SAP que necessitam de licenças para SQL Server e o Windows, este poderá ser uma oportunidade para cobrir que mostra basicamente a necessidade de licenças por girar o VMs com o SQL Server já instalados. Para que possa utilizar essas imagens para SAP, as seguintes considerações tem de ser efetuadas:

* As versões do SQL Server não avaliação adquirem custos mais elevados que apenas uma 'Só de Windows' VM implementado a partir do Azure Marketplace. Consulte estes artigos para comparar os preços: <https://azure.microsoft.com/pricing/details/virtual-machines/> e <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>. 
* Só pode utilizar versões do SQL Server que são suportadas pelo SAP, como o SQL Server 2012.
* O agrupamento da instância do SQL Server que é instalado no VMs oferecidas no Azure Marketplace não se encontra o agrupamento que SAP NetWeaver requer instância do SQL Server para executar. Pode alterar o agrupamento apesar com as instruções na secção seguinte.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterar o agrupamento de servidor SQL do Microsoft Windows/SQL Server VM
Uma vez que as imagens do SQL Server no Azure Marketplace não são configuradas para utilizar o agrupamento necessária por aplicações SAP NetWeaver, necessita de ser alteradas imediatamente após a implementação. Para o SQL Server 2012, pode fazê-lo com os passos seguintes, logo a VM ter sido implementada e um administrador é possível que inicie sessão na VM implementada:

* Abra uma janela de comando do Windows 'como administrador'.
* Altere o directório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Executar o comando: Setup.exe /QUIET /ACTION = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
    * `<local_admin_account_name`> é a conta que tenha sido definida como a conta de administrador quando implementar a VM pela primeira vez através da galeria.

O processo só deverá tomar alguns minutos. Para se certificar de se o passo terminou com o resultado correto, execute os seguintes passos:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort na base de dados do SQL Server principal.

O resultado pretendido deve aspeto:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se não for o resultado, pare a implementar o SAP e investigar por que motivo o comando de configuração não funcionarem conforme esperado. Implementação de aplicações SAP NetWeaver para instância do SQL Server com diferentes páginas de código do SQL Server que a mencionado acima **não** é suportada.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server alta disponibilidade para SAP no Azure
Tal como mencionado anteriormente deste documento, não existe possibilidade para criar armazenamento partilhado que é necessário para a utilização da funcionalidade de elevada disponibilidade mais antiga do SQL Server. Esta funcionalidade seria instalar duas ou mais instâncias do SQL Server num Windows Server activação pós-falha Cluster (WSFC) com um disco partilhado para as bases de dados de utilizador (e eventualmente tempdb). Este é o método de padrão elevada disponibilidade de muito tempo que também é suportado pelo SAP. Porque Azure não suporta o armazenamento partilhado, não podem ser realizadas configurações de elevada disponibilidade do SQL Server com uma configuração de cluster de disco partilhado. No entanto, muitos outros métodos de elevada disponibilidade são continua a ser possíveis e são descritos nas secções seguintes.

[comentário]: <>  (Artigo refere-se ainda referencia ASM)
[comentário]: <>  (Antes de ler as tecnologias de diferentes específico elevada disponibilidade utilizáveis para SQL Server no Azure, é um documento muito bom que fornece mais detalhes e ponteiros [here][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### <a name="sql-server-log-shipping"></a>Envio de registo do SQL Server
Um dos métodos de elevada disponibilidade (HA) é o envio de registo do SQL Server. Se tiverem o VMs participar na configuração HA trabalhar resolução do nome, não existe não há problema e a configuração no Azure não serão diferentes a partir de qualquer programa de configuração que é concluído no local. Não é recomendado para depender de resolução de IP apenas. No que respeita a configuração de registo de envio e os princípios à volta Log envio verifique esta documentação:

<https://technet.microsoft.com/library/ms187103.aspx>
 
Para poder atingir realmente a qualquer elevada disponibilidade, um tem de implementar as VMs que são utilizadas em tal registo envio configuração para ser no mesmo Azure disponibilidade conjunto de.

#### <a name="database-mirroring"></a>Espelhando as bases de dados
Base de dados reflexo como suportado pelo SAP (ver nota SAP [965908]) depende do que define um parceiro de activação pós-falha na cadeia de ligação de SAP. Para casos cruzada local, vamos assumir que são os dois VMs no mesmo domínio e se o contexto de utilizador as duas instâncias do SQL Server estão a ser executado em são também os utilizadores do domínio e tem privilégios suficientes nas duas instâncias do SQL Server envolvidas. Por conseguinte, a configuração da base de dados espelhando as no Azure não diferem entre uma configuração típico no local.

Partir implementações apenas na nuvem, o método mais fácil é tem outro programa de configuração do domínio no Azure ter esses DBMS VMs (e Idealmente dedicada SAP VMs) dentro de um domínio.

Se um domínio não é possível, um também pode utilizar certificados para a base de dados espelhando as pontos finais conforme é aqui descrito: <https://technet.microsoft.com/library/ms191477.aspx>

Um tutorial para configuração da compilação espelhando as bases de dados no Azure pode ser encontrado aqui: <https://technet.microsoft.com/library/ms189852.aspx> 

#### <a name="alwayson"></a>AlwaysOn
Como AlwaysOn é suportada para SAP no local (ver nota SAP [1772688]), que é suportado para ser utilizada em conjunto com SAP no Azure. O facto de que não é possível criar discos partilhados no Azure não significa que um não é possível criar uma configuração AlwaysOn Windows Server activação pós-falha Cluster (WSFC) entre VMs diferentes. Apenas significa que não tem a possibilidade de utilizar um disco partilhado como um quórum na configuração do cluster. Por conseguinte, pode criar uma configuração AlwaysOn WSFC no Azure e simplesmente não, selecione o tipo de quórum que utiliza o disco partilhado. O ambiente do Azure esses VMs são implementadas no deverá resolver VMs por nome e os VMs devem estar no mesmo domínio. Este é verdadeiro para apenas Azure e implementações cruzada local. Existem algumas considerações especiais à volta de implementar a escuta de grupo de disponibilidade (não devem ser confundidas com o conjunto de disponibilidade de Azure) do SQL Server uma vez que o Azure neste ponto específico não permite a basta criar um objeto de AD/DNS, tal como é possível no local. Por conseguinte, alguns passos de instalação diferente são necessários para ultrapassar o comportamento específico do Azure.

Algumas considerações utilizando um serviço de grupo de disponibilidade escuta são:

* Utilizar uma escuta de grupo de disponibilidade só é possível fazer com o Windows Server 2012 ou Windows Server 2012 R2 como convidado SO da VM. Para o Windows Server 2012 precisa para se certificar de que esta correção é aplicada: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2 Esta correção não existir e AlwaysOn precisa de ser utilizada da mesma forma que espelhando as bases de dados ao especificar um parceiro activação pós-falha na cadeia de ligações (concluído através do SAP default.pfl parâmetro dbs/mss/servidor – ver nota SAP [965908]).
* Ao utilizar um serviço de escuta do grupo de disponibilidade, VMs a base de dados tem de estar ligada a um dedicada Balanceador de carga. Resolução do nome em implementações apenas na nuvem quer seria necessário VMs todos os de um sistema SAP (servidores de aplicações, DBMS servidor e (um) servidor SCS) são na mesma rede virtual ou seriam exigir a partir de uma camada de aplicação SAP a manutenção do ficheiro etc\host para obter os nomes VM da VMs de servidor SQL resolvidos. Para evitar Azure está a atribuir novos endereços IP nos casos em que ambos os VMs ocasionalmente encerramento, um deve atribuir endereços IP estáticos para interfaces de aqueles rede VMs na configuração AlwaysOn (que define um endereço IP estático é descrito [neste] [ virtual-networks-reserved-private-ip] artigo) [comentário]: <> (antigos blogues) [comentário]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx> <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Existem especiais passos necessários quando criar a configuração do cluster WSFC onde o cluster necessita de um endereço IP especial atribuído, porque o Azure com a funcionalidade atual seria atribuir o nome do cluster o mesmo endereço IP tal como o nó do cluster é criado no. Isto significa que um passo manual têm de ser executado para atribuir um endereço IP diferente ao cluster.
* O serviço de grupo de disponibilidade escuta vai ser criado no Azure com os pontos finais de TCP/IP que estão atribuídos as VMs executar as principais e secundárias réplicas do grupo de disponibilidade.
* Poderão existir uma necessidade de seguro estes pontos finais com ACL.

[comentário]: <>  (Blogue antigo TODO)
[comentário]: <>  (Os passos detalhados e necessidades de instalar uma configuração AlwaysOn no Azure são mais cómodas quando observar a [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups] disponíveis tutorial)
[comentário]: <>  (Pré-configurado AlwaysOn configuração através da Galeria Azure < https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comentário]: <>  (Criar um serviço de grupo de disponibilidade escuta é melhor descrito tutorial [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comentário]: <>  (Proteger os pontos finais rede com ACL são explicados melhor aqui:)
[comentário]: <>  (* < https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>)
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

É possível implementar um grupo de disponibilidade do SQL Server AlwaysOn através de diferentes regiões de Azure também. Esta funcionalidade irá tirar partido de conectividade do Azure VNet para Vnet ([mais detalhes][virtual-networks-configure-vnet-to-vnet-connection]).
[comentário]: <> (TODO antigo blogue) [comentário]: <> (na configuração do SQL Server grupos de Disponibilidade AlwaysOn num cenário é aqui descrita: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Resumo do SQL Server elevada disponibilidade no Azure
Tendo em conta o facto de armazenamento do Windows Azure está a proteger o conteúdo, não existe um menos motivo para impor uma imagem em espera activo. Isto significa que o seu cenário de elevada disponibilidade precisa apenas proteger contra casos seguintes:

* A indisponibilidade da VM como um todo devido a manutenção no servidor de cluster no Azure ou outras razões
* Problemas de software no instância do SQL Server
* Proteger a partir do erro manual onde dados obtém eliminados e não é necessária uma recuperação no momento

A opção correspondentes tecnologias um pode exigir que os dois primeiros casos podem ser cobertos por espelhando as bases de dados ou AlwaysOn, Considerando que o terceiro caso só pode ser coberto por envio de registo.

Terá de saldo a configuração mais complexa do AlwaysOn, comparado com espelhando as bases de dados, com as vantagens da AlwaysOn. Podem ser listadas essas vantagens como:

*   Legíveis réplicas secundárias.
*   Cópias de segurança de réplicas secundárias.
*   Melhor escalabilidade.
*   Mais uma réplicas secundário.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Geral SQL Server para SAP no Azure resumo
Existem muitos recomendações neste guia e recomendamos que lê-la mais de uma vez antes de planear a implementação do Azure. Em geral, no entanto, certifique-se seguir o DBMS geral dez principal no Azure pontos específicos:

[comentário]: <> débito  (2.3 mais elevados que o que? Mais uma VHD?)
1. Utilize a versão mais recente do DBMS, como o SQL Server 2014, que tem as maioria das vantagens no Azure. Para o SQL Server, este é o SQL Server 2012 SP1 CU4 que iria incluem a funcionalidade de segurança compara armazenamento do Windows Azure. No entanto, juntamente com SAP seria recomendamos, pelo menos, SQL Server 2014 SP1 CU1 ou SQL Server 2012 SP2 e o CU mais recente.
1. Planear cuidadosamente seu horizontal de sistema do SAP no Azure equilibrar o esquema de ficheiro de dados e o Azure restrições:
    * Não ter VHDs demasiadas, mas tem espaço suficiente para se certificar de que possa aceder às sua IOPS necessários.
    * Lembre-se de que IOPS também estão limitados por conta de armazenamento do Azure e que as contas de armazenamento estão limitadas dentro de cada subscrição Azure ([mais detalhes][azure-subscription-service-limits]). 
    * Apenas faixa ao longo de VHDs se precisar de alcançar um débito superior.
1. Nunca instalar software ou colocar os ficheiros que requerem persistente na unidade de D:\ como é que não sejam permanentes e nada nesta unidade serão perdido na reiniciar o computador Windows.
1. Não utilize Azure VHD colocação em cache para armazenamento padrão do Windows Azure.
1. Não utilize Azure contas de armazenamento de replicadas geo.  Utilize localmente redundantes das cargas de trabalho DBMS.
1. Utilize a solução HA/DR do seu fornecedor DBMS para criar uma réplica da base de dados.
1. Utilize sempre utilizar resolução do nome, não depender de endereços IP.
1. Utilize a compressão de base de dados mais alta possível. Para o SQL Server, esta é a compressão de página.
1. Tenha cuidado quando utilizar imagens do SQL Server do Azure Marketplace. Se utilizar o SQL Server uma, tem de alterar o agrupamento de instância antes de instalar qualquer sistema SAP NetWeaver no mesmo.
1. Instalar e configurar a monitorização de anfitrião de SAP para Azure conforme descrito no [guia de implementação] [guia de implementação].

## <a name="specifics-to-sap-ase-on-windows"></a>Detalhes do SAP Auxiliar no Windows
A partir do Microsoft Azure, pode migrar facilmente as aplicações SAP Auxiliar existentes para máquinas virtuais do Azure. Auxiliar de SAP numa máquina Virtual permite-lhe reduzir o custo total da propriedade de implementação, gestão e manutenção das aplicações de boca empresariais ao migrar facilmente estas aplicações para o Microsoft Azure. Com o Auxiliar SAP numa máquina de Virtual do Azure, os administradores e programadores ainda podem utilizar as ferramentas de administração que estão disponível no local e desenvolvimento do mesmo.

Existe um SLA para o máquinas virtuais do Azure que podem ser encontradas aqui: <https://azure.microsoft.com/support/legal/sla>

Estamos a certeza de que a Microsoft Azure alojado máquinas virtuais fará muito bem em comparação com outros ofertas de virtualização da nuvem público, mas resultados individuais podem variar. SAP Dimensionar SAP números do SAP diferentes certificadas que uma nota SAP separada [1928533]serão fornecidos VM SKUs.

Declarações e recomendações no que diz respeito a utilização de armazenamento do Windows Azure, implementação do SAP VMs ou SAP monitorização aplicam implementações do SAP Auxiliar juntamente com as aplicações SAP, tal como indicado em todo os primeiros quatro capítulos deste documento.

### <a name="sap-ase-version-support"></a>Suporte de versão Auxiliar do SAP 
SAP atualmente suporta SAP Auxiliar versão 16,0 para utilizar com os produtos do conjunto de aplicações do SAP empresas. Todas as atualizações para o servidor do SAP Auxiliar ou JDBC e ODBC controladores para ser utilizado com produtos do conjunto de aplicações do SAP empresas são fornecidas exclusivamente através de mercado de serviço SAP na: <https://support.sap.com/swdc>.

Para instalações no local, não transfira as atualizações para o servidor de SAP Auxiliar ou para os controladores JDBC e ODBC diretamente a partir de Web sites de Sybase. Para obter informações detalhadas sobre patches que são suportadas para utilização com o conjunto de aplicações do SAP empresas produtos no local e em máquinas virtuais do Azure consulte as seguintes notas SAP:

* [1590719]
* [1973241]
 
Podem encontrar informações gerais sobre como executar o conjunto de aplicações do SAP empresas no SAP Auxiliar no [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração de Auxiliar SAP para SAP relacionados com SAP Auxiliar instalações no Azure VMs

#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura de implementação de Auxiliar o SAP
Em conformidade com a descrição geral, SAP Auxiliar executáveis deverão ser localizado ou instalado na unidade de sistema do VHD base a VM (unidade c:\). Normalmente, a maior parte das SAP Auxiliar sistema e ferramentas de bases de dados não é realmente utilizadas definitivamente pela carga de trabalho SAP NetWeaver. Por conseguinte sistema e ferramentas de bases de dados (modelo global, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecem no também a C:\drive. 

Uma exceção dever-se a base de dados temporário que contém todas as tabelas de trabalho e temporárias tabelas criadas pela Auxiliar SAP, que em caso de alguns ERP SAP e todos os p/b das cargas de trabalho pode ser necessário volume de dados mais elevado ou volume de operações de e/s que não se ajustam a VHD base a VM original (unidade c:\).
 
Dependendo da versão do SAPInst/SWPM utilizado para instalar o sistema, pode conter a base de dados:

* Uma única tempdb do SAP Auxiliar que é criado ao instalar o Auxiliar do SAP
* Um tempdb SAP Auxiliar criado pela instalação do SAP Auxiliar e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb SAP Auxiliar criado pela instalação do SAP Auxiliar e um tempdb adicional que foi criada manualmente (por exemplo, seguintes SAP nota [1752266]) para cumprir requisitos de tempdb específico ERP/p/b

Em caso de sistemas ERP específica ou todas as cargas de trabalho p/b faz sentido, no que diz respeito desempenho, para manter os dispositivos de tempdb dos tempdb para além disso criado (por SWPM ou manualmente) numa unidade que não seja c:\. se sem tempdb adicional não existir-é recomendado para criar uma (nota SAP [1752266]).

Para tais sistemas os passos seguintes devem ser executados para tempdb para além disso criado:

* Deslocar-se no primeiro dispositivo tempdb para o primeiro dispositivo da base de dados do SAP
* Adicionar tempdb dispositivos para cada um dos VHDs que contém uma dispositivos da base de dados do SAP

Esta configuração permite tempdb quer consumir mais espaço que forneçam a unidade de sistema. Como uma referência de um pode verificar os tamanhos de dispositivo tempdb nos sistemas existentes que se deslocam no local. Ou tal configuração permitirá números IOPS contra tempdb que não pode ser fornecida com a unidade de sistema. Novamente os sistemas de que estão a executar o no local podem ser utilizados para monitorizar a carga de trabalho e/s contra tempdb.

Nunca coloca quaisquer dispositivos SAP Auxiliar na unidade D:\ da VM. Isto também se aplica ao tempdb, mesmo se os objetos a par de tempdb são apenas temporários.

#### <a name="impact-of-database-compression"></a>Impacto da base de dados compressão
Em configurações onde largura de banda e/s pode tornar-se um fator de limitação, todas as medidas que reduz IOPS poderão ajudar a esticar a carga de trabalho um pode executar um cenário de IaaS como Azure. Por conseguinte, é fortemente recomendado para se certificar de que a compressão de SAP Auxiliar é utilizada antes de carregar uma base de dados existente do SAP para Azure.

Recomendação para efetuar compressão antes de carregar para Azure se não for implementada já é dado terminar vários motivos:

* A quantidade de dados ser carregado para Azure é inferior
* A duração da execução compressão é mais curta partindo do princípio que podem ser utilizados mais forte do hardware com mais CPUs ou largura de banda e/s superior ou inferior e/s latência no local
* Tamanho da base de dados mais pequeno pode conduzir a menos os custos de atribuição de disco

Compressão de dados e LOB trabalhar numa VM alojada no máquinas virtuais do Azure, como o faz no local. Para obter mais detalhes sobre como verificar se compressão já está a ser utilizado numa base de dados existente SAP Auxiliar verifique SAP nota [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar as instâncias da base de dados
Para sistemas SAP que estão a utilizar o SAP Auxiliar como plataforma base de dados, o DBACockpit está acessível como janelas do browser incorporado numa única transação da DBACockpit ou como Webdynpro. No entanto a funcionalidade completa para monitorizar e administrar a base de dados está disponível na aplicação Webdynpro da apenas a DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela aplicação Webdynpro da DBACockpit. Siga SAP nota [1245200] para ativar a utilização de webdynpros e gerar aqueles necessários. Quando seguir as instruções nas notas acima também irá configurar o Gestor de comunicação de Internet (icm) juntamente com as portas a ser utilizado para ligações http e https. A predefinição do http tem o seguinte aspeto:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
>
> ICM/server_port_1 = PROT = HTTPS, porta = 443$ $, PROCTIMEOUT = 600, tempo limite = 600

e as ligações geradas numa única transação da DBACockpit terá uma aspeto semelhante ao seguinte:

> https://`<fullyqualifiedhostname`>: sap/44300/bc/sap/webdynpro/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: sap/8000/bc/sap/webdynpro/dba_cockpit

Dependendo se e como a máquina de Virtual Azure que aloja o sistema SAP está ligado através do site para site múltiplos sites ou ExpressRoute (implementação de publicação em local), é necessário para se certificar de que ICM está a utilizar um nome de anfitrião completamente qualificado que pode ser resolvida no computador onde está a tentar abrir DBACockpit a partir de. Consulte SAP nota [773830] para compreender como ICM determina o nome do anfitrião completamente qualificado dependendo parâmetros de perfil e definir parâmetros icm/host_name_full explicitamente se necessário.

Se implementado a VM num cenário de apenas na nuvem sem conectividade de publicação em local entre no local e Azure, tem de definir um endereço IP público e um domainlabel. O formato do nome de DNS público da VM terá, em seguida, o seguinte aspeto:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Obter mais detalhes relacionados com o nome DNS podem ser encontradas [aqui][virtual-machines-azurerm-versus-azuresm].

Definir um parâmetro de perfil de SAP icm/host_name_full para o nome de DNS da VM Azure a ligação poderá ter um aspeto semelhante a:

> sap/https://mydomainlabel.westeurope.cloudapp.NET:44300/bc/sap/webdynpro/dba_cockpit

> sap/http://mydomainlabel.westeurope.cloudapp.NET:8000/bc/sap/webdynpro/dba_cockpit

Neste caso precisa de efetuar-se de que:

* Adicionar regras de entrada ao grupo de segurança de rede no Portal do Azure para as portas TCP/IP utilizadas para comunicar com ICM
* Adicionar regras de entrada para a configuração de Firewall do Windows para as portas TCP/IP utilizados para comunicar com o ICM

Para um automatizado importados de todas as correções disponíveis, é recomendável para aplicar periodicamente a coleção de correção SAP nota aplicáveis à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar mais informações sobre DBA voo para SAP Auxiliar na notas SAP que se seguem:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação Auxiliar do SAP
Quando implementar o Auxiliar SAP para Azure a sua metodologia de cópia de segurança tem de ser revista. Mesmo se o sistema não é um sistema produtivo, a base de dados do SAP alojado pelo SAP Auxiliar deve ser cópia de segurança periodicamente. Uma vez que o armazenamento do Windows Azure mantém três imagens, uma cópia de segurança agora é menos importante em relação à compensadores uma falha de armazenamento. O motivo principal para manutenção de um plano de cópia de segurança e restauro adequado é mais do que pode compensar erros lógicos/manual, fornecendo ponto no capacidades de recuperação do tempo. Para que o objetivo é uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para outro sistema de propagar ao copiar a base de dados existente. Por exemplo, poderia transferir a partir de uma configuração de SAP 2 camada para a configuração do mesmo sistema de um sistema de níveis de 3 por restaurar uma cópia de segurança.

Cópias de segurança e restaurar uma base de dados no Azure funciona da mesma forma como o faz no local. Consulte o artigo SAP notas:

* [1588316]
* [1585981]

Para obter detalhes sobre criar configurações de informação e cópias de segurança de agendamento. Dependendo do seu estratégia e as suas necessidades, pode configurar a base de dados e registo regista para do disco para um dos VHDs existentes ou adicionar um VHD adicional para a cópia de segurança.  Para reduzir o risco de perda de dados em caso de um erro que é recomendado utilizar um VHD onde não se encontra nenhum dispositivo de base de dados.

Para além de dados e LOB compressão SAP Auxiliar também oferece compressão cópia de segurança. Ocupar menos espaço com as informações de estado da base de dados e registo é recomendado para utilizar a compressão de cópia de segurança. Consulte o artigo SAP nota [1588316] para obter mais informações. Comprimir a cópia de segurança também é crucial para reduzir a quantidade de dados para serem transferidas se planeia transferir cópias de segurança ou VHDs que contém a cópia de segurança informações de estado a partir do Azure Virtual Machine para no local.

Não utilize unidade D:\ como base de dados ou o registo de destino de informações de estado.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para cópias de segurança/restaura
Tal como base implementações, desempenho de cópia de segurança/restauro é dependente volumes quantos podem ser lidos em paralelo e o que poderá ser débito desses volumes. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança poderá ser reproduzido um papel significativo no VMs com apenas até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dispositivos de base de dados mais pequeno débito global no modo de leitura
* Mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança
* Os destinos menos (faixa diretórios, VHDs) para escrever a cópia de segurança, o menor o débito

Para aumentar o número de destinos para escrever que existem duas opções que pode ser utilizado/combinados consoante as suas necessidades:

* Dividir o volume de destino de cópia de segurança sobre vários VHDs montados para melhorar o débito IOPS nesse volume às riscas
* Criar uma cópia da configuração no nível de Auxiliar SAP, que utiliza mais do que um diretório de destino para escrever a informação de estado

Dividir um volume através de vários VHDs montados debatido anteriormente neste guia. Para obter mais informações sobre como utilizar vários directórios na configuração de informação do SAP Auxiliar, consulte a documentação sobre sp_config_dump procedimento armazenado que é utilizado para criar a configuração de informação sobre o [Centro de Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de falhas com Azure VMs

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o Auxiliar SAP SAP Sybase replicação servidor SRS () fornece uma solução quente em espera para transferir as transações de base de dados para uma localização distante modo assíncrono. 

A instalação e operação do SRS também funciona funcional numa VM alojada no Azure Máquina Virtual serviços como o faz no local.

Auxiliar HADR através do servidor de replicação do SAP é planeado com um lançamento futuro. Serão testado com e lançada para plataformas do Microsoft Azure assim que está disponível.

## <a name="specifics-to-sap-ase-on-linux"></a>Detalhes do SAP Auxiliar no Linux

A partir do Microsoft Azure, pode migrar facilmente as aplicações SAP Auxiliar existentes para máquinas virtuais do Azure. Auxiliar de SAP numa máquina Virtual permite-lhe reduzir o custo total da propriedade de implementação, gestão e manutenção das aplicações de boca empresariais ao migrar facilmente estas aplicações para o Microsoft Azure. Com o Auxiliar SAP numa máquina de Virtual do Azure, os administradores e programadores ainda podem utilizar as ferramentas de administração que estão disponível no local e desenvolvimento do mesmo.

Para implementar o Azure VMs é importante saber a SLA oficial que pode ser encontradas aqui: <https://azure.microsoft.com/support/legal/sla>

Informações de redimensionamento do SAP e uma lista de SAP certificada VM SKUs serão fornecidos na nota SAP [1928533]. SAP adicional dimensionamento documentos para máquinas virtuais do Azure pode ser encontrada aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Declarações e recomendações no que diz respeito a utilização de armazenamento do Windows Azure, implementação do SAP VMs ou SAP monitorização aplicam implementações do SAP Auxiliar juntamente com as aplicações SAP, tal como indicado em todo os primeiros quatro capítulos deste documento.

As duas seguintes SAP notas incluem informações gerais sobre Auxiliar no Linux e Auxiliar na nuvem:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte de versão Auxiliar do SAP 
SAP atualmente suporta SAP Auxiliar versão 16,0 para utilizar com os produtos do conjunto de aplicações do SAP empresas. Todas as atualizações para o servidor do SAP Auxiliar ou JDBC e ODBC controladores para ser utilizado com produtos do conjunto de aplicações do SAP empresas são fornecidas exclusivamente através de mercado de serviço SAP na: <https://support.sap.com/swdc>.

Para instalações no local, não transfira as atualizações para o servidor de SAP Auxiliar ou para os controladores JDBC e ODBC diretamente a partir de Web sites de Sybase. Para obter informações detalhadas sobre patches que são suportadas para utilização com o conjunto de aplicações do SAP empresas produtos no local e em máquinas virtuais do Azure consulte as seguintes notas SAP:

* [1590719]
* [1973241]
 
Podem encontrar informações gerais sobre como executar o conjunto de aplicações do SAP empresas no SAP Auxiliar no [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração de Auxiliar SAP para SAP relacionados com SAP Auxiliar instalações no Azure VMs

#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura de implementação de Auxiliar o SAP
Em conformidade com a descrição geral, SAP Auxiliar executáveis devem ser localizado ou instalado para o sistema de ficheiros de raiz da VM (/sybase). Normalmente, a maior parte das SAP Auxiliar sistema e ferramentas de bases de dados não é realmente utilizadas definitivamente pela carga de trabalho SAP NetWeaver. Por conseguinte sistema e ferramentas de bases de dados (modelo global, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecem no também o sistema de ficheiros de raiz. 

Uma exceção dever-se a base de dados temporário que contém todas as tabelas de trabalho e temporárias tabelas criadas pela Auxiliar SAP, que em caso de alguns ERP SAP e todos os p/b das cargas de trabalho pode ser necessário volume de dados mais elevado ou volume de operações de e/s que não se ajustam a disco de SO a VM original.
 
Dependendo da versão do SAPInst/SWPM utilizado para instalar o sistema, pode conter a base de dados:

* Uma única tempdb do SAP Auxiliar que é criado ao instalar o Auxiliar do SAP
* Um tempdb SAP Auxiliar criado pela instalação do SAP Auxiliar e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb SAP Auxiliar criado pela instalação do SAP Auxiliar e um tempdb adicional que foi criada manualmente (por exemplo, seguintes SAP nota [1752266]) para cumprir requisitos de tempdb específico ERP/p/b

Em caso de sistemas ERP específica ou todas as cargas de trabalho p/b faz sentido, no que diz respeito desempenho, para manter os dispositivos de tempdb dos tempdb para além disso criado (por SWPM ou manualmente) num sistema de ficheiros em separado que pode ser representado por um disco de dados de Azure único ou um RAID Linux que abrangem vários discos dados Azure. Se não existir sem tempdb adicional-é recomendado para criar uma (nota SAP [1752266]).

Para tais sistemas os passos seguintes devem ser executados para tempdb para além disso criado:

* Deslocar-se o diretório tempdb primeiro para o primeiro sistema de ficheiros da base de dados do SAP
* Adicionar tempdb directórios para cada um dos VHDs que contém um sistema de ficheiros da base de dados do SAP

Esta configuração permite tempdb quer consumir mais espaço que forneçam a unidade de sistema. Como uma referência de um pode verificar os tamanhos de diretório tempdb nos sistemas existentes que se deslocam no local. Ou tal configuração permitirá números IOPS contra tempdb que não pode ser fornecida com a unidade de sistema. Novamente os sistemas de que estão a executar o no local podem ser utilizados para monitorizar a carga de trabalho e/s contra tempdb.

Nunca coloca quaisquer directórios SAP Auxiliar /mnt ou /mnt/resource da VM. Isto também se aplica ao tempdb, mesmo se os objetos a par de tempdb são apenas temporários porque /mnt ou /mnt/resource é um espaço temp Azure VM predefinido que não é persistente. Podem encontrar mais detalhes sobre o espaço temp Azure VM [neste] artigo[virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impacto da base de dados compressão
Em configurações onde largura de banda e/s pode tornar-se um fator de limitação, todas as medidas que reduz IOPS poderão ajudar a esticar a carga de trabalho um pode executar um cenário de IaaS como Azure. Por conseguinte, é fortemente recomendado para se certificar de que a compressão de SAP Auxiliar é utilizada antes de carregar uma base de dados existente do SAP para Azure.

Recomendação para efetuar compressão antes de carregar para Azure se não for implementada já é dado terminar vários motivos:

* A quantidade de dados ser carregado para Azure é inferior
* A duração da execução compressão é mais curta partindo do princípio que podem ser utilizados mais forte do hardware com mais CPUs ou largura de banda e/s superior ou inferior e/s latência no local
* Tamanho da base de dados mais pequeno pode conduzir a menos os custos de atribuição de disco

Compressão de dados e LOB trabalhar numa VM alojada no máquinas virtuais do Azure, como o faz no local. Para obter mais detalhes sobre como verificar se compressão já está a ser utilizado numa base de dados existente SAP Auxiliar verifique SAP nota [1750510]. Consulte também SAP nota [2121797] para informações adicionais sobre a compressão de base de dados.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar as instâncias da base de dados
Para sistemas SAP que estão a utilizar o SAP Auxiliar como plataforma base de dados, o DBACockpit está acessível como janelas do browser incorporado numa única transação da DBACockpit ou como Webdynpro. No entanto a funcionalidade completa para monitorizar e administrar a base de dados está disponível na aplicação Webdynpro da apenas a DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela aplicação Webdynpro da DBACockpit. Siga SAP nota [1245200] para ativar a utilização de webdynpros e gerar aqueles necessários. Quando seguir as instruções nas notas acima também irá configurar o Gestor de comunicação de Internet (icm) juntamente com as portas a ser utilizado para ligações http e https. A predefinição do http tem o seguinte aspeto:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
>
> ICM/server_port_1 = PROT = HTTPS, porta = 443$ $, PROCTIMEOUT = 600, tempo limite = 600

e as ligações geradas numa única transação da DBACockpit terá uma aspeto semelhante ao seguinte:

> https://`<fullyqualifiedhostname`>: sap/44300/bc/sap/webdynpro/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: sap/8000/bc/sap/webdynpro/dba_cockpit

Dependendo se e como a máquina de Virtual Azure que aloja o sistema SAP está ligado através do site para site múltiplos sites ou ExpressRoute (implementação de publicação em local), é necessário para se certificar de que ICM está a utilizar um nome de anfitrião completamente qualificado que pode ser resolvida no computador onde está a tentar abrir DBACockpit a partir de. Consulte SAP nota [773830] para compreender como ICM determina o nome do anfitrião completamente qualificado dependendo parâmetros de perfil e definir parâmetros icm/host_name_full explicitamente se necessário.

Se implementado a VM num cenário de apenas na nuvem sem conectividade de publicação em local entre no local e Azure, tem de definir um endereço IP público e um domainlabel. O formato do nome de DNS público da VM terá, em seguida, o seguinte aspeto:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Obter mais detalhes relacionados com o nome DNS podem ser encontradas [aqui][virtual-machines-azurerm-versus-azuresm].

Definir um parâmetro de perfil de SAP icm/host_name_full para o nome de DNS da VM Azure a ligação poderá ter um aspeto semelhante a:

> sap/https://mydomainlabel.westeurope.cloudapp.NET:44300/bc/sap/webdynpro/dba_cockpit
> 
> sap/http://mydomainlabel.westeurope.cloudapp.NET:8000/bc/sap/webdynpro/dba_cockpit

Neste caso precisa de efetuar-se de que:

* Adicionar regras de entrada ao grupo de segurança de rede no Portal do Azure para as portas TCP/IP utilizadas para comunicar com ICM
* Adicionar regras de entrada para a configuração de Firewall do Windows para as portas TCP/IP utilizados para comunicar com o ICM

Para um automatizado importados de todas as correções disponíveis, é recomendável para aplicar periodicamente a coleção de correção SAP nota aplicáveis à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar mais informações sobre DBA voo para SAP Auxiliar na notas SAP que se seguem:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação Auxiliar do SAP
Quando implementar o Auxiliar SAP para Azure a sua metodologia de cópia de segurança tem de ser revista. Mesmo se o sistema não é um sistema produtivo, a base de dados do SAP alojado pelo SAP Auxiliar deve ser cópia de segurança periodicamente. Uma vez que o armazenamento do Windows Azure mantém três imagens, uma cópia de segurança agora é menos importante em relação à compensadores uma falha de armazenamento. O motivo principal para manutenção de um plano de cópia de segurança e restauro adequado é mais do que pode compensar erros lógicos/manual, fornecendo ponto no capacidades de recuperação do tempo. Para que o objetivo é uma das cópias de segurança de utilização para restaurar a base de dados para um determinado ponto no tempo ou utilizar as cópias de segurança no Azure para outro sistema de propagar ao copiar a base de dados existente. Por exemplo, poderia transferir a partir de uma configuração de SAP 2 camada para a configuração do mesmo sistema de um sistema de níveis de 3 por restaurar uma cópia de segurança.

Cópias de segurança e restaurar uma base de dados no Azure funciona da mesma forma como o faz no local. Consulte o artigo SAP notas:

* [1588316]
* [1585981]

Para obter detalhes sobre criar configurações de informação e cópias de segurança de agendamento. Dependendo do seu estratégia e as suas necessidades, pode configurar a base de dados e registo regista para do disco para um dos VHDs existentes ou adicionar um VHD adicional para a cópia de segurança.  Para reduzir o risco de perda de dados em caso de um erro que é recomendado utilizar um VHD onde não se encontra nenhum diretório/ficheiro de base de dados.

Para além de dados e LOB compressão SAP Auxiliar também oferece compressão cópia de segurança. Ocupar menos espaço com as informações de estado da base de dados e registo é recomendado para utilizar a compressão de cópia de segurança. Consulte o artigo SAP nota [1588316] para obter mais informações. Comprimir a cópia de segurança também é crucial para reduzir a quantidade de dados para serem transferidas se planeia transferir cópias de segurança ou VHDs que contém a cópia de segurança informações de estado a partir do Azure Virtual Machine para no local.

Não utilize o Azure VM espaço temp /mnt ou /mnt/resource como base de dados ou o registo de destino de informações de estado.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para cópias de segurança/restaura
Tal como base implementações, desempenho de cópia de segurança/restauro é dependente volumes quantos podem ser lidos em paralelo e o que poderá ser débito desses volumes. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança poderá ser reproduzido um papel significativo no VMs com apenas até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dispositivos de base de dados mais pequeno débito global no modo de leitura
* Mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança
* Os destinos menos (Linux software RAID, VHDs) para escrever a cópia de segurança, o menor o débito

Para aumentar o número de destinos para escrever que existem duas opções que pode ser utilizado/combinados consoante as suas necessidades:

* Dividir o volume de destino de cópia de segurança sobre vários VHDs montados para melhorar o débito IOPS nesse volume às riscas
* Criar uma cópia da configuração no nível de Auxiliar SAP, que utiliza mais do que um diretório de destino para escrever a informação de estado

Dividir um volume através de vários VHDs montados debatido anteriormente neste guia. Para obter mais informações sobre como utilizar vários directórios na configuração de informação do SAP Auxiliar, consulte a documentação sobre sp_config_dump procedimento armazenado que é utilizado para criar a configuração de informação sobre o [Centro de Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de falhas com Azure VMs

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o Auxiliar SAP SAP Sybase replicação servidor SRS () fornece uma solução quente em espera para transferir as transações de base de dados para uma localização distante modo assíncrono. 

A instalação e operação do SRS também funciona funcional numa VM alojada no Azure Máquina Virtual serviços como o faz no local.

Auxiliar HADR através do servidor do SAP replicação não é suportado neste ponto específico. Poderá ser testado com e lançada para plataformas do Microsoft Azure no futuro.

## <a name="specifics-to-oracle-database-on-windows"></a>Especificações da base de dados Oracle no Windows
Desde midyear 2013, o Oracle software é suportado pelo Oracle para executar no Microsoft Windows Hyper-V e Azure. Leia este artigo para obter mais detalhes sobre o suporte de geral do Windows Hyper-V e Azure pela Oracle: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Após o suporte técnico da geral, o cenário específico para as aplicações SAP tirar partido da bases de dados Oracle é suportado também. Detalhes são com nome nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão Oracle
Todos os detalhes sobre Oracle versões e correspondente SO que são suportadas para executar SAP num Oracle em máquinas virtuais do Azure podem ser encontrados na nota SAP seguintes [2039619]

Pode encontrar informações gerais sobre como executar o conjunto de aplicações do SAP empresas no Oracle no SCN: <https://scn.sap.com/community/oracle>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração de Oracle para as instalações do SAP no Azure VMs

#### <a name="storage-configuration"></a>Configuração de armazenamento
Apenas única ocorrência Oracle utilizando formatado em NTFS discos é suportado. Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS com base em discos VHD. Estes VHDs são instalados para a VM Azure e são baseados no armazenamento de BLOBS do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualquer tipo de unidades de rede ou partilhas remotas como ficheiro Azure serviços:
 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
**não** são suportadas para ficheiros de base de dados Oracle!

Utilizar o Azure VHDs com base em armazenamento de BLOBS do Azure página, as declarações constantes este documento em capítulo [colocação em cache para VMs e VHDs] [dbms-guia 2.1] e [armazenamento do Windows Azure] [dbms-guia 2.3] aplicam-se para implementações com a base de dados do Oracle também.

Tal como é explicado anteriormente a parte do documento geral, existem quotas em débito IOPS para Azure VHDs. As quotas exatas dependendo do tipo VM utilizadas. Uma lista dos tipos VM com as suas próprias quotas pode ser encontrada [aqui][virtual-machines-sizes]

Para identificar os tipos de Azure VM suportados, consulte SAP nota [1928533]

Desde que a quota IOPS atual por disco satisfaz os requisitos, é possível armazenar todos os ficheiros de base de dados um único montada Azure VHD. 

Se forem necessárias mais IOPS, recomenda-se vivamente para utilizar agrupamentos de armazenamento de janela (apenas disponível no Windows Server 2012 e superior) ou repartição do Windows para o Windows 2008 R2 para criar um dispositivo lógico grande através de vários discos VHD montados. Consulte também capítulo [Software RAID] [dbms-guia 2.2] deste documento. Esta abordagem simplifica o overhead administrativo para gerir o espaço em disco e evita esforço para distribuir manualmente ficheiros por vários VHDs montados.

#### <a name="backup--restore"></a>Fazer cópia de segurança / restaurar
Para cópia de segurança / restaure a funcionalidade, a BR SAP * ferramentas para Oracle são suportadas da mesma forma como os sistemas operativos do Windows Server e Hyper-V padrão. Gestor de recuperação de Oracle (RMAN) também é suportado para cópias de segurança no disco e restaurar a partir do disco.

#### <a name="high-availability"></a>Disponibilidade de alta
[comentário]: <>  (ligação refere-se para ASM)
Oracle dados atento é suportado para elevada disponibilidade e efeitos de recuperação de falhas. Detalhes podem ser encontrados neste [] [ virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como monitorização de conjuntos de disponibilidade do Azure ou SAP aplicam-se tal como descrito nos três primeiros capítulos deste documento para implementações do VMs com a base de dados do Oracle também.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Detalhes do SAP MaxDB base de dados do Windows

### <a name="sap-maxdb-version-support"></a>Suporte de versão MaxDB do SAP
SAP atualmente suporta SAP MaxDB versão 7.9 para utilizar com os produtos SAP NetWeaver baseadas no Azure. Todas as atualizações para o servidor SAP MaxDB ou JDBC e ODBC controladores para ser utilizado com produtos do SAP NetWeaver são fornecidas exclusivamente através de mercado de serviço SAP na <https://support.sap.com/swdc>.
Informações gerais sobre como executar SAP NetWeaver no SAP MaxDB podem ser encontradas em <https://scn.sap.com/community/maxdb>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Tipos de versões do Microsoft Windows e Azure VM suportados para SAP MaxDB DBMS
Para localizar a versão suportada do Microsoft Windows do SAP MaxDB DBMS no Azure, consulte:

* [Matriz de disponibilidade do SAP produto (PAM)][sap-pam]
* SAP nota [1928533]

Recomenda-se vivamente para utilizar a versão mais recente do sistema operativo Microsoft Windows, Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentação de MaxDB SAP disponíveis
Pode encontrar a lista actualizada de documentação SAP MaxDB a nota SAP seguintes [767598]
    
### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP MaxDB para as instalações do SAP no Azure VMs

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração de armazenamento
Práticas recomendadas do armazenamento Azure para SAP MaxDB siga as recomendações gerais mencionadas na capítulo [estrutura de uma implementação RDBMS] [dbms-guia de-2].

> [AZURE.IMPORTANT] Como outras bases de dados, SAP MaxDB também tem dados e ficheiros de registo. No entanto, SAP MaxDB terminologia o termo correcto é "volume" (não "ficheiro"). Por exemplo, existem SAP MaxDB volumes de dados e registo. Não confunda estes com volumes de disco SO. 

Resumindo tem de:

* Defina a conta de armazenamento Azure que detém os volumes de dados e de registo do SAP MaxDB (ou seja, ficheiros) para o **Local redundantes armazenamento (LRS)** como especificado no capítulo [armazenamento do Windows Azure] [dbms-guia 2.3].
* Separe o caminho de es para volumes de dados do SAP MaxDB (ou seja, ficheiros) a partir do caminho de es para volumes de registo (ou seja, ficheiros). Isto significa que volumes de dados do SAP MaxDB (ou seja, ficheiros) têm de ser instalada numa unidade lógica e volumes de registo do SAP MaxDB (ou seja, ficheiros) têm de ser instalado em outra unidade lógica.
* Definir a cache de ficheiro adequado para cada BLOBs do Azure, dependendo se utilizá-lo para SAP MaxDB dados ou registo volumes (ou seja, ficheiros) e, se utilizar o Azure padrão ou de armazenamento do Windows Azure Premium, conforme descrito capítulo [colocação em cache para VMs] [dbms-guia 2.1].
* Desde que a quota IOPS atual por disco satisfaz os requisitos, é possível armazenar todos os volumes de dados num único VHD Azure montada e também armazenar todos os volumes de registo de base de dados no outro VHD Azure montada único.
* Se mais IOPS e/ou o espaço é necessário, recomenda-se vivamente para utilizar o Microsoft janela armazenamento conjuntos de dados (apenas disponível no Microsoft Windows Server 2012 e superior) ou repartição do Microsoft Windows para o Microsoft Windows 2008 R2 para criar um dispositivo lógico grande através de vários discos VHD montados. Consulte também capítulo [Software RAID] [dbms-guia 2.2] deste documento. Esta abordagem simplifica o overhead administrativo para gerir o espaço em disco e evita o esforço de distribuir manualmente a ficheiros através de vários VHDs montados.
* Para obter os requisitos de IOPS mais altos, pode utilizar o Azure Premium armazenamento, que se encontra disponível na série de DS e VMs da série s.

![Referência de configuração da Azure IaaS VM para SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Cópia de segurança e restauro
Quando implementar o SAP MaxDB para Azure, tem de rever a sua metodologia de cópia de segurança. Mesmo se o sistema não é um sistema produtivo, a base de dados do SAP alojado pelo SAP MaxDB deve ser cópia de segurança periodicamente. Uma vez que o armazenamento do Windows Azure mantém três imagens, uma cópia de segurança agora é menos importante em termos de proteger o seu sistema de falha de armazenamento e mais importantes falhas operacionais ou administrativas. O motivo principal para manter uma cópia de segurança inicial e restaurar plano é para que pode compensar erros manuais ou lógicos, fornecendo capacidades de recuperação em qualquer altura. Para que o objetivo é utilizar cópias de segurança para restaurar a base de dados para um determinado ponto altura ou utilizar as cópias de segurança no Azure para outro sistema de propagar ao copiar a base de dados existente. Por exemplo, poderia transferir a partir de uma configuração de SAP 2 camada para a configuração do mesmo sistema de um sistema de níveis de 3 por restaurar uma cópia de segurança.

Cópias de segurança e restaurar uma base de dados no Azure funciona da mesma forma como o faz para sistemas de no local, para que possa utilizar ferramentas de cópia de segurança/restauro SAP MaxDB padrão, que são descritas num dos documentos documentação SAP MaxDB listados na nota SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações sobre o desempenho para cópia de segurança e restauro
Tal como base implementações, é dependente volumes quantos podem ser lida em paralelo e o débito desses volumes desempenho de cópia de segurança e restauro. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança pode reproduzir um papel significativo no VMs com até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dispositivos de base de dados, no canto inferior débito de leitura global
* Mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança
* Os destinos menos (faixa diretórios, VHDs) para escrever a cópia de segurança para a parte inferior do débito

Para aumentar o número de destinos ao escrever, existem duas opções que pode utilizar, possivelmente em conjunto, consoante as suas necessidades:

* Dedicar volumes separados para cópia de segurança
* Dividir o volume de destino de cópia de segurança sobre vários VHDs montados para melhorar o débito IOPS nesse volume de disco às riscas
* Está com dificuldades dispositivos de disco lógico dedicada separado para:
    * SAP volumes de cópia de segurança MaxDB (ou seja, ficheiros)
    * SAP MaxDB volumes de dados (ou seja, ficheiros)
    * SAP MaxDB volumes de registo (ou seja, ficheiros)

Dividir um volume através de vários VHDs montados debatido anteriormente no capítulo [Software RAID] [dbms-guia 2.2] deste documento. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outros
Todos os outros tópicos gerais como monitorização de conjuntos de disponibilidade do Azure ou SAP também se aplicam conforme descrito nos três primeiros capítulos deste documento para implementações do VMs com a base de dados do SAP MaxDB.
Outras definições específicas do SAP MaxDB são transparentes para Azure VMs e são descritas nos documentos diferentes listados na nota de SAP [767598] e nas notas SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Detalhes do SAP liveCache no Windows

### <a name="sap-livecache-version-support"></a>SAP liveCache suporte versão
Versão mínima do SAP liveCache suportada em máquinas virtuais do Azure é **SAP LC/LCAPPS 10.0 SP 25** incluindo **liveCache 7.9.08.31** e **25 de compilação LCA**, disponibilizada para **EhP 2 para SAP SMS 7.0** e superior.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Tipos de versões do Microsoft Windows e Azure VM suportados para SAP liveCache DBMS
Para localizar a versão suportada do Microsoft Windows para liveCache SAP no Azure, consulte:

* [Matriz de disponibilidade do SAP produto (PAM)][sap-pam]
* SAP nota [1928533]

Recomenda-se vivamente para utilizar a versão mais recente do sistema operativo Microsoft Windows, Microsoft Windows 2012 R2. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache diretrizes de configuração para as instalações do SAP no Azure VMs

#### <a name="recommended-azure-vm-types"></a>Recomendado tipos Azure VM
Como SAP liveCache é uma aplicação que executa enormes cálculos, a quantidade e velocidade de RAM e CPU tem uma influência principal no desempenho de liveCache SAP. 

Para os tipos de Azure VM suportados pelo SAP (nota SAP [1928533]), todos os recursos de CPU virtuais atribuídos a VM são cópias por recursos de CPU físicos dedicados do hipervisor. Ocorre nenhum overprovisioning (e, consequentemente, não existe concorrência para recursos de CPU).

Da mesma forma, para todos os Azure VM instância tipos suportados pela SAP, a memória VM está a 100% mapeado para a memória física – overprovisioning (compromisso excessiva), por exemplo, não é utilizada.

Este perspetiva recomenda-se vivamente para utilizar o novo tipo de série de D ou DS-séries (juntamente com o armazenamento do Windows Azure Premium) Azure VM, à medida que tenham 60% processadores mais rápidos que a série de respostas. Para a carga de RAM e CPU mais alta, pode utilizar série G e VMs da série s (em conjunto com o armazenamento do Windows Azure Premium) com a família v3 de processador E5 da Intel Xeon® mais recente, que têm duas vezes a memória e quatro vezes o estado sólido unidade armazenamento (SSDs) dos D/DS-série.

#### <a name="storage-configuration"></a>Configuração de armazenamento
Como SAP liveCache baseia-se a tecnologia de SAP MaxDB, todo o armazenamento Azure recomendações de procedimentos recomendados para SAP MaxDB mencionadas capítulo [configuração de armazenamento] [dbms-guia 8.4.1] também são válidos para SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedicada Azure VM para liveCache
Tal como acontece SAP liveCache intensivamente utiliza power utilizaria, para uma utilização produtiva recomenda-se vivamente para implementar no dedicada Azure Máquina Virtual. 
 
![Dedicado Azure VM para liveCache para casos de utilização produtivo][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Cópia de segurança e restauro
Cópia de segurança e restauro, incluindo considerações sobre o desempenho, já são descritos nos capítulos SAP MaxDB relevantes [cópia de segurança e restauro] [dbms-guia de 8.4.2] e [considerações de desempenho para cópia de segurança e restauro] [dbms-guia de 8.4.3]. 

#### <a name="other"></a>Outros
Todos os outros tópicos gerais já sejam descritas no MaxDB SAP [isto] [dbms-guia-8.4.4] capítulo relevante. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Detalhes para o servidor de conteúdo do SAP no Windows
O servidor de conteúdo do SAP é um componente baseadas no servidor, separado para armazenar conteúdo tal como documentos Eletrónicos em diferentes formatos. O servidor de conteúdo do SAP é fornecido pela desenvolvimento de tecnologia e deve ser a aplicação de publicação em utilizada para todas as aplicações SAP. Este é instalado num sistema em separado. Conteúdo típico é materiais de formação e documentação do armazém de conhecimento ou desenhos técnicos com origem a mySAP PLM sistema de gestão de documentos. 

### <a name="sap-content-server-version-support"></a>Suporte de versão de servidor de conteúdo do SAP
SAP atualmente suporta:

* **SAP Server conteúdo** com a versão **6.50 (e posterior)**
* **SAP MaxDB versão 7.9**
* **Microsoft IIS (servidor de informação Internet) versão 8.0 (e superior)**

Recomenda-se vivamente para utilizar a versão mais recente do servidor conteúdo SAP, que, no momento da escrever este documento, é **6.50 SP4**e a versão mais recente do **Microsoft IIS 8.5**. 

Verificar as versões mais recentes suportadas do SAP Server conteúdo e Microsoft IIS no [SAP produto disponibilidade de matriz (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de Microsoft Windows e o Azure VM suportados para o servidor de conteúdo do SAP
Para saber versão suportada do Windows para SAP Server conteúdo no Azure, consulte:

* [Matriz de disponibilidade do SAP produto (PAM)][sap-pam]
* SAP nota [1928533]

Recomenda-se vivamente para utilizar a versão mais recente do Microsoft Windows, o qual no momento da escrever este documento é **Windows Server 2012 R2**.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Directrizes de configuração do SAP Server conteúdo para instalações SAP no Azure VMs

#### <a name="storage-configuration"></a>Configuração de armazenamento 
Se configurar SAP Server conteúdo para armazenar ficheiros da base de dados do SAP MaxDB, todos os armazenamento Azure melhor práticas recomendação mencionada para SAP MaxDB capítulo [configuração de armazenamento] [dbms-guia 8.4.1] também são válidos para o cenário de servidor de conteúdo do SAP. 

Se configurar SAP Server conteúdo para armazenar ficheiros no sistema de ficheiros, recomenda-se para utilizar uma unidade de lógica dedicada. Utilizar espaços de armazenamento permite-lhe também aumentar tamanho do disco lógico e débito IOPS, tal como descrito em capítulo [Software RAID] [dbms-guia 2.2]. 

#### <a name="sap-content-server-location"></a>Localização de servidor de conteúdo do SAP
SAP Server conteúdo tem de ser implementada na mesma região Azure e Azure VNET onde o sistema SAP é implementado. Está livre decidir se pretende implementar componentes de servidor de conteúdo do SAP uma VM Azure dedicado ou a mesma VM onde o sistema SAP está em execução. 
 
![Dedicada VM Azure para servidor de conteúdo do SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Localização do servidor de Cache SAP
O servidor de Cache SAP é um componente adicional para fornecer acesso aos documentos (em cache) localmente baseadas no servidor. O servidor de Cache SAP coloca em cache de documentos de um servidor de conteúdo do SAP. Este é para otimizar o tráfego de rede se os documentos têm a serem obtidos mais de uma vez a partir de localizações diferentes. A regra geral é que o servidor de Cache SAP tem de ter uma ligação física perto do cliente que acede ao servidor de Cache SAP. 

Aqui tem duas opções:

1. **Cliente é um sistema SAP back-end** Se um sistema SAP back-end estiver configurado para aceder ao servidor de conteúdo do SAP, que sistema SAP é um cliente. Tal como o sistema SAP e SAP Server conteúdo são implementadas na mesma região Azure – no Centro de dados Azure mesmo – são física perto entre si. Por conseguinte, não é necessário ter um servidor de Cache dedicado do SAP. Clientes de SAP IU (interface gráfica do SAP ou web browser) acedem diretamente ao sistema SAP e o sistema SAP obtém documentos a partir do servidor de conteúdo de SAP.
1. **Cliente é um browser da web no local** O servidor de conteúdo do SAP pode ser configurado para ser acedidas diretamente pelo web browser. Neste caso, num web browser em execução no local é um cliente de servidor de conteúdo do SAP. Centro de dados no local e Azure Centro de dados são colocados em diferentes localizações físicas (Idealmente fechar para outro). O Centro de dados no local está ligado ao Azure através do Azure VPN de Site para o Site ou ExpressRoute. Apesar de ambas as opções oferecem segura ligação de rede VPN para Azure, ligação de rede de site para o site não oferecer um SLA de largura de banda e a latência de rede entre o Centro de dados no local e o Azure Centro de dados. Para acelerar o acesso a documentos, pode efetuar um dos seguintes procedimentos:
    1. Instalar o SAP Cache Server no local, fechar para o local web browser (opção em figura [this][dbms-guide-900-sap-cache-server-on-premises])
    1. Configure o Azure ExpressRoute, que disponibiliza uma ligação de rede dedicada de alta velocidade e baixa latência entre o Centro de dados no local e o Centro de dados do Azure.
 
![Opção de instalação do SAP Cache Server no local][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Fazer cópia de segurança / restaurar
Se configurar o servidor de conteúdo do SAP para armazenar ficheiros da base de dados do SAP MaxDB, as considerações de procedimento e o desempenho de cópia de segurança/restauro já estão descritas nos SAP MaxDB capítulo [cópia de segurança e restauro] [dbms-guia 8.4.2] e capítulo [considerações de desempenho para cópia de segurança e restauro] [dbms-guia-8.4.3]. 

Se configurar o servidor de conteúdo do SAP para armazenar ficheiros no sistema de ficheiros, uma das opções é executar cópia de segurança manual/restauro da estrutura de todo o ficheiro onde estão localizados os documentos. Semelhante ao SAP MaxDB cópia de segurança/restauro, recomenda-se de ter o volume de um disco dedicado para finalidade de cópia de segurança. 

#### <a name="other"></a>Outros
Outras definições específicas do servidor de conteúdo do SAP são transparentes para Azure VMs e são descritas nos vários documentos e SAP notas:

* <https://Service.SAP.com/contentserver> 
* SAP nota [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Detalhes do IBM DB2 para LUW no Windows
Com o Microsoft Azure, pode facilmente migrar a aplicação SAP existente em execução no IBM DB2 para Linux, UNIX e Windows (LUW) máquinas virtuais Azure. Com SAP no IBM DB2 para LUW, os administradores e programadores ainda podem utilizar a mesma desenvolvimento e ferramentas de administração que estão disponível no local.
Informações gerais sobre como executar o conjunto de aplicações do SAP empresas no IBM DB2 para LUW podem ser encontradas na rede de Comunidade do SAP (SCN) na <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Para obter informações adicionais e atualizações sobre SAP no DB2 para LUW no Azure, consulte o artigo SAP nota [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP no IBM DB2 para LUW no Microsoft Azure Máquina Virtual Services é suportada tal como a versão de DB2 10.5.

Para obter informações sobre produtos SAP suportados e os tipos de Azure VM, consulte SAP nota [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para as instalações do SAP no Azure VMs

#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS com base em discos VHD. Estes VHDs são instalados para a VM Azure e são baseados no armazenamento de BLOBS do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualquer tipo de unidades de rede ou partilhas remotas como os seguintes serviços de ficheiro Azure estão **não** suportado para ficheiros de base de dados: 

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
Se estiver a utilizar o Azure VHDs com base em armazenamento de BLOBS do Azure página, as declarações constantes este documento em capítulo [estrutura de uma implementação RDBMS] [dbms-guia de-2] também se aplicam a implementações com o IBM DB2 para LUW base de dados. 

Tal como é explicado anteriormente a parte do documento geral, existem quotas em débito IOPS para Azure VHDs. As quotas exatas variam consoante o tipo de VM utilizado. Uma lista dos tipos VM com as suas próprias quotas pode ser encontrada [aqui][virtual-machines-sizes].

Desde que a quota IOPS atual por disco é suficiente, é possível armazenar todos os ficheiros de base de dados um único montada Azure VHD. 

Para obter um desempenho considerações também se referir a capítulo "Dados de segurança e desempenho considerações para base de dados nos diretórios" guias de instalação do SAP.

Em alternativa, pode utilizar agrupamentos de armazenamento do Windows (apenas disponível no Windows Server 2012 e superior) ou repartição do Windows para o Windows 2008 R2 conforme descrito capítulo [Software RAID] [dbms-guia 2.2] deste documento para criar um dispositivo lógico grande através de vários discos VHD montados.
Para os discos que contém os caminhos de armazenamento de DB2 para sua directórios sapdata e saptmp, tem de especificar um tamanho de sector disco físico de 512 KB. Quando utilizar agrupamentos de armazenamento do Windows, tem de criar os conjuntos de armazenamento manualmente através da interface de linha de comandos utilizando o parâmetro "-LogicalSectorSizeDefault". Para obter detalhes, consulte <https://technet.microsoft.com/library/hh848689.aspx>.

#### <a name="backuprestore"></a>Cópia de segurança/restauro
A funcionalidade de cópia de segurança/restauro para IBM DB2 para LUW é suportada da mesma forma que no Hyper-V e sistemas operativos do Windows Server padrão.

Deve certificar-se de que tem uma estratégia de cópia de segurança da base de dados válido no local. 

Tal como implementações base, desempenho de cópia de segurança/restauro depende volumes quantos podem ser lidos em paralelo e o que poderá ser débito desses volumes. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança poderá ser reproduzido um papel significativo no VMs com apenas até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dispositivos de base de dados mais pequeno débito global no modo de leitura
* Mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança
* Os destinos menos (faixa diretórios, VHDs) para escrever a cópia de segurança para a parte inferior do débito

Para aumentar o número de destinos ao escrever, as duas opções podem ser utilizado/combinados consoante as suas necessidades:

* Dividir o volume de destino de cópia de segurança sobre vários VHDs montados para melhorar o débito IOPS nesse volume às riscas
* Utilizar mais do que um diretório de destino para escrever a cópia de segurança

#### <a name="high-availability-and-disaster-recovery"></a>Disponibilidade de alta e recuperação de falhas
Microsoft (Cluster Server MSCS) não é suportada.

Recuperação de falhas de elevada disponibilidade DB2 (HADR) é suportada. Se as máquinas virtuais da configuração do HA tiver trabalhar resolução do nome, a configuração no Azure não serão diferentes a partir de qualquer programa de configuração que é concluído no local. Não é recomendado para depender de resolução de IP apenas.

Não utilize Azure loja Geo-replicação. Para obter mais informações, consulte capítulo [armazenamento do Windows Azure] [dbms-guia de 2.3] e capítulo [elevada disponibilidade e recuperação de falhas com Azure VMs] [dbms-guia-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como monitorização de conjuntos de disponibilidade do Azure ou SAP aplicam-se tal como descrito nos três primeiros capítulos deste documento para implementações do VMs com IBM DB2 para LUW também. 

Também consultar a capítulo [geral do SQL Server para SAP no Azure resumo] [dbms-guia 5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Detalhes do IBM DB2 para LUW em Linux
Com o Microsoft Azure, pode facilmente migrar a aplicação SAP existente em execução no IBM DB2 para Linux, UNIX e Windows (LUW) máquinas virtuais Azure. Com SAP no IBM DB2 para LUW, os administradores e programadores ainda podem utilizar a mesma desenvolvimento e ferramentas de administração que estão disponível no local. Informações gerais sobre como executar o conjunto de aplicações do SAP empresas no IBM DB2 para LUW podem ser encontradas na rede de Comunidade do SAP (SCN) na <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Para obter informações adicionais e atualizações sobre SAP no DB2 para LUW no Azure, consulte o artigo SAP nota [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP no IBM DB2 para LUW no Microsoft Azure Máquina Virtual Services é suportada tal como a versão de DB2 10.5.

Para obter informações sobre produtos SAP suportados e os tipos de Azure VM, consulte SAP nota [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para as instalações do SAP no Azure VMs

#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os ficheiros de base de dados devem ser armazenados num sistema de ficheiros com base em discos VHD. Estes VHDs são instalados para a VM Azure e são baseados no armazenamento de BLOBS do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>).
Qualquer tipo de unidades de rede ou partilhas remotas como os seguintes serviços de ficheiro Azure estão **não** suportado para ficheiros de base de dados:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/persisting-Connections-to-Microsoft-Azure-Files.aspx>

Se estiver a utilizar o Azure VHDs com base em armazenamento de BLOBS do Azure página, as declarações constantes este documento em capítulo [estrutura de uma implementação RDBMS] [dbms-guia de-2] também se aplicam a implementações com o IBM DB2 para LUW base de dados.

Tal como é explicado anteriormente a parte do documento geral, existem quotas em débito IOPS para Azure VHDs. As quotas exatas variam consoante o tipo de VM utilizado. Uma lista dos tipos VM com as suas próprias quotas pode ser encontrada [aqui][virtual-machines-sizes].

Desde que a quota IOPS atual por disco é suficiente, é possível armazenar todos os ficheiros de base de dados um único montada Azure VHD.

Para obter um desempenho considerações também se referir a capítulo "Dados de segurança e desempenho considerações para base de dados nos diretórios" guias de instalação do SAP.

Em alternativa, pode utilizar LVM (Gestor de Volume lógico) ou MDADM conforme descrito capítulo [Software RAID] [dbms-guia 2.2] deste documento para criar um dispositivo lógico grande através de vários discos VHD montados.
Para os discos que contém os caminhos de armazenamento de DB2 para sua directórios sapdata e saptmp, tem de especificar um tamanho de sector disco físico de 512 KB.

#### <a name="backuprestore"></a>Cópia de segurança/restauro
A funcionalidade de cópia de segurança/restauro para IBM DB2 para LUW é suportada da mesma forma que no padrão Linux instalação no local.

Deve certificar-se de que tem uma estratégia de cópia de segurança da base de dados válido no local.

Tal como implementações base, desempenho de cópia de segurança/restauro depende volumes quantos podem ser lidos em paralelo e o que poderá ser débito desses volumes. Além disso, o consumo de CPU utilizado pelo compressão cópia de segurança poderá ser reproduzido um papel significativo no VMs com apenas até 8 threads CPU. Por conseguinte, um pode partem do pressuposto de:

* Menos o número de VHDs utilizada para armazenar os dispositivos de base de dados mais pequeno débito global no modo de leitura
* Mais pequeno que o número de CPU threads na VM, mais extremas o impacto de compressão cópia de segurança
* Os destinos menos (faixa diretórios, VHDs) para escrever a cópia de segurança para a parte inferior do débito

Para aumentar o número de destinos ao escrever, as duas opções podem ser utilizado/combinados consoante as suas necessidades:

* Dividir o volume de destino de cópia de segurança sobre vários VHDs montados para melhorar o débito IOPS nesse volume às riscas
* Utilizar mais do que um diretório de destino para escrever a cópia de segurança

#### <a name="high-availability-and-disaster-recovery"></a>Disponibilidade de alta e recuperação de falhas
Recuperação de falhas de elevada disponibilidade DB2 (HADR) é suportada. Se as máquinas virtuais da configuração do HA tiver trabalhar resolução do nome, a configuração no Azure não serão diferentes a partir de qualquer programa de configuração que é concluído no local. Não é recomendado para depender de resolução de IP apenas.

Não utilize Azure loja Geo-replicação. Para obter mais informações, consulte capítulo [armazenamento do Windows Azure] [dbms-guia de 2.3] e capítulo [elevada disponibilidade e recuperação de falhas com Azure VMs] [dbms-guia-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como monitorização de conjuntos de disponibilidade do Azure ou SAP aplicam-se tal como descrito nos três primeiros capítulos deste documento para implementações do VMs com IBM DB2 para LUW também.

Também consultar a capítulo [geral do SQL Server para SAP no Azure resumo] [dbms-guia 5.8].