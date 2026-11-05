# Qualidade e validação do BuildCalc AI

Documento de requisitos e critérios de aceite, preparado em 05/11/2026. Não descreve funcionalidades já implementadas nem comprova desempenho comercial. A execução, os responsáveis, os bloqueios e a situação das entregas pertencem ao **Beads**; este documento não é um quadro de tarefas.

## 1. Objetivo da validação

Demonstrar que um terceiro consegue reproduzir o quantitativo a partir das mesmas fontes, reconhecer o alcance físico considerado e compreender as premissas e limitações. Uma soma correta com contorno errado continua sendo um resultado incorreto. Uma imagem bonita com parcelas incompletas também não atende ao contrato de qualidade.

A liberação de cada família de serviço exige três avaliações complementares:

1. **Resultado:** unidade, geometria, contagem, alturas, fórmulas e limite do total estão corretos para o critério adotado.
2. **Evidência:** cada parcela pode ser localizada no projeto, e a marcação corresponde à versão atual da conta.
3. **Interpretação técnica:** o serviço, a condição existente/proposta, os materiais e as inclusões/exclusões foram interpretados adequadamente.

Testes automatizados verificam regras e regressões. A avaliação por orçamentista independente verifica também cobertura e interpretação. Aprovação humana não deve apagar as premissas que tornam uma quantidade estimada.

## 2. Contrato mínimo por serviço

Cada resultado deve possuir uma chave estável de origem: arquivo de planilha, aba, tabela/seção, linha/identificador original e revisão de importação. Códigos como `1.10` permanecem texto. O identificador isolado não resolve códigos repetidos em abas diferentes.

O registro auditável inclui:

| Grupo | Dados exigidos |
|---|---|
| Serviço recebido | Descrição integral, unidade, localização na planilha e limites do escopo autorizado |
| Elementos físicos | IDs, ambiente, pavimento, material, fase e relação entre vistas do mesmo elemento |
| Base geométrica | Entidades ou contorno, coordenadas, plano de referência, transformações, unidade e calibração aplicáveis |
| Critério | Eixo/face/projeção/desenvolvimento, vãos, retornos, multiplicidade, componentes de pacote e exclusões |
| Cálculo | Entradas, fórmulas, subtotais, reservas e arredondamentos distinguíveis |
| Proveniência | Arquivo, revisão, prancha/vista, localizador da informação e método efetivamente empregado |
| Evidência | Recortes reais marcados com os mesmos IDs das parcelas, legenda e representação de inclusões/deduções |
| Premissas e pendências | Motivo, origem, alcance, efeitos nos itens dependentes e modo de resolução |
| Revisão | Quem alterou/aprovou, quando, versão anterior e motivo da decisão |

Um hash comprova a identidade de um arquivo ou artefato; não comprova que o contorno foi bem escolhido. Um nome de arquivo ou número de prancha não substitui a explicação do item. Uma propriedade CAD extraída não deve ser descrita como comando nativo executado.

## 3. Estados que não podem ser confundidos

Separar o estado técnico da quantidade do estado de revisão do usuário e do estado operacional do processamento.

| Estado técnico | Comportamento esperado |
|---|---|
| Confirmado | Base suficiente para o alcance declarado, com origem e evidência disponíveis; continua sujeito à revisão do usuário |
| Estimativa | Uma ou mais entradas ou critérios são adotados; os efeitos dessas adoções ficam identificados |
| Parcial | Existe subtotal independente, mas falta uma parcela necessária ao total do serviço |
| Pendente | Falta informação que impede obter quantidade utilizável no alcance declarado |
| Não aplicável | Exclusão de escopo explicada especificamente para aquele serviço; quantidade desconhecida não é convertida em zero |
| A definir pela engenharia | Itens de planejamento/custo indireto ou decisões sem levantamento físico pertinente |

O processamento pode estar concluído e conter serviços parciais ou pendentes. Um resultado revisado ou aceito pelo usuário pode continuar sendo uma estimativa. Um campo obrigatório vazio deve propagar a dependência; não pode resultar em zero saudável, em total reduzido silenciosamente ou em indicador de conclusão indevido.

## 4. Matriz de regressões derivada dos aprendizados

Os exemplos abaixo são requisitos generalizados. Não autorizam copiar projetos, planilhas ou medidas reais para o repositório público. Os IDs `QV-*` identificam cenários de teste, não tarefas ou estados de execução.

| ID | Cenário de entrada | Resultado e evidência exigidos |
|---|---|---|
| QV-01 | Excel com várias abas, uma aba oculta e serviços repetidos em resumo/detalhamento | Inventariar todas as abas, pedir/registrar o escopo, preservar as excluídas e evitar somar resumo aos detalhes |
| QV-02 | Colunas recebidas com fórmula, valor, zero e vazio | Preservar cada condição original; produzir levantamento independente em coluna própria; não preencher nossa medição com a quantidade recebida |
| QV-03 | Ambientes no térreo e mezanino, com representações em regiões distintas do Model/layouts | Cobertura por pavimento e representação, com IDs e prints próprios; nenhuma seleção limitada à vista inicial é apresentada como levantamento completo |
| QV-04 | Área principal e corredor ou apêndice separado | Demonstrar a inclusão ou exclusão específica do corredor; recalcular também os serviços que realmente dependem desse contorno |
| QV-05 | Contorno com arestas não ordenadas, furos e segmentos desconectados | Rejeitar polígono autointersectante e linhas de ligação artificiais; renderizar cada trecho de modo fiel, sem diagonais criadas pela ordem da lista |
| QV-06 | Porta com profundidade, soleira, batentes, pilares e ilha | Auditar faixas, deduções e retornos; cada faixa entra uma vez; piso e forro podem ter contornos diferentes |
| QV-07 | Legenda, implantação e detalhe com informação conflitante | Mostrar ambas as fontes pertinentes, registrar a hierarquia/adoção e seu efeito; não ocultar o conflito por referência genérica a arquivo/revisão |
| QV-08 | Reforma com concreto existente, sem novo escopo de fundação definido | Permitir exclusão orçamentária explícita, por exemplo preservar a estrutura existente e não executar novas fundações, com fundamento e limite; não inferir inexistência absoluta de elementos ocultos |
| QV-09 | Item fora do escopo ou não aplicável | Explicação curta e específica na linha, com fonte quando representável; não criar página individual vazia apenas para repetir uma mensagem padrão |
| QV-10 | Custos indiretos como equipe, administração ou instalações provisórias por duração | Registrar definição pela engenharia/planejamento quando esse for o critério do usuário; não inventar medição geométrica ou quantidade física |
| QV-11 | Base de alvenaria no mezanino e fechamento em drywall acima | Separar base e fechamento, alturas e vãos; identificar dependências de pintura, acabamento e portas sem criar portas novas a partir de simples manutenção |
| QV-12 | Dois rótulos de circuito representam dezesseis terminais físicos | Distinguir grupo/rótulo de ocorrência; a contagem demonstra os dezesseis terminais, sem somar os dois rótulos como mais dois equipamentos |
| QV-13 | Mesmo elemento em planta, corte, ampliação e legenda | Unificar a ocorrência física e usar as vistas como evidências complementares; amostra de legenda não vira unidade implantada |
| QV-14 | DWG sem cota escrita, mas com geometria mensurável | Validar unidade, plano e transformação; selecionar ou traçar contorno e medir por coordenadas ou comando realmente executado, registrando corretamente o método |
| QV-15 | DWG com unidades indefinidas ou PDF sem escala/referência confiável | Explicitar limite de calibração; não converter pixels/unidades gráficas em metros por suposição silenciosa. Altura ausente em desenho 2D não surge de uma medição horizontal |
| QV-16 | Rotas elétricas em dois pisos e terminais em alturas distintas | Usar datum por piso e comprimentos horizontais/verticais separados; prumada entre plantas não é diagonal em planta; tubo compartilhado é contado fisicamente uma vez |
| QV-17 | Circuitos com fases, neutro, PE e retornos | Manter papéis, seções e multiplicadores verificáveis. PE provisionado é adoção separada; envelope conservador de condutores é estimativa explícita, não leitura executiva de cada trecho |
| QV-18 | Cabo PP multipolar e cabo unipolar de mesma seção | Separar produto, unidade de fornecimento e pacote; não transformar um metro de cabo multipolar em vários metros do item unipolar nem duplicar reservas |
| QV-19 | Duas linhas do BID com descrição de cabo de 16 mm² | Exigir alocação disjunta e explicada, por exemplo circuito terminal e PE do alimentador; não copiar a mesma quantidade nas duas linhas |
| QV-20 | Material do projeto sem composição compatível no Excel | Medir em apoio e demonstrar a incompatibilidade; não recodificar aço como PVC, nem encaixar outra seção apenas para preencher uma linha |
| QV-21 | Trecho interno de alimentador conhecido, origem externa sem percurso/níveis | Exibir subtotal interno e parcela externa desconhecida; quantidade principal não é apresentada como total completo nem a parcela ausente como zero |
| QV-22 | Infraestrutura completa em pontos/verbas e os mesmos materiais detalhados em metros | Identificar sobreposição de composição para precificação; não somar grandezas ou pacotes como se representassem serviços físicos adicionais |
| QV-23 | Luminárias existentes preservadas e alimentação nova adotada | Separar fornecimento de equipamento, religação e nova alimentação; a premissa de execução não transforma todos os símbolos em equipamentos novos |
| QV-24 | Revisão de contorno, altura ou escopo depois de gerada a memória | Atualizar parcelas, resultado, estado, imagem, principal e relatório dependentes; evidência de versão anterior não pode acompanhar resultado novo |

Cada cenário deve ter entrada, resultado esperado, fundamento independente, verificações de evidência e limite conhecido. Uma divergência de número, sozinha, não prova qual fonte está errada.

## 5. Estratégia de testes do produto

### 5.1. Extração e importação

Testar os formatos efetivamente declarados como suportados em cada entrega. O primeiro fluxo pode aceitar apenas `.xlsx`; outros formatos devem ser recusados ou tratados com um limite explícito até sua implementação.

O conjunto inclui linhas de título, células mescladas, tabelas múltiplas na mesma aba, códigos como texto, unidades locais, números em convenções diferentes, abas ocultas, linhas vazias, fórmulas com cache ausente e IDs duplicados. Cabeçalho ambíguo deve produzir uma seleção ou correção observável pelo usuário, sem correspondência silenciosa.

Nos projetos, verificar blocos aninhados e dinâmicos, referências externas ausentes, fontes não disponíveis, objetos não suportados, camadas congeladas/desligadas, layouts, viewports e matrizes de transformação. Converter sem erro de processo não prova que todo o conteúdo tenha sido preservado.

### 5.2. Motor geométrico e quantitativo

Usar casos analíticos com dimensões conhecidas para retângulos, polígonos côncavos, regiões com furos, arcos, encontros de paredes e percursos em múltiplos níveis. Verificar invariantes como fechamento, orientação, área não negativa, unidade dimensional, deduplicação e continuidade declarada.

Comparar, quando aplicável, decomposição independente e medição nativa realmente executada. Proximidade numérica não valida seleção física errada. Contagem deve reconciliar IDs únicos, contagem por família e distribuição por ambiente.

Para cabos, testar separadamente comprimento instalado, quantidade de condutores, reservas de ligação, perda de compra e itens remunerados em pacote. Nenhuma taxa global de perda é padrão implícito do produto. Para tubos, unir trechos verticais compartilhados e demonstrar multiplicidades de vias paralelas.

### 5.3. Integração com IA e computer use

Testes com respostas simuladas da API verificam contratos, falhas e a interface. Eles não validam leitura real de projetos, escolha do contorno nem operação de um CAD verdadeiro.

O teste real de integração precisa executar a cadeia com arquivos controlados: a IA solicita uma operação, o executor autorizado a realiza, devolve a observação real e verifica o resultado. Sessão, arquivo, vista, unidade e objetos devem ser identificáveis. Um comando que falhou não pode ser registrado como medição concluída.

O executor deve suportar limites de tempo, passos e custo, cancelamento e recuperação de sessão. Alterações de foco, diálogos e falhas de conversão não podem fazer a IA operar outro arquivo ou atribuir um resultado antigo ao serviço atual. Cópias de conferência preservam os originais.

### 5.4. Evidências e interface de revisão

Verificar cada item físico, incluindo estimativas e parciais. A figura deve permitir reconhecer o alcance, localizar os IDs e distinguir fonte, inclusão, dedução, exclusão e trecho adotado. Cores precisam ter legenda; identificação não deve depender somente da cor.

Uma captura de área deve mostrar contorno e recortes; uma parede precisa de faces e limites de altura; uma contagem precisa localizar todas as ocorrências; um percurso precisa demonstrar todos os trechos e a tabela de verticais. Legenda isolada não demonstra implantação. Redimensionamento e compressão não podem tornar números, linhas ou IDs ilegíveis.

Na revisão, editar um contorno ou uma premissa deve mostrar os itens afetados e atualizar seus cálculos. Aprovar, rejeitar ou corrigir é um evento registrado; o original permanece recuperável. Resultado anterior ainda não recalculado deve ser identificado como desatualizado.

### 5.5. Fórmulas, exportação e preservação

Comparar o arquivo recebido com o exportado no alcance de preservação declarado: valores, fórmulas, vazios, estilos, mesclas, dimensões, visibilidade, nomes, links, objetos e recursos nativos. Quantidades e fórmulas originais não devem ser redirecionadas silenciosamente ao novo levantamento.

No Excel entregue, verificar vínculos entre principal e memórias, caches, referências, recálculo e ausência de erros inesperados. Respeitar os limites do formato/engine escolhido. Reabrir o pacote salvo e validar relações e imagens incorporadas; sucesso de exportação e um print do builder não comprovam comportamento nativo do Excel.

Um cenário significativo altera uma altura ou outro driver e confere a propagação até a principal. Outro remove uma entrada obrigatória e confirma a pendência. Um terceiro mantém uma parcela independente utilizável apesar da falta de outra. Todos os valores temporários devem ser restaurados antes da entrega.

Macros, fórmulas externas e links do arquivo de entrada não são autorização para execução. Macros não serão executadas pelo importador. Formatos com macros só podem ser declarados suportados quando a preservação tiver sido validada; caso contrário, a interface explica a limitação antes de alterar o arquivo. Arquivo exportado não deve ganhar conteúdo ativo não solicitado.

## 6. Base de avaliação e tolerâncias

Manter duas classes de dados:

- **Fixtures sintéticas públicas:** projetos e planilhas produzidos para teste, com direitos claros, oráculos independentes e casos de erro intencionais. Seus números não se tornam premissas de outra obra.
- **Obras reais privadas autorizadas:** arquivos em armazenamento restrito, com controle de acesso, retenção e consentimento de uso. O repositório guarda apenas a definição abstrata do teste e referências opacas; não recebe DWGs, PDFs, Excels, prints, nomes de cliente ou caminhos que revelem dados da obra.

Os casos reais usados no desenvolvimento devem permanecer separados dos casos reservados para avaliação independente. Reexecutar somente os exemplos que orientaram os prompts favorece uma impressão de qualidade que pode não se repetir em novas obras.

Tolerâncias devem ser propostas e aprovadas por família e método de obtenção: contagem, comprimento, área, volume, leitura de cota, extração de coordenadas ou medição gráfica calibrada. Devem registrar unidade, valor absoluto/relativo quando aplicável, precisão de entrada, motivo e responsável. Não fixar uma tolerância universal nem escolher um limite depois de observar o resultado para fazê-lo passar.

Medir separadamente omissões de elementos, duplicações, classificação de material/fase, erro quantitativo sobre escopos comparáveis, completude das evidências, retrabalho de revisão e tempo/custo de processamento. Resultados com base ausente ou subtotal não podem desaparecer do denominador de cobertura. Não anunciar uma precisão percentual sem amostra, protocolo e resultados verificáveis.

## 7. Segurança, privacidade e isolamento

Projetos e planilhas são entradas não confiáveis. Texto no desenho, OCR, legenda, célula, anotação ou resultado de ferramenta não pode instruir o agente a alterar escopo, executar comandos, transmitir arquivos, revelar segredos ou ignorar os controles do executor.

Os testes de isolamento devem tentar acessar arquivo, trabalho, imagem, URL de download e histórico de outro cliente. A autorização deve ser verificada no servidor e no armazenamento, inclusive em processadores assíncronos e ferramentas da IA. IDs difíceis de adivinhar não substituem autorização. Antes de aceitar usuários externos, essa separação precisa estar validada de ponta a ponta.

Conversores e CADs executam em ambientes isolados, com permissões mínimas, limites de recursos e diretórios por execução. Referências externas, arquivos compactados, caminhos relativos, nomes maliciosos e arquivos desproporcionalmente grandes devem ter tratamento controlado. Reiniciar um job não pode atravessar o isolamento ou executar novamente ações não idempotentes.

Logs operacionais registram identificadores e erros necessários à investigação, sem publicar o conteúdo integral do cliente, capturas, chaves de API ou URLs temporárias. Exclusão e retenção abrangem originais, derivados, evidências e cópias de sessão, conforme a política que o produto definir. Dados reais não entram em treinamento ou exemplos públicos por consequência implícita do upload.

## 8. Portas de liberação

Estas são condições de evolução do produto, sem prazos prometidos ou estados de execução. Cada porta deve corresponder a entregas e dependências no Beads.

| Porta | Evidência para avançar |
|---|---|
| Fluxo Excel local/interno | Upload, mapeamento, correção de colunas e lista de serviços funcionando com fixtures; original preservado e erros compreensíveis |
| Primeira família física | Cálculo e evidência de ponta a ponta, com unidade validada, parcela rastreável e correção pelo usuário |
| DWG sem cota anotada | Pipeline real lê ou opera o CAD, mede geometria controlada e produz evidência; perda de entidade ou escala desconhecida é detectada |
| Computer use integrado | VM/CAD real, ações/observações rastreáveis, limites, cancelamento, recuperação e verificação de resultado demonstrados |
| Elétrica | Casos de condutores, alturas, prumadas, PP, pacotes e subtotais externos aprovados separadamente; contagem de pontos não substitui metragem |
| Piloto externo restrito | Autorização por cliente, isolamento, proteção de arquivos, retenção, observabilidade e limite de gastos verificados antes de receber dados externos |
| Validação comercial por família | Obras autorizadas avaliadas por orçamentistas independentes, divergências investigadas e cobertura/limitações publicáveis documentadas |
| Ampliação de cobertura | Nova família/formato repete o processo de validação; desempenho de uma família não é apresentado como prova de todas as disciplinas |

## 9. Registro de uma falha e sua resolução

Uma falha encontrada em teste ou revisão gera um registro no Beads com cenário, escopo, evidência reproduzível autorizada, comportamento observado/esperado e dependências. A correção deve atualizar a regra ou implementação aplicável e acrescentar uma regressão que capture a causa. Não ajustar apenas o resultado final de uma obra por fora do pipeline.

No relatório técnico da obra, registrar o antes/depois, a fonte da resolução e os serviços afetados. Em caso de premissa aceita, preservar a distinção entre aceitação do cenário e confirmação da informação no projeto. Se uma pendência localizada continuar aberta, entregar as parcelas independentes com seu alcance correto.

## 10. Referências operacionais do projeto

- [Fluxo mestre](../procedimentos-orcamento/references/00_fluxo_mestre.md).
- [Planilha, escopo e mapeamento](../procedimentos-orcamento/references/02_planilha_escopo_e_mapeamento.md).
- [Dúvidas e continuidade](../procedimentos-orcamento/references/03_duvidas_e_continuidade.md).
- [Cálculo de áreas](../procedimentos-orcamento/references/04_calculo_de_areas.md).
- [Perímetros e alturas](../procedimentos-orcamento/references/05_perimetros_e_alturas.md).
- [Quantitativos, memórias e evidências por item](../procedimentos-orcamento/references/06_quantitativos_e_memorias.md).
- [Computer use e encerramento](../procedimentos-orcamento/references/07_computer_use_e_encerramento.md).
- [Conferência e entrega](../procedimentos-orcamento/references/08_conferencia_comparacao_e_entrega.md).

Os procedimentos são a base operacional a converter em contratos, verificações e experiências de revisão. A conformidade com o texto de um procedimento, sozinha, não comprova que sua implementação tenha sido validada.
