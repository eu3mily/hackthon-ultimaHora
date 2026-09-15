# 🎓🔒 OnFocus — Plataforma Integrada de Gestão & Foco Pedagógico Digital

> **Solução desenvolvida para o Hackathon Última Hora:** Transformação temporária e segura do smartphone do aluno em uma ferramenta pedagógica ativa via escaneamento de QR Code, sem vigilância invasiva e com total conformidade à LGPD.

---

## 📑 Sumário

- [📌 Visão Geral da Solução](#-visão-geral-da-solução)
- [🏗️ Arquitetura do Sistema](#️-arquitetura-do-sistema)
- [📂 Estrutura de Pastas do Projeto](#-estrutura-de-pastas-do-projeto)
- [🖥️ 1. Módulo Gestor (Web SaaS)](#️-1-módulo-gestor-web-saas)
  - [Funcionalidades Principais](#funcionalidades-principais-do-gestor)
  - [Tecnologias do Gestor](#tecnologias-do-gestor)
- [📱 2. Módulo App Mobile (Dispositivo do Aluno)](#-2-módulo-app-mobile-dispositivo-do-aluno)
  - [O Ciclo do Aluno em 6 Etapas](#o-ciclo-do-aluno-em-6-etapas)
  - [Segurança do Dispositivo & Kiosk Mode](#segurança-do-dispositivo--kiosk-mode)
- [📊 3. Pasta Apresentação](#-3-pasta-apresentação)
- [🔄 Fluxo de Funcionamento Integrado](#-fluxo-de-funcionamento-integrado)
- [🛡️ Privacidade, Segurança & LGPD](#️-privacidade-segurança--lgpd)
- [🚀 Como Executar o Projeto](#-como-executar-o-projeto)
- [👥 Hackathon & Equipe](#-hackathon--equipe)

---

## 📌 Visão Geral da Solução

O **OnFocus** resolve o dilema entre o potencial pedagógico dos smartphones e as distrações digitais em sala de aula. Em vez de recolher os aparelhos ou adotar softwares invasivos de espionagem, a plataforma estabelece uma **política pedagógica temporária**:

1. **Leitura de QR Code**: O aluno escaneia com o smartphone o QR Code da carteira/sala no início da aula.
2. **Whitelist Pedagógica**: O aparelho entra no *Launcher Protegido*, disponibilizando exclusivamente os aplicativos autorizados pelo professor para aquela disciplina (ex: GeoGebra, Calculadora, Dicionário).
3. **Foco Ativo**: Notificações, redes sociais e jogos são suspensos durante o período da aula.
4. **Desbloqueio Automático**: Ao término do horário ou por comando do docente, o smartphone retorna instantaneamente ao modo pessoal do aluno.

---

## 🏗️ Arquitetura do Sistema

```mermaid
graph TD
    subgraph "🏫 Ambiente Escolar"
        QRCode[🔳 QR Code da Sala / Carteira]
        Prof[👨‍🏫 Professor / Gestor]
        Aluno[📱 Smartphone do Aluno]
    end

    subgraph "🖥️ Gestor (Web SaaS)"
        Dash[Dashboard & Monitoramento em Tempo Real]
        Turmas[Gestão de Turmas & Aulas]
        Whitelist[Catálogo de Apps Permitidos]
        Relatorios[Relatórios de Frequência & Engajamento]
        Seguranca[Gestão de Chaves ECDSA & Logs LGPD]
    end

    subgraph "📱 App Mobile (Aluno)"
        Standby[1. Standby & Conexão de Rede]
        ScanQR[2. Validação Criptográfica do QR Code]
        Launcher[3. Launcher Protegido]
        AppEducativo[4. App Pedagógico em Execução]
        Bloqueio[5. Alerta de Foco / App Restrito]
        Unlock[6. Desbloqueio Pós-Aula]
    end

    Prof -->|Configura Políticas & Acompanha| Dash
    QRCode -.->|Escaneamento pela Câmera| Aluno
    Aluno --> Standby --> ScanQR --> Launcher --> AppEducativo
    Launcher -->|Tentativa de Distração| Bloqueio
    Prof -->|Encerra Aula| Unlock
    Aluno <-->|WebSockets / REST API (Telemetry & Policy)| Dash
---

## 📂 Estrutura de Pastas do Projeto

O repositório principal (`plataforma/`) é organizado em **3 pastas principais**:

```bash
plataforma/
├── README.md                      # Documentação geral do projeto
├── Gestor/                        # Painel Web SaaS (Next.js 14, TypeScript, Tailwind)
│   ├── package.json               # Dependências e scripts do Gestor
│   ├── tsconfig.json              # Configurações TypeScript
│   ├── tailwind.config.js         # Configurações de estilos Tailwind
│   ├── next.config.js             # Configurações do Next.js
│   ├── public/                    # Arquivos estáticos e ícones
│   └── src/                       # Código-fonte (App Router, Componentes, Estado)
├── AppMobile/                     # Aplicativo Mobile do Aluno
│   └── app.jsx                    # Entrypoint e componentes da aplicação mobile
└── apresentação/                  # Materiais, slides e assets da apresentação do Hackathon
```

---

## 🖥️ 1. Módulo Gestor (Web SaaS)

Localizado no diretório `/Gestor`, o **Gestor** é uma aplicação web moderna voltada para coordenadores pedagógicos, diretores e professores.

### Funcionalidades Principais do Gestor

- 📊 **Dashboard Dinâmico**:
  - Indicadores em tempo real (turmas ativas, alunos conectados, salas ocupadas, aulas do dia).
  - Gráficos de distribuição e uso de aplicativos educativos.
  - Painel de status rápido e atalhos operacionais.
- 👥 **Gerenciamento de Turmas & Alunos**:
  - Cadastro, listagem com paginação e busca por nome/disciplina/status.
  - Exportação de dados operacionais em formato CSV.
- 📅 **Controle de Aulas & Políticas**:
  - Agendamento de aulas com vinculação de sala, professor e horário.
  - Seleção granular de aplicativos permitidos por aula (*Whitelist*).
- 📦 **Biblioteca de Aplicativos**:
  - Catálogo de apps com validação de *Package Names* do Android (ex: `org.geogebra.android`).
  - Categorização pedagógica (Matemática, Ciências, Línguas, Produtividade).
  - Versionamento e controle de status (Ativo / Pendente / Inativo).
- 🏫 **Monitoramento de Salas & Dispositivos (Tempo Real)**:
  - Telemetria de bateria, status de conexão (Conectado, Ocioso, Bloqueado, Alerta) e aplicativo ativo por aluno.
  - Alertas automáticos para inatividade prolongada ou tentativas de abertura de apps não autorizados.
- 📈 **Relatórios & Analytics**:
  - Frequência diária de presença e taxa de adesão ao OnFocus.
  - Ranking dos aplicativos mais utilizados e engajamento por disciplina.
- ⚙️ **Configurações, Segurança & LGPD**:
  - Gerenciamento de chaves criptográficas para os QR Codes (ECDSA P-256).
  - Logs de auditoria imutáveis com minimização de dados.

### Tecnologias do Gestor

| Tecnologia | Finalidade |
| :--- | :--- |
| **Next.js 14 (App Router)** | Framework React com SSR/SSG e API Routes |
| **React 18 & TypeScript** | Componentização modular com tipagem estática rigorosa |
| **Tailwind CSS** | Design system responsivo, moderno e customizado |
| **Lucide React** | Ícones vetoriais modernos e acessíveis |
| **Recharts** | Visualização de dados analíticos e gráficos interativos |
| **React Context API** | Gerenciamento de estado global reativo da aplicação |

---

## 📱 2. Módulo App Mobile (Dispositivo do Aluno)

Localizado no diretório `/AppMobile`, o **App Mobile** é o cliente Android/React Native responsável pela aplicação das restrições e entrega da interface pedagógica no smartphone do estudante.

### O Ciclo do Aluno em 6 Etapas

1. **Tela 1 — Standby & Conexão**:
   - O aluno abre o aplicativo na escola e verifica a conexão com a rede Wi-Fi institucional segura.
2. **Tela 2 — Scan do QR Code da Sala**:
   - Ao entrar na sala de aula, o aluno escaneia o QR Code fixado na mesa ou no ambiente.
   - O app valida a assinatura digital do QR Code (chave ECDSA) e registra a presença na aula ativa.
3. **Tela 3 — Launcher Protegido**:
   - O launcher padrão do Android é temporariamente substituído pelo *Launcher Seguro OnFocus*.
   - Apenas os ícones dos aplicativos liberados para a aula atual ficam visíveis e acessíveis.
4. **Tela 4 — App Educacional em Execução**:
   - O aluno utiliza a ferramenta pedagógica designada (exemplo: **GeoGebra interativo**, calculadora, leitor de PDF).
5. **Tela 5 — Alerta Educativo de Foco**:
   - Caso o aluno tente alternar para um aplicativo não autorizado ou burlar a proteção, o sistema intercepta e exibe uma tela educativa de conscientização, emitindo alerta discreto no painel do professor.
6. **Tela 6 — Validação de Saída + Aula Finalizada / Desbloqueio**:
   - Ao tocar em **“Encerrar”**, o aluno valida a saída com uma nova leitura do **QR Code da sala** (com loading simulado).
   - Confirmada a saída, o smartphone desativa o Modo Aula e retorna ao estado normal do sistema operacional.
   - O “Retornar ao Início” leva à tela inicial do celular (papel de parede + relógio), provando que o aluno saiu totalmente do app.

### Segurança do Dispositivo & Kiosk Mode

- **Lock Task Mode / Kiosk Mode**: Fixação da interface pedagógica sem acesso à barra de navegação do sistema.
- **Accessibility Service / DPM (Device Policy Manager)**: Detecção de troca de janela em primeiro plano para garantir que apenas pacotes autorizados sejam executados.
- **Assinatura de QR Code Criptografada**: Impede o uso de códigos falsos ou gerados fora da plataforma através de tokens de sessão temporários.

---

## 📊 3. Pasta Apresentação

Localizada no diretório `/apresentação`, esta pasta é reservada para:
- Slides e pitch deck do projeto.
- Roteiro de demonstração ao vivo para a banca avaliadora.
- Documentos visuais e materiais de apoio do Hackathon.

---

## 🔄 Fluxo de Funcionamento Integrado

```text
[Início da Aula]
       │
       ▼
[Aluno escaneia o QR Code] ───► [Validação Criptográfica]
                                      │
                                      ▼
                        [Sincroniza com o Painel do Gestor]
                                      │
       ┌──────────────────────────────┴──────────────────────────────┐
       ▼                                                             ▼
[Mobile: Ativa Launcher Seguro]                          [Gestor: Atualiza Telemetria]
 • Bloqueia apps não autorizados                          • Aluno marcado como Presente
 • Disponibiliza Whitelist (ex: GeoGebra)                 • Status de bateria & app ativo
       │                                                             │
       ▼                                                             ▼
[Durante a Aula]                                         [Monitoramento em Tempo Real]
 • Aluno estuda com app pedagógico                        • Notificações de inatividade
 • Tentativas de saída acionam Alerta de Foco             • Alertas visuais para o docente
       │                                                             │
       └──────────────────────────────┬──────────────────────────────┘
                                      ▼
                      [Término da Aula / Desconexão]
                                      │
                                      ▼
                    [Mobile restaura o sistema normal]
                    [Gestor consolida relatório pedagógico]
```

---

## 🛡️ Privacidade, Segurança & LGPD

O projeto foi concebido sob o princípio de **Privacy by Design**:

- ❌ **Sem monitoramento invasivo**: O sistema **não** captura mensagens, fotos, histórico de navegação pessoal nem conversas privadas.
- 🔍 **Detecção apenas de Primeiro Plano**: Apenas o identificador do pacote (*Package Name*) em execução durante o horário da aula é verificado para autorização.
- 🔐 **Logs Imutáveis & Auditoria**: Registro transparente de eventos de entrada e saída, permitindo auditoria escolar em conformidade com as diretrizes da LGPD (Lei Geral de Proteção de Dados).
- 🔑 **Criptografia ECDSA**: Comunicação assinada com chaves assimétricas para garantir a integridade e autenticidade dos dados validados a partir dos QR Codes.

---

## 🚀 Como Executar o Projeto

### Pré-requisitos
- **Node.js**: Versão 18.17 ou superior
- **npm** ou **yarn** / **pnpm**

### 1. Clonando o Repositório
```bash
git clone git@github.com:beniprado/Hackthon-ultimaHora.git
cd Hackthon-ultimaHora
```

### 2. Executando o Painel do Gestor (Web SaaS)
```bash
# Entre na pasta do Gestor
cd Gestor

# Instale as dependências
npm install

# Inicie o servidor de desenvolvimento
npm run dev
```

Abra seu navegador e acesse:
```text
http://localhost:3000
```

> 💡 **Dica de Navegação:**
> - Acesse `/dashboard` para ver a visão geral pedagógica e de dispositivos.
> - Acesse `/salas` para acompanhar a telemetria em tempo real dos dispositivos conectados.

---

## 👥 Hackathon & Equipe

Projeto concebido e desenvolvido com foco em inovação educacional, privacidade e impacto pedagógico para o **Hackathon Última Hora**.

---
*Transformando tecnologia em aliada da educação com respeito, segurança e foco.* 📚✨
