Isso mesmo, Rafael ✅

Pelo que você descreveu:

* **Adapter 1** (no ESXi) → Port Group `Internet` → **Ethernet0** (no Windows) → Cabo vindo do **link dedicado (vmnic1)**.

* **Adapter 2** (no ESXi) → Port Group `VM Network_LAN` → **Ethernet1** (no Windows) → Rede interna (vmnic0).

* * *

📌 **Ou seja:**

* Ethernet0 → Conectada direto na WAN (provedor).

* Ethernet1 → Conectada à LAN (sua rede interna).

* * *

💡 Agora, **se dentro do Windows Server a Ethernet0 está dizendo “Cabo desconectado”**, significa que:

1. **vmnic1** no ESXi não está realmente com link ativo (cabo errado, porta de switch errada, ou desligada no provedor).

2. Ou o provedor exige **VLAN** ou autenticação PPPoE e não vai entregar IP automaticamente.

3. Ou a placa virtual no ESXi está com tipo incompatível (às vezes `VMXNET3` resolve).

* * *

⚠ **Importante**:  
Se você ligar o Windows Server direto na internet, ele **não vai compartilhar internet sozinho** — você precisará configurar:

* **RRAS (Routing and Remote Access)** no Windows Server,  
  ou

* Usar uma VM de firewall (pfSense, Mikrotik CHR) que tem as duas placas e faz NAT para a LAN.

* * *

Se quiser, posso te montar **o esquema completo** de como o tráfego vai fluir, com vmnic0 (LAN), vmnic1 (WAN) e Windows Server roteando internet para sua rede interna, para ficar claro como configurar no ESXi e no Windows.  
Quer que eu monte?
