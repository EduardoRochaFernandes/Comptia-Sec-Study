# Domínio 5 — Security Program Management & Oversight

> Convenção: **termo em inglês** — explicação em português. (Baseado no teu resumo + tópicos do chat; corrigi o essencial e acrescentei o que faltava dos objetivos 5.1–5.6.)

## 5.1 — Security Governance

### Guidelines, Policies, Standards, Procedures
Hierarquia dos documentos (framework típico = **Policies, Standards, Procedures, Guidelines**):
- **Policy** — *declaração de intenção* de alto nível (o "porquê"). Não é o detalhe.
- **Standard** — define **como** as regras reforçam a policy (o "quê", obrigatório; ex.: AES-256, HTTPS).
- **Procedure** — processo passo-a-passo (o "como" detalhado).
- **Guideline** — **conselho/recomendação** (não obrigatório) para cumprir policies e standards.

**Policies (tipos):**
- **AUP (Acceptable Use Policy)** — descreve o uso aceitável da rede/sistemas pelos utilizadores (assinado no onboarding).
- **Information security policies**, **Business continuity (BC)**, **Disaster recovery (DR)**, **Incident response (IR)**, **SDLC (Software Development Lifecycle)** (documenta ex.: Agile + CI/CD), **Change management**.
  - *Policy vs Plan:* a **policy** = intenção de alto nível; o **plan** = os passos detalhados da resposta.
  - **BC plan** = manter a organização a funcionar durante uma falha (sistema/pessoa/natural). **DR plan** = o que fazer **antes, durante e depois** de um desastre. **COOP** = continuidade de operações.

**Standards:** **Password** (armazenar com **hash + salt**, min. length, sem hints/expiração forçada — NIST SP 800-63B; **não** usar perguntas de conhecimento tipo "nome de solteira da mãe" porque se descobrem nas redes sociais), **Access control** (login por horário/role, DAC/MAC), **Physical security** (CCTV, RFID badges, altura das cercas), **Encryption** (AES-256, HTTPS a todo o tráfego).

**Procedures:** **Change management** (só uma mudança de cada vez; desvios a um processo de segurança aprovam-se **via change management**; tipos: standard, preauthorized, emergency — *"legislated" não é um tipo*), **Onboarding/Offboarding**, **Playbooks** (checklist detalhado passo-a-passo para um cenário específico, ex.: ransomware, DoS).

**External considerations:** Regulatory, Legal, Industry, Local/regional, National, Global.
**Monitoring and revision** — rever policies usando logs, feedback do staff e revisão de regulações (**NDAs não** ajudam a rever policies).

### Governance structures
- **Boards** — supervisão de estratégia/direção; reúnem poucas vezes por ano; **eleitos pelos shareholders**.
- **Committees** — grupo de apoio (SMEs, gestão, jurídico interno — **não** reguladores).
- **Government entities** — reguladores externos (ex.: US Treasury regula bancos).
- **Centralized vs. Decentralized** — controlo central vs. delegado às business units.

### Roles for systems and data
- **Data owner** — cargo **mais sénior**; classifica, protege e garante qualidade dos dados (**não** os processa).
- **Data controller** — decide o **propósito e os métodos** de processamento.
- **Data processor** — processa os dados **em nome do controller** (ex.: empresa de analytics).
- **Data custodian / steward** — garante a **segurança** / a **qualidade** dos dados (o custodian atribui **data labels**).
- **Data subject** — a pessoa identificável pelos dados (quem exerce o *right to be forgotten* continua a ser data subject).

**Onboarding** — criar conta com permissões corretas + **AUP** assinado + training + IAM/dispositivo.
**Offboarding** — **desativar (não apagar)** a conta, devolver equipamento, mudar passwords de contas partilhadas (*copiar ficheiros para uma pen do ex-colaborador **não** faz parte*).
**SOAR (Security Orchestration, Automation and Response)** — centraliza várias apps de segurança num só fluxo.

## 5.2 — Risk Management
Processo periódico para análise do sistema de modo a descobrir riscos e definir KRIs.

### Risk assessment (tipos)
- **Ad hoc** — perante um novo risco/necessidade rápida com expertise interna (ex.: avaliar aquisição de subsidiária depressa).
- **One-time** — evento específico (ex.: instalar novo software).
- **Recurring** — periódico (compliance regulatório é tipicamente **recurring**).
- **Continuous** — associado a pipelines **CI/CD**.

### Risk analysis
- **Qualitative** — escala **low/medium/high** por equipa experiente (rápido, mas **subjetivo**).
- **Quantitative** — valores **monetários** (precisa de dados financeiros).
- **Fórmulas (decorar!):**
  - **EF (Exposure Factor)** — **% do valor** do ativo perdido no evento.
  - **SLE (Single Loss Expectancy)** = **AV × EF** (perda de **um** evento).
  - **ARO (Annualized Rate of Occurrence)** — nº de vezes por **ano** (1 vez em 4 anos → ARO = **0.25**; ARO 3 = 3×/ano).
  - **ALE (Annualized Loss Expectancy)** = **SLE × ARO** (perda esperada por ano).
  - *Exemplo:* AV $50 000, EF 50%, ARO 2 → SLE = $25 000 → ALE = **$50 000**.
- **Risk = Likelihood × Impact** (ou Probability × Impact). **Likelihood** = possibilidade (rare/possible/certain); **Probability** = média numérica.

### Risk register & governance
- **Risk register** — documento que enumera e classifica os riscos.
- **KRI (Key Risk Indicator)** — deve ser **acionável, mensurável, relevante** (*"inexpensive" não é característica-chave*).
- **KPI (Key Performance Indicator)** - número que mostra se estamos a alcança um objetivo definido
- **Risk owner** — responsável por gerir um risco específico.
- **Risk threshold** — nível de impacto/probabilidade a partir do qual se aceita ou se trata o risco.
- **Risk tolerance** — variação tolerada face ao plano. **Risk appetite** — quanta incerteza a empresa aceita para atingir objetivos: **Expansionary** (aceita muito, para expandir), **Conservative** (governo/regulado), **Neutral** (equilíbrio).

### Risk strategies
- **Transfer** — passar a terceiros (ex.: **cybersecurity insurance**).
- **Accept** — aceitar; **Exception** (temporária) ou **Exemption** (permanente, ex.: legacy útil). *Categorizar **residual risk** é mais importante na **aceitação**.*
- **Avoid** — eliminar o risco (ex.: mover o datacenter fora da zona de cheias).
- **Mitigate** — reduzir impacto (ex.: WAF contra SQLi, segmentar rede, EDR/backups contra ransomware, patch imediato).

### Risk reporting & BIA
- **Risk reporting** — a gestão de topo quer visual: **risk matrix** (resumo do register), **risk trend analysis** (evolução ao longo do tempo).
- **BIA (Business Impact Analysis)** — identifica **funções críticas**: com que frequência falham, quanto demora repor e os objetivos de recuperação. Usa:
  - **RTO (Recovery Time Objective)** — tempo até estar **operacional**.
  - **RPO (Recovery Point Objective)** — **quantos dados** se podem perder (define a frequência de backup; **journaling** melhora o RPO).
  - **MTTR (Mean Time To Repair)** — tempo médio para **reparar** (mede a *maintainability*).
  - **MTBF (Mean Time Between Failures)** — vida útil média / tempo entre falhas = **uptime total / nº falhas** (MTBF 300 000h → 1.ª falha esperada às ~300 000h).
  - **AV (Asset Value)** — custo original, depreciado **ou** de substituição (à escolha da organização).

## 5.3 — Third-Party Risk

### Vendor assessment
- **Penetration testing** (com **rules of engagement**), **Right-to-audit clause** (auditar o vendor de tempos a tempos — **cloud IaaS raramente aceita**, pelo risco aos outros clientes), **Evidence of internal audits** (pedir se não houver auditoria de terceiro), **Independent assessments**, **Supply chain analysis** (a maioria dos ataques vem daqui; ex.: falta de SSDs/CPUs = **supply chain risk**).
- **SOC 2 report** — **Type 1** (num ponto no tempo) vs **Type 2** (ao longo do tempo, mais fiável); pede-se um **independent third-party audit**.

### Vendor selection
- **Due diligence** — investigar o terceiro **antes** de negociar. **Due care** = atividades **internas** de compliance. (Due diligence = terceiros; due care = interno.)
- **Conflict of interest** — o vendor tem um interesse concorrente que prejudica o cliente.

### Agreement types
- **SLA (Service Level Agreement)** — termos mínimos de serviço (ex.: **uptime 99.9%**), com penalidades definidas no contrato.
- **MOU (Memorandum of Understanding)** — objetivos amplos, **menos formal** que um contrato.
- **MOA (Memorandum of Agreement)** — mais detalhado que o MOU; pode ter partes legais; define papéis/responsabilidades.
- **MSA (Master Service Agreement)** — contrato-base para tudo; depois criam-se **SOW/Work Order** para o trabalho específico.
- **SOW (Statement of Work) / Work Order** — tarefas/entregáveis concretos sob o MSA.
- **NDA (Non-Disclosure Agreement)** — protege dados confidenciais (unilateral/bilateral/multilateral).
- **BPA (Business Partners Agreement)** — reparte lucros/perdas e regula adição/remoção de parceiros.
- **ISA (Interconnection Security Agreement)** — requisitos de segurança ao interligar sistemas IT.

### Ongoing
- **Vendor monitoring** — verificar desempenho contínuo (via **KPIs**, auditorias). **Questionnaires** — forma mais **simples** de recolher práticas de vários vendors. **Rules of engagement** — âmbito, horários, dados sensíveis, contactos de emergência (*lista de passwords **não** faz parte*).

## 5.4 — Compliance
- **Compliance reporting:** **Internal** e **External** (este é impulsionado por **reguladores + obrigações contratuais**).
- **Consequences of non-compliance:** **Fines** (a consequência estatutária mais comum), **Sanctions** (mais sobre **países** que empresas), **Reputational damage** (crítico quando houve negligência — caso **Uber**: esconder o breach agravou tudo), **Loss of license** (maior dano **operacional**), **Contractual impacts**.
- **Compliance monitoring:** **Due diligence/care**, **Attestation and acknowledgement** (um executivo assina a veracidade do compliance), **Internal/External**, **Automation** (melhor para compliance **contínuo**, ex.: PCI DSS).
- **CCO (Chief Compliance Officer)** — garante conformidade e informa a organização.
- **Privacy:**
  - **Legal implications:** Local/regional → National (ex.: **HIPAA**) → Global (ex.: **GDPR**).
  - **Data subject** — pessoa identificável pelos dados.
  - **Controller vs Processor**, **Ownership**, **Data inventory** (lista de todos os dados recolhidos) e **retention** (ex.: transações financeiras 7 anos).
  - **Right to be forgotten** — direito (GDPR) de pedir a **eliminação** dos dados pessoais.
  - **DPO (Data Protection Officer)** — exigido pelo **GDPR**.

**Regulações-chave:** **SOX** (financeiro), **HIPAA** (saúde), **GLBA** (privacidade financeira — origem das cartas de privacidade do banco), **PCI DSS** (cartões de crédito), **GDPR** (UE — controlo dos dados ao indivíduo), **COPPA** (crianças).

## 5.5 — Audits & Assessments
- **Audit vs Attestation** — o **audit** é o **processo de examinar**; a **attestation** é a **opinião formal de veracidade** sobre os resultados (audita-se primeiro, depois atesta-se).
- **Internal audit** — dentro da organização; **audit committee** (do **board of directors**) inicia/termina; começa por **self-assessments** (ex.: formulário PCI DSS submetido ao banco).
- **External audit** — exigido por certas regulações (tem de ser **terceiro**); **Regulatory** (ex.: HIPAA/GDPR/PCI DSS por terceiro = external regulatory/compliance audit), **Examinations** (mais simples/barato que audit), **Independent third-party audit** (ex.: **ISO 27001**).
- **Penetration testing (tipos):**
  - **Physical** — entrar por portas/janelas/tailgating; testar vestibule/badges/guardas.
  - **Offensive** (red team, ataca), **Defensive** (blue team, deteta/bloqueia em tempo real), **Integrated** (red+blue em loop contínuo).
  - **Known / Partially known / Unknown environment** — full disclosure / misto / sem info ("blind test"; *known* = máxima info e otimiza tempo; *unknown* = simula atacante).
  - **Reconnaissance:** **Passive** (sem tocar no alvo — Shodan, OSINT, redes sociais, dumpster diving; **não** deixa rasto) vs **Active** (interage — **nmap/port scan**, DNS queries, OS fingerprinting; **deixa rasto** nos logs).

## 5.6 — Security Awareness
- **Phishing:** **Campaigns** (simulações), **Recognizing** (urgência falsa, problema com a password, URL suspeito — *o IP do destinatário **não** é sinal*), **Responding** — os utilizadores devem **reportar à equipa de segurança interna** (input mais útil).
- **Anomalous behavior recognition:** **Risky** (ex.: clicar num link sem verificar a URL por curiosidade), **Unexpected** (ex.: exfiltração de dados para servidor externo; login simultâneo de dois países = partilha de conta), **Unintentional** (ex.: errar a password várias vezes).
- **User guidance training:** **Policy/handbooks** (metas e práticas de alto nível para novos utilizadores), **Situational awareness** (ex.: evitar que outros vejam o ecrã em espaço público), **Insider threat**, **Password management** (o **password manager** tem o maior impacto), **Removable media/cables** (USB/cabo desconhecido = malware/juice jacking; 2.º teclado ligado = **USB malicioso**), **Social engineering**, **OPSEC** (não revelar controlos/ferramentas), **Hybrid/remote work** (VPN, Wi-Fi seguro, segurança física — *câmaras de segurança **não** são prática comum de teletrabalho*).
- **Reporting and monitoring:** Initial (baseline do estado inicial) e **Recurring**; avaliar eficácia com **KPIs** e taxa de incidentes vs participação no training.
- **Development/Education** — 1.º passo ao criar um programa: **avaliar as ameaças/riscos** da organização; usar **vários métodos** (workshops/online/simulações) para diferentes estilos de aprendizagem.

## Correções feitas ao teu resumo
- **RPO** = *quantos dados* se podem perder (não "ponto do tempo até ser resolvido"). **RTO** = tempo até estar operacional.
- Faltavam as **fórmulas de risco**: **SLE = AV × EF**, **ALE = SLE × ARO**, e o **ARO** como taxa anual (1 em 4 anos = 0.25). É a matéria mais testada do domínio.
- **MOA** ≠ apenas "passo acima do MOU" — é o documento que **detalha papéis/responsabilidades** entre organizações.

## Distratores comuns (revisão rápida)
- **SLE = AV × EF**; **ALE = SLE × ARO**; **ARO** = vezes por ano (1 em 4 anos = 0.25).
- **Policy** = intenção; **Standard** = obrigatório (como); **Procedure** = passos; **Guideline** = conselho.
- **Due care** = interno; **Due diligence** = terceiros.
- **Data owner** = mais sénior (não processa); **controller** = define propósito; **processor** = executa; **custodian** = segurança/labels.
- **Transfer** = seguro; **Avoid** = eliminar; **Accept** = exception/exemption; **Mitigate** = reduzir.
- **Audit** = examinar; **Attestation** = opinião formal sobre o resultado.
- **Passive recon** não deixa rasto (Shodan/OSINT); **active** deixa (nmap/port scan).
- **Known env** = full disclosure; **unknown** = blind test.
- **Right to be forgotten** e **DPO** = GDPR; **PCI DSS** = cartões; **GLBA** = privacidade financeira.
- **Offboarding** = desativar (não apagar) a conta.

## Acrónimos (Domínio 5)
- **AUP** — Acceptable Use Policy
- **SDLC** — Software Development Lifecycle
- **BC / DR / COOP** — Business Continuity / Disaster Recovery / Continuity of Operations Plan
- **IR** — Incident Response
- **SOAR** — Security Orchestration, Automation and Response
- **KRI / KPI** — Key Risk Indicator / Key Performance Indicator
- **EF / AV / SLE / ARO / ALE** — Exposure Factor / Asset Value / Single Loss Expectancy / Annualized Rate of Occurrence / Annualized Loss Expectancy
- **BIA** — Business Impact Analysis
- **RTO / RPO / MTTR / MTBF** — Recovery Time / Point Objective / Mean Time To Repair / Between Failures
- **SLA / MOU / MOA / MSA / SOW / WO** — os agreement types
- **NDA / BPA / ISA** — Non-Disclosure / Business Partners / Interconnection Security Agreement
- **SOC 2 (Type 1/2)** — System and Organization Controls report
- **CCO / DPO** — Chief Compliance Officer / Data Protection Officer
- **SOX / HIPAA / GLBA / PCI DSS / GDPR / COPPA** — regulações-chave
- **ISO 27001 / 27002 / 27701 / 31000** — normas (27001 = ISMS; 27002 = controlos; 27701 = privacidade; 31000 = risco)
- **NIST SP 800-53 / 800-63B** — controlos / identidade e passwords
- **OSINT** — Open-Source Intelligence
- **DAC / MAC** — Discretionary / Mandatory Access Control
