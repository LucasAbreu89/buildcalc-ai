# Operação do Beads

O Beads é a fonte de tarefas, responsáveis, estado e dependências deste projeto. O PRD define o produto e as etapas BC-xx especificam comportamentos e aceites. Os IDs executáveis são bca-*.

## Rotina de um incremento

```bash
bd prime
bd ready
bd search BC-01
bd show <id>
bd update <id> --claim
# Implementar o escopo solicitado e conferir seus critérios de aceite.
bd close <id> --reason "Aceite demonstrado e validação concluída"
```

Usar `bd dep add <dependente> <pre-requisito>` ao descobrir uma dependência real. As etapas futuras não ficam em andamento apenas porque foram cadastradas. O pedido atual preparou a documentação; não implementou BC-01.

## Persistência e sincronização

As issues vivem no banco Dolt local de `.beads/`. A configuração aponta para o repositório GitHub deste projeto. O Beads sincroniza a base por sua integração Dolt/Git, em `refs/dolt/data`; isso é diferente de fazer push da branch de código.

```bash
bd dolt commit -m "Registra alterações do planejamento"
bd dolt pull
bd dolt push
```

Executar sincronização apenas conforme o fluxo autorizado do repositório. Se houver conflito ou divergência, conferir antes de resolver; não usar `--force` como rotina. Falha de sincronização precisa ser informada. O banco local e seu histórico não devem ser apagados ou reinicializados para contornar um erro de acesso.

O arquivo `.beads/issues.jsonl`, quando exportado, é uma **fotografia passiva para consulta/intercâmbio**, não a base autoritativa nem um backup completo. Configuração de backup e restauração usa os mecanismos próprios do Beads/Dolt. Não editar o JSONL à mão para mudar o estado das tarefas.

## Memória e handoff

Usar `bd remember` para decisões persistentes relevantes à retomada e `bd show`/`bd search` para localizar o trabalho. Não criar `MEMORY.md`, `TODO.md` ou documento de status paralelo.

Documentação de requisitos, contratos, critérios de cálculo e procedimentos técnicos não é memória de sessão. Esses documentos ficam versionados no Git. A transcrição de conversas e projetos privados de clientes não deve ser publicada para transportar contexto; os princípios reutilizáveis estão no PRD e nos procedimentos.

Em nova máquina ou clone, verificar a conexão Dolt e consultar a documentação da versão instalada antes de inicializar qualquer base. `bd prime`, `bd where`, `bd doctor` e `bd dolt remote list` ajudam no diagnóstico. Não importar um JSONL como substituto de uma base remota existente sem analisar a situação.
