# GameStream-AR

# Arquitetura de Referência - GameStream

A TechCorp lançou o serviço **GameStream**, um streaming de jogos de altíssima qualidade.  
O objetivo desta documentação é apresentar a **Arquitetura de Referência inicial** para os dois componentes mais críticos do sistema, com foco em **Escalabilidade** e **Resiliência**.

---

## 1. Divisão da Aplicação (Microsserviços e Desacoplamento)

### Microsserviço 1 (Função Crítica)
**Serviço de Autenticação e Gerenciamento de Sessões**
- Responsável por autenticar usuários e gerenciar tokens de sessão.
- Isolado para que falhas neste serviço não interrompam o streaming ativo dos usuários já conectados.

### Microsserviço 2 (Função Crítica)
**Serviço de Transmissão de Vídeo em Tempo Real (Streaming Engine)**
- Responsável por codificar, enviar e gerenciar a transmissão de jogos.
- Falhas neste serviço impactam diretamente a experiência de streaming, mas não afetam outros microsserviços.

### Mecanismo de Comunicação
**Mensageria Assíncrona (ex: Amazon SQS, Google Pub/Sub ou RabbitMQ)**
- Permite comunicação desacoplada entre microsserviços.
- Se um microsserviço estiver offline, as mensagens ficam na fila e são processadas posteriormente.

---

## 2. Alta Resiliência e Disponibilidade (Geografia)

**Conceito de Distribuição:**  
**Regiões e Zonas de Disponibilidade (Regions & Availability Zones)**

**Justificativa:**  
- Distribuir o streaming em múltiplas Zonas de Disponibilidade (AZs) e regiões garante alta disponibilidade mesmo se uma AZ ou região falhar.  
- Reduz latência global e mantém a experiência de streaming consistente.  
- Permite failover automático e recuperação rápida de desastres.

---

## 3. Escalabilidade de Tráfego (Entrada)

**Serviço Necessário:**  
**Load Balancer (Balanceador de Carga)**
- Recebe todas as requisições de usuários simultâneos.
- Distribui de forma balanceada para múltiplas instâncias do serviço de streaming.
- Detecta instâncias com problemas e redireciona tráfego para instâncias saudáveis, evitando sobrecarga.

---

## 4. Segurança por Design (Identity and Access)

**Pilar de Segurança (Conceito):**  
**Autenticação e Autorização entre microsserviços (IAM, JWT, mTLS)**

**Justificativa:**  
- Garante que apenas o Microsserviço 1 autenticado possa acessar o Microsserviço 2.  
- Protege contra acessos não autorizados, ataques internos ou exploração de APIs.  
- Mantém confidencialidade, integridade e controle de acesso entre componentes da arquitetura.

---

## 5. Benefícios da Arquitetura

- **Alta coesão e baixo acoplamento:** cada microsserviço cumpre uma única função.  
- **Escalabilidade horizontal:** permite atender 1 milhão de usuários simultâneos.  
- **Alta disponibilidade:** tolera falhas de AZs ou regiões sem impacto no streaming.  
- **Segurança:** comunicação entre microsserviços autenticada e autorizada. 