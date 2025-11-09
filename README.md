# ⚙️ Auditoria de Força Bruta com Medusa e Kali Linux - DIO & Santander

Este repositório documenta o projeto prático de simulação de ataque de Força Bruta do curso de **Cibersegurança da Digital Innovation One (DIO) & Santander**.

O objetivo foi configurar um ambiente controlado para simular cenários reais de quebra de credenciais utilizando a ferramenta **Medusa** do Kali Linux, e analisar as vulnerabilidades para propor medidas de mitigação eficazes.

---

## 🔬 1. Metodologia e Configuração do Ambiente

### 1.1. Ambiente de Teste
A simulação foi executada em um ambiente virtualizado e isolado (rede Host-Only no VirtualBox):
* **Atacante:** Kali Linux VM (ferramentas Medusa, Nmap, Enum4linux).
* **Alvo Vulnerável:** Metasploitable 2 VM (serviços FTP, SMB) e DVWA (formulário web vulnerável).

### 1.2. Fases do Ataque (Testes de Penetração)
A auditoria foi dividida em três cenários, demonstrando a versatilidade dos ataques de Força Bruta:

| Cenário | Serviço Alvo | Objetivo | Ferramentas |
| :--- | :--- | :--- | :--- |
| **1. Força Bruta Direta** | FTP (Porta 21) | Identificar credenciais fracas em um serviço exposto. | Nmap, Medusa |
| **2. Ataque a Formulário Web** | DVWA (HTTP) | Automatizar tentativas de login em uma interface web sem bloqueio de tentativas. | Medusa (Módulo HTTP/FORM) |
| **3. Ataque em Cadeia (Spray)** | SMB (Porta 445) | Combinar enumeração de usuários e ataque de Password Spraying, testando poucas senhas em muitos usuários. | Enum4linux, Medusa (Módulo SMBNT) |

---

## 💻 2. Detalhamento dos Ataques Simulados

### 2.1. Cenário FTP: Força Bruta

1.  **Reconhecimento:** Uso do `Nmap` para confirmar o serviço FTP ativo.
2.  **Ataque:** O comando `medusa -h 192.168.56.102 -U wordlists/users.txt -P wordlists/pass.txt -M ftp -t 6` foi executado para testar todas as combinações.
3.  **Resultado:** Login de sucesso: `msfadmin:msfadmin`.

### 2.2. Cenário Web: Quebra de Formulário

1.  **Análise:** Identificação dos parâmetros do formulário de login do DVWA (`username=^USER^$password=^PASS^&Login=Login`).
2.  **Ataque:** O comando `medusa -h [IP] -U ... -P ... -M http -m PAGE... -m FORM...` automatizou a injeção das credenciais no formulário.
3.  **Resultado:** Login de sucesso: `admin:password`.

### 2.3. Cenário SMB: Password Spraying

1.  **Enumeração de Usuários:** Uso do `enum4linux -a [IP]` para listar contas de usuário válidas (dados registrados em `outputs/enum4_output.txt`).
2.  **Ataque Spray:** O comando Medusa foi configurado para testar poucas senhas (em `senhas_spray.txt`) em uma lista maior de usuários (em `smb_users.txt`).
3.  **Resultado:** Acesso de validação ao serviço SMB confirmado via `smbclient -L //192.168.56.102 -U msfadmin`.

---

## 🛡️ 3. Mitigação e Estratégias de Defesa

A principal lição deste módulo é que senhas fracas e serviços expostos representam uma ameaça crítica. As seguintes medidas são essenciais para mitigar ataques de Força Bruta:

1.  **Políticas de Senha Fortes:** Imposição de senhas longas (mínimo 14 caracteres), complexas e imprevisíveis.
2.  **Bloqueio de Contas (Account Lockout):** Implementar bloqueio automático de uma conta após um pequeno número de tentativas falhas (ex: 3 a 5 tentativas).
3.  **Controle de Acesso de Rede:** Utilizar Firewalls ou Listas de Controle de Acesso (ACLs) para restringir o acesso a serviços sensíveis (como FTP/SMB) apenas a IPs confiáveis (VPNs ou rede interna).
4.  **Desativação de Serviços:** Desativar ou remover serviços desnecessários (`nmap -sV` pode revelar serviços que não deveriam estar ativos).
5.  **Autenticação de Múltiplos Fatores (MFA):** O MFA anula a eficácia de quase todos os ataques de força bruta, pois a senha sozinha não é suficiente para acesso.

---
## 👩‍💻 Autora
**Ravena Souza**  
Projeto prático desenvolvido para o curso **Cibersegurança - Universia / DIO**
