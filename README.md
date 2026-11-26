# Estudo-Ransomware-Keylogger
Aviso Legal: Este repositório foi desenvolvido exclusivamente para fins educacionais e de pesquisa em segurança da informação, como parte das atividades de conclusão de curso. Nenhuma ferramenta maliciosa ativa foi desenvolvida ou hospedada aqui. O foco deste trabalho é a compreensão dos vetores de ataque para implementação de defesas robustas.

# Sobre o Projeto
Este projeto documenta o estudo detalhado, análise arquitetural e estratégias de mitigação contra duas das ameaças cibernéticas mais prevalentes no cenário atual: Ransomware e Keylogger.
O objetivo central é dissecar o ciclo de vida dessas ameaças para compreender como ocorrem as brechas e, principalmente, como organizações e usuários podem construir defesas resilientes em camadas (Defesa em Profundidade).


   Índice
Análise Técnica: Ransomware;
  Ciclo de Vida do Ataque, Mecanismos Criptográficos Utilizados
  
Análise Técnica: Keylogger;
  Como Funciona a Captura de Entradas, Vetores de Exfiltração de Dados
  
Fator Humano e Engenharia Social

Estratégias de Mitigação e Defesa em Camadas;
  Proteção contra Ransomware, Proteção contra Keyloggers
  
Conclusão e Aprendizados
Referências e Materiais de Apoio


   Análise Técnica: Ransomware
O ransomware é um tipo de malware projetado para sequestrar recursos computacionais ou dados — tipicamente por meio de criptografia forte —, exigindo um resgate para a restauração do acesso.

Ciclo de Vida do Ataque
[Infecção Inicial / Phishing] ↓ [Execução & Evasão de Defesas] ↓ [Varredura do Sistema de Arquivos] ↓ [Criptografia Assimétrica/Simétrica dos Dados] ↓ [Exclusão de Cópias de Sombra (VSS)] ↓ [Apresentação da Nota de Resgate (Ransom Note)]



### Mecanismos Criptográficos Utilizados

- **Criptografia Híbrida**: Malwares modernos costumam combinar:
  - **Criptografia Simétrica (ex: AES-256 ou ChaCha20)**: Utilizada para cifrar os arquivos do disco rapidamente devido à sua alta performance.
  - **Criptografia Assimétrica (ex: RSA-2048 / RSA-4096)**: Utilizada para cifrar a chave simétrica gerada na máquina da vítima com uma chave pública pertencente ao atacante. Apenas o atacante, detentor da chave privada, consegue descriptografar a chave simétrica original.
- **Eliminação de Pontos de Restauração**: Ransomwares direcionados a sistemas operacionais comuns frequentemente executam rotinas para apagar instantâneos de restauração de volume (Volume Shadow Copies) para impedir recuperação offline.

---

##    Análise Técnica: Keylogger

Um keylogger (registrador de teclas) é uma ferramenta projetada para monitorar e registrar em segredo as teclas digitadas pelo usuário no teclado.

### Como Funciona a Captura de Entradas

1. **Ganchos no Sistema Operacional (Hooks / APIs de Sistema)**:
   - Em sistemas operacionais, ferramentas desse tipo monitoram eventos de entrada por meio de interceptores na fila global de eventos ou chamadas de API (como leitura assíncrona do estado do teclado).
2. **Registro Local**:
   - As teclas capturadas são gravadas em buffers de memória ou arquivos ocultos no sistema de arquivos local.
3. **Persistência**:
   - Mecanismos de persistência comuns incluem entradas no registro do sistema, tarefas agendadas ou serviços em segundo plano iniciados no boot.

### Vetores de Exfiltração de Dados

- **Exfiltração via SMTP (E-mail)**: Os dados capturados são compactados e enviados periodicamente para um endereço de e-mail pré-configurado.
- **Exfiltração Web (HTTP/HTTPS / Webhooks)**: Envio via requisições POST para servidores de comando e controle (C2) ou canais de mensageria (como bots de Discord/Telegram).
- **DNS Tunneling**: Exfiltração em pequenas frações por meio de consultas DNS para contornar restrições rígidas de firewall.

---

##    Fator Humano e Engenharia Social

A maioria das infecções por malwares não se inicia explorando falhas técnicas complexas de dia zero (*zero-day*), mas sim explorando a vulnerabilidade humana:

- **Phishing e Spear-Phishing**: E-mails persuasivos que induzem a vítima a abrir anexos aparentemente legítimos (faturas, comprovantes, notificações judiciais) contendo macros maliciosas ou executáveis mascarados.
- **Engenharia Social**: Uso de táticas de urgência, autoridade ou medo para fazer com que o usuário ignore alertas do sistema operacional e conceda privilégios de execução ou permissões administrativas.
- **Uso de Softwares Não Confiáveis / Pirataria**: Softwares ativados ilegalmente (*cracks*, *patchers*) representam um dos maiores vetores de distribuição de trojans que baixam keyloggers e ransomwares silenciosamente.

---

##    Estratégias de Mitigação e Defesa em Camadas

A segurança eficaz não depende de uma única ferramenta, mas sim do conceito de **Defesa em Profundidade (Defense-in-Depth)**.

### Proteção contra Ransomware

| Camada | Medida de Defesa | Objetivo |
| :--- | :--- | :--- |
| **Prevenção** | Treinamento Contínuo de Segurança | Reduzir a taxa de cliques em links e anexos suspeitos. |
| **Acesso** | Princípio do Menor Privilégio (PoLP) | Usuários padrão não devem ter privilégios de administrador local. |
| **Segmentação** | Segmentação de Rede e VLANs | Impedir o movimento lateral do ransomware pela infraestrutura. |
| **Resiliência** | **Estratégia de Backup 3-2-1** | 3 cópias dos dados, em 2 mídias diferentes, sendo 1 **offline / imutável**. |
| **Detecção** | Soluções EDR / XDR | Monitorar taxas anômalas de I/O em disco e criação rápida de arquivos com extensões desconhecidas. |

### Proteção contra Keyloggers

- **Autenticação Multifator (MFA / 2FA)**: Mesmo que o invasor capture a senha via keylogger, ele não conseguirá acesso à conta sem o segundo fator (TOTP, chave de segurança física, biometria).
- **Gerenciadores de Senhas**: Utilizar preenchimento automático (*autofill*) de credenciais reduz drasticamente a digitação de senhas em teclados monitorados.
- **Ambientes de Sandboxing e EDR**: Monitorar processos que tentam registrar *hooks* globais no sistema de eventos do teclado.
- **Teclados Virtuais / Mecanismos Anti-Keylogger**: Embaralham ou alteram o modo como entradas sensíveis são inseridas no sistema.

---

##    Conclusão e Aprendizados

Através deste estudo foi possível concluir que:
1. **A criptografia é uma ferramenta neutra**: Ela protege dados em repouso e em trânsito no dia a dia, mas pode ser utilizada de forma adversária em ataques de ransomware.
2. **A confidencialidade das credenciais exige defesas ativas**: A dependência exclusiva de senhas alfanuméricas é obsoleta contra ameaças como keyloggers, tornando o uso de MFA indispensável.
3. **Backup imutável é a linha final de defesa**: Em cenários de ransomware, backups atualizados, testados e isolados da rede principal representam a única garantia de continuidade dos negócios sem pagamento de resgates.

---

##    Referências e Materiais de Apoio

- [MITRE ATT&CK Matrix for Enterprise](https://attack.mitre.org/)
- [CISA - Stop Ransomware Guide](https://www.cisa.gov/stopransomware)
- [NIST Cybersecurity Framework (CSF)](https://www.nist.gov/cyberframework)
- Conteúdos e aulas ministradas no curso.
