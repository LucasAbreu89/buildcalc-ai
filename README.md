# BuildCalc AI

Plataforma proposta para transformar projetos PDF/DWG e uma planilha de serviços em quantitativos com memórias de cálculo e evidências visuais auditáveis.

Este repositório começa pela especificação do produto e pelos procedimentos de orçamento já construídos. A aplicação ainda não está implementada. O desenvolvimento será incremental: cada etapa entrega frontend e backend do mesmo fluxo, começando por upload de Excel, extração e exibição dos serviços.

## Começar a leitura

1. [PRD completo](docs/PRD.md): requisitos, jornada, critérios de medição e limites.
2. [Etapas do produto](docs/etapas-produto.md): fatias de frontend/backend e critérios de aceite.
3. [Arquitetura proposta](docs/arquitetura-proposta.md): contratos e execução de ferramentas/IA/CAD.
4. [Qualidade e validação](docs/qualidade-e-validacao.md): referências e gates para uma entrega auditável.
5. [Procedimentos de orçamento](procedimentos-orcamento/LEIA_PRIMEIRO.md): pacote técnico reutilizável.

## Trabalhar passo a passo

O Beads é a fonte do andamento e das dependências. Os documentos descrevem requisitos; não mantêm listas paralelas de tarefas concluídas.

```bash
bd prime
bd ready
bd show <id>
bd update <id> --claim
# Implementar apenas a etapa solicitada, demonstrar e validar.
bd close <id> --reason "Critérios de aceite cumpridos"
```

O primeiro incremento funcional é **BC-01 — upload de XLSX até a listagem de serviços**. Consulte seu ID executável com `bd search BC-01`. Os detalhes de operação, persistência e sincronização estão em [docs/beads.md](docs/beads.md).

Arquivos de clientes, DWGs, PDFs e planilhas de obras não fazem parte deste repositório. Fixtures públicas futuras devem ser sintéticas, pequenas e identificadas. A origem e os hashes dos procedimentos copiados estão em [docs/procedimentos-origem.md](docs/procedimentos-origem.md).
