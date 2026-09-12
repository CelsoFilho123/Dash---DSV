# Calçada com carrinho de bebê em São Paulo

Painel estático de uma tela que responde **uma** pergunta: *onde, em São Paulo, dá pra empurrar
um carrinho de bebê pela calçada?*

Pai ou mãe com carrinho de bebê é, por lei, **pessoa com mobilidade reduzida**
(Lei 13.146/2015, art. 3º, IX). Três coisas travam o carrinho e o Censo 2022 mede as três,
face de quadra a face de quadra: não ter calçada, não ter rampa na esquina, e ter um obstáculo
(árvore, poste, lixeira) no meio da calçada.

**Resposta curta:** em 81 de cada 100 calçadas da cidade, não. E o problema não está espalhado —
ele se empilha. A correlação entre "ter rampa" e "ter obstáculo" é de **−0,90** entre os
96 distritos: onde falta rampa, também tem poste no meio e mais rua sem calçada nenhuma.

## Publicado em

<https://celsofilho123.github.io/Dash---DSV/>

## Como ler

| Número | O que é |
|---|---|
| **81,4%** | das calçadas de SP não têm rampa de acessibilidade |
| **18,5%** | das faces de quadra não têm calçada nenhuma |
| **72,7%** | das calçadas têm obstáculo no meio |
| **287.699** | faces de quadra medidas no município |

Uma **face de quadra** é um lado de quadra: o trecho de rua entre duas esquinas.
Os percentuais de rampa e de obstáculo têm como base as faces que *têm* calçada — não faz
sentido perguntar se uma rua sem calçada tem rampa.

**A interatividade:** clicar num distrito em qualquer um dos dois gráficos fixa o destaque
**nos dois ao mesmo tempo**, e reescreve o título e o indicador. É o que muda a leitura: o painel
em repouso diz "a cidade é ruim"; com um distrito fixado, ele diz *quanto* cada lugar está
longe da média — Jardim Ângela em 96,9% sem rampa, República em 19,2%.

## Fonte

IBGE, **Censo Demográfico 2022** — Características urbanísticas do entorno dos domicílios,
tabela já agregada por distrito.

- Arquivo servido pelo painel: [`dados/infraestrutura_por_distrito_censo2022.csv`](dados/infraestrutura_por_distrito_censo2022.csv) (3,5 KB, 96 linhas)
- Nomes de distrito conforme a malha oficial de distritos do município de São Paulo (GeoSampa, `geoportal:distrito_municipal`)

Os números da cidade são **soma de faces**, não média dos 96 distritos — média de distrito daria
peso igual a Marsilac (351 faces) e a Grajaú (7 mil faces).

## O que este painel **não** faz

Cada item aqui é algo que alguém já pediu ao ver alguma versão deste trabalho.

1. **Não calcula rota.** "Me dá o caminho até o parque" foi o primeiro pedido, e é o que ele não
   faz. Rota exige grafo roteável com custo por aresta (largura, declividade, rampa, obstáculo) e
   Dijkstra/A* em cima — não é um recorte deste dado, é outro projeto.
2. **Não desce do distrito para a sua rua.** "E a minha rua?" não tem resposta aqui. O dado do
   Censo 2022 usado é o agregado **por distrito**. O dado original é por face de quadra, mas o
   arquivo agregado que este painel serve já perdeu essa granularidade — e um distrito de 7 mil
   faces esconde ruas muito boas e muito ruins dentro da mesma barra.
3. **Não junta calçada com segurança numa nota única.** Pediram "uma nota só, de 0 a 10, por
   bairro". Recusado de propósito: no projeto maior, infraestrutura e taxa de furto/roubo por
   habitante têm correlação de −0,71, e parte disso é viés de população flutuante nos distritos
   centrais (a própria SSP-SP registra a ressalva). Somar as duas produziria um número que parece
   mais informado do que é. Para quem empurra um carrinho, calçada intransponível é impedimento
   absoluto e furto é outro tipo de decisão — não se somam.
4. **Não mostra as outras dimensões do projeto.** Segurança (SSP-SP), iluminação pública
   (GeoSampa, 662.945 pontos de luz) e fluxo de pedestres existem no projeto maior e ficaram
   **fora** desta tela de propósito — uma tela, uma pergunta.
5. **Não é dado vivo.** É uma foto do Censo 2022. Rampa construída em 2024 não aparece, e o painel
   não sabe a data de hoje. Se a pergunta fosse "como está agora", a arquitetura estática estaria
   errada.

## Arquitetura: por que estático

As duas perguntas antes de escolher ferramenta:

- **Tem segredo?** Não. Dado público do IBGE, nenhuma chave, nenhum token.
- **A API libera CORS?** Não se aplica — o dado não vem de API em tempo de carregamento. Ele foi
  baixado, tratado uma vez e commitado como CSV.

Logo, **degrau 1**: um `index.html` e um arquivo. Sem build, sem servidor, sem conta em provedor,
sem dependência de CDN — os dois gráficos são SVG escrito à mão. O painel inteiro pesa ~21 KB
(17,4 KB de HTML + 3,5 KB de CSV) em duas requisições e abre em janela anônima sem login.

## Rodar local

O painel lê o CSV com `fetch`, então precisa de HTTP — abrir o arquivo direto com `file://`
não funciona (o navegador bloqueia). Da raiz do projeto:

```bash
python -m http.server 8099
```

E abrir <http://localhost:8099>.

## Publicar

No GitHub: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.
Não precisa de Actions nem de build.
