# Desafio Técnico Nasajon — Anderson Souza

## Score obtido
🏆 **10/10** — *"Excelente! Seu resultado está praticamente idêntico ao gabarito."*

## Sobre a solução
Solução em Python para enriquecimento de dados municipais via API do IBGE,
com autenticação Supabase e envio automático de estatísticas.

## Como executar
1. Abra o notebook no [Google Colab](https://colab.research.google.com)
2. Ao executar a Célula 1, informe seu e-mail e senha quando solicitado
3. Execute todas as células em ordem

## Arquitetura — Service Layer
| Célula | Classe | Responsabilidade |
|--------|--------|-----------------|
| 2 | DataClasses | Estruturas de dados tipadas |
| 3 | AuthService | Autenticação Supabase / JWT |
| 4 | IBGEService | Integração e cache da API IBGE |
| 5 | Processor | Matching e regras de negócio |
| 6 | StatsCalculator + Exporter | Estatísticas e geração do CSV |
| 7 | Submitter | Envio para a API de correção |
| 8 | Main | Orquestrador geral |

## Notas Explicativas — Decisões Técnicas

### Linguagem e Ambiente
Python no Google Colab — escolhido pela agilidade no tratamento de dados
e por não exigir instalação de ambiente local.

### Segurança
Credenciais solicitadas via `input()` e `getpass` em tempo de execução —
nunca ficam salvas no código ou expostas no repositório.

### Estratégia de Matching
1. **Match exato normalizado** — remove acentos e diferenças de case
2. **Fuzzy matching** (difflib, cutoff=0.85) — corrige erros de digitação
   como `Belo Horzionte` → `Belo Horizonte` e `Curitba` → `Curitiba`
3. **Deduplicação** — `Santoo Andre` detectado como duplicata corrompida
   de `Santo Andre` e marcado como `NAO_ENCONTRADO`

### Performance
Uma única requisição ao IBGE (~5290 municípios) indexada em dicionário
em memória — busca O(1) ao invés de N requisições individuais.

### Tratamento de Erros
Exceptions tipadas por camada (AuthError, IBGEError, SubmitError)
com timeout em todas as requisições HTTP.

### O que faria diferente em produção
- Variáveis sensíveis em variáveis de ambiente (.env)
- Cache persistente do índice IBGE para evitar recarga a cada execução
- Testes unitários para cada Service
- Logging estruturado ao invés de print()
