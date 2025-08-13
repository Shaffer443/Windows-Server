Beleza, Rafael, agora entendi bem o cenário.  
Vamos separar em **duas partes** para ficar claro:

* * *

**1️⃣ O que significa cada campo**
----------------------------------

* **Active Ports** → Quantas conexões de rede ativas existem nesse Port Group/vSwitch (conta as VMs ligadas e usando essa rede, ou a própria Management Network).

* **VMs** → Quantas **máquinas virtuais** estão conectadas a esse Port Group.
  
  * Se está `0`, quer dizer que nenhuma VM está configurada para usar essa rede.

* **VLAN ID** → Número da VLAN que a rede está usando.
  
  * `0` = sem VLAN (rede padrão).

* **vSwitch0** → Switch virtual interno que liga VMs e uplinks físicos (vmnic).

* * *

**2️⃣ Como deixar a porta 2 (vmnic2) para a Internet**
------------------------------------------------------

No ESXi, cada porta física (vmnic) pode ser atribuída como uplink de um vSwitch.  
Se o seu **link dedicado** entra pela **porta 2 física do servidor**:

1. **Identificar a vmnic certa**
   
   * No ESXi → Networking → Physical NICs, veja qual vmnic corresponde à **porta física 2** (geralmente `vmnic1` ou `vmnic2`, dependendo da ordem).
   
   * Vai aparecer `Connected` se estiver com cabo e link ativo.

2. **Criar um Port Group para Internet**
   
   * Vá em **Networking → Port Groups → Add Port Group**.
   
   * Nome: `Internet`
   
   * vSwitch: escolha o vSwitch que vai usar essa vmnic dedicada.
   
   * VLAN ID: `0` (se não usar VLAN).

3. **Vincular uplink (vmnic)** ao vSwitch**
   
   * Em **Networking → vSwitches**, edite o vSwitch que usará a Internet.
   
   * Adicione a vmnic correspondente à porta física 2 como uplink.
   
   * Esse vSwitch pode ser **novo** só para Internet ou pode ser usado no vSwitch0 (mas cuidado para não misturar redes internas e externas se não for NAT).

* * *

**3️⃣ Ligando uma VM nessa Internet**
-------------------------------------

* Ao criar a VM (ex.: Windows Server para AD), na aba **Network Adapter**:
  
  * Escolha o **Port Group** que você criou (`Internet`).
  
  * Isso liga a placa de rede virtual da VM diretamente ao link dedicado.

* * *

📌 **Importante**:  
Se você ligar a VM diretamente no link dedicado **sem firewall**, ela ficará exposta na internet pública, o que é perigoso.  
Se a ideia é **distribuir internet para outras VMs ou rede interna**, você vai precisar de **duas interfaces na VM**:

* **Uma ligada ao link dedicado (WAN)**.

* **Outra ligada à rede interna (LAN)**.

Assim, você pode configurar NAT/roteamento dentro dessa VM (ex.: pfSense, Windows RRAS, etc.).

* * *

Se quiser, eu posso te desenhar o esquema do **vSwitch + vmnic + VMs** mostrando como ficaria com:

* **vmnic1** → Gerenciamento ESXi

* **vmnic2** → Internet dedicada

* **vmnic0** → Rede interna para estações

Quer que eu monte esse desenho?
