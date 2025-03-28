
# 🚀 Pipeline Generator CLI

Ferramenta CLI para geração automatizada de pipelines do GitHub Actions com foco em boas práticas de engenharia de software, DevOps e escalabilidade de times.

---

## 🛠 Requisitos

- Python 3.10+
- `yamllint` (`pip install yamllint`)
- Docker (para os workflows)
- GitHub Actions configurado
- Branch protection rule no github (de acordo com as regras do time)
- Packages permissions no github (para permitir o armazenamento dos containers)

---

## 🧩 Modularização do Código

A ferramenta foi totalmente modularizada com base em princípios de responsabilidade única e organização por funcionalidade, facilitando testes, manutenção e extensibilidade.

---

## 📁 Estrutura de Diretórios

```
tooling-interview-main/
├── .github/
│   └── workflows/                  # Local onde os arquivos custom.yml e default.yml são gerados
│       ├── custom.yml             # Pipeline com os steps escolhidos via CLI
│       └── default.yml            # Pipeline padrão com todos os steps definidos
├── pipeline/                      # Código-fonte da ferramenta
│   ├── __init__.py
│   ├── args.py                    # Parseador de argumentos CLI
│   ├── constants.py               # Caminhos e variáveis fixas
│   ├── generator.py               # Função principal de geração da pipeline
│   ├── pipeline.py                # Ponto de entrada (entrypoint): `python -m pipeline.pipeline`
│   ├── templates.py               # Manipulação de templates
│   ├── validator.py               # Validação YAML com yamllint
│   └── templates/                 # Templates dos arquivos que compõem o pipeline
│       ├── base.yml               # Base comum dos workflows
│       ├── node/                  # Steps específicos para Node.js
│       └── python/                # Steps específicos para Python
├── projects/                      # Projetos que usam os pipelines
│   ├── users/
│   └── products/
├── .gitignore
├── README.md
└── DESAFIO.pdf
```

---

## ✨ Funcionalidades

- Geração dinâmica de pipelines com os steps desejados (`--steps`)
- Geração automática de `custom.yml` e `default.yml`
- Injeção automática das variáveis `PROJECT_NAME` e `IMAGE_NAME`
- Suporte a múltiplas linguagens: `python`, `node`
- Adição automática do step `build` caso omitido
- Validação com `yamllint`
- Compatível com **WSL**, **Linux**, **Windows** (via terminal do VSCode)
- Estrategia de versionamento
- Foi adicionado **SHA-based immutable tagging** para garantir builds paralelos seguros, rastreabilidade por commit e autoria por usuário via ${{ github.repository_owner }} e ${{ github.sha }}. Isso assegura controle e auditoria total no ciclo de vida das imagens.

---

## 💡 Filosofia da Ferramenta

- O **desenvolvedor tem liberdade** para montar sua própria pipeline por meio da ferramenta CLI usando os comandos e a propria ferramenta personaliza `custom.yml` que será usado para rodar a CI no github Actions pegando o SHA commit em seu **push**.
- Porém, **toda PR (pull request)** direcionada às branches `main`, `develop` e `release/**` terá como base o `default.yml`, que **contém todos os steps obrigatórios** definidos pela equipe DevOps. Estás 3 branchs só executam por meio de PR.
- Isso garante flexibilidade no desenvolvimento, mas controle e confiabilidade na integração e entrega contínua.

---

## ✅ Validação

Todos os arquivos `.yml` gerados passam por validação automática com `yamllint`, que verifica:

- Quebra de linha padrão `\n`
- Chave duplicada
- Booleans válidos
- Limite de 80 caracteres por linha
- Presença de `---` no início

---
## ⚙️ Execução

### ▶️ Aprendendo a utilizar

```bash
python -m pipeline.pipeline -h
```

---

### 🔧 Exemplo de Geração

```bash
python -m pipeline.pipeline --lang python --project products --steps test,deploy
```

esse comando vai gerar os arquivos abaixo de forma dinâmica:

```
.github/workflows/
├── custom.yml   # Contém: build, test, deploy
└── default.yml  # Contém: build, test, deploy (sempre completo)
```

**commit** as alterações e faça **push**. Se você não estiver em uma das 3 branchs principais (main, release, develop) o gatilho do github actions será acionado!

O gatilho para acionar a pipeline em uma das 3 branchs principais (main, release, develop) é **PR (pull request)**

---

## 📌 Melhorias Futuras

| Item                         | Descrição                                                                 |
|------------------------------|---------------------------------------------------------------------------|
| 🐳 Geração de `Dockerfile`   | Automatizar criação do Dockerfile para `node` e `python`                  |
| 🌎 Flag de ambiente          | `--env dev|staging|prod` para definir comportamentos específicos          |
| 🧩 Plugin System             | Suporte a novas linguagens com `templates/<lang>/`                        |
| 🔁 Rollback automático       | Step para desfazer deploys falhos                                         |
| 🧪 Testes unitários          | Cobertura com `pytest` para as funções principais                         |
| 📦 Empacotamento no PyPI     | Tornar instalável via `pip install pipeline-gen`                          |
| 🧪 Testes com matrix         | (futuramente) gerar workflows com `matrix.strategy`                       |

---

## 📌 Pontos de atenção
- Os testes podem ser acompanhado na aba actions
- Clone o projeto e use o comando python -m pipeline.pipeline -h para poder aprender a utilizar
- Utilize a branch feature/teste para testar ou abra uma PR para uma das 3 principais branchs

- A solução do desafio foi totalmente voltada a construção da ferramenta pensando explicitamente em tooling
- Todos os ajustes feitos nos projetos foram apenas para validar este MVP
- O desenvolvedo cogitou implantar mais steps, realizar deploy oficial, adicionar flags e integrar a outras ferramentas porém decidiu focar na ferramenta python e mostrar a possibilidade de evolução da ferramenta sem correr o risco de over-engineering para um unico desafio.
