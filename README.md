# Automação de Cotação de Câmbio com n8n

Este projeto consiste em um workflow automatizado desenvolvido no n8n que realiza a consulta de moedas estrangeiras (Dólar, Euro e Libra) em relação ao Real (BRL) e armazena o histórico de dados de forma estruturada em uma planilha do Google Sheets.


## 📊 Arquitetura do Fluxo

O design do workflow foi estruturado seguindo as melhores práticas de manipulação de dados em JSON:

Manual Trigger ➔ HTTP Request (API) ➔ Set Node (Tratamento) ➔ Google Sheets (Append)

1. **Trigger**: Execução manual (passível de alteração para `Schedule Trigger` para automação diária).
2. **HTTP Request**: Consumo de API externa de câmbio.
3. **Set Node**: Filtragem de propriedades do JSON, inversão matemática de taxas de câmbio e padronização para 2 casas decimais.
4. **Google Sheets**: Inserção automatizada de novas linhas (`Append Row`) mapeando os cabeçalhos dinamicamente.


## 🧠 Desafios Técnicos & Tomada de Decisão (Pivoting)

Durante o desenvolvimento, a abordagem inicial utilizava uma API pública sem autenticação. Devido a limitações de infraestrutura do servidor de origem e bloqueios de IP por requisições em lote originadas da nuvem (Erro **HTTP 429 - Too Many Requests**), o projeto foi refatorado para utilizar a **ExchangeRate-API** com autenticação via Query Parameters, garantindo resiliência e estabilidade ao fluxo.

Como a nova API retorna o valor de câmbio invertido (base BRL para moedas estrangeiras), foi implementada uma lógica em JavaScript dentro do nó *Set* para calcular o valor real de compra da moeda:

```javascript
// Exemplo de normalização matemática de dados no nó Set
Dolar: {{ (1 / $json.conversion_rates.USD).toFixed(2) }}
```
Isso garante que o dado seja gravado na planilha final com o valor real de mercado e perfeitamente arredondado.

## 🚀 Como Reutilizar este Workflow
1. Baixe o arquivo `workflow.json` deste repositório.
2. No seu painel do n8n, crie um novo workflow.
3. Clique no menu de opções (três pontos no canto superior direito) e selecione **Import from File**.
4. Configure suas credenciais do Google Sheets no último nó.
5. Obtenha sua chave gratuita em [exchangerate-api.com](https://www.exchangerate-api.com) e substitua no nó de HTTP Request.
6. Pronto! É só rodar.
