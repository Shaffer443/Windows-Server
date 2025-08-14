Esse erro que você está vendo é bem característico do problema de **CredSSP Encryption Oracle Remediation**, que começou a aparecer no Windows depois das atualizações de segurança de 2018.

Basicamente, o que acontece é:

* O **RDP** usa o **CredSSP** para transmitir credenciais com segurança.
* Depois da atualização de segurança da Microsoft, clientes e servidores precisam estar com a mesma configuração de segurança do CredSSP.
* Se o **Windows Server** ou o **computador cliente** estiver com uma política mais restritiva (ou desatualizada), o login remoto falha com esse erro de "bloqueio de autenticação NTLM" ou "não há suporte para a função solicitada".

---

## 🔹 Como resolver

Você tem **duas opções**:

### **1. Atualizar ambos os sistemas**

* Certifique-se que **o Windows Server** e **o computador cliente** estão com **todas as atualizações do Windows instaladas**.
* Isso garante que o CredSSP esteja na mesma versão e evite incompatibilidade.

---

### **2. Ajustar a Política de Grupo (GPO)**

Se não puder atualizar de imediato, você pode **reduzir temporariamente** a exigência do CredSSP:

1. No computador **cliente** ou **servidor**, abra:

   ```
   gpedit.msc
   ```
2. Vá para:

   ```
   Configuração do Computador → Modelos Administrativos → Sistema → Delegação de Credenciais
   ```
3. Encontre e habilite a opção:

   ```
   Correção do Oracle de Criptografia
   ```
4. No menu suspenso, escolha:

   ```
   Vulnerável
   ```
5. Clique em **Aplicar** e **OK**.
6. Em um Prompt de Comando (como administrador), execute:

   ```
   gpupdate /force
   ```

> ⚠️ **Atenção**: Colocar como "Vulnerável" é apenas para permitir a conexão temporariamente. Depois que atualizar o Windows Server e o cliente, volte para "Mitigated" ou "Enforced".

---

Se você quiser, posso já te passar **o comando do PowerShell** para fazer esse ajuste sem precisar abrir a GPO, o que é mais rápido para testes.

Quer que eu já prepare esse comando?
