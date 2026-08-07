# Domínio 1 — General Security Concepts

> Convenção: **termo em inglês** — explicação em português.

## 1.1 — Security Controls

**4 categorias** (quem/como implementa):

- **Technical** — implementado por tecnologia (firewall, antivírus, MFA, encriptação, ACLs).
- **Managerial** — políticas, procedimentos, avaliações de risco, planeamento de segurança. *(Firewall NÃO é managerial — é technical.)*
- **Operational** — executado por pessoas no dia-a-dia (formação, guardas, gestão de acessos, **log monitoring**).
- **Physical** — barreiras do mundo real (fechaduras, cercas, câmaras).

**6 tipos** (o que o controlo faz):

- **Preventive** — impede o incidente antes de acontecer (firewall, fechadura, DLP que bloqueia envio).
- **Deterrent** — faz pensar duas vezes, mas **não bloqueia** (placa de aviso, guarda visível, **lighting**, **fence**).
- **Detective** — identifica/regista o que aconteceu (IDS, câmaras, logs, SIEM, **FIM**).
- **Corrective** — repara e recupera após o incidente (backups, patch da vulnerabilidade, quarentena, reset de password de conta comprometida).
- **Compensating** — solução alternativa/temporária quando o controlo ideal não é possível (firewall à frente de um dispositivo sem patch).
- **Directive** — orienta o comportamento; é o **mais fraco**, só aponta a direção (policy/procedure, formação, placa "só pessoal autorizado").

> **Exame:** o mesmo controlo pode encaixar em várias células consoante o contexto. Os mais testados: **deterrent** (não bloqueia) e **directive** (o mais fraco).

**Termos de controlo que aparecem no exame:**
- **DLP (Data Loss Prevention)** — inspeciona dados e sinaliza/bloqueia exfiltração antes de saírem (pode ser preventive).
- **FIM (File Integrity Monitoring)** — deteta e alerta quando ficheiros são modificados (detective).
- **EDR (Endpoint Detection and Response)** — deteção/resposta ao nível do endpoint.
- **Availability controls** — **UPS**, **redundant connectivity/Internet**, **load balancers** (encriptação de disco NÃO é controlo de disponibilidade — é de confidencialidade).

## 1.2 — Fundamental Security Concepts

### CIA / AIC Triad
Estrutura base que qualquer empresa deve implementar.

- **Confidentiality** — só vê quem pode ver (encriptação, controlo de acesso, MFA).
- **Integrity** — chega ao destino tal e igual como foi enviado (hashing, digital signature, certificados, metadados).
- **Availability** — o sistema deve estar sempre acessível a quem pode; subdividir em componentes independentes (redundância) e fazer revisões regulares. *(Uma maintenance window que se prolonga ameaça a **availability**.)*

A tensão central é que nem sempre há equilíbrio: se um aumenta (p.ex. confidentiality), outro tende a diminuir (p.ex. availability).

### Non-repudiation
O emissor não pode negar que enviou algo, nem o destinatário negar que o recebeu tal e igual à forma como foi enviado. Encaixa no conceito de integridade.

**Hashing + Digital Signature = Non-repudiation.** Para não-repúdio de logs: **fazer hash → depois assinar digitalmente** (por esta ordem).

> **Exame:** a **symmetric encryption NÃO garante non-repudiation** (nem sempre autenticação) — para isso é preciso **asymmetric**.

### AAA Framework
- **Authentication** — provar quem se é.
- **Authorization** — definir a que recursos se tem acesso.
- **Accounting** — registo de tudo o que se faz (logs).

O **RADIUS** é o protocolo AAA mais usado; o **TACACS+** é outro protocolo AAA para dispositivos de rede. Fluxo RADIUS: **supplicant** (cliente) → **NAS (Network Access Server)** num ponto de acesso (tipo switch), que recebe as credenciais sem as validar e redireciona → **RADIUS Server**, onde as passwords são confirmadas.

**Authenticating people — os 3 fatores** (muito testado):
- **Something you know** — password, PIN.
- **Something you have** — token, smartphone, cartão.
- **Something you are** — biometria (guardada como *representação matemática*, não a imagem real).

Regra do **MFA (Multi-Factor Authentication)**: combinar fatores de **categorias diferentes**. Password + PIN **não é** MFA (ambos "know"); password + fingerprint **é**.

**Identification vs. Authentication** — *identification* é dizer quem és (username); *authentication* é prová-lo (password/MFA). São passos separados.

**Authenticating systems** — para provar que um dispositivo é da empresa e não um intruso, a **CA** emite um **certificate** único assinado; no login valida-se a assinatura. Os **certificates** identificam **pessoas E sistemas** — a *system authentication* é tipicamente **certificate-based**.

**Authorization models** — em vez de dar permissões utilizador-a-utilizador (não escala), usa-se **abstração** por **groups/roles**: define-se a permissão uma vez e adicionam-se utilizadores. Termos relacionados (detalhados em 4.6): **SSO (Single Sign-On)**, **Federation**, **OpenID (OIDC)**, **LDAP**, **Kerberos** (usa *ticket-granting tickets*).

### Gap Analysis
Estudo que compara o **programa de segurança tal como está implementado vs. as best practices** (ou os objetivos de controlo vs. os controlos que os cumprem). Passos: descrever o nível atual → avaliar o que temos → identificar fraquezas (a "lacuna") → **criar um plano de ação para a colmatar** → relatório final. Um **heat map** é um bom exemplo de relatório final.

### Zero Trust
Nada — utilizadores ou processos — está 100% seguro. Aplica **verificação contínua**. Divide-se em dois planos:

- **Data Plane** — onde a ação acontece. Contém o **subject/system** (quem quer aceder), as **implicit trust zones** e o **PEP (Policy Enforcement Point)**, que aplica a decisão (é o componente com que interages quando recebes o prompt de autenticação).
- **Control Plane** — onde as decisões são tomadas. O **Policy Engine** decide conceder/negar com base nas políticas e nos dados dos sistemas de segurança; o **Policy Administrator** comunica essa decisão ao PEP.

Fluxo: **subject** faz pedido → **PEP** intercepta e consulta o **Control Plane** → determina primeiro o **nível de confiança** → autoriza/nega → o PEP aplica.

Complementos: **Adaptive identity** (IP, tipo de ligação, dispositivo, login recente noutro dispositivo), **Threat scope reduction**, **Policy-driven access control** (regras a partir do estado de segurança do sistema + threat data), **security zones** (semelhante a *least privilege*).

> **Exame:** o **Policy Engine** automatiza políticas com dados de **SIEM**, **threat feeds** e **EDR** (não com dados de sensores físicos). **"Business model" NÃO é** pilar do NIST Zero Trust Maturity Model (Identity, Devices, Networks, etc. são).

### Physical Security
- **Bollards** — deixam passar pessoas, bloqueiam **veículos**; canalizam o fluxo (betão, água, etc.).
- **Access control vestibule (mantrap)** — **duas portas em série**; contornar por *tailgating* exige que te deixem passar duas portas, não uma.
- **Fencing** — perímetro; funciona como **deterrent**.
- **Lighting** — perímetro; é **deterrent**.
- **Video surveillance** — **detective**; o maior custo **contínuo** não são as câmaras, é o **armazenamento** do vídeo.
- **Security guard** — o controlo físico **mais caro** e, ao mesmo tempo, o de **maior flexibilidade** (lida com exceções que um controlo automático não trata).
- **Access badge** — crachá **RFID** (leitor sem fios + foto impressa).
- **Sensors**: **Infrared** (calor/movimento; bom em open office sem excesso de sensibilidade), **Pressure** (peso/passos), **Microwave** (movimento em área ampla), **Ultrasonic** (ondas sonoras e reflexão).

### Deception and Disruption
- **Honeynet** — infraestrutura completa com múltiplos honeypots (routers, servidores, workstations).
- **Honeypot** — sistema isolado (físico/virtual) que simula um ambiente real, mas com ficheiros/objetos falsos.
- **Honeyfile** — ficheiro falso (isco).
- **Honeytoken** — dado rastreado que, se copiado/usado, revela de imediato o intruso (funciona bem com **DLP** para detetar exfiltração).
- **Honeyfarm** — coleção de vários honeypots numa rede, para capturar informação de ameaças em maior escala.

## 1.3 — Change Management
Uma mudança não controlada pode afetar sistemas inteiros. Usa-se um processo com **SOP (Standard Operating Procedure)** — garante um processo consistente para cada mudança. Só se faz o que está documentado, mesmo que demore mais.

1. **Approval process** — formulário a pedir autorização.
2. **Stakeholders** e **ownership** (quem é dono da mudança); **impact analysis** (uma **lista de dependências** ajuda a avaliar o impacto).
3. Verificar riscos.
4. **Test results** (testar em ambiente de teste — é a melhor forma de estimar **downtime** e apanhar problemas de dependências) e **backout plan** (o que fazer se a mudança falha). Podem usar-se **allow lists** / **deny lists**.
5. **Approval → Maintenance window → Implementation**, pelo **CCB (Change Control Board)**.

**Technical implications:**
- **Downtime / service restart / application restart** — muitas mudanças obrigam a parar/reiniciar (a maior preocupação de um restart costuma ser o **downtime**).
- **Legacy applications** e **dependencies** (conectividade a BD/rede, acesso ao sistema de autenticação, disponibilidade de *network time*, etc.).
- **Restricted activities** — durante a janela restringem-se atividades como patching, scaling de clusters, mudar hostnames e alterar configs de BD.
- **Allow lists / deny lists** — allow list é restritiva por natureza (para websites, é **trabalhosa de manter**).

**Documentation (pós-mudança):** atualizar **diagrams** e **policies/procedures**. *(Atualizar **contratos** NÃO faz parte da change management.)*

**Version control** (tipicamente **git**): guarda automaticamente o histórico, mas **não faz testes por si**.
- **Atomic operations** — verificam conflitos entre commits.
- **File locking** — check-out para só um colaborador editar de cada vez.
- **Tagging and labeling** — marcar/ver versões.
> **Exame:** **regression testing NÃO é** feature de version control; rastrear a *carga de trabalho* de cada contribuidor não é uma razão de *segurança*.

## 1.4 — Cryptographic Solutions

### Encryption — Symmetric vs. Asymmetric
- **Symmetric** — uma única **chave partilhada/secreta** para cifrar grandes volumes rapidamente (todos os participantes têm a mesma chave). Não oferece non-repudiation.
- **Asymmetric** — par **public key** + **private key**, para digital signatures e authentication; resolve o problema do **key exchange**.

**Regra direcional (decorar):**
- **Encrypt** → com a **public key do destinatário**; **Decrypt** → com a **private key do destinatário**.
- **Sign** → com a **private key do emissor**; **Verify** → com a **public key do emissor**.

**Digital Signature vs. Certificate** — a *signature* é um algoritmo gerado pela **private key** que garante consentimento/autoria. O *certificate* é um "bilhete" que associa uma **public key** à respetiva pessoa/sistema.

**Key exchange** — **Diffie-Hellman** e **RSA** são algoritmos de **key exchange**:
- **Out-of-band** — entregar em mãos/courier;
- **In-band** — usar assimétrica (DH/RSA) para acordar a chave simétrica;
- **Session keys** — chave temporária (ephemeral) criada por sessão.
> Nunca se transmite a **private key** — o que se partilha/estabelece é a **chave simétrica (de sessão)**.

**Transport/communication encryption** — **TLS**, **IPSec**, **SSH** são protocolos de cifra em trânsito. *(SAML NÃO é — é federação/SSO.)*

### Encryption Levels
- **Full-disk encryption** — protege dados se o dispositivo for roubado estando bloqueado/desligado (BitLocker, FileVault).
- **Partition / Volume-level** — cifra uma partição/volume.
- **File-level** — cifra ficheiros; suporta segurança granular **por utilizador** e protege dados em trânsito entre shares e em repouso (EFS).
- **Database encryption** (3 tipos comuns): **Transparent Data Encryption (TDE)**, **Column-level**, **Field-level**. **Record-level** associa-se a **bases de dados**. *("Sensitivity-based" NÃO é um tipo comum.)*

### Password Protection
- **Hashing** — impressão digital de tamanho fixo, de sentido único.
- **Salting** — adicionar dados aleatórios antes do hash (derrota rainbow tables).
- **Pepper** — como o salt, mas com um **segredo do sistema** guardado à parte.
- **Purpose-built password hashing algorithm** — bcrypt, PBKDF2, Argon2 (feitos para serem lentos).
- **Key stretching** — repetir o hash milhares de vezes para tornar o brute-force mais lento.
- **Salt:** password + salt → hash | **Pepper:** password + salt + (segredo) → hash.

**Hash algorithms:** **MD5** e **SHA-1** são **inseguros** (colisões conhecidas) → usar **SHA-256**. **AES** é cifra, **não** é hash.

### Tools
- **TPM (Trusted Platform Module)** — gera/guarda/gere chaves no dispositivo; resistente a brute-force (lockout); valida **secure boot** e é **root of trust** do dispositivo.
- **HSM (Hardware Security Module)** — gera/guarda chaves e faz operações cripto em escala; *não* valida secure boot.
- **KMS (Key Management System)** — serviço (muitas vezes cloud) para guardar/gerir *secrets* e atribuir chaves.
- **Secure enclave** — porção dedicada do SoC (p.ex. Apple) que guarda chaves e biometria.

### Obfuscation
- **Steganography** — esconder dados dentro de ficheiros inócuos (imagens, vídeos, tráfego).
- **Data masking** — ocultar caracteres (p.ex. `XXXX-XXXX-XXXX-1234`).
- **Tokenization** — substituir o dado por um **token** sem valor intrínseco; um "guarda-roupa" onde uma chave temporária diz onde está o original (pagamentos; usa **Token Service Server**).

### PKI & Certificates
- **Public Key Infrastructure (PKI)** — gere **public key**, **private key** e **key escrow** (guardar a chave, p.ex. do BitLocker, para recuperação — repositório que aceita upload mas não leitura).
- **Certificate Authority (CA)** — emite e assina certificados. **Subordinate CA** — emite certificados evitando o custo/risco de expor a **root CA**.
- **Web of trust** — X confia em Y, Y em Z, logo X confia em Z.
- **CSR (Certificate Signing Request)** — pedido gerado pelo servidor (p.ex. via `openssl req`); campos típicos: common name, nome legal, email (o **telefone não** é campo típico).
- **Wildcard certificate** — cobre vários subdomínios (`*.dominio.com`).
- **Self-signed certificate** — barato para testes internos, mas falha a validação de **root of trust** (browsers avisam).
- **Third-party certificate** — de CA comercial; permite certificar domínios/serviços para além do do provedor.
- **CRL (Certificate Revocation List)** — lista os certificados **revogados** antecipadamente (não os que expiraram na data). Se uma **private key** é exposta → **revogar** e pôr na CRL.
- **OCSP (Online Certificate Status Protocol)** — protocolo para verificar o estado atual (revogado ou não) de um certificado.
- **Root of trust** — numa cadeia de certificados, o root of trust é o **root certificate**; num arranque, é o módulo seguro que valida cada fase assinada do **boot**.

### Blockchain
- **Blockchain** — registo **shared + immutable**: uma transação errada **não pode ser apagada**; corrige-se com uma nova, ficando ambas no registo. Descentralizado = **ninguém** (indivíduo ou grupo) o controla.
- **Open public ledger** — **qualquer pessoa se pode juntar a qualquer momento**, sem votação nem aprovação.

## Notas
- **Root of trust** — valida que todos os processos do boot correm corretamente e sem exceções.
- **Contratos** atualizam-se via *agreement types* entre empresas, não dentro da change management.

## Distratores comuns (revisão rápida)
- **MFA** = fatores de **categorias diferentes** (password + PIN ≠ MFA).
- **Symmetric** não dá **non-repudiation** (precisa de asymmetric).
- **Encrypt** = public key; **Decrypt** = private key; **Sign** = private; **Verify** = public.
- **MD5/SHA-1** inseguros → **SHA-256**; **AES ≠ hash**.
- **TLS/IPSec/SSH** = cifra em trânsito; **SAML** não.
- **Access control vestibule** = **duas portas**.
- **Directive** = mais fraco; **deterrent** = não bloqueia.
- **Security guard** = mais caro **e** mais flexível; **video surveillance** = custo maior é o armazenamento.
- **Blockchain** = shared + immutable; **open public ledger** = qualquer um se junta.
- **CSR** não inclui telefone; **root of trust** numa cadeia = **root certificate**.
- **"Business model"** não é pilar do NIST ZTMM.
- **Regression testing** não é feature de version control.
