# GerencIA — painel de rastreabilidade e governança de IA

Painel do **GerencIA**, solução do Desafio 2 (Stark Bank) do ITA Challenge Sprint: mostra qual informação foi usada em ferramentas de IA, por quem, onde, quando, com qual sensibilidade e se há risco.

> Dados fictícios, declarados na própria planilha oficial do desafio. Nenhuma informação real.

## Arquivos

| Arquivo | O que é |
|---|---|
| `painel-novo.html` | Painel completo com os dados embutidos. Abre com dois cliques, sem servidor. |
| `index.html` | Fonte do painel. Substitui o `index.html` do pacote `espia-telas` e lê o `dados.js` ao lado. |
| `previa-cadastro.html` | Prévia das telas de **Pessoas e máquinas** (dados de exemplo; nada é gravado). Lê o `dados.js` ao lado. |
| `dados.js` | Dados gerados pelo pipeline: 650 eventos da planilha oficial + 12 eventos da coleta. |
| `ACESSO-TESTE.md` | Usuário de teste do login. **Não vai para o repositório.** |

Mantenha os arquivos na mesma pasta.

## Como abrir

- **Direto:** dois cliques em `painel-novo.html`.
- **Com servidor local:** `python3 -m http.server 8765` na pasta e abra `http://localhost:8765/painel-novo.html`.

Cada tela e cada filtro têm endereço próprio, por exemplo `#/alertas?fonte=coleta` ou `#/acompanhamento?risco=Crítico`.

## Telas

- **Acompanhamento:** quatro indicadores no mesmo formato (valor, “X de Y eventos” e meta quando houver), alertas por situação com total e %, eventos por semana por risco (número e % ao passar o mouse), uso em ferramenta não aprovada, informações mais expostas, para onde vai a informação sensível e risco por área, sempre com número e %.
- **Investigar:** exposição por tipo de informação. Clicar numa informação, pessoa, ferramenta, área ou evento abre um **resumo em modal** (quem usou, ferramentas, linha do tempo) sem sair da página.
- **Alertas:** quadro (kanban) com as colunas **Aberto → Em análise → Tratado**. Cada coluna pode ser ordenada por risco (maior → menor ou menor → maior) ou por data. Arraste o cartão para mudar de etapa, ou clique para ver a evidência e registrar o tratamento (desfecho, ação tomada e comentário). Todo evento da **coleta** entra no quadro em Aberto, até alguém classificar.
- **Governança:** somente **cadastros** (classificações, ferramentas de IA, usuários e áreas, tipos de informação, regras de risco e fontes de log). As regras trazem também a ordem de avaliação, as regras propostas, a validação do motor e a auditoria da base.

## Acesso

O painel abre numa tela de login. Nesta fase há um **usuário de teste**; o e-mail e a senha estão no arquivo `ACESSO-TESTE.md`, que fica fora do repositório (`.gitignore`). A opção “Manter conectado” guarda a sessão no navegador; o botão **Sair** fica no menu lateral.

O login é de demonstração: a conferência acontece no navegador e não protege os dados, que já estão no arquivo. Na operação, o acesso deve passar pelo SSO da empresa, no servidor.

## Cadastros

Em **Governança → Cadastros** dá para criar, editar e excluir novos itens nas mesmas tabelas da planilha:

| Cadastro | Aba da planilha | Código gerado |
|---|---|---|
| Classificações da informação | `Classificacao_Informacao` | — |
| Ferramentas de IA | `Ferramentas_IA` | `IA-09`, `IA-10`… |
| Usuários e áreas | `Usuarios_Areas` | `USR-0073`… |
| Tipos de informação | `Tipos_Informacao` | `INF-21`… |
| Regras de risco | `Regras_Risco` | `R18`… |
| Fontes de log | `Fontes_Logs` | `SRC-08`… |

Os itens da planilha aparecem como referência, somente leitura. Os novos entram em todo o painel (filtros, inventário, política). Ficam salvos no navegador e podem ser exportados em JSON (**Exportar cadastros**) para levar ao pipeline. Uma regra nova entra na política, mas só passa a classificar eventos depois de incluída no motor de risco.

## Fonte dos eventos

| Fonte | Significado |
|---|---|
| **Planilha** | Carga legada da base oficial do desafio (eventos `EVT-`). |
| **Coleta** | Eventos registrados pelo coletor da GerencIA na operação (todo evento que não veio da planilha). |

O filtro **Fonte** (Todas / Planilha / Coleta) vale em todas as telas.

## Rotina da fila de alertas

1. **Aberto:** o motor gera o alerta e indica um responsável pela regra (credencial → Segurança; dado pessoal → Privacidade; ferramenta não aprovada ou financeiro → Compliance; demais → gestor da área).
2. **Em análise:** o responsável assume o caso, confere a evidência e fala com o colaborador.
3. **Tratado:** fecha com desfecho, ação e comentário; fica no histórico do alerta, com quem tratou e quando.

## Visual

- Layout baseado na referência **Opção 1 · Grafite**; logo **GerencIA (opção A)**, versão vertical no menu lateral.
- Paleta da marca: azul `#3B6FE0`, azul claro `#7DB3F7`, azul-marinho `#1F2733`, cinza texto `#4A5565`, fundo `#F4F6FA`.
- Risco em tons de azul (do claro ao marinho), classificação da informação em cinza e **vermelho só para alerta em aberto**.
- Tamanhos se ajustam à largura da janela; abaixo de 900 px o menu vira barra no topo.

## Limitações conhecidas

- O tratamento de alertas feito no painel fica salvo **só no navegador** em que foi feito (demonstração). Na operação, deve ir para o coletor e para o histórico de auditoria.
- O histórico registra "Você (demonstração)" porque o painel ainda não identifica o usuário logado.
- A divisão de responsável por regra é uma proposta; na base, o responsável já vem atribuído.
- Os alertas da base não têm data de tratamento, então o prazo de 24h só passa a ser medido para os tratados no painel.
