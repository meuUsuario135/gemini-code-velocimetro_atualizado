# Velocímetro Pré-Market

Dashboard visual (HTML/CSS/JS puro, sem dependências) para leitura rápida do humor
do mercado no pré-market, combinando dados macro globais com o fluxo das principais
ADRs brasileiras.

## Como rodar

Não há build nem instalação. Basta abrir o arquivo `index.html` em qualquer navegador:

- **Localmente**: dê duplo clique em `index.html`, ou rode um servidor simples:
  ```bash
  python3 -m http.server 8000
  ```
  e acesse `http://localhost:8000`.
- **GitHub Pages** (recomendado para acesso via link, de qualquer lugar):
  1. Vá em *Settings → Pages* no repositório.
  2. Em *Source*, selecione a branch `main` e a pasta raiz (`/`).
  3. Salve. O dashboard ficará disponível em
     `https://meuUsuario135.github.io/gemini-code-velocimetro_atualizado/`.

## Atualização automática

Ao abrir a página, o dashboard já busca as cotações automaticamente e continua
atualizando sozinho, sem precisar de nenhuma ação:

- **DXY** é alimentado pela variação do USD/BRL (via [AwesomeAPI](https://docs.awesomeapi.com.br/)).
- **VALE** é alimentado pela variação de VALE3 (via [brapi.dev](https://brapi.dev/)).
- Um ciclo de atualização roda a cada **60 segundos** (`INTERVALO_ATUALIZACAO` no script).
- O status da última sincronia aparece no topo da página (sucesso, buscando, ou falha).
- Os demais ativos (VIX, FEF2!, CL1!, PBR, ITUB, BBDC, BBAS, B3SA) continuam manuais,
  já que as APIs públicas gratuitas usadas aqui não cobrem esses tickers. Use os
  botões `+`/`-` ou digite o valor diretamente.

Se as APIs falharem (fora do ar, CORS bloqueado, limite de uso), o dashboard não trava:
ele mantém os últimos valores manuais e sinaliza a falha no status de atualização.

## Lógica do cálculo

- **VIX** e **DXY** têm correlação inversa clássica com o mercado, por isso entram
  invertidos na soma (queda do dólar/volatilidade = viés de alta).
- **Soma Macro** = -VIX + FEF2! + CL1! - DXY
- **Soma ADRs** = VALE + PBR + ITUB + BBDC + BBAS + B3SA
- **Soma Global** = Soma Macro + Soma ADRs, exibida no velocímetro central.
- A intensidade do movimento (Lateralizada / Fraca / Moderada / Forte Impulso) e o
  viés (Comprador / Vendedor / Range) são derivados do valor absoluto da Soma Global.

## Estrutura do repositório

```
.
├── index.html   # aplicação completa (HTML + CSS + JS em um único arquivo)
└── README.md    # este arquivo
```

> **Nota sobre a limpeza:** os arquivos legados `gemini-code-velocimetro` e
> `gemini-code-velocimetro_atualizado.html` foram consolidados neste único `index.html`.
> Como a integração MCP usada aqui não tem permissão de exclusão de arquivos,
> recomenda-se apagar manualmente os arquivos antigos pela interface do GitHub
> para deixar o repositório limpo.

## Limitações conhecidas

- APIs públicas gratuitas (AwesomeAPI, brapi.dev) podem ter limite de requisições ou
  instabilidade — não há SLA garantido.
- Sem chave de API configurada, `brapi.dev` opera no nível gratuito, que pode ter atraso
  de cotação.
