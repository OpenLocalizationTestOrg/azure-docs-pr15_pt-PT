<properties
   pageTitle="SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação | Microsoft Azure"
   description="SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação"
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

# <a name="sap-netweaver-on-windows-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação

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

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação do) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidade (HA) e falhas recuperação (DR) para o SAP NetWeaver em execução no máquinas virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (elevada disponibilidade para os servidores de aplicações SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (utilizando iniciar instâncias do SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md# 1625df66-4CC6-4d60-9202-de8a0b77f803 (apenas na nuvem - Máquina Virtual híbridas para o Azure sem dependências da rede de cliente no local) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (cruzada local - implementação de único ou vários VMs SAP para Azure com o requisito de que está a ser totalmente integrada sessão na rede no local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regiões) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (falhas de domínios) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (atualizar domínios) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088- F9BE - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (do Microsoft Azure Máquina Virtual conceito) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (mover uma VM nos locais para o Azure com um disco não generalized) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (implementar uma VM com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparação para mover uma VM nos locais para o Azure com um não generalized disco) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparação para implementar uma VM com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (preparar VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferença entre um Azure disco e imagem do Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (carregar um VHD nos locais para Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiar discos entre contas de armazenamento do Azure) [5.5.1 de planeamento-guia]: virtual-Machines-Windows-SAP-Planning-Guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (estrutura VM/VHD para implementações do SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montagem definição automática para discos ligados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (única VM com SAP NetWeaver demonstração/formação cenário) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implementação conceitos de Cloud-Only de instâncias do SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitorização solução para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md# ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (armazenamento de Azure Premium)

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
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
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
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
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

Microsoft Azure permite às empresas adquirir recursos cluster e armazenamento altura mínimas sem ciclos de contratos comprida. Azure máquinas virtuais permite às empresas implementar clássica aplicações, como SAP NetWeaver com base aplicações para o Azure e expandir as respetivas fiabilidade e disponibilidade sem ter ainda mais recursos disponíveis no local. Microsoft Azure também suporta a conectividade de publicação em local, que permite aos empresas ativamente entregar máquinas virtuais do Azure nos seus domínios no local, os respetivos nuvens privado e os respetivos horizontal de sistema do SAP.

Este documento técnico passo a passo descreve como uma máquina de Virtual Azure está preparado para a implementação de aplicações SAP NetWeaver com base. Parte do princípio de que as informações contidas num [planeamento e guia de implementação do] [-guia de planeamento] é conhecida. Caso contrário, o documento respetivos deve ser lidas pela primeira vez.

Papel completa a documentação de instalação do SAP e SAP notas que representam os recursos principais para implementações do software SAP e instalações em determinadas plataformas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="introduction"></a>Introdução
Um grande número de empresas em todo o mundo utiliza as aplicações do SAP NetWeaver com base – mais proeminentes o conjunto de negócio do SAP – para executar a sua missão crítico processos de negócio. Estado de funcionamento do sistema, por isso, é um activo crucial e a capacidade de fornecer suporte enterprise em caso de uma falha, incluindo incidentes de desempenho, torna-se um requisito importante.
Microsoft Azure fornece instrumentação plataforma superior para acomodar os requisitos de compatibilidade de todas as aplicações críticas de negócio. Este guia torna-se de que uma Máquina Virtual do Microsoft Azure orientados para a implementação do SAP Software está configurada assim que suporte enterprise pode ser-lhe fornecido, independentemente que obtém criada a forma como a Máquina Virtual, ser-tomadas fora do Azure Marketplace ou utilizar uma imagem específica do cliente.
Na configuração do seguinte, todas as necessárias passos são descritos detalhadamente.

## <a name="prerequisites-and-resources"></a>Pré-requisitos e recursos
### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que os pré-requisitos que são descritos nos seguintes capítulos forem cumpridos.

#### <a name="local-personal-computer"></a>Computador pessoal local
A configuração de uma máquina de Virtual do Azure para implementação de Software do SAP compreende vários passos. Para gerir Windows VMs ou Linux VMs, tem de utilizar um script PowerShell e o Portal do Microsoft Azure. Para que, é necessário um computador pessoal local a executar o Windows 7 ou superior. Se apenas quiser gerir Linux VMs e pretender utilizar uma máquina de Linux para esta tarefa, também pode utilizar a Interface de linha de comandos do Azure (Azure CLI).

#### <a name="internet-connection"></a>Ligação à Internet
Para transferir e executar os scripts e ferramentas necessárias, é necessária uma ligação à Internet. Além disso, o Microsoft Azure Virtual Machine a executar o Azure avançada monitorização extensão precisa de aceder à Internet. Caso esta VM Azure fizer parte de uma rede Virtual Azure ou o domínio no local, certifique-se de que as definições de proxy relevantes estão definidas conforme descrito capítulo [Configurar Proxy] [ deployment-guide-configure-proxy] neste documento.

#### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure
Já existe uma conta Azure e em função credenciais de início de sessão são conhecidas.

#### <a name="topology-consideration-and-networking"></a>Considerações de topologia e redes
A topologia de arquitectura e de implementação do SAP no Azure tem de ser definidos. Arquitetura no que respeita a:

* (S) de armazenamento do Windows Azure para ser utilizado
* Rede virtual para implementar o sistema SAP para
* Grupo de recursos para implementar o sistema SAP para
* Azure região para implementar o sistema do SAP
* Configuração do SAP (2 camada ou camadas 3)
* VM s e o número de VHDs adicionais a ser instalados para o VM(s)
* SAP transporte e a correção de configuração do sistema

Azure armazenamento contas ou redes virtuais Azure como tal deve foram criados e já configurado. Como criar e configurá-los está incluído no [planeamento e guia de implementação do] [-guia de planeamento].

#### <a name="sap-sizing"></a>Redimensionamento do SAP
* A carga de trabalho SAP prevista foi determinada, por exemplo, utilizando SAP Quicksizer, e o número em função do SAP é conhecido 
* Deve conhecer o necessário recurso e da memória consumo de CPU do sistema SAP
* Devem conhecer as operações e/s necessárias por segundo
* Chama-se a largura de banda de rede necessários no eventual comunicação entre diferentes VMs no Azure
* A largura de banda de rede necessários entre os ativos no local e o Azure implementado SAP sistemas é conhecido

#### <a name="resource-groups"></a>Grupos de recursos
Grupos de recursos são um novo conceito que contêm todos os recursos que têm o mesmo ciclo de vida, por exemplo, são criadas e eliminados ao mesmo tempo. Leia [Este artigo] [ resource-group-overview] para obter mais informações sobre os grupos do recurso. 

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do SAP
Durante o trabalho de configuração, são necessários os seguintes recursos:

* SAP nota [1928533]
    * a lista de tamanhos de máquina de Virtual Azure, que são suportadas para a implementação do Software do SAP 
    * informação sobre a capacidade importantes por tamanho de máquina de Virtual do Azure
    * software SAP suportada e a combinação sistema operativo e DB
* SAP nota [2015553] listar pré-requisitos para sejam suportadas pelo SAP quando implementar o software do SAP no Microsoft Azure.
* SAP nota [1999351] que contém informações adicionais de resolução de problemas para o avançada Azure monitorização para SAP.
* SAP nota [2178632] que contém informações de detalhes sobre todos os métricas de monitorização disponíveis para SAP no Microsoft Azure. 
* SAP nota [1409604] que contém a versão de agente de anfitrião do SAP necessária para o Windows no Microsoft Azure quando implementar no novo recurso Gestor da Azure.
* SAP nota [2191498] que contém a versão do agente de anfitrião do SAP necessária para Linux no Microsoft Azure quando implementar no novo recurso Gestor da Azure.
* Nota SAP [2243692] que contém informações sobre o licenciamento do SAP no Linux no Azure
* SAP nota [1984787] que contém informações gerais sobre SUSE LINUX Enterprise Server 12
* SAP nota [2002167] que contêm informações gerais vermelho chapéu Enterprise Linux 7. x
* [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todos os necessário SAP notas para Linux
* SAP específico os cmdlets do PowerShell que fazem parte do [PowerShell do Azure][azure-ps]
* Clip Azure específico SAP que fazem parte do [Clip do Azure][azure-cli]
* [Portal do Microsoft Azure][azure-portal]

[comment]: <> (Nível de patches MSSedusch TODO adicionar processador para agente de anfitrião do SAP no 1409604 de nota do SAP)
 
Os seguintes guias cobrir o tópico de SAP no Microsoft Azure, assim:

* [SAP NetWeaver no Windows máquinas virtuais (VMs) – planeamento e guia de implementação do] [-guia de planeamento]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação (este documento)] [guia de implementação]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação do DBMS] [dbms guia]
* [SAP NetWeaver no Windows máquinas virtuais (VMs) – guia de implementação de elevada disponibilidade][ha-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implementação das VMs para SAP no Microsoft Azure
Neste capítulo, saiba as diferentes maneiras de implementação e os passos únicos para cada tipo de implementação.

### <a name="deployment-of-vms-for-sap"></a>Implementação do VMs para SAP
Microsoft Azure oferece várias formas para implementar VMs e discos associados. Portanto é muito importante compreender as diferenças desde preparações dos VMs podem ser diferentes depende da forma de implementação. Em geral, vamos analisar os cenários seguintes:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementar uma VM fora do Azure Marketplace
Como tirar da Microsoft ou festa 3º fornecidos imagem fora do Azure Marketplace para implementar o seu VM. Depois de implementado a VM no Microsoft Azure, siga o mesmo diretrizes e ferramentas para instalar o software do SAP dentro da sua VM, tal como o faria num ambiente no local. Para instalar o software SAP dentro da VM Azure, SAP e o Microsoft recomendado para carregar e armazenar o suporte de instalação do SAP no Azure VHDs ou para criar um VM Azure trabalhar como um 'servidor do ficheiro' que contém todos os necessário SAP suporte de instalação.

[comment]: <> (MSSedusch TODO porque é que precisamos de recomendar um gestão de ficheiros, por exemplo, servidor de ficheiros ou VHD? É que o modo diferente no local?)

Para obter mais detalhes, consulte capítulo [cenário 1: implementar uma VM terminar o Azure Marketplace para SAP] [3,2 de guia de implementação].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Implementar uma VM com uma imagem personalizada
Devido a requisitos de patches específico no que respeita à sua versão do SO ou DBMS, as imagens fornecidas fora do Azure Marketplace não poderão corresponder às suas necessidades. Por conseguinte, poderá ter de criar uma VM utilizando a sua própria imagem SO/DB VM 'private' que pode ser implementada posteriormente várias vezes.
Os passos para criar uma imagem privada diferem entre o Windows e uma imagem de Linux.

___

> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode ser utilizada para implementar várias máquinas virtuais, as definições do Windows (como SID do Windows e o nome do anfitrião) tem de ser captadas/generalized na VM no local. Isto pode ser feito usando sysprep, tal como descrito no <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem de Linux que pode ser utilizada para implementar várias máquinas virtuais, algumas definições de Linux tem de ser captadas/generalized na VM no local. Isto pode ser feito utilizando waagent-deprovision conforme descrito [neste] artigo[ virtual-machines-linux-capture-image] ou [neste]artigo[virtual-machines-linux-agent-user-guide-command-line-options].

___

Pode configurar o seu conteúdo da base de dados utilizando um do SAP Software aprovisionar gestor para instalar um novo sistema de SAP, restaurar uma cópia de segurança da base de dados a partir de um VHD que está anexado à máquina virtual ou diretamente restaurar uma cópia de segurança da base de dados a partir de armazenamento do Windows Azure se DBMS suporta-lo. (consulte o artigo [Guide][dbms-guide]) de implementação DBMS. Se já tiver instalado um sistema SAP na sua VM no local (especialmente para sistemas de camada 2), pode adaptá as definições do sistema SAP depois da implementação da VM Azure através do procedimento de mudar o nome do sistema suportado pelo Gestor de aprovisionamento de Software SAP (nota SAP [1619720]). Caso contrário, pode instalar o software do SAP mais tarde depois da implementação da Azure VM.

Para obter mais detalhes, consulte capítulo [cenário 2: implementar uma VM com uma imagem personalizada para SAP] [3.3 de guia de implementação].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Mover uma VM no local para o Microsoft Azure com um disco não generalized
Planear mover um sistema SAP específico no local para Microsoft Azure. Isto pode ser feito ao carregá VHD que contém o SO, binários SAP e eventual binários do DBMS plus os VHDs com os ficheiros de dados e de registo do Microsoft Azure ao DBMS. Em oposto o cenário descrito capítulo [implementar uma VM com uma imagem personalizada] [implementação-guia-3.1.2] acima, que mantém o nome do anfitrião, SAP SID e contas de utilizador do SAP na Azure VM como estavam configuradas no ambiente no local. Por conseguinte, generalizar o sistema operativo não é necessário. Neste caso, principalmente serão aplicadas para cenários de publicação em local em que uma parte da paisagem SAP é executada no local e peças no Microsoft Azure.

Para obter mais detalhes, consulte capítulo [cenário 3: mover uma VM no local utilizando um VHD de Azure não generalized com SAP] [3.4 de guia de implementação].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implementar uma VM terminar o Azure Marketplace para SAP
Microsoft Azure oferece a possibilidade de implementar uma instância VM fora do Azure Marketplace, que disponibiliza algumas imagens SO padrão do Windows Server e diferentes Linux distribuições. Também é possível implementar uma imagem que inclui DBMS SKUs, por exemplo, SQL Server. Para obter mais detalhes de utilizar essas imagens com DBMS SKUs consulte [DBMS guia de implementação do] [dbms guia]

A sequência de específica do SAP dos passos implementar uma VM fora do Azure Marketplace teria o seguinte aspeto:

![Fluxograma de implementação de VM para sistemas SAP utilizando uma imagem VM do Azure Marketplace][deployment-guide-figure-100]

Após o fluxograma os passos seguintes tem de ser executadas:

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Criar máquina virtual utilizando o Portal do Azure
A forma mais fácil para criar uma nova máquina virtual com uma imagem a partir do Azure Marketplace é através do Portal do Azure. Navegue até ao <https://portal.azure.com/#create>. Introduza o tipo do sistema operativo que pretende implementar no campo de pesquisa, por exemplo, Windows, SLES ou RHEL e selecione a versão. Certifique-se selecionar o modelo de implementação "Gestor de recursos do Azure" e clique em criar.

O assistente irá guiá-lo através de parâmetros necessários para criar a máquina virtual juntamente com todos os recursos necessários como interfaces de rede ou contas de armazenamento. Alguns destes parâmetros são:

1. Noções básicas
    1. Nome: O nome do recurso, ou seja, nome da máquina virtual
    1. Nome de utilizador e palavra-passe/SSH chave pública: introduza o nome de utilizador e palavra-passe do utilizador que é criado durante o aprovisionamento. Para uma máquina de virtual Linux, também pode introduzir a chave SSH pública que pretende utilizar para iniciar sessão com a máquina utilizando SSH.
    1. Subscrição: Selecione a subscrição que pretende utilizar para aprovisionar a nova máquina virtual.
    1. Grupo de recursos: O nome do grupo de recursos. Pode inserir o nome de um novo grupo de recursos ou de um grupo de recursos que já existe
    1. Localização: Selecione a localização onde a nova máquina virtual deve ser implementada. Se pretender estabelecer ligação a máquina virtual à sua rede no local, certifique-se selecionar a localização da rede Virtual Azure que liga à sua rede no local. Para obter mais detalhes, consulte o artigo capítulo [Rede do Microsoft Azure] [ planning-guide-microsoft-azure-networking] no [guia de planeamento] [-guia de planeamento].
1. Tamanho: Leia SAP nota [1928533] para uma lista de tipos VM suportados. Certifique-se também selecionar o tipo correto se pretender utilizar o armazenamento de Premium. Nem todos os tipos VM suportam o armazenamento de Premium. Consulte o artigo capítulo [armazenamento: armazenamento do Windows Azure e os dados discos] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] [Azure Premium armazenamento] e [planeamento-guia de armazenamento do azure-premium-] no [guia de planeamento] [-guia de planeamento] para obter mais detalhes.
1. Definições
    1. Conta de armazenamento: Pode selecionar uma conta de armazenamento existente ou crie um novo. Leia capítulo [armazenamento do Windows Azure] [dbms-guia 2.3], [DBMS do guia de] [dbms guia] para obter mais detalhes sobre os tipos de armazenamento diferente. Tenha em atenção que são suportados nem todos os tipos de armazenamento para executar as aplicações SAP.
    1. Rede virtual e de sub-rede: selecione a rede virtual que está ligada à rede no local, se pretender integrar a máquina virtual numa intranet.
    1. Endereço IP público: selecione o endereço IP público que pretende utilizar ou introduza os parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da internet. Certifique-se também pode criar um grupo de segurança de rede para filtrar o acesso ao seu máquina virtual.
    1. Grupo de segurança de rede: consulte [o que é um grupo de segurança de rede (NSG)] [ virtual-networks-nsg] para obter mais detalhes.
    1. Monitorização: Pode desativar a definição de diagnóstico. -Irão estar ativada automaticamente ao executar os comandos para ativar o Azure avançada monitorização conforme descrito capítulo [Configurar monitorização][deployment-guide-configure-monitoring-scenario-1].
    1. Disponibilidade: Selecione um conjunto de disponibilidade ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações consulte o artigo capítulo [Azure disponibilidade conjuntos] [3.2.3 de planeamento-guia].
1. Resumo: Validar as informações fornecidas na página de resumo e clique em OK.

Depois de concluir o assistente, máquina virtual será implementada no grupo de recursos que selecionou.

#### <a name="create-virtual-machine-using-a-template"></a>Criar máquina virtual através de um modelo
Também pode criar uma implementação do utilizando um dos modelos SAP publicados no [repositório de modelos de guia de introdução azure github][azure-quickstart-templates-github]. Ou pode criar uma máquina de virtual utilizando o [Portal do Azure][virtual-machines-windows-tutorial], [PowerShell] [ virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] ou [Clip de Azure] [ virtual-machines-linux-tutorial] manualmente.

* [modelo de configuração de camada de 2 (apenas uma máquina de virtual)] [ sap-templates-2-tier-marketplace-image] Utilize este modelo se pretender criar um sistema de 2 camada utilizando apenas uma máquina virtual.
* [modelo de configuração de 3 camadas (várias máquinas virtuais)] [ sap-templates-3-tier-marketplace-image] Utilize este modelo se pretender criar um sistema de 3 camadas utilizando várias máquinas virtuais.

Depois de abrir um dos modelos acima, o Portal do Azure navega para o painel de editar parâmetros. Introduza as seguintes informações:

* **sapSystemId**: ID de sistema do SAP
* **osType**: sistema operativo que pretende implementar, por exemplo, Windows Server 2012 R2, SLES 12 ou RHEL 7.2
    * A lista que contém apenas versões que são suportadas pelo SAP no Microsoft Azure
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAP o novo sistema irá fornecer. Se não tiver a certeza SAP quantos o sistema irá requerer, pergunte ao seu parceiro de tecnologia do SAP ou integrador de sistema
* **systemAvailability**: (apenas para modelo de 3 camadas) disponibilidade do sistema 
    * Selecione HA para uma configuração que é adequada para uma instalação HA. Dois servidores de base de dados e dois servidores para o ASCS serão criados.
* storageType: (apenas para modelo de camada 2) tipo de armazenamento que deve ser utilizado 
    * Para sistemas maiores, a utilização de armazenamento de Premium é vivamente recomendada. Para obter mais informações sobre os tipos de armazenamento diferente, leia o artigo 
        * [Armazenamento do Microsoft Azure] [dbms-guia-2.3] [DBMS do guia de] [dbms guia]
        * [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho][storage-premium-storage-preview-portal]
        * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome de utilizador e palavra-passe
    * Um novo utilizador for criado que podem ser utilizados para iniciar sessão no computador.
* **newOrExistingSubnet**: determina se devem ser criadas uma nova rede virtual e sub-rede ou uma sub-rede existente deverá ser utilizada. Se já tiver uma rede virtual que está ligada à rede no local, selecione existente.
* **subnetId**: O ID da sub-rede para que as máquinas virtuais devem estar ligadas. Selecione a sub-rede da sua rede virtual VPN ou encaminhar Express para ligar a máquina virtual à sua rede no local. O ID do aspeto normalmente /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Depois de introduzido todos os parâmetros, selecione a subscrição e o grupo de recursos que pretende utilizar. Pode selecionar um grupo de recursos existente ou crie um novo ao selecionar "+ novo" no menu pendente. Se criar um novo grupo de recursos, também tem de selecionar a região onde o grupo de recursos e a máquina virtual serão criados.

Reveja os termos de legais, aceitá-los e clique em criar.

Tenha em atenção que o agente de VM Azure é implementado por predefinição, ao utilizar uma imagem a partir do Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configurar definições de Proxy
Dependendo da sua configuração de rede no local, poderá ser necessário para configurar o proxy no seu computador virtual se estiver ligado à rede no local através de VPN ou encaminhar Express. Caso contrário, a máquina virtual poderá não conseguir aceder à internet e, por conseguinte, não é possível transferir as extensões de necessárias ou recolher dados de monitorização. Consulte o artigo [Configurar o Proxy] de capítulo[ deployment-guide-configure-proxy] deste documento.

#### <a name="join-domain-windows-only"></a>Aderir a domínio (apenas para o Windows)
No caso que está ligada no local a implementação no Azure AD/DNS através do Azure Site para Site ou encaminhar Express (também referenciada como cruzada local na [planeamento e implementação Guide][planning-guide]), espera-se que a VM aderir a um domínio no local. Considerações deste passo sejam descritas no capítulo [participar VM para o domínio no local (apenas para o Windows)] [4.3 de guia de implementação] deste documento.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar a monitorização
Configurar o Azure avançada monitorização extensão para SAP, tal como descrito no capítulo [configurar Azure avançada monitorização extensão para SAP] [implementação-guia-4,5] deste documento.

Verifique os pré-requisitos para a monitorização de SAP para versões de mínimas necessárias do SAP Kernel e agente de anfitrião do SAP nos recursos listados em capítulo [SAP recursos] [2.2 de guia de implementação] deste documento.

#### <a name="monitoring-check"></a>Verificação de monitorização
Verifique se o controlo está a funcionar conforme descrito capítulo [verifica e resolução de problemas para o programa de configuração do fim para fim monitorização do SAP no Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Publicar passos de implementação
Depois de criar a VM, será implementada e, em seguida, é no-o a instalar todos os componentes de software necessário para a VM. Por conseguinte, este tipo de implementação de VM necessitam que quer o software para ser instalado beeing já disponível no Microsoft Azure algumas outras VM ou como disco que pode ser anexado. Ou Estamos à procura para cenários de publicação em local onde está a conectividade aos ativos no local (instalar partilhas de) uma determinada.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implementar uma VM com uma imagem personalizada para SAP
Conforme descrito no [planeamento e guia de implementação do], [-guia de planeamento] já existentes no obter os passos detalhados existe uma forma para preparar e criar uma imagem personalizada e utilizá-la para criar múltiplas VMs novos. A sequência de passos no gráfico fluxo teria o seguinte aspeto:
 
![Fluxograma de implementação de VM para sistemas SAP utilizando uma imagem VM no privado Marketplace][deployment-guide-figure-300]

Após o fluxograma os passos seguintes tem de ser executadas:

#### <a name="create-virtual-machine"></a>Criar máquina virtual
Para criar uma implementação utilizando uma imagem SO privada através do Portal do Azure, utilize um dos modelos SAP publicados no [repositório de modelos de guia de introdução azure github][azure-quickstart-templates-github].
Também pode criar uma máquina de virtual utilizando o [PowerShell] [ virtual-machines-upload-image-windows-resource-manager] manualmente. 

* [modelo de configuração de camada de 2 (apenas uma máquina de virtual)] [ sap-templates-2-tier-user-image] Utilize este modelo se pretender criar um sistema de 2 camada utilizando apenas uma máquina virtual e a sua própria imagem SO.
* [modelo de configuração de 3 camadas (várias máquinas virtuais)] [ sap-templates-3-tier-user-image] Utilize este modelo se pretender criar um sistema de 3 camadas utilizando várias máquinas virtuais e a sua própria imagem SO.

Depois de abrir um dos modelos acima, o Portal do Azure navega para o painel de editar parâmetros. Introduza as seguintes informações:

* **sapSystemId**: ID de sistema do SAP
* **osType**: tipo de sistema operativo que pretende implementar, Windows ou Linux
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAP o novo sistema irá fornecer. Se não tiver a certeza SAP quantos o sistema irá requerer, pergunte ao seu parceiro de tecnologia do SAP ou integrador de sistema
* **systemAvailability**: (apenas para modelo de 3 camadas) disponibilidade do sistema 
    * Selecione HA para uma configuração que é adequada para uma instalação HA. Dois servidores de base de dados e dois servidores para o ASCS serão criados.
* **storageType**: (apenas para modelo de camada 2) tipo de armazenamento que deve ser utilizado 
    * Para sistemas maiores, a utilização de armazenamento de Premium é vivamente recomendada. Para obter mais informações sobre os tipos de armazenamento diferente, leia o artigo 
        * [Armazenamento do Microsoft Azure] [dbms-guia-2.3] [DBMS do guia de] [dbms guia]
        * [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho][storage-premium-storage-preview-portal]
        * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome de utilizador e palavra-passe
    * Um novo utilizador for criado que podem ser utilizados para iniciar sessão no computador.
* **userImageVhdUri**: URI do vhd privado SO de imagem, por exemplo, https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
* **userImageStorageAccount**: nome da conta de armazenamento onde a imagem do sistema operativo privada está armazenada, por exemplo, `<accountname`> no exemplo acima URI
* **newOrExistingSubnet**: determina se devem ser criadas uma nova rede virtual e sub-rede ou uma sub-rede existente deverá ser utilizada. Se já tiver uma rede virtual que está ligada à rede no local, selecione existente.
* **subnetId**: O ID da sub-rede para que as máquinas virtuais devem estar ligadas. Selecione a sub-rede da sua rede virtual VPN ou encaminhar Express para ligar a máquina virtual à sua rede no local. O ID do aspeto normalmente /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Depois de introduzido todos os parâmetros, selecione a subscrição e o grupo de recursos que pretende utilizar. Pode selecionar um grupo de recursos existente ou crie um novo ao selecionar "+ novo" no menu pendente. Se criar um novo grupo de recursos, também tem de selecionar a região onde o grupo de recursos e a máquina virtual serão criados.

Reveja os termos de legais, aceitá-los e clique em criar.

#### <a name="install-vm-agent-linux-only"></a>Instalar o agente VM (apenas Linux)
O agente Linux já tem de estar instalado na imagem do utilizador se pretender utilizar os modelos acima. Caso contrário, a implementação irá falhar. Transferir e instalar o agente VM na imagem do utilizador, conforme descrito capítulo [transferir, instalar e ativar o Azure VM agente] [implementação-guia-4.4] deste documento.
Se não utiliza os modelos acima, também pode instalar o agente VM posteriormente.

#### <a name="join-domain-windows-only"></a>Aderir a domínio (apenas para o Windows)
No caso que está ligada no local a implementação no Azure AD/DNS através do Azure Site para Site ou encaminhar Express (também referenciada como cruzada local na [planeamento e implementação Guide][planning-guide]), espera-se que a VM aderir a um domínio no local. Considerações deste passo sejam descritas no capítulo [participar VM para o domínio no local (apenas para o Windows)] [4.3 de guia de implementação] deste documento.

#### <a name="configure-proxy-settings"></a>Configurar definições de Proxy
Dependendo da sua configuração de rede no local, poderá ser necessário para configurar o proxy no seu computador virtual se estiver ligado à rede no local através de VPN ou encaminhar Express. Caso contrário, a máquina virtual poderá não conseguir aceder à internet e, por conseguinte, não é possível transferir as extensões de necessárias ou recolher dados de monitorização. Consulte o artigo [Configurar o Proxy] de capítulo[ deployment-guide-configure-proxy] deste documento.

#### <a name="configure-monitoring"></a>Configurar a monitorização
Configurar Azure extensão de monitorização para SAP, tal como descrito no capítulo [configurar Azure avançada monitorização extensão para SAP] [implementação-guia-4,5] deste documento.
Verifique os pré-requisitos para a monitorização de SAP para versões de mínimas necessárias do SAP Kernel e agente de anfitrião do SAP nos recursos listados em capítulo [SAP recursos] [2.2 de guia de implementação] deste documento.

#### <a name="monitoring-check"></a>Verificação de monitorização
Verifique se o controlo está a funcionar conforme descrito capítulo [verifica e resolução de problemas para o programa de configuração do fim para fim monitorização do SAP no Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Mover uma VM no local utilizando um VHD de Azure não generalized com SAP
Este cenário é endereçar um sistema SAP simplesmente a ser movido nos seus formulários atuais e forma de no local para Azure maiúsculas/minúsculas. Meios sem nome desta forma a alterar o nome de anfitrião do Windows ou Linux e SAP SID ou algo ocorre o mais. Neste caso, o VHD não é referenciado como uma imagem durante a implementação mas diretamente é utilizado como o disco SO. No que respeita a implementação, neste caso difere os dois casos antigos pelo facto de que o agente VM não podem ser instalado automaticamente durante a implementação. Assim, o agente de VM Azure tem de ser transferido da Microsoft e precisa de ser instalada e ativada dentro da VM manualmente posteriormente. Depois dessa tarefa foi concluída com êxito, pode continuar a iniciar a extensão de Azure monitorização do SAP anfitrião e a respectiva configuração. Para obter detalhes sobre a função do agente de VM Azure, verifique este artigo:

[comment]: <> (Ligação Windows Update de MSSedusch TODO abaixo) 

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/Archive/2014/02/17/bginfo-Guest-Agent-Extension-for-Azure-VMS.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Guia de utilizador do agente de Linux Azure][virtual-machines-linux-agent-user-guide]

___

O fluxo de trabalho dos passos diferentes tem a seguinte apresentação:
 
![Fluxograma de implementação de VM para sistemas SAP a utilizar um disco VM][deployment-guide-figure-400]

Partindo do princípio que o disco está já carregado e definido no Azure (consulte o artigo [planeamento e implementação Guide][planning-guide]), siga estes passos

#### <a name="create-virtual-machine"></a>Criar máquina virtual
Para criar uma implementação do utilizando um disco SO privado através do Portal do Azure, utilize o modelo SAP publicado no [repositório de modelos de guia de introdução azure github][azure-quickstart-templates-github]. Também pode criar uma máquina virtual utilizando o [PowerShell ou clip de Azure manualmente.

* [modelo de configuração de camada de 2 (apenas uma máquina de virtual)][sap-templates-2-tier-os-disk]
    * Utilize este modelo se pretender criar um sistema de 2 camada utilizando apenas uma máquina virtual.

Depois de abrir o modelo acima, o Portal do Azure navega para o painel de editar parâmetros. Introduza as seguintes informações:

* **sapSystemId**: ID de sistema do SAP
* **osType**: tipo de sistema operativo que pretende implementar, Windows ou Linux
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAP o novo sistema irá fornecer. Se não tiver a certeza SAP quantos o sistema irá requerer, pergunte ao seu parceiro de tecnologia do SAP ou integrador de sistema
* **storageType**: (apenas para modelo de camada 2) tipo de armazenamento que deve ser utilizado 
    * Para sistemas maiores, a utilização de armazenamento de Premium é vivamente recomendada. Para obter mais informações sobre os tipos de armazenamento diferente, leia o artigo 
        * [Armazenamento do Microsoft Azure] [dbms-guia-2.3] [DBMS do guia de] [dbms guia]
        * [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho][storage-premium-storage-preview-portal]
        * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
* **osDiskVhdUri**: URI do SO privado do disco, por exemplo, https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
* **newOrExistingSubnet**: determina se devem ser criadas uma nova rede virtual e sub-rede ou uma sub-rede existente deverá ser utilizada. Se já tiver uma rede virtual que está ligada à rede no local, selecione existente.
* **subnetId**: O ID da sub-rede para que as máquinas virtuais devem estar ligadas. Selecione a sub-rede da sua rede virtual VPN ou encaminhar Express para ligar a máquina virtual à sua rede no local. O ID do aspeto normalmente /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Depois de introduzido todos os parâmetros, selecione a subscrição e o grupo de recursos que pretende utilizar. Pode selecionar um grupo de recursos existente ou crie um novo ao selecionar "+ novo" no menu pendente. Se criar um novo grupo de recursos, também tem de selecionar a região onde o grupo de recursos e a máquina virtual serão criados.

Reveja os termos de legais, aceitá-los e clique em criar.

#### <a name="install-vm-agent"></a>Instalar o agente VM
O agente VM já tem de estar instalado no disco SO se pretender utilizar os modelos acima. Caso contrário, a implementação irá falhar. Transferir e instalar o agente VM na VM conforme descrito capítulo [transferir, instalar e ativar o Azure VM agente] [implementação-guia-4.4] deste documento.

Se não utiliza os modelos acima, também pode instalar o agente VM posteriormente.

#### <a name="join-domain-windows-only"></a>Aderir a domínio (apenas para o Windows)
No caso que está ligada no local a implementação no Azure AD/DNS através do Azure Site para Site ou encaminhar Express (também referenciada como cruzada local na [planeamento e implementação Guide][planning-guide]), espera-se que a VM aderir a um domínio no local. Considerações deste passo sejam descritas no capítulo [participar VM para o domínio no local (apenas para o Windows)] [4.3 de guia de implementação] deste documento.

#### <a name="configure-proxy-settings"></a>Configurar definições de Proxy
Dependendo da sua configuração de rede no local, poderá ser necessário para configurar o proxy no seu computador virtual se estiver ligado à rede no local através de VPN ou encaminhar Express. Caso contrário, a máquina virtual poderá não conseguir aceder à internet e, por conseguinte, não é possível transferir as extensões de necessárias ou recolher dados de monitorização. Consulte o artigo [Configurar o Proxy] de capítulo[ deployment-guide-configure-proxy] deste documento.

#### <a name="configure-monitoring"></a>Configurar a monitorização
Configurar Azure avançada monitorização extensão para SAP, tal como descrito no capítulo [configurar Azure avançada monitorização extensão para SAP] [implementação-guia-4,5] deste documento.

Verifique os pré-requisitos para a monitorização de SAP para versões de mínimos necessários do SAP kernel e de agente de anfitrião do SAP nos recursos listados em capítulo [SAP recursos] [2.2 de guia de implementação] deste documento.

#### <a name="monitoring-check"></a>Verificação de monitorização
Verifique se o controlo está a funcionar conforme descrito capítulo [verifica e resolução de problemas para o programa de configuração do fim para fim monitorização do SAP no Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Cenário 4: Atualizar a configuração de monitorização para SAP
Existem casos onde que precisa para atualizar a configuração de monitorização:

* A equipa de MS/SAP conjunta alargado as capacidades de monitorização e decidido adicionar mais contadores ou eliminar alguns contadores. 
* Microsoft apresenta uma nova versão infraestrutura do Azure subjacente fornecer dados de monitorização e o Azure avançada monitorização extensão para SAP adaptação a essas alterações.
* Adicionar VHDs adicionais instalados para sua VM Azure ou remover um VHD. Neste caso, necessita de atualizar a coleção de armazenamento de dados relacionados. Se alterar a sua configuração ao adicionar ou eliminar pontos finais ou atribuir endereços IP para uma VM, isto irá não afeta a configuração de monitorização.
* Pode alterar o tamanho da sua VM Azure, por exemplo, a partir do A5 para qualquer outro tamanho da VM.
* Adicionar novas interfaces de rede para o seu VM Azure

Para atualizar a configuração de monitorização, proceda da seguinte forma:

* Atualizar a infraestrutura de monitorização ao seguir os passos explicados capítulo [configurar Azure avançada monitorização extensão para SAP] [implementação-guia-4,5] deste documento. Uma voltar a executar do script descrita neste capítulo irá detetar que uma configuração de monitorização é implementada e irá efetuar as alterações necessárias para a configuração de monitorização. 

___

> ![Windows][Logo_Windows] Windows
>
> Para a atualização do agente de VM Azure, é necessária sem intervenção do utilizador. Automática VM agente atualiza propriamente dito e não requer um reinício VM.
>
> ![Linux][Logo_Linux] Linux
>
> Siga os passos [neste] artigo[ virtual-machines-linux-update-agent] para atualizar o agente do Azure Linux. 

___

## <a name="detailed-single-deployment-steps"></a>Detalhadas passos de implementação única

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar os cmdlets do PowerShell do Azure
* Aceda a <https://azure.microsoft.com/downloads/>
* Na secção 'Da linha de comandos ferramentas' não existem é uma secção denominada 'Windows PowerShell'. Siga a ligação 'Instalar'.
* Gestor de transferência do Microsoft irá pop-up com um item de linha terminando .exe. Selecione a opção 'Executar'.
* Um pop-up seja reencaminhado para cima a perguntar se pretende executar o programa de instalação do Microsoft Web plataforma. Prima Sim
* É apresentado um ecrã como este:
 
![Ecrã de instalação para os cmdlets do PowerShell do Azure][deployment-guide-figure-500]
<a name="figure-5"></a>

* Prima instalar e aceitar o EULA.

Verifica com frequência se tiverem sido atualizados os cmdlets do PowerShell. Normalmente, existem atualizações durante um período mensal. A forma mais fácil para o fazer consiste em siga os passos de instalação, conforme descrito acima por excesso para o ecrã de instalação apresentado neste [] [ deployment-guide-figure-5] figura. Neste ecrã, é apresentada a data de lançamento dos cmdlets assim como o número de versão real. Salvo indicação de forma diferente em notas de SAP [1928533] ou [2015553], recomenda-se para trabalhar com a versão mais recente do cmdlets do Azure PowerShell.

Pode ser dada a versão atual dos cmdlets Azure no ambiente de trabalho/portátil instalada com o comando PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

O resultado deve ser apresentado conforme apresentado abaixo neste [] [ deployment-guide-figure-6] figura.

![Resultado da verificação da versão do cmdlet Azure PS][deployment-guide-figure-600]
<a name="figure-6"></a>

Se a versão do Azure cmdlet instalada no ambiente de trabalho/portátil aquele atual, o primeiro ecrã depois de iniciar o Microsoft Web plataforma Installer será o aspeto ligeiramente diferente em comparação com aquele apresentado neste [] [ deployment-guide-figure-5] figura.

Tenha em atenção o círculo vermelho abaixo na [figura] [ deployment-guide-figure-7] abaixo.
 
![Ecrã de instalação para cmdlets do PowerShell do Azure que indica que a versão mais recente do Azure PS cmdlets estão instalados][deployment-guide-figure-700]
<a name="figure-7"></a>

Se o ecrã parecer como [acima][deployment-guide-figure-7], que indica que a versão mais recente do Azure cmdlet já está instalada, não precisa de continuar com a instalação. Neste caso, pode 'saída' a instalação nesta fase.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementar o clip Azure
* Aceda a <https://azure.microsoft.com/downloads/>
* Na secção 'Da linha de comandos ferramentas' não existem é uma secção denominada 'Azure da linha de comandos interface'. Siga a hiperligação de instalação do seu sistema operativo.

Verifica com frequência se o clip do Azure tiverem sido atualizados. Normalmente, existem atualizações durante um período mensal. A forma mais fácil para fazer isto é seguir os passos de instalação, tal como descrito acima.

Pode ser dada a versão instalada atual do Azure clip no ambiente de trabalho/portátil com o comando:

```
azure --version
```

O resultado deve ser apresentado conforme apresentado abaixo neste [] [ deployment-guide-figure-azure-cli-version] figura.

![Resultado da verificação da versão do clip do Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Participar VM para o domínio no local (apenas para o Windows)
Em casos onde implementar SAP VMs para um cenário de publicação em local onde no local AD e DNS é expandido para Azure, é esperado que são associadas as VMs num domínio no local. Os passos detalhados do participar uma VM para um domínio no local e software adicional necessário para ser que um membro de um domínio no local é dependente de cliente. Participar normalmente uma VM para um domínio no local, significa que a instalar software como o software de proteção contra software maligno ou adicionais vários agentes do software de cópia de segurança ou monitorização.

Para além disso, tem de Certifique-se para os casos onde definições de proxy de Internet são forçadas ao participar um domínio, que o Account(S-1-5-18) de sistema Local do Windows na VM convidado tem também estas definições. É easiest forçar o proxy com política de grupo do domínio que se aplicam aos sistemas dentro do domínio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Transferir, instalar e ativar o Azure VM agente
Os passos seguintes são necessários quando uma VM para SAP for implementada a partir de imagens de SO que não seja generalized, por exemplo, não processada por Sysprep para Windows. Não é necessário instalar o agente para máquinas virtuais implementado a partir do Azure marketplace. Estas imagens já contêm o agente do Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Transfira o agente de Azure VM:
    * Transferir o pacote de instalação a partir do Azure VM agente: <https://go.microsoft.com/fwlink/?LinkId=394789>
    * Armazenar o pacote VM agente MSI localmente num portátil ou num servidor
* Instale o agente de Azure VM:
    * Ligar para a implementado VM do Azure com serviços de Terminal (RDP)
    * Abra uma janela do Explorador do Windows na VM e abra um diretório de destino para o ficheiro MSI do agente VM
    * Arrastar e largar o Azure VM agente ficheiro MSI Installer a partir do seu computador portátil/servidor local o directório de destino do agente VM na VM
    * Faça duplo clique no ficheiro MSI na VM
    * Para VM associado aos domínios no local, certifique-se de que as definições de proxy de Internet eventual aplicam-se para a conta de sistema Local do Windows (S-1-5-18) na VM, bem como descrito em capítulo [Configurar Proxy][deployment-guide-configure-proxy]. O agente VM irão executar neste contexto e tem de ser conseguir ligar ao Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Instale o agente VM para Linux utilizando o seguinte comando

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar o Proxy
Os passos para configurar o proxy diferem entre o Windows e Linux.

#### <a name="windows"></a>Windows
Estas definições também têm de ser válidas para a conta de sistema local aceder à Internet. Se as definições de proxy não estão definidas pela política de grupo, pode configurá-los para a conta de sistema local siga estes passos para configurá-lo.

1.  Abrir gpedit
1.  Navegue para a configuração do computador –> modelos administrativos -> componentes do Windows -> Internet Explorer e ativar o "efetuar proxy definições por máquina (em vez de por utilizador)
1.  Abra o painel de controlo e navegue até à rede e Internet -> Opções da Internet
1.  Abra o separador ligações e clique em definições de LAN
1.  Desativar "Detetar definições automaticamente"
1.  Ativar "Utilizar um servidor proxy para a sua LAN" e introduza o anfitrião de proxy e porta

#### <a name="linux"></a>Linux
Configure o proxy correto no ficheiro de configuração do Microsoft Azure Agent de convidado, que está localizado em /etc/waagent.conf. Tem de definir os seguintes parâmetros:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Reiniciar o agente depois de ter mudado sua configuração com

```
sudo service waagent restart
```

As definições de proxy /etc/waagent.conf também são aplicadas para as extensões de VM necessárias. Se pretender utilizar os Azure repositórios, certifique-se de que o tráfego para estes repositórios não vai através da intranet no local. Se tiver criado rotas de definidos pelo utilizador para ativar forçada túnel, certifique-se adicionar uma rota que encaminha o tráfego para os repositórios diretamente à Internet e não através da ligação de site para o site.

- **SLES** Também precisa de adicionar rotas para os endereços IP listados na /etc/regionserverclnt.cfg. Um exemplo é apresentado na captura de ecrã abaixo. 

- **RHEL** Também precisa de adicionar rotas para os endereços IP dos anfitriões listados na /etc/yum.repos.d/rhui-load-balancers. Um exemplo é apresentado na captura de ecrã abaixo.

Para obter mais detalhes sobre rotas de definidos pelo utilizador, consulte [Este artigo][virtual-networks-udr-overview].

![Forçada túnel][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar o Azure extensão melhorada de monitorização para SAP
Assim que a VM está preparada conforme descrito capítulo [implementação cenários de VMs para SAP no Microsoft Azure] [implementação-guia-3], o agente de VM Azure está instalado no computador. É o próximo passo importante implementar o Azure avançada monitorização extensão para SAP, que se encontra disponível no repositório de extensão Azure nos globais centros de dados do Microsoft Azure. Para obter mais detalhes, verifique se existem [planeamento e guia de implementação do] [9.1 de planeamento-guia]. 

Pode utilizar o Azure PowerShell ou clip de Azure para instalar e configurar o Azure avançada monitorização extensão para SAP. Leia capítulo [Azure PowerShell] [implementação-guia-4.5.1] se pretende instalar a extensão num Windows ou Linux VM a utilizar um computador Windows. Para instalar a extensão numa VM Linux utilizando um Linux o ambiente de trabalho leia capítulo [Azure clip] [4.5.2 de guia de implementação].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>PowerShell para Linux e VMs do Windows Azure
Para realizar a tarefa de instalação Azure avançada monitorização extensão para SAP, execute os seguintes passos:

* Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Microsoft Azure. Consulte o artigo capítulo [cmdlets do PowerShell de Azure implementar] [implementação-guia-4.1] deste documento.  
* Execute o seguinte cmdlet do PowerShell. Para uma lista de ambientes disponíveis, execute o commandlet Get-AzureRmEnvironment. Se pretender utilizar o Azure público, o seu ambiente é AzureCloud. Azure na China, selecione AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Depois de fornecido os dados da conta e o Azure Virtual Machine, o script implementar as extensões de necessárias e ativar as funcionalidades necessárias. Isto pode demorar alguns minutos.
Leia [Este artigo do MSDN] [ msdn-set-azurermvmaemextension] para mais informações sobre o AzureRmVMAEMExtension conjunto.
  
![Ecrã de resultado da execução bem sucedida SAP específico Azure cmdlet conjunto AzureRmVMAEMExtension][deployment-guide-figure-900]

Um executar com êxito do conjunto AzureRmVMAEMExtension executará todos os passos necessários para configurar o anfitrião da funcionalidade de monitorização para SAP. 

O resultado que deve entregar o script tem a seguinte apresentação:

* Confirmação de que a configuração de monitorização para VHD Base (que contém o SO), bem como todos os VHDs adicionais instalada para a VM ter sido configurada.
* As mensagens de duas estão a confirmar a configuração de métricas de armazenamento para uma conta de armazenamento específico. 
* Uma linha de saída irá dar-um estado sobre a actualização real da configuração de monitorização.
* Outro irá aparecer a confirmar que a configuração foi implementada ou atualizada.
* A última linha do resultado é informativa que mostra a possibilidade de teste a configuração de monitorização.
* Para verificar, todos os passos da Azure avançada monitorização que foram executados com êxito e que a infraestrutura do Azure fornece os dados necessários, continuar com a verificação de preparação para Azure avançada monitorização extensão para SAP, conforme descrito capítulo [preparação procurar Azure as funcionalidades de monitorização para SAP] [implementação-guia-5.1] neste documento. 
* Para continuar a este procedimento, aguarde 15-30 minutos até que o diagnóstico do Azure terá os dados relevantes recolhidos.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Clip Azure para Linux VMs

Para realizar a tarefa de instalar o Azure avançada monitorização extensão do SAP com o Azure clip, execute os seguintes passos:

1. Instalar o clip do Azure conforme descrito neste [] [ azure-cli] artigo
1. Início de sessão com a sua conta Azure

    ```
    azure login
    ```
1. Mudar para o modo de Gestor de recursos do Azure

    ```
    azure config mode arm
    ```
1. Ativar a monitorização de melhorado Azure

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Verifique se o Azure avançada monitorização está ativo na VM de Linux Azure. Verifique se existe /var/lib/AzureEnhancedMonitor/PerfCounters o ficheiro. Se existir, apresentar informações recolhidas por AEM com:

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    Em seguida, irá obter o resultado como:
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e resolução de problemas para a configuração do fim para fim de monitorização para SAP no Azure
Depois de ter implementado a VM Azure e configurar a infraestrutura de monitorização Azure relevante, verifique se todos os componentes da Azure avançada monitorização estão a funcionar de forma adequada. 

Por conseguinte, executar a verificação de preparação para a Azure avançada monitorização extensão para SAP conforme descrito capítulo [preparação procurar Azure avançada monitorização para SAP] [5.1 de guia de implementação]. Se o resultado desta verificação é positivo e obtém todos os contadores relevantes do desempenho, monitorização Azure foi configuração com êxito. Neste caso, avance com a instalação do agente de anfitrião SAP, tal como descrito nas notas SAP listados na capítulo [SAP recursos] [2.2 de guia de implementação] deste documento. Se o resultado da verificação de disponibilidade indica contadores em falta, avance executar a verificação de integridade de infraestrutura de monitorização do Azure conforme descrito capítulo [verificação de integridade de configuração de infraestrutura de monitorização Azure] [5.2 de guia de implementação]. Em caso de qualquer problema com a configuração de monitorização do Azure, verifique capítulo [ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP] [implementação-guia-5.3] para obter mais ajuda sobre como resolver problemas.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de preparação para a monitorização avançada Azure para SAP
Com esta verificação, certifique-se de que as métricas que serão apresentadas dentro da sua aplicação SAP fornecidas completamente pela infraestrutura de monitorização do Azure subjacente. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Executar a verificação de disponibilidade de uma VM do Windows
Para executar a verificação de disponibilidade, de início de sessão para a Azure Máquina Virtual (conta de administrador não é necessária) e execute os seguintes passos:

* Abra uma linha de comandos do Windows e altere para a pasta de instalação da extensão monitorização Azure para SAP C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop

A peça de versão fornecida no caminho para a extensão de monitorização acima pode variar. Se vir várias pastas da versão extensão monitorização na pasta de instalação, verifique a configuração do serviço Windows 'AzureEnhancedMonitoring' e mude para a pasta indicada como 'Path para executável'.
 
![Propriedades do serviço a executar o Azure avançada monitorização extensão para SAP][deployment-guide-figure-1000]

* Execute azperflib.exe na janela do comando sem quaisquer parâmetros.

> [AZURE.NOTE] O azperflib.exe é executado num ciclo e atualiza os contadores recolhidos cada 60 segundos. Para poder concluir o ciclo, feche a janela de comando.

Se a extensão Azure avançada monitorização não está instalada ou o serviço 'AzureEnhancedMonitoring' não está em execução, a extensão não foi configurada corretamente. Neste caso, siga capítulo [ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP] [implementação-guia-5.3] para obter instruções detalhadas como implementar a extensão.

##### <a name="check-the-output-of-azperflibexe"></a>Verifique a saída do azperflib.exe
O resultado de azperflib.exe mostra que todas as povoada contadores de desempenho Azure para SAP. Na parte inferior da lista de contadores recolhidos, localize um resumo e um indicador de estado de funcionamento, indica o estado da monitorização de Azure. 
 
![Resultado de verificação de integridade através da execução azperflib.exe que indica que não existirem problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verificar o resultado devolvido para o resultado da quantidade de «Total contadores», quais são reportados como vazia e para a 'verificação de integridade' conforme acima o figura [acima][deployment-guide-figure-11].

Pode interpretar os valores do resultado da seguinte forma:

| Valores Azperflib.exe resultados | Monitorizar o estado de disponibilidade do Azure |
| ------------------------------|----------------------------------- |
| **Total de contadores: vazia** | Os seguintes contadores armazenamento Azure 2 podem estar em branco: <ul><li>Abrir latência de leitura de armazenamento MS de servidor</li><li>Abrir latência de leitura de armazenamento E2E MS</li></ul>Todos os outros contadores tem de conter valores. |
| **Verificação de integridade** | Estado OK se remetente mostra apenas OK |

Se não crie ambos devolvem valores dos azperflib.exe mostram que todos os contadores povoados são devolvidos corretamente, siga as instruções da verificação de integridade para a configuração de infraestrutura de monitorização do Azure conforme descrito capítulo [verificação de integridade de configuração de infraestrutura de monitorização Azure] [implementação-guia-5.2] abaixo.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Executar a verificação de disponibilidade de uma VM Linux
Para executar a verificação de disponibilidade, ligar-se a SSH a máquina de Virtual do Azure e execute os seguintes passos:

* Verifique a saída do Azure avançada monitorização extensão
    * /var/lib/AzureEnhancedMonitor/PerfCounters mais
        * Deve dar-lhe uma lista de contadores de desempenho. O ficheiro não deve estar vazio
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | GREP erro
        * Deve devolver uma linha onde o erro é nenhum, por exemplo, 3; config; Erro; 0; 0; **nenhum**; 0; 1456416792; tst servercs;
    * /var/lib/AzureEnhancedMonitor/LatestErrorRecord mais
        * Deve estar vazias ou não deve existir
* Se a verificação do primeira acima não foi bem sucedida, realize estes testes adicionais:
    * Certifique-se de que o waagent estiver instalado e iniciado
        * sudo ls-al/var/lib/waagent /
            * deve listar o conteúdo do directório waagent
        * PS-ax | GREP waagent
            * deve ser semelhante a uma entrada ' python /usr/sbin/waagent-daemon'
    * Certifique-se de que a extensão de diagnóstico Linux estiver instalada e iniciada
        * sudo m - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
            * deve listar o conteúdo do directório Linux extensão de diagnóstico
        * PS-ax | GREP diagnóstico
            * deve ser semelhante a uma entrada ' python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py-daemon'
    * Certifique-se de que a extensão Azure avançada monitorização estiver instalada e iniciada
        * sudo m - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
            * deve listar o conteúdo do diretório do Azure avançada extensão de monitorização
        * PS-ax | GREP AzureEnhanced
            * deve ser semelhante a 'daemon de /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py python' uma entrada
* Instalar o agente de anfitrião do SAP conforme descrito na nota SAP [1031096] e verifique a saída do saposcol
    * Executar /usr/sap/hostctrl/exe/saposcol -d
    * Executar ccm de informação
    * Verifique se a métrica do "Virtualization_Configuration\Enhanced monitorização Access" for verdadeira
* Se já tiver um servidor de aplicações SAP NetWeaver ABAP instalado, abra transação ST06 e verifique se monitorização melhorada está ativada.

Se nenhuma das verificações acima falhas, siga capítulo [ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP] [implementação-guia-5.3] para obter instruções detalhadas como implementar a extensão.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de integridade para a configuração de infraestrutura de monitorização do Azure
Se alguns da monitorização de dados não são entregue corretamente como indicado pelo teste descrito capítulo [preparação procurar Azure avançada monitorização para SAP] [5.1 de guia de implementação] acima, execute o cmdlet AzureRmVMAEMExtension de teste para testar se a configuração atual da infraestrutura de monitorização do Azure e a extensão de monitorização para SAP está correta.

Para testar a configuração de monitorização, consulte execute a seguinte sequência:

* Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Microsoft Azure conforme descrito capítulo [cmdlets do PowerShell de Azure implementar] [implementação-guia-4.1] deste documento.
* Execute o seguinte cmdlet do PowerShell. Para uma lista de ambientes disponíveis, execute o commandlet Get-AzureRmEnvironment. Se pretender utilizar o Azure público, o seu ambiente é AzureCloud. Azure na China, selecione AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Depois de fornecido os dados da conta e o Azure Virtual Machine, o script irá teste a configuração da máquina virtual que escolher.

 
![Entrada ecrã do cmdlet Azure específico SAP VMConfigForSAP_GUI de teste][deployment-guide-figure-1200]

Depois de introduzir as informações sobre a sua conta e o Azure Virtual Machine, o script irá teste a configuração da máquina virtual que escolher.
 
![Resultado de teste com êxito, a infraestrutura de monitorização Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que cada verificação está marcada com OK. Se alguns dos controlos não estiverem ok, deve executar o cmdlet atualização conforme descrito capítulo [configurar Azure avançada monitorização extensão para SAP] [implementação-guia-4,5] deste documento. Outro aguarde 15 minutos e executar as verificações descritas capítulo [preparação procurar Azure as funcionalidades de monitorização para SAP] [5.1 de guia de implementação] e [verificação de integridade de configuração de infraestrutura de monitorização Azure] [implementação-guia-5.2] novamente. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, avance para capítulo [ainda mais resolução de problemas de infraestrutura de monitorização do Azure para SAP] [5.3 de guia de implementação].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Ainda mais resolução de problemas de monitorização do Azure infraestrutura do SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Contadores de desempenho Azure não aparecem em todos os
A coleção de métricas de desempenho no Azure é feita pelo serviço do Windows 'AzureEnhancedMonitoring'. Se o serviço de não ter sido instalado corretamente ou se não estiver em execução no seu VM, sem métricas de desempenho podem ser recolhidas de todo.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão Azure avançada monitorização estiver em branco 

###### <a name="issue"></a>Problema
O diretório de instalação C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop estiver em branco.

###### <a name="solution"></a>Solução
A extensão não está instalada. Verifique se é um problema de proxy (conforme descrito antes). Poderá ter de reiniciar o computador e/ou voltar a executar o script de script de configuração AzureRmVMAEMExtension conjunto

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Não existe suporte para as funcionalidades de monitorização Azure 

###### <a name="issue"></a>Problema
Serviço Windows 'AzureEnhancedMonitoring' não existe. Azperflib.exe: A saída de azperlib.exe emitir um erro conforme apresentado na [figura abaixo][deployment-guide-figure-14].
 
![Execução dos azperflib.exe indica que o serviço do Azure avançada monitorização extensão pela SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução
Se o serviço não existir conforme apresentado na [figura acima][deployment-guide-figure-14], a extensão de monitorização do Azure para SAP corretamente não foi instalada. Implementar a extensão de acordo com os passos descritos para o seu cenário de implementação do capítulo [implementação cenários de VMs para SAP no Microsoft Azure] [3 de guia de implementação]. 

Após a implementação da extensão, voltar a verificar se os contadores de desempenho Azure são fornecidos dentro da VM Azure após 1 hora.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Suporte para as funcionalidades de monitorização Azure existe, mas não inicia 

###### <a name="issue"></a>Problema
Serviço Windows 'AzureEnhancedMonitoring' existe e é activado mas não inicia. Verifique o registo de eventos de aplicação para obter mais informações.

###### <a name="solution"></a>Solução
Configuração incorretas. Voltar a ativar a extensão de monitorização para a VM conforme descrito capítulo [configurar Azure avançada monitorização extensão para SAP] [4,5 de guia de implementação].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alguns contadores de desempenho Azure estão em falta
A coleção de métricas de desempenho no Azure é feita pelo serviço do Windows 'AzureEnhancedMonitoring', obtém os dados a partir de várias origens. Alguns dados de configuração são recolhidos localmente, métricas de desempenho são lidos a partir dos diagnósticos do Azure e contadores que armazenamento são utilizados a partir do seu registo no nível de armazenamento de subscrição.

Se a resolução de problemas utilizando SAP nota [1999351] não ajuda, volte a executar o script de configuração AzureRmVMAEMExtension conjunto. Poderá ter de aguardar uma hora de uma vez que não podem ser criados contadores analytics ou diagnóstico de armazenamento imediatamente depois de se encontram ativadas. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-abrir NT AZR.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Contadores de desempenho Azure não aparecem em todos os

A coleção de métricas de desempenho no Azure é efectuada por um deamon. Se o deamon não está em execução, sem métricas de desempenho podem ser recolhidas de todo.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão Azure avançada monitorização estiver em branco 

###### <a name="issue"></a>Problema
O diretório/var/biblioteca/waagent/não contém um subdirectório para a extensão do Azure as funcionalidades de monitorização.

###### <a name="solution"></a>Solução
A extensão não está instalada. Verifique se é um problema de proxy (conforme descrito antes). Poderá ter de reiniciar o computador e/ou voltar a executar o script AzureRmVMAEMExtension do conjunto de configuração

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alguns contadores de desempenho Azure estão em falta

A coleção de métricas de desempenho no Azure é efectuada por um daemon que obtém os dados a partir de várias origens. Alguns dados de configuração são recolhidos localmente, métricas de desempenho são lidos a partir dos diagnósticos do Azure e contadores que armazenamento são utilizados a partir do seu registo no nível de armazenamento de subscrição.

Para uma completa e atualizados lista de problemas conhecidos Consulte o artigo SAP nota [1999351] que contém informações adicionais de resolução de problemas para o avançada Azure monitorização para SAP.

Se resolução de problemas utilizando SAP nota [1999351] não ajudá-lo, volte a executar o script de configuração conjunto AzureRmVMAEMExtension conforme descrito capítulo [configurar Azure avançada monitorização extensão para SAP] [4,5 de guia de implementação]. Poderá ter de aguardar uma hora de uma vez que não podem ser criados contadores analytics ou diagnóstico de armazenamento imediatamente depois de se encontram ativadas. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-abrir NT AZR para Windows ou BC-abrir-LNX-AZR para uma máquina de virtual Linux.
