# 🟢 GreenFlow

> Sistema inteligente de localização, logística de fluxo e previsão de ocupação para academias.

Projeto desenvolvido como Trabalho de Conclusão de Curso (TCC) do Ensino Médio Técnico em Logística, propondo uma solução tecnológica para otimizar o fluxo de pessoas e o gerenciamento de equipamentos em academias.

---

## Arquivos

| Arquivo | Descrição |
|---|---|
| `greenflow.html` | Página completa (estrutura, estilos e scripts) |

---

## Estrutura da página

1. **Header / Nav** — logo, menu de âncoras (`#problema`, `#solucao`, `#beneficios`, `#dashboard-proposal`, `#antes-depois`) e botão de CTA fixo no topo (sticky, e retrátil ao rolar).
2. **Hero** — headline, texto de apoio, CTAs e um mockup animado em SVG de um "mapa da sala" mostrando estações de treino livres/ocupadas com uma rota sendo traçada em tempo real. Todo o bloco entra em cena com uma animação escalonada.
3. **O problema** (`#problema`) — três cartões descrevendo os atritos que o produto resolve: congestionamento, inventário de halteres e ineficiência de tempo.
4. **Como funciona** (`#solucao`) — três pilares explicados com diagramas SVG próprios:
   - `01` Roteirização inteligente (antes/depois da fila)
   - `02` Endereçamento de inventário (rack de halteres com sensor de falta)
   - `03` Fluxo catraca → app → sensores → rota
5. **Benefícios** (`#beneficios`) — métricas lado a lado para aluno e para academia, com os números contando de 0 até o valor final ao entrar na tela.
6. **Console ao vivo** (`#dashboard-proposal`) — dashboard, mapa interativo e simulador de fluxo em um único bloco funcional (detalhado na seção [Console ao vivo](#console-ao-vivo-dashboard--mapa--simulador) abaixo).
7. **Racional de design** — três blocos de texto explicando as decisões de produto por trás do dashboard, do mapa interativo e da arquitetura técnica de dados em tempo real (proposta de migração de dados simulados para sensores reais via MQTT/WebSocket).
8. **Previsão de IA** (`#previsao-ia`) — card de previsão de pico de ocupação, com gauge circular, badge de alerta e gráfico de barras por horário do dia.
9. **Antes x Depois** (`#antes-depois`) — comparativo por equipamento (fila de pessoas x uso sem espera), com toggle interativo e resumo de tempo médio de espera.
10. **CTA final** (`#cta`) — chamada para agendar demonstração ou falar com um especialista.
11. **Footer** — marca e informações de rodapé.

---

## Design system

Definido em variáveis CSS (`:root`) no topo do `<style>`:

- **Cores**: fundo grafite escuro (`--bg`), superfícies (`--surface`, `--surface-2`), texto (`--text`, `--text-muted`, `--text-faint`) e quatro acentos semânticos — verde `--green` (livre/positivo), âmbar `--amber` (reservado/atenção), vermelho `--red` (ocupado/alerta) e azul `--blue` (manutenção/neutro).
- **Tipografia**:
  - `Big Shoulders Display` — títulos (`--display`)
  - `Inter` — corpo de texto (`--body`)
  - `JetBrains Mono` — dados, labels de sensores, timestamps (`--mono`)
  - Carregadas via Google Fonts no `<head>`.
- **Componentes reutilizáveis**: `.btn` (`.btn-primary` / `.btn-ghost`), `.problem-card`, `.pillar` / `.pillar.reverse`, `.benefit-col`, `.eyebrow`, `.alert-tag`, `.metric-card`, `.station`, `.ba-card`, `.prose`.
- **Responsivo**: breakpoints em `1000px`/`920px` (grids do dashboard e do hero viram coluna única), `900px`/`860px` (pilares, painel de mapa e cartões antes/depois empilham) e `760px`/`620px`/`560px` (menu de âncoras oculto e grids reduzidos no mobile).

---

## Console ao vivo (Dashboard + Mapa + Simulador)

Bloco central da página (`#dashboard-proposal`), pensado como três visões da **mesma fonte de dados**:

- **Dashboard de métricas**: ocupação (métrica-herói, com gauge circular em SVG), aparelhos livres, aparelhos ocupados, tempo médio de espera, pessoas treinando e rotas ativas — todos recalculados a cada mudança de estado.
- **Mapa interativo**: grade de estações clicáveis (não por hover, pensado para uso em totens/tablets), com 4 estados possíveis — livre, reservado, ocupado e manutenção — e um painel lateral (drawer) com tempo de uso atual, próximo aluno agendado e histórico de uso do aparelho selecionado.
- **Simulador de fluxo**: botão que liga um `setInterval` (~1,4s) simulando entradas de alunos (catraca → reserva → deslocamento animado por um "ponto" na tela → ocupação), liberação de aparelhos após um tempo aleatório e entradas/saídas de manutenção — tudo gerado no navegador com `Math.random()` para fins de demonstração.
- **Toggle Antes/Depois do console**: alterna entre o cenário simulado com o GreenFlow ativo e um cenário "sem sistema", em que todos os aparelhos aparecem como ocupados e sem informação de status, desabilitando o simulador.

Os blocos de **racional de design** logo abaixo do console documentam, em texto, por que a ocupação foi escolhida como métrica-herói, por que o mapa usa clique em vez de hover, o porquê da paleta de 4 cores fixas, e como a arquitetura trocaria dados simulados por sensores reais (MQTT, WebSocket/SSE, banco de séries temporais) em um cenário de produção.

---

## Efeitos dinâmicos e interatividade

A página tem uma camada extra de interatividade, toda em JavaScript puro (sem bibliotecas externas):

| Efeito | Onde | Como funciona |
|---|---|---|
| **Barra de progresso de rolagem** | Topo fixo da página (`#scrollProgress`) | Atualiza a largura de uma barra verde conforme a porcentagem da página já rolada. |
| **Spotlight seguindo o cursor** | Fundo da página inteira (`#spotlight`) | Um brilho verde radial acompanha a posição do mouse via variáveis CSS (`--mx`, `--my`) atualizadas no `mousemove`. |
| **Nav retrátil** | Cabeçalho (`#siteHeader`) | Ao passar de 40px de rolagem, a classe `.scrolled` reduz o padding da nav e adiciona uma sombra sutil. |
| **Entrada escalonada do Hero** | Seção hero | Eyebrow, título, texto, botões e métricas aparecem em sequência via `@keyframes rise-in` com delays diferentes, em vez de tudo de uma vez. |
| **Tilt 3D no mockup do mapa** | Card do "mapa da sala" (`#floorCard`) | Rotaciona sutilmente em 3D (`rotateX`/`rotateY`) conforme a posição do mouse dentro do card. Só ativa em dispositivos com mouse (`hover: hover`). |
| **Botões magnéticos** | Todos os `.btn` | O botão se desloca levemente na direção do cursor ao passar por cima, e o botão verde (`.btn-primary`) ganha um brilho que "varre" a superfície no hover. |
| **Contadores animados** | Seção de benefícios (`#benefitsGrid`) | Números com `data-target` (ex: `-70`, `100`, `-90`, `+22`) contam de 0 até o valor final com easing, disparado pelo `IntersectionObserver` quando a seção entra na tela. |
| **Reveal ao rolar + stagger** | Cartões de problema, pilares, seção de benefícios, console, blocos de racional, previsão de IA, antes/depois | Classe `.reveal` observada por `IntersectionObserver`; os três `.problem-card` têm `transition-delay` escalonado para não aparecerem todos ao mesmo tempo. |
| **Simulador de fluxo** | Console ao vivo (`#dashboard-proposal`) | Loop em `setInterval` que muda estados de estação, anima "pontos" de pessoas entre a catraca e os aparelhos, e recalcula o dashboard a cada ciclo. |
| **Micro-interações existentes** | Diagramas SVG | Pulso (`.pulse-ring`) e traço animado (`.route-line`) simulando sensores "ao vivo" e a rota sendo recalculada; barras do gráfico de previsão de IA com destaque visual no horário de pico. |

### Acessibilidade (`prefers-reduced-motion`)

Todos os efeitos acima respeitam a preferência do sistema operacional por movimento reduzido:
- Animações e transições são desativadas globalmente (`animation: none !important; transition: none !important;`).
- A barra de progresso e o spotlight são ocultados (`display: none`).
- Elementos que dependiam de animação para ficar visíveis (hero, floor-card, `.reveal`) recebem `opacity: 1` e `transform: none` diretamente, garantindo que o conteúdo apareça normalmente mesmo sem as animações.
- O JavaScript verifica `window.matchMedia('(prefers-reduced-motion: reduce)')` antes de registrar os listeners de spotlight, botão magnético e tilt 3D — ou seja, nem chegam a rodar para quem prefere menos movimento. Os contadores animados também caem direto no valor final sem animação de contagem.

---

## Integração com WhatsApp

Script no final do arquivo intercepta cliques em botões/links com texto específico e abre o WhatsApp com mensagem pré-preenchida, em nova aba.

### Configuração atual

```js
const WHATSAPP_NUMBER = '5519998007852'; // (19) 99800-7852, com código do país +55

const BUTTON_CONFIGS = [
  { text: 'Agendar demonstração',       message: 'Olá, Gostaria de agendar uma demonstração' },
  { text: 'Falar com um especialista',  message: 'Olá, Preciso de uma ajuda por favor!' },
];
```

### Como funciona

- Usa **delegação de eventos** no `document` (`document.addEventListener('click', ...)`), então funciona também em botões adicionados **dinamicamente** ao DOM — não é necessário registrar listeners manualmente em cada um.
- O texto do botão é normalizado (removendo acentos, espaços extras e diferenças de maiúsculas/minúsculas) antes da comparação, então "Agendar demonstração" e "Agendar Demonstração" são tratados como iguais.
- Ao encontrar um botão correspondente, o clique original é bloqueado (`preventDefault`) e uma nova aba é aberta com `window.open(url, '_blank', 'noopener,noreferrer')`.
- Todos os botões de contato também têm o link do WhatsApp no próprio `href` (com `target="_blank"`), como **fallback** caso o JavaScript não carregue.

### Adicionar um novo botão de contato

Para que outro botão (ex: "Suporte técnico") também abra o WhatsApp, basta:

1. Adicionar uma entrada no array `BUTTON_CONFIGS`:
   ```js
   { text: 'Suporte técnico', message: 'Olá, preciso de suporte técnico' }
   ```
2. Usar exatamente esse texto no `<a>` ou `<button>` correspondente no HTML.

Nenhuma outra alteração é necessária — o script detecta o novo botão automaticamente, mesmo que ele seja inserido depois do carregamento da página.

---

## Como usar

1. Baixe o `greenflow.html`.
2. Abra diretamente no navegador **ou** hospede em qualquer servidor estático (Netlify, Vercel, GitHub Pages, S3, etc.) — não há build step nem dependências de servidor.
3. Para customizar:
   - **Textos**: edite diretamente no HTML dentro de cada `<section>`.
   - **Cores**: altere as variáveis em `:root` no `<style>`.
   - **Número/mensagens do WhatsApp**: altere `WHATSAPP_NUMBER` e o array `BUTTON_CONFIGS` no script.
   - **Dados do simulador**: ajuste `EQUIPMENT_POOL`, `FIRST_NAMES` e as probabilidades em `weightedStatus()` no script do console ao vivo.
   - **Velocidade/intensidade dos efeitos**: os principais parâmetros ajustáveis estão no primeiro `<script>` do arquivo — `duration` (velocidade dos contadores), os multiplicadores `* 0.15` / `* 0.3` (intensidade do efeito magnético dos botões) e `* 7` (intensidade do tilt 3D do mockup). O intervalo do simulador (`1400`ms) fica no script do console.

---

## Principais Funcionalidades

- 🗺️ Localização inteligente de equipamentos
- 🤖 IA de previsão de ocupação
- 📊 Dashboard em tempo real
- 🖱️ Mapa interativo com painel de detalhes por aparelho
- ▶️ Simulador de fluxo de alunos
- 📈 Indicadores de utilização
- 📍 Roteirização inteligente
- 📦 Controle de inventário dos halteres
- ⚖️ Comparativo Antes x Depois por equipamento
- 📱 Interface responsiva
- 💬 Integração com WhatsApp
- 🎨 Design moderno em Dark Mode

---

## Compatibilidade

- Navegadores modernos (Chrome, Firefox, Safari, Edge — últimas versões).
- Sem dependências externas além das fontes do Google Fonts (requer conexão à internet para carregá-las; caso contrário, cai no fallback `sans-serif`).
- Nenhum framework, bundler ou etapa de build necessária.
- Efeitos de mouse (spotlight, tilt, magnético) são condicionados a dispositivos com ponteiro (`hover: hover`) e à ausência de `prefers-reduced-motion`, então o site continua leve e funcional em celulares e para quem usa configurações de acessibilidade.

---

## Diferenciais

- Sistema voltado para logística aplicada às academias.
- Interface moderna inspirada em dashboards SaaS.
- Navegação inteligente baseada na ocupação dos equipamentos.
- Previsão de horários de pico utilizando IA.
- Controle de inventário por sensores.
- Console ao vivo com dashboard, mapa e simulador de fluxo integrados na mesma fonte de dados.
- Código desenvolvido sem frameworks, utilizando apenas HTML, CSS e JavaScript.

---

## Autor

**Eduardo Godoy**

- GitHub: https://github.com/godoyeduardo135
- Linkedin: https://www.linkedin.com/in/eduardo-godoy-b73615272/
- Instagram: https://www.instagram.com/eduardogodoys_

---

## Status

🟢 Projeto possui um conceitual em desenvolvimento para apresentação do TCC.

---

## Licença

Este projeto foi desenvolvido exclusivamente para fins acadêmicos como Trabalho de Conclusão de Curso (TCC). A utilização do código para estudos é permitida, porém a reprodução integral do projeto para fins comerciais depende da autorização do autor.
