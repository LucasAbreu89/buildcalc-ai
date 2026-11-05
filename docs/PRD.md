# BuildCalc AI — PRD

Versão inicial: 0.1 — 05/11/2026. Documento de requisitos e proposta de evolução. A aplicação ainda não foi implementada. O andamento executável, responsáveis e bloqueios são mantidos no Beads; este documento não é uma lista paralela de status.

## 1. Objetivo do produto

Permitir que um orçamentista envie uma planilha de serviços e projetos em PDF/DWG, defina o escopo, obtenha quantitativos e revise visualmente cada parcela antes de exportar um Excel com memórias de cálculo. O sistema deve mostrar o que considerou, onde encontrou a informação, como mediu e quais premissas adotou.

O produto vende produtividade e capacidade de auditoria. Sua primeira entrega comercial é levantamento de quantitativos assistido, com revisão explícita. Preços unitários, composições, encargos e BDI constituem uma evolução própria; preencher quantidades não autoriza inventar preços nem apresentar um orçamento executivo completo.

## 2. Origem e aprendizado

O produto parte de procedimentos já usados em uma reforma comercial. Os testes conseguiram relacionar Excel, representações CAD/PDF, cálculos, prints e resultados. Também revelaram falhas que precisam virar requisitos: escolha da aba errada, omissão de corredores e mezaninos, sobreposição entre vistas, contornos inválidos, explicações genéricas, mistura de elementos preservados com obra nova e trajetos elétricos sem alturas.

Uma entrega em uma obra não comprova desempenho generalizado. O produto deve ser avaliado em famílias de serviços e conjuntos de projetos distintos, com referência conferida por orçamentistas. Scripts específicos da obra e números de um cliente não são padrões para outras obras.

## 3. Usuários e situações de uso

| Usuário | Necessidade | Resultado desejado |
|---|---|---|
| Orçamentista | Medir serviços de uma lista recebida e justificar as contas | Quantidades rastreáveis, ajustes rápidos e Excel compatível |
| Engenheiro/revisor | Conferir inclusões, exclusões, alturas e conflitos | Projeto marcado, parcelas identificadas e decisões compreensíveis |
| Empresa de reformas/lojas | Repetir trabalhos semelhantes mantendo critérios | Projetos separados, modelos de critérios e histórico de revisões |
| Administrador da plataforma | Operar clientes, processamentos e cobrança | Isolamento, limites, custos observados e suporte auditável |

A operação inicial é local/interna, de um usuário. Autenticação, autorização e isolamento por organização são requisitos de passagem para qualquer piloto externo, mesmo quando houver apenas um cliente convidado.

## 4. Diretriz de desenvolvimento incremental

Cada etapa deve entregar um comportamento pequeno, utilizável pela interface e atendido pelo backend real. Não construir todo o backend antes do frontend nem telas sem a operação correspondente. Cada etapa inclui contrato, persistência necessária, erros, demonstração e verificações apropriadas.

O pedido atual prepara projeto, documentação, procedimentos e Beads. Não autoriza implementar todas as etapas em sequência. A implementação começa pela etapa solicitada pelo usuário em cada ciclo; a existência do planejamento não dispara construção automática das fases futuras.

As especificações detalhadas de todas as etapas estão em [etapas-produto.md](etapas-produto.md). Os IDs BC-01 etc. identificam requisitos; os IDs bca-* e suas dependências no Beads identificam o trabalho executável.

## 5. Primeiro ciclo: Excel até lista de serviços

**BC-01: o usuário envia um XLSX e vê as abas e linhas de serviços extraídas.** Esta é a primeira demonstração funcional, sem depender de IA, CAD ou PDF. BC-02 acrescenta mapeamento ajustável de cabeçalhos/colunas e confirmação do escopo; BC-03 acrescenta obra persistente e retomada após reiniciar a aplicação.

### Experiência

1. Abrir a aplicação local e escolher o arquivo `.xlsx`.
2. Ver validação do formato/tamanho e progresso do envio/processamento.
3. Ver as abas existentes, incluindo a indicação de abas ocultas. O sistema pode sugerir uma aba, mas não fixa silenciosamente a primeira como escopo.
4. Selecionar uma aba e ver as linhas extraídas. Na BC-02, conferir a linha de cabeçalho e confirmar ou ajustar o mapeamento de código, descrição e unidade. Quantidade recebida é opcional e somente leitura.
5. Ver uma tabela de serviços com origem da linha, código, descrição e unidade. Títulos, subtítulos e totais não podem ser misturados silenciosamente a itens físicos.
6. Na BC-02, corrigir o mapeamento e atualizar a visualização. Se a classificação estiver ambígua, mostrar as linhas originais em BC-01 e permitir correção no incremento seguinte, em vez de omiti-las.

### Backend e dados

- Receber multipart, validar o conteúdo de XLSX e conservar o arquivo original sem executar fórmulas, macros ou links externos.
- Extrair estrutura completa das abas e células necessárias. A ferramenta deve ser capaz de lidar com mais de mil linhas, sem depender de um resumo parcial do modelo.
- Distinguir vazio, zero, número, texto, fórmula e valor em cache. Fórmula sem cache não se torna zero.
- Preservar coordenadas, células mescladas, hierarquia e referências suficientes para rever o mapeamento. Nome da aba não identifica uma linha: usar arquivo/aba/linha com ID próprio.
- Conservar o arquivo e o resultado com ID durante o fluxo inicial. BC-02 salva o mapeamento da importação; BC-03 consolida persistência por obra e retomada após reinício. Não exigir a plataforma de obras completa para demonstrar BC-01.
- Suportar `.xlsx` no primeiro incremento. `.xls`, `.xlsm`, proteção/senha e formatos não suportados devem produzir resposta explícita e recuperável; formatos adicionais serão etapas próprias.
- Limites de bytes, células e expansão do ZIP são configuráveis. Valores definitivos dependem de ensaio; nenhum limite proposto deve ser apresentado como capacidade já medida.

### Aceite do primeiro ciclo, sem concentrar tudo em BC-01

- Um arquivo sintético com duas abas, uma oculta, itens, subtítulos, mesclagens, quantidades zero/vazias e fórmulas aparece corretamente no navegador.
- Na BC-02, uma planilha com cabeçalho deslocado permite mapeamento ajustável sem alterar o arquivo enviado; BC-01 preserva e mostra as linhas originais sem classificação silenciosa.
- O usuário vê pelo menos código/descrição/unidade e a linha de origem de cada serviço, sem depender da organização de um cliente específico.
- Arquivo inválido, extensão disfarçada, senha e erro de parsing aparecem como erros claros; a interface permite tentar novamente.
- O fluxo navegador → backend → processamento → tabela funciona com dados reais do arquivo. Um mock de lista não cumpre o aceite.
- Não há chamada de IA, cálculo de quantidades levantadas ou exposição pública nesta primeira entrega.

## 6. Jornada completa pretendida

### 6.1 Criar obra e importar serviços

O usuário cria uma obra, informa se é reforma/construção e envia a planilha. O sistema cataloga todas as listas e permite escolher abas, grupos, itens e disciplinas. Um resumo do escopo mostra inclusões e exclusões antes do levantamento. Alterar o escopo cria uma nova versão, sem apagar resultados anteriores.

Quantidades recebidas permanecem intactas, com valores, fórmulas e vazios. A quantidade levantada fica em campo/coluna próprios. Valores recebidos podem ser usados em uma comparação posterior, mas não servem de alvo para ajustar uma medição.

### 6.2 Receber e catalogar projetos

PDF, DWG e referências associadas são arquivos versionados. A importação identifica plantas, detalhes, cortes, Model, layouts, camadas, pavimentos, revisões e unidades. Upload não significa arquivo examinado. A interface distingue recebido, processado, parcialmente examinado, incompatível e faltante.

O mesmo DWG pode conter múltiplas plantas e representações do mesmo elemento. O sistema deve localizar e vincular representações sem somá-las como novas ocorrências. Xrefs, fontes e objetos não suportados precisam aparecer em um relatório de cobertura; leitura incompleta não pode parecer leitura completa.

### 6.3 Relacionar serviço e fonte

A IA usa descrições, unidades, legendas, notas, plantas e cortes para propor as representações pertinentes ao serviço. Cada associação tem fonte e contexto. O usuário consegue substituir uma fonte e refazer somente as dependências afetadas.

Por obra, existem critérios explícitos de fase e fornecimento: existente, novo, a demolir, a preservar, a reaproveitar, manutenção e fora do escopo. Uma planta de proposta não prova que todos os elementos nela desenhados sejam novos.

### 6.4 Definir e medir parcelas

O sistema pode selecionar geometria existente ou criar contornos/trechos em uma cópia de conferência. Deve mostrar a seleção antes de publicar a quantidade. Portas, soleiras, profundidade de vãos, ilhas, recortes, pilares e faces precisam seguir o critério do serviço.

**Medir sem cota escrita é requisito:** um DWG pode conter geometria mensurável sem ter uma anotação de dimensão. O sistema pode extrair coordenadas ou operar um CAD para medir, após conferir unidades e escala. Registrar se o valor foi transcrito, calculado por cotas/coordenadas, medido por comando nativo ou estimado graficamente. Nunca afirmar operação nativa quando ela não ocorreu.

Uma grandeza que não está representada não aparece por automação: por exemplo, altura não modelada nem indicada depende de outra fonte ou de premissa explícita. PDF vetorial precisa de escala/referência válida; imagem escaneada exige calibração verificável. Cada método tem limitações visíveis.

### 6.5 Resolver lacunas e conflitos

Premissas comuns podem ser propostas e adotadas segundo política escolhida pelo usuário: exigir revisão por premissa ou permitir um conjunto de critérios autorizado para a obra. Essa autorização é específica e versionada. Não usar uma dimensão do piloto como padrão universal.

Uma ausência de altura de rodapé pode receber um valor adotado com justificativa; um percurso externo de alimentação inexistente nos documentos permanece parcial. A classificação decorre da grandeza e do escopo, não da vontade de preencher a planilha.

Pendências devem informar serviço, descrição, fonte/vista, informação ausente ou conflitante, parcelas afetadas, ação necessária e efeito sobre a quantidade. Agrupar uma mesma causa entre serviços, preservando a explicação específica em cada um. Não abrir centenas de mensagens genéricas.

### 6.6 Revisar e entregar

A tela de auditoria combina lista de serviços, projeto, seleção e memória. Ao clicar em uma parcela, a respectiva geometria/ponto/trecho aparece marcado. Ao clicar na marca, a memória correspondente aparece. Uma única imagem genérica não cobre um serviço espalhado por múltiplas plantas.

O Excel de saída preserva o original e acrescenta campos próprios, fórmulas e vínculos. Exportação deve incluir memórias, evidências e resumos de premissas/pendências. Itens não aplicáveis podem ter apenas uma explicação clara e fonte compartilhada quando uma memória individual seria vazia. Custos indiretos podem ficar a definir pela engenharia, segundo o escopo, sem levantamento físico fictício.

Uma revisão de fonte ou premissa produz novos cálculos e evidências, mantendo histórico. A comparação informa qual alteração provocou cada diferença.

## 7. Estados e significados

Estados de execução de arquivo/job não são estados técnicos do resultado nem estados de revisão humana.

| Estado técnico | Significado para o usuário |
|---|---|
| Não levantado | O serviço está no escopo, mas ainda não teve medição suficiente |
| Confirmado | As parcelas exigidas pelo critério têm fonte e método suficientes; confirmação documental não significa aprovação humana automática |
| Estimativa | Existe quantidade com premissas, aproximações ou abrangência adotada visíveis |
| Parcial | Há subtotal conhecido e parcela não medida; subtotal e total pretendido são campos distintos |
| Pendente | Falta uma informação necessária para o resultado dependente |
| Não aplicável | O serviço foi excluído por uma razão específica do cenário adotado |
| A definir pela engenharia | Item de planejamento, custo indireto ou definição que não decorre de medição física neste escopo |

Zero é um número válido apenas quando demonstrado por critério e dados. Não representa não levantado, pendente ou não aplicável. Resultado estimado não se torna confirmado porque alguém aprovou sua utilização orçamentária: revisão humana e método continuam separados.

## 8. Requisitos funcionais transversais

| ID | Requisito |
|---|---|
| RF-01 | Importar listas de Excel sem perder a origem e sem assumir uma aba única |
| RF-02 | Selecionar e versionar o escopo de abas, itens e disciplinas |
| RF-03 | Catalogar fontes por representação e revisão, com cobertura examinada explícita |
| RF-04 | Extrair, visualizar e referenciar geometria CAD/PDF em sistemas de coordenadas conhecidos |
| RF-05 | Relacionar serviços a elementos e fases de execução, com revisão humana |
| RF-06 | Calcular parcelas por ferramentas verificáveis com unidades compatíveis |
| RF-07 | Gerenciar alturas, níveis, vãos, faces, deduções e trechos compartilhados |
| RF-08 | Propor premissas conforme política da obra e propagar alterações |
| RF-09 | Produzir evidências reais marcadas e vinculadas aos mesmos IDs das parcelas |
| RF-10 | Explicar conflitos, exclusões e resultados parciais de forma específica |
| RF-11 | Revisar e corrigir contornos/trechos sem editar os originais |
| RF-12 | Exportar Excel validado e relatório completo das decisões |
| RF-13 | Reprocessar revisões por dependências, sem sobrescrever versões |
| RF-14 | Limitar, cancelar e retomar processamentos, observando custo e execução |
| RF-15 | Isolar organizações/obras e controlar acesso a arquivos, resultados e compartilhamentos |

Os contratos detalhados, entidades, endpoints e processamento estão em [arquitetura-proposta.md](arquitetura-proposta.md).

## 9. Regras de cálculo por família

### Áreas e acabamentos

- Usar contornos válidos, sistema de coordenadas e unidade conferidos, com recortes e vãos demonstrados.
- Piso, forro, pintura e impermeabilização podem ter escopos distintos no mesmo ambiente. Uma área base não pode ser copiada sem conferir o serviço.
- Área indicada no projeto e área recalculada são origens diferentes; investigar diferenças em vez de ajustar vértices para coincidir com uma etiqueta.
- Térreo, mezanino, corredores e detalhes ampliados precisam de cobertura e deduplicação.

### Paredes, bases, revestimentos e portas

- Distinguir eixo, faces livres, espessura, altura, base e fechamento superior.
- Contar vãos segundo a finalidade; não transformar um vão existente em fornecimento de uma porta nova sem critério de fase.
- Uma alteração em base/drywall pode afetar pintura, revestimento e outras dependências; recalcular apenas os itens vinculados.

### Elétrica

- Separar ocorrência física, grupo de circuito, ponto, estação, conector e verba.
- Identificar caminhos horizontais e verticais, plano de encaminhamento, altura terminal e datum entre pisos. Não desenhar diagonais ligando duas representações de uma prumada.
- Contar cada suporte/eletroduto físico uma vez; contar condutores por circuito/trecho e função F/N/PE/retorno, conforme a informação efetivamente disponível.
- Se for usado um envelope conservador de condutores por circuito, classificar como estimativa e declarar a diferença para uma contagem executiva trecho a trecho.
- Distinguir cabo multipolar PP de unipolares, geometria instalada de reserva de ligação e perdas de compra. Não aplicar percentual global silencioso.
- Verificar sobreposição entre pacotes completos por ponto/verba e metragens de materiais; o levantamento não deve induzir cobrança duplicada na precificação.
- Material/seção sem linha compatível na lista gera medição de apoio e compatibilização. Não converter aço em PVC ou copiar uma metragem para duas descrições iguais.
- Permitir subtotal interno conhecido quando a origem externa não estiver definida, mantendo a dependência do trecho faltante.

### Fundações e estrutura

A arquitetura pode informar demolição/construção e preservação, mas não autoriza dimensionar aço, estacas ou blocos sem base correspondente. O cenário pode preservar a estrutura existente; explicar essa premissa diretamente no item. Ampliação para quantitativos estruturais detalhados exige fontes e validações próprias, sem inferências executivas a partir de aparência.

## 10. Arquitetura e papel da IA

O sistema terá frontend, backend, armazenamento por obra, processamento em segundo plano quando necessário e ferramentas de leitura/cálculo/renderização. A IA interpreta descrições, legendas e relações, propõe seleções e aciona ferramentas. Ferramentas retornam dados e evidências verificáveis; números e rótulos de sucesso não são aceitos apenas porque vieram do modelo.

Existem duas rotas complementares para CAD:

1. Leitura estruturada por biblioteca/motor, com acesso a entidades, coordenadas e representações.
2. Aplicativo CAD em ambiente isolado, operado por API de computer use com capturas de tela e ações efetivamente executadas.

Instalar um CAD numa VM não implementa a ponte com o modelo. O produto precisa manter a sessão, executar ações permitidas, devolver observações, limitar o processamento e comprovar o resultado. Biblioteca aberta, renderizador e CAD Reader têm capacidades diferentes e devem ser ensaiados nos mesmos casos de referência.

Uma VM/containers e um banco simples bastam como ponto de partida proposto. Kubernetes não é pré-requisito para provar leitura e medição. CAD comercial, API de IA, renderização e revisão humana entram no custo observado de cada trabalho. Escolha do motor, versões, provedor e modelo permanecem decisões a validar; disponibilidade nesta estação não implica direito/licença de uso no produto.

## 11. Requisitos não funcionais e operação

- Integridade: originais imutáveis, versões de procedimentos/ferramentas/modelo, parâmetros e decisões registrados.
- Reprodutibilidade: mesmos dados e parâmetros no motor determinístico reproduzem a conta; mudanças de interpretação da IA geram novas versões identificáveis.
- Segurança: arquivos e seus textos são dados não confiáveis, nunca instruções para executar comandos ou revelar segredos. Parsing e CAD em processos/ambientes isolados e com limites.
- Privacidade: acesso por organização/obra, URLs controladas, retenção e exclusão implementadas, sem inserir arquivos de clientes ou credenciais no Git.
- Recuperação: idempotência, tentativas limitadas, cancelamento, resultados parciais preservados e sinalização de execução incompleta.
- Custo: registrar chamadas, páginas/recortes processados, tempo de CAD e recursos. Não retransmitir indiscriminadamente um DWG enorme ou todas as páginas ao modelo a cada passo.
- Limites essenciais de tempo, tentativas, chamadas e orçamento entram antes da primeira operação externa de IA/VM (BC-08/16/17). A gestão comercial de BC-36 aproveita esses controles; não é sua primeira implementação.
- Observabilidade: logs por execução sem conteúdo sensível desnecessário, versões e eventos suficientes para suporte.
- Usabilidade: progresso por trabalho real; estado vazio/erro/cancelamento compreensível; tabelas navegáveis e geometria legível.
- Qualidade de exportação: fórmulas e caches conferidos, vínculos válidos, imagens legíveis, recursos preservados ou limitação de formato explícita.

## 12. Critérios de passagem do produto

1. **Fatia local funcional:** importação real de Excel e serviços revisáveis no navegador.
2. **Base documental:** upload/catalogação/visualização e unidades de projeto auditadas.
3. **Uma medição completa:** serviço escolhido → fonte → seleção → cálculo → print → memória → Excel, sem edição manual oculta.
4. **Famílias civis repetíveis:** áreas, paredes e contagens avaliadas em dados sintéticos e obras distintas.
5. **CAD e computer use comprovados:** leitura/representações e medição sem cota escrita comparadas com referência independente; arquivos incompatíveis identificados.
6. **Piloto externo protegido:** autenticação e isolamento testados, operação limitada, custos observados e revisão humana explícita.
7. **Instalações e revisões:** topologia/altimetria e alterações de fontes/premissas com dependências verificadas.
8. **Produto comercial:** plano de cobrança, capacidade medida, suporte, retenção e recuperação exercitados. Preços e promessas dependem do resultado dos pilotos.

Não há percentual de acurácia, prazo de processamento ou data de lançamento aprovados nesta versão. Tolerâncias precisam ser definidas por família/método e avaliadas contra referências independentes. Os ensaios e falhas de referência estão em [qualidade-e-validacao.md](qualidade-e-validacao.md).

Os gates são incrementais: uma família em PDF pode ser demonstrada sem aguardar o motor DWG; cada rota CAD/CUA tem sua validação própria. A conferência independente por família e formato é pré-requisito de seu piloto externo. BC-35 consolida a generalização entre obras, sem adiar toda avaliação técnica até a última fase ou exigir elétrica para liberar uma família civil já validada.

## 13. Decisões ainda necessárias

| Decisão | Como chegar a uma decisão concreta |
|---|---|
| Motor DWG e renderização | Comparativo de leitura, unidades, blocos, layouts, referências e medição real em arquivos representativos |
| Computer use e aplicativo | Prova em VM compatível, captura/ação/medida comprovadas, retomada e custo; verificar licença/automação do aplicativo escolhido |
| Política de premissas | Configuração por obra e exemplos revisados, com autorização e efeito em cada serviço |
| Preservação de Excel complexo | Corpus de arquivos com fórmulas, desenhos, nomes, validações e formatos; definir matriz de compatibilidade |
| Limites e cobrança | Medir recursos e tempo humano por perfil de trabalho antes de definir franquias ou preço |
| Modelo/API de IA | Avaliar tarefas reais, rastreabilidade, custo e estabilidade de contrato; manter adaptador substituível |
| Momento de exposição pública | Demonstrar requisitos de autenticação, isolamento, retenção, recuperação e avaliação técnica |
| Quantitativos estruturais/preços | Requisitos e fontes próprios; implementação posterior ao núcleo de levantamento revisável |

## 14. Procedimentos e documentação relacionada

- [Guia dos procedimentos](../procedimentos-orcamento/LEIA_PRIMEIRO.md).
- [Fluxo mestre](../procedimentos-orcamento/references/00_fluxo_mestre.md).
- [Planilha e escopo](../procedimentos-orcamento/references/02_planilha_escopo_e_mapeamento.md).
- [Pendências](../procedimentos-orcamento/references/03_duvidas_e_continuidade.md).
- [Áreas](../procedimentos-orcamento/references/04_calculo_de_areas.md).
- [Perímetros e alturas](../procedimentos-orcamento/references/05_perimetros_e_alturas.md).
- [Quantitativos e evidências](../procedimentos-orcamento/references/06_quantitativos_e_memorias.md).
- [Computer use](../procedimentos-orcamento/references/07_computer_use_e_encerramento.md).
- [Conferência](../procedimentos-orcamento/references/08_conferencia_comparacao_e_entrega.md).
- [Etapas frontend/backend](etapas-produto.md).
- [Arquitetura proposta](arquitetura-proposta.md).
- [Qualidade e avaliação](qualidade-e-validacao.md).
- [Origem da cópia dos procedimentos](procedimentos-origem.md).

O pacote copiado preserva as instruções atuais. Referências ao Codex e aplicativos locais orientam os testes; sua adaptação para funções de backend/API é trabalho de implementação, não uma capacidade já entregue por estes arquivos Markdown.
