# Etapas do produto BuildCalc AI

Especificação de incrementos demonstráveis, a partir do [PRD](PRD.md). Cada incremento liga frontend, backend e uma conferência de resultado. Este documento define produto e critérios; a situação de execução, responsáveis, bloqueios e dependências operacionais são mantidos exclusivamente no Beads.

A ordem numérica é uma sequência recomendada de aprendizagem e demonstração. Dependências indicam pré-requisitos técnicos mínimos; não autorizam executar todos os incrementos sem o escopo solicitado pelo usuário. BC-01 a BC-05 são locais e fechados. Nenhum piloto externo é liberado antes de BC-06/07 e da validação independente da família/formato oferecidos.

Limites essenciais e telemetria entram em BC-08, antes das chamadas de IA/VM; BC-36 acrescenta gestão comercial. A primeira rota PDF pode avançar sem aguardar motor DWG. CAD e CUA possuem gates próprios; suporte de cada formato só é oferecido depois de demonstrado. Validação independente é incremental por família, e BC-35 consolida a avaliação entre obras.

O primeiro incremento permite enviar um XLSX e enxergar abas e linhas extraídas pelo backend; BC-02 confirma os campos e o escopo e BC-03 acrescenta obra persistente. IA, CAD e cálculo de projeto não são pré-requisitos dessa primeira entrega.

## Mapa de incrementos

| Etapa | Incremento demonstrável | Pré-requisitos |
|---|---|---|
| [BC-01](#bc-01) | Enviar Excel e visualizar os serviços extraídos | Nenhum |
| [BC-02](#bc-02) | Mapear colunas, cabeçalhos e abas do escopo | BC-01 |
| [BC-03](#bc-03) | Salvar a obra e retomar a importação | BC-02 |
| [BC-04](#bc-04) | Revisar os serviços normalizados e a cobertura | BC-03 |
| [BC-05](#bc-05) | Validar a preservação do Excel na exportação básica | BC-04 |
| [BC-06](#bc-06) | Entrar no sistema e isolar as obras por organização | BC-03, BC-05 |
| [BC-07](#bc-07) | Proteger uploads, arquivos e exclusão de dados | BC-06 |
| [BC-08](#bc-08) | Acompanhar processamento em segundo plano | BC-07 |
| [BC-09](#bc-09) | Enviar projetos e construir o catálogo de fontes | BC-08 |
| [BC-10](#bc-10) | Visualizar PDFs e detalhes na obra | BC-09 |
| [BC-11](#bc-11) | Extrair textos, vetores e leitura assistida de PDFs | BC-10 |
| [BC-12](#bc-12) | Conferir unidades, escalas e referências de nível | BC-11 |
| [BC-13](#bc-13) | Comparar motores DWG com arquivos de referência | BC-07, BC-09, BC-12 |
| [BC-14](#bc-14) | Catalogar e visualizar Model, layouts e camadas DWG | BC-13, BC-10 |
| [BC-15](#bc-15) | Medir geometria CAD sem depender de cotas escritas | BC-12, BC-14 |
| [BC-16](#bc-16) | Provar computer use de CAD numa VM pela API | BC-08, BC-12, BC-14 |
| [BC-17](#bc-17) | Conectar a IA às ferramentas do levantamento | BC-04, BC-08, BC-11, BC-12 |
| [BC-18](#bc-18) | Associar cada serviço às fontes e ao estado físico | BC-17, BC-09 |
| [BC-19](#bc-19) | Definir critérios, premissas e estados por obra | BC-18 |
| [BC-20](#bc-20) | Levantar áreas com contornos e deduções auditáveis | BC-12, BC-19 |
| [BC-21](#bc-21) | Levantar trechos, faces de parede, alturas e vãos | BC-19, BC-20 |
| [BC-22](#bc-22) | Contar ocorrências físicas e multiplicidades | BC-12, BC-18, BC-19 |
| [BC-23](#bc-23) | Aplicar quantitativos dos serviços civis | BC-20, BC-21, BC-22 |
| [BC-24](#bc-24) | Gerar prints reais marcados por parcela | BC-23, BC-10 |
| [BC-25](#bc-25) | Montar memórias e resolver pendências agrupadas | BC-19, BC-24 |
| [BC-26](#bc-26) | Entregar Excel com quantitativos e memórias vinculadas | BC-05, BC-25 |
| [BC-27](#bc-27) | Revisar e aprovar a entrega com histórico | BC-26, BC-06 |
| [BC-28](#bc-28) | Comparar revisões e recalcular o alcance alterado | BC-27, BC-09 |
| [BC-29](#bc-29) | Levantar quadros e pontos elétricos | BC-22, BC-25, BC-27 |
| [BC-30](#bc-30) | Medir infraestrutura elétrica física em planta | BC-29, BC-15, BC-21 |
| [BC-31](#bc-31) | Quantificar cabos por circuito, trecho e função | BC-30, BC-19 |
| [BC-32](#bc-32) | Completar trajetos elétricos com subidas e descidas | BC-12, BC-30, BC-31, BC-26 |
| [BC-33](#bc-33) | Conferir interfaces entre pacotes e materiais | BC-32, BC-25 |
| [BC-34](#bc-34) | Colaborar na revisão com permissões e conflitos controlados | BC-06, BC-27, BC-28 |
| [BC-35](#bc-35) | Validar o produto em obras conferidas independentemente | BC-28, BC-34 |
| [BC-36](#bc-36) | Controlar custos, cotas e operação dos processamentos | BC-08, BC-17, BC-35 |
| [BC-37](#bc-37) | Vender planos e projetos com cobrança integrada | BC-06, BC-36 |
| [BC-38](#bc-38) | Publicar e operar a versão comercial com critérios de liberação | BC-07, BC-35, BC-36, BC-37 |

## Importação utilizável

<a id="bc-01"></a>

### BC-01 — Enviar Excel e visualizar os serviços extraídos

Entregar o primeiro fluxo completo: escolher um XLSX, processá-lo no backend e mostrar suas linhas ao usuário.

**Frontend**

- Tela de upload com arquivo, progresso, erro e opção de tentar novamente.
- Tabela de abas e linhas/células extraídas, com valores e referências de origem. Colunas de serviço reconhecidas aparecem como sugestões; confirmações e mapeamento ajustável entram em BC-02.

**Backend**

- Endpoint de upload XLSX com limites de tamanho, extensão/conteúdo, expansão ZIP e tempo de leitura; arquivo original imutável.
- Extrator determinístico que enumera todas as abas, inclusive ocultas, e devolve células, fórmulas e valores em cache sem executar macros ou fórmulas recebidas.

**Aceite demonstrável**

1. Um XLSX sintético com duas abas produz no navegador as abas e linhas/células corretas, usando a resposta real do backend; colunas não reconhecidas não causam omissão silenciosa.
2. Um arquivo inválido ou acima do limite configurado mostra erro útil e não deixa importação marcada como concluída.
3. Fórmula e valor em cache permanecem distinguíveis; nenhum campo vazio vira quantidade zero.

**Pré-requisitos:** nenhum

**Limites do incremento:** Execução local de desenvolvimento; somente XLSX, sem IA, PDF, DWG ou cálculo de quantitativo.

**Risco a verificar:** Planilhas sem padrão podem exigir mapeamento manual; a primeira entrega não promete classificação perfeita de qualquer Excel.

<a id="bc-02"></a>

### BC-02 — Mapear colunas, cabeçalhos e abas do escopo

Permitir que o usuário confirme onde estão os serviços e quais listas devem ser trabalhadas.

**Frontend**

- Prévia com escolha de aba, linha de cabeçalho e colunas de código, descrição, unidade e quantidade original.
- Seleção de abas e linhas elegíveis; indicação de abas ocultas, títulos e resumos.

**Backend**

- Salvar o mapeamento na importação temporária do backend e manter coordenadas de origem; associação definitiva à obra e persistência após reinício entram em BC-03.
- Sinalizar relações prováveis entre resumo e detalhe sem somá-los automaticamente; separar linha de serviço, agrupador e total.

**Aceite demonstrável**

1. Uma planilha com títulos antes da tabela e células mescladas pode ser mapeada pela interface.
2. A seleção de uma única aba exclui as demais do processamento e continua visível após recarregar o navegador; a retomada após reinício do backend é requisito da BC-03.
3. O usuário consegue corrigir uma sugestão de mapeamento sem reenviar o arquivo.

**Pré-requisitos:** BC-01

**Limites do incremento:** O usuário confirma o escopo; a quantidade recebida continua como referência preservada.

**Risco a verificar:** Cabeçalhos semelhantes e abas de compras podem representar alcances diferentes.

<a id="bc-03"></a>

### BC-03 — Salvar a obra e retomar a importação

Transformar o upload em uma obra persistente, com identificação estável dos arquivos e serviços.

**Frontend**

- Tela de obras com criar, abrir e retomar uma importação.
- Identificação do arquivo, versão e data de envio dentro da obra.

**Backend**

- Persistir obra, arquivo, importação, mapeamento e linhas com IDs próprios e vínculos à origem.
- Guardar hash, tamanho e tipo do arquivo; distinguir reenvio idêntico de nova versão sem sobrescrever a origem.

**Aceite demonstrável**

1. Reiniciar frontend e backend preserva a obra e seu mapeamento.
2. O reenvio do mesmo arquivo não cria silenciosamente duas listas equivalentes.
3. Uma versão alterada mantém acessível a importação anterior e suas referências.

**Pré-requisitos:** BC-02

**Limites do incremento:** Uso local fechado; o vínculo definitivo a usuário e organização entra antes de exposição externa.

**Risco a verificar:** Nome de arquivo não identifica uma revisão; caminhos locais do cliente não podem virar identidade do dado.

<a id="bc-04"></a>

### BC-04 — Revisar os serviços normalizados e a cobertura

Oferecer uma lista de serviços compreensível sem perder a descrição e a estrutura recebidas.

**Frontend**

- Tabela com filtros por grupo, unidade e seleção; visualização lado a lado do texto original e da classificação preparada.
- Alertas explicados para código repetido, unidade desconhecida e composição por verba, duração ou ponto.

**Backend**

- Normalizar unidades e hierarquia em campos separados, conservando texto e quantidades originais.
- Gerar uma conferência de cobertura: cada linha elegível tem ID e origem, e cada exclusão tem motivo.

**Aceite demonstrável**

1. Dois serviços com o mesmo código em abas diferentes conservam IDs distintos.
2. Um item em verba não é convertido em metros por inferência.
3. O usuário pode alterar a classificação sem alterar a célula original recebida.

**Pré-requisitos:** BC-03

**Limites do incremento:** Não calcular quantidades a partir dos números já fornecidos no orçamento.

**Risco a verificar:** Normalização de texto não comprova equivalência técnica entre composições.

<a id="bc-05"></a>

### BC-05 — Validar a preservação do Excel na exportação básica

Provar cedo que o produto consegue devolver uma cópia do Excel sem danificar sua estrutura.

**Frontend**

- Ação para baixar cópia de conferência com o mapa de serviços e o escopo selecionado.
- Mensagem clara para recursos do arquivo que ainda não têm suporte de exportação.

**Backend**

- Implementar um adaptador de leitura/escrita que preserve células originais, fórmulas, vazios, abas ocultas e recursos suportados.
- Comparar a cópia salva com a origem e declarar os limites de compatibilidade; não executar macros.

**Aceite demonstrável**

1. Os arquivos de teste preservam as quantidades recebidas, inclusive fórmulas e campos vazios.
2. Abas fora do escopo permanecem intactas na conferência estrutural.
3. Um recurso não suportado bloqueia a exportação correspondente ou é explicitamente tratado, sem perda silenciosa.

**Pré-requisitos:** BC-04

**Limites do incremento:** Exportação estrutural; memórias de cálculo e quantitativos próprios serão adicionados depois.

**Risco a verificar:** Uma biblioteca conseguir abrir XLSX não significa que preserve todos os recursos nativos.

## Fundação do serviço

<a id="bc-06"></a>

### BC-06 — Entrar no sistema e isolar as obras por organização

Permitir uso autenticado com isolamento real dos dados antes de qualquer piloto externo.

**Frontend**

- Login, logout, sessão expirada e escolha da organização autorizada.
- Listagem de obras limitada à organização ativa e estados de acesso negado.

**Backend**

- Associar obras, arquivos e importações a organização e usuário, com autorização verificada em todo endpoint.
- Definir papéis iniciais de proprietário, colaborador e leitor; migrar dados locais de desenvolvimento de forma explícita.

**Aceite demonstrável**

1. Um usuário não acessa obra ou arquivo de outra organização alterando um ID na requisição.
2. Revogação de acesso e expiração de sessão impedem operações protegidas no servidor.
3. As mesmas restrições funcionam em download e consulta, além da listagem.

**Pré-requisitos:** BC-03, BC-05

**Limites do incremento:** Pré-requisito para disponibilizar o serviço fora do ambiente local controlado.

**Risco a verificar:** Filtrar apenas a interface não oferece isolamento dos dados.

<a id="bc-07"></a>

### BC-07 — Proteger uploads, arquivos e exclusão de dados

Tornar o armazenamento e o processamento de arquivos adequados ao uso por clientes distintos.

**Frontend**

- Lista de arquivos com permissões, situação de validação e ação de exclusão com alcance informado.
- Erros legíveis para arquivo corrompido, protegido por senha ou não suportado.

**Backend**

- Armazenamento privado, autorização de download, nomes internos seguros e processamento isolado com limites de recursos.
- Definir retenção, limpeza de temporários e exclusão dos derivados; impedir leitura de caminhos arbitrários e execução de conteúdo recebido.

**Aceite demonstrável**

1. Nomes de arquivo com caminhos e arquivos compactados maliciosos não escapam da área da obra.
2. Documentos de organizações diferentes não compartilham caminhos ou permissões de acesso.
3. A exclusão autorizada cobre originais e derivados conforme a política exibida e registra o resultado.

**Pré-requisitos:** BC-06

**Limites do incremento:** Regras básicas de tamanho e conteúdo já existem desde BC-01; esta etapa amplia a proteção para o serviço.

**Risco a verificar:** Desenhos e planilhas devem ser tratados como dados não confiáveis, inclusive textos que pareçam instruções para a IA.

<a id="bc-08"></a>

### BC-08 — Acompanhar processamento em segundo plano

Executar trabalhos longos sem prender a navegação ou perder o estado de uma execução.

**Frontend**

- Painel com etapa atual, itens concluídos, erro específico, cancelamento e retomada permitida.
- Atualização do andamento após fechar e reabrir a página.

**Backend**

- Fila de trabalhos com IDs de execução, transições de estado, limites de tempo e checkpoints.
- Repetição idempotente de etapas, registro de falhas e cancelamento cooperativo; separar estado da obra e estado da execução.
- Registrar telemetria por execução e aplicar limites essenciais de etapas, tentativas, tempo e orçamento configurado antes de habilitar chamadas externas ou sessões CAD/IA.

**Aceite demonstrável**

1. Fechar a aba não interrompe o trabalho e a reabertura mostra seu estado real.
2. Repetir uma tarefa interrompida não duplica serviços, arquivos ou memórias.
3. Cancelar impede novas operações da execução e preserva resultados parciais identificados.
4. Ao atingir o limite de tentativas, tempo ou orçamento, a execução impede novas operações e registra motivo e consumo; BC-36 acrescenta gestão comercial sobre essa proteção já existente.

**Pré-requisitos:** BC-07

**Limites do incremento:** Andamento mensurável por etapas; não inventar porcentagens baseadas apenas em tempo decorrido.

**Risco a verificar:** Uma resposta HTTP bem-sucedida não comprova que o levantamento terminou.

## Projetos e geometria

<a id="bc-09"></a>

### BC-09 — Enviar projetos e construir o catálogo de fontes

Organizar documentos de projeto por disciplina, pavimento, revisão e representação examinada.

**Frontend**

- Upload de projetos vinculado à obra, com lista de documentos e campos revisáveis de disciplina e revisão.
- Catálogo que distingue arquivo recebido, processado, examinado e fonte com informação ainda não localizada.

**Backend**

- Registrar fontes e representações com IDs estáveis, hash e relações entre revisões.
- Separar inventário de arquivos de cobertura efetivamente examinada; registrar referências externas ausentes.

**Aceite demonstrável**

1. Duas revisões de mesmo nome não se sobrescrevem.
2. O catálogo mostra que uma planta foi examinada sem afirmar que todo o documento foi revisado.
3. Uma referência externa ausente aparece como limitação localizada, sem ser tratada como desenho vazio.

**Pré-requisitos:** BC-08

**Limites do incremento:** Nenhum arquivo ou revisão é escolhido silenciosamente como verdade definitiva.

**Risco a verificar:** Um único DWG ou PDF pode conter várias plantas, vistas e alcances diferentes.

<a id="bc-10"></a>

### BC-10 — Visualizar PDFs e detalhes na obra

Oferecer uma base visual de projeto com navegação e coordenadas consistentes para marcações futuras.

**Frontend**

- Visualizador de PDF com páginas, zoom, rotação, recortes e indicação da fonte/revisão.
- Seleção de região com retorno previsível à mesma posição após recarregar.

**Backend**

- Renderizar páginas e recortes sem alterar os originais, mantendo transformações entre coordenadas do PDF e pixels.
- Gerar derivados em resolução adequada para textos pequenos e desenhos extensos, com cache identificado por versão.

**Aceite demonstrável**

1. Uma marca de teste retorna ao mesmo ponto após zoom e rotação.
2. A imagem ampliada continua vinculada à página e revisão corretas.
3. Um PDF grande é navegado sem exigir envio de todas as páginas em alta resolução para a IA.

**Pré-requisitos:** BC-09

**Limites do incremento:** Visualização e localização; escala física e medição entram em etapa própria.

**Risco a verificar:** Uma imagem legível visualmente pode ter transformação incorreta de coordenadas.

<a id="bc-11"></a>

### BC-11 — Extrair textos, vetores e leitura assistida de PDFs

Converter o conteúdo dos PDFs em fontes pesquisáveis sem perder sua posição e método de obtenção.

**Frontend**

- Pesquisa de texto com destaque no projeto e indicação de texto nativo ou OCR.
- Inspeção de legendas, cotas e entidades extraídas com acesso ao recorte correspondente.

**Backend**

- Extrair texto e geometria vetorial quando disponíveis; aplicar OCR apenas ao conteúdo rasterizado necessário.
- Conservar página, região, método, unidade textual e limitações; detectar arquivos escaneados ou texto ilegível.

**Aceite demonstrável**

1. Uma legenda encontrada pela pesquisa abre seu recorte real.
2. O sistema distingue vetor nativo, OCR e interpretação visual.
3. Um erro de OCR em uma cota pode ser corrigido e não se torna automaticamente uma dimensão aprovada.

**Pré-requisitos:** BC-10

**Limites do incremento:** Extração não é aprovação de escopo nem confirmação automática de uma medida.

**Risco a verificar:** PDFs rasterizados, símbolos e textos sobrepostos precisam de tratamento e revisão distintos.

<a id="bc-12"></a>

### BC-12 — Conferir unidades, escalas e referências de nível

Estabelecer uma referência física verificável antes de converter desenho em metros, áreas ou alturas.

**Frontend**

- Ferramenta para confirmar unidade e calibrar uma vista por referência conhecida, mostrando a fonte usada.
- Quadro de níveis e alturas por pavimento com valores indicados, calculados e adotados distinguíveis.

**Backend**

- Persistir transformações de escala por representação e datum por pavimento, com dependências dos quantitativos.
- Rejeitar medição física sem referência suficiente; suportar múltiplas escalas no mesmo documento.

**Aceite demonstrável**

1. Uma distância conhecida confere após a calibração e fica documentada.
2. Duas vistas com escalas diferentes não compartilham automaticamente o mesmo fator.
3. Remover a referência obrigatória deixa os resultados dependentes pendentes, sem produzir zero ou dimensão adivinhada.

**Pré-requisitos:** BC-11

**Limites do incremento:** Ausência de cota escrita difere de ausência de informação física: geometria pode ser medida quando unidade e escala estão comprovadas.

**Risco a verificar:** A unidade declarada no arquivo pode não corresponder à forma como o desenho foi produzido.

<a id="bc-13"></a>

### BC-13 — Comparar motores DWG com arquivos de referência

Escolher um caminho CAD demonstrado, avaliando bibliotecas abertas e uma alternativa de compatibilidade.

**Frontend**

- Tela técnica de comparação com entidades, textos, camadas e vistas retornados por cada candidato.
- Exibição de falhas e diferenças de extração sobre uma amostra conhecida.

**Backend**

- Executar os candidatos em processos isolados e registrar cobertura de versões, blocos dinâmicos/aninhados, layouts, fontes e referências externas.
- Avaliar separadamente extração, renderização, medições e condições de licença; documentar o motor escolhido e o fallback admitido.

**Aceite demonstrável**

1. Os candidatos são comparados com os mesmos DWGs e resultados conferidos independentemente.
2. Entidades puladas ou alteradas aparecem no diagnóstico, em vez de serem consideradas inexistentes.
3. A escolha registra evidência técnica e condições de uso verificadas, sem alegar compatibilidade universal.

**Pré-requisitos:** BC-07, BC-09, BC-12

**Limites do incremento:** Experimento com decisão técnica; não instalar dependências comerciais ou aceitar contratos sem a autorização aplicável.

**Risco a verificar:** Conseguir abrir um DWG não comprova preservar os detalhes necessários ao quantitativo.

<a id="bc-14"></a>

### BC-14 — Catalogar e visualizar Model, layouts e camadas DWG

Integrar o motor CAD aprovado ao catálogo e ao visualizador da obra.

**Frontend**

- Seleção de Model, layouts, vistas e combinações de camadas relevantes.
- Visualização com IDs de entidades e indicação das dependências ausentes ou não suportadas.

**Backend**

- Extrair entidades e transformações de blocos, além das relações entre representações.
- Produzir imagens/vetores de conferência vinculados à fonte e registrar quais representações foram examinadas.

**Aceite demonstrável**

1. Um DWG com várias plantas não se limita à vista inicial.
2. Um bloco inserido com transformação é localizado corretamente na imagem e nas coordenadas.
3. A mesma peça repetida em outra representação pode ser relacionada sem gerar uma nova ocorrência física automática.

**Pré-requisitos:** BC-13, BC-10

**Limites do incremento:** Visualizador e extração com suporte declarado; não construir um editor CAD completo nesta etapa.

**Risco a verificar:** Camadas visíveis e variantes de blocos podem diferir entre representações do mesmo arquivo.

<a id="bc-15"></a>

### BC-15 — Medir geometria CAD sem depender de cotas escritas

Demonstrar que uma área ou comprimento pode ser obtido da geometria verdadeira do desenho.

**Frontend**

- Seleção de entidades e edição de contorno de conferência com inclusão de vazios.
- Painel com unidade, fechamento, comprimento, área, origem da medida e fonte.

**Backend**

- Calcular por coordenadas e propriedades geométricas suportadas, auditando fechamento, arcos, transformações e duplicações.
- Guardar cópias/derivados de conferência; diferenciar propriedade extraída, cálculo geométrico e comando CAD nativo realmente executado.

**Aceite demonstrável**

1. Um desenho de referência sem anotação de cota produz a área esperada após validar sua unidade.
2. Um contorno aberto ou inválido exige correção explícita antes de gerar área aprovada.
3. Uma seleção retorna a mesma geometria na tabela e no print, sem linhas ligando pontos indevidos.

**Pré-requisitos:** BC-12, BC-14

**Limites do incremento:** Medir a geometria não decide sozinho se ela representa o serviço a executar.

**Risco a verificar:** A precisão numérica de um polígono incorreto não o torna uma medição correta.

<a id="bc-16"></a>

### BC-16 — Provar computer use de CAD numa VM pela API

Testar a operação visual de CAD como ferramenta complementar do agente, inclusive para medições nativas.

**Frontend**

- Painel de execução assistida com capturas, ações observáveis, resultado e cancelamento.
- Exibição da medição junto ao contorno e ao comando usado quando a operação nativa for comprovada.

**Backend**

- Manter uma sessão desktop isolada por trabalho, receber ações do modelo e devolver observações/capturas reais pela API.
- Limitar ações, tempo e acesso; validar aplicativo, sistema operacional, licença e capacidade de seleção/medição/exportação antes de habilitar o recurso.

**Aceite demonstrável**

1. Um teste abre uma cópia identificada do DWG, mede uma geometria conhecida e comprova comando, resultado, unidade e contorno.
2. A sessão pode ser cancelada e encerrada sem atingir documentos de outra execução.
3. Falha de interface é registrada; cálculo alternativo não recebe falsamente o rótulo de medição nativa.
4. Limites e cancelamento de BC-08 são exercitados numa sessão real; uma parada por orçamento ou tempo impede novas ações, preservando a evidência do que já ocorreu.

**Pré-requisitos:** BC-08, BC-12, BC-14

**Limites do incremento:** Experimento com ativação controlada; não é requisito usar cliques para toda medição nem presumir que instalar CAD Reader cria uma API.

**Risco a verificar:** Diálogos, foco, versões de aplicativo e sessões desktop afetam a repetibilidade.

## Interpretação e critérios

<a id="bc-17"></a>

### BC-17 — Conectar a IA às ferramentas do levantamento

Executar um ciclo real de API no qual a IA solicita operações e o backend devolve resultados verificáveis.

**Frontend**

- Execução por serviço mostrando etapa, fontes consultadas e operações realizadas.
- Erros explicados e indicação de resultado ainda não aprovado.

**Backend**

- Adaptador de modelo com contratos estruturados para consultar serviços, fontes, geometria e evidências; versionar instruções e esquemas.
- Aplicar permissões no executor, validar argumentos/resultados e registrar consumo; textos de arquivos não autorizam ferramentas ou mudança de escopo.

**Aceite demonstrável**

1. Um serviço sintético percorre consulta, ferramenta e resposta do modelo usando a API real em teste autorizado.
2. Uma chamada com entidade ou arquivo de outra obra é recusada pelo backend.
3. Um resultado sem fonte ou com saída inválida não entra como quantidade confirmada.
4. Limites de chamadas, tempo e orçamento são aplicados antes da primeira requisição de IA e a interface distingue saída proposta de quantidade tecnicamente confirmada.

**Pré-requisitos:** BC-04, BC-08, BC-11, BC-12

**Limites do incremento:** A API fornece interpretação; medidas e cálculos têm ferramentas próprias. O recurso CUA pode ser integrado se aprovado em BC-16. A primeira demonstração pode usar PDF já validado. Ferramentas CAD e CUA só são habilitadas após seus próprios gates BC-13–16; a escolha de motor DWG não bloqueia o primeiro fluxo PDF.

**Risco a verificar:** Resposta estruturada válida não comprova correção técnica do conteúdo.

<a id="bc-18"></a>

### BC-18 — Associar cada serviço às fontes e ao estado físico

Localizar os elementos candidatos e declarar o alcance do serviço antes da conta.

**Frontend**

- Serviço com descrição original, fontes candidatas, pavimento, ambiente e destaque no projeto.
- Revisão da classificação existente, novo, demolir, preservar, reaproveitar e fora do escopo.

**Backend**

- Persistir vínculos serviço-fonte-elemento, critérios, exclusões e representações equivalentes.
- Detectar descrição genérica, conflito e informação não localizada, com dependências compartilhadas entre serviços.

**Aceite demonstrável**

1. Um serviço pode apontar para planta, corte e legenda sem contar o elemento três vezes.
2. Preservar uma estrutura existente gera uma exclusão clara do novo serviço correspondente, sem zero desconhecido.
3. O usuário consegue revisar a associação proposta antes de aceitar a quantidade.

**Pré-requisitos:** BC-17, BC-09

**Limites do incremento:** Conferir a fase da reforma e a relação contratual; não inferir obra nova apenas pela existência de um desenho.

**Risco a verificar:** Um nome de camada ou uma cor isolada não define o serviço a executar.

<a id="bc-19"></a>

### BC-19 — Definir critérios, premissas e estados por obra

Resolver escolhas rotineiras de orçamento de forma visível e conservar as lacunas realmente bloqueantes.

**Frontend**

- Painel de premissas com valor, unidade, origem, justificativa e serviços afetados.
- Estados explicado de confirmado, estimado, parcial, pendente e não aplicável.

**Backend**

- Regras versionadas por família de serviço e configuração específica da obra; distinguir dado indicado, calculado e adotado.
- Propagar dependências e impedir que um dado ausente seja interpretado como zero; agrupar dúvidas pela informação necessária.

**Aceite demonstrável**

1. Alterar uma altura adotada atualiza todos os serviços dependentes identificados.
2. Remover uma informação obrigatória torna o resultado dependente pendente e mantém os itens independentes válidos.
3. Uma premissa de uma obra não é automaticamente aplicada a outra; uma exclusão possui motivo específico.

**Pré-requisitos:** BC-18

**Limites do incremento:** Valores usuais, como rodapé de 10 cm, só entram como adoção explícita e justificada, nunca como dimensão encontrada no projeto.

**Risco a verificar:** Uma lista extensa de pendências genéricas pode ocultar uma única lacuna comum ou uma decisão já resolvível.

## Levantamento civil

<a id="bc-20"></a>

### BC-20 — Levantar áreas com contornos e deduções auditáveis

Medir superfícies elegíveis para cada serviço com geometria demonstrável.

**Frontend**

- Contornos e vazios editáveis com IDs, pavimentos, inclusões e deduções visíveis.
- Tabela de parcelas que destaca a região correspondente ao selecionar uma linha.

**Backend**

- Validar geometrias, calcular áreas e tratar união/interseção apenas segundo o critério do serviço.
- Conferir corredores, mezaninos, soleiras, profundidade de vãos e representações repetidas; manter precisão interna sem ajustar a etiquetas.

**Aceite demonstrável**

1. Os casos de corredor e mezanino possuem parcelas e marcações correspondentes.
2. Adicionar ou retirar um vazio altera a área na memória e nos dependentes.
3. Contorno inválido, auto-interseção ou sobreposição indevida é detectado antes de confirmar o resultado.

**Pré-requisitos:** BC-12, BC-19

**Limites do incremento:** Área indicada, cálculo por cotas, coordenadas e comando nativo continuam com origens distintas. A rota PDF calibrada pode ser entregue primeiro. Medição de DWG exige BC-15; uma medição nativa por interface exige BC-16. Não declarar suporte a uma rota não validada.

**Risco a verificar:** Uma área de piso não é automaticamente a área de pintura de forro ou de impermeabilização.

<a id="bc-21"></a>

### BC-21 — Levantar trechos, faces de parede, alturas e vãos

Medir comprimentos e superfícies verticais respeitando as faces e interrupções do serviço.

**Frontend**

- Seleção de faces e trechos com altura, pavimento, lado, vãos e retornos identificados.
- Inspeção da fonte das alturas em planta, corte, detalhe ou premissa.

**Backend**

- Separar comprimento de eixo, comprimento livre, perímetro e faces físicas; calcular alturas e áreas desenvolvidas.
- Tratar base de alvenaria, drywall acima da base, encontros, vergas e vãos conforme o alcance de cada item.

**Aceite demonstrável**

1. Uma parede com base e drywall gera parcelas diferentes, com alturas e unidades próprias.
2. Um vão pode afetar área e comprimento de formas distintas conforme o critério registrado.
3. Revisar a altura recalcula somente as parcelas e serviços dependentes.

**Pré-requisitos:** BC-19, BC-20

**Limites do incremento:** Não converter perímetro geométrico automaticamente em rodapé, pintura ou alvenaria.

**Risco a verificar:** Confundir eixo, face e comprimento livre altera quantitativos mesmo quando o contorno geral parece correto.

<a id="bc-22"></a>

### BC-22 — Contar ocorrências físicas e multiplicidades

Distinguir elemento físico, rótulo de grupo e quantidade de terminações.

**Frontend**

- Marcas com IDs de portas, peças e pontos; tabela de ocorrências e multiplicidades.
- Visualização das duplicatas descartadas e da justificativa de agrupamento.

**Backend**

- Expandir blocos aninhados e variantes suportadas, deduplicar representações e registrar o critério da ocorrência.
- Separar local físico, unidade do item e multiplicidade, sem usar a quantidade original como alvo.

**Aceite demonstrável**

1. Um grupo de 16 spots com dois rótulos resulta 16 terminais físicos quando esse é o critério adotado, sem somar 18.
2. Uma estação de dois conectores mantém um local físico e duas terminações distinguíveis.
3. Toda ocorrência contada pode ser localizada na evidência e na fonte.

**Pré-requisitos:** BC-12, BC-18, BC-19

**Limites do incremento:** Casos de teste públicos ou sintéticos reproduzem as armadilhas; arquivos de cliente não são publicados como fixtures. Contagens podem começar em PDF validado; contagens por entidades DWG dependem também de BC-14 para essa rota.

**Risco a verificar:** Contagem visual aproximada não deve receber o mesmo estado de uma contagem auditada por ocorrências.

<a id="bc-23"></a>

### BC-23 — Aplicar quantitativos dos serviços civis

Converter bases de área, face, comprimento e contagem em quantitativos por serviço da planilha.

**Frontend**

- Resultado por item com parcelas, inclusões, exclusões e estado da revisão.
- Revisão de demolição, construção, piso, forro, pintura, alvenaria e drywall no alcance habilitado.

**Backend**

- Aplicar regras de composição e unidade, mantendo geometria, ajustes de execução e perdas de compra em parcelas separadas.
- Identificar custos indiretos/duração e itens não aplicáveis com observação específica, sem criar memórias vazias desnecessárias.

**Aceite demonstrável**

1. A quantidade levantada é independente da quantidade recebida e possui fórmula reproduzível.
2. Demolir e construir o mesmo ambiente não reutilizam automaticamente o mesmo alcance.
3. Uma perda adotada pode ser alterada sem modificar a geometria de origem.

**Pré-requisitos:** BC-20, BC-21, BC-22

**Limites do incremento:** MVP quantitativo de reforma comercial; aço, fundações e disciplinas não habilitadas exigem módulo e fontes próprios.

**Risco a verificar:** Serviços parecidos podem ter critérios e faces diferentes; composição genérica não determina execução existente.

## Auditoria e entrega

<a id="bc-24"></a>

### BC-24 — Gerar prints reais marcados por parcela

Demonstrar visualmente tudo o que entrou na memória de cada serviço físico.

**Frontend**

- Galeria por serviço com marcações, legenda, IDs das parcelas e vínculo de ida e volta à tabela.
- Inspeção de exclusões e conflitos com o recorte da fonte pertinente.

**Backend**

- Renderizar a fonte real e aplicar sobreposição vetorial a partir das mesmas geometrias da conta.
- Conferir cobertura de IDs, resolução, transformações e revisão; produzir vistas complementares quando uma só imagem não cobre todas as parcelas.

**Aceite demonstrável**

1. Toda parcela física elegível possui evidência correspondente e nenhum ID fica sem imagem.
2. O print não liga trechos independentes nem inclui regiões que não participaram do cálculo.
3. Cotas, textos e legendas necessários à decisão são legíveis; nome ou hash do arquivo não substitui a explicação.

**Pré-requisitos:** BC-23, BC-10

**Limites do incremento:** As imagens são fontes reais marcadas, nunca uma planta fictícia gerada para parecer evidência. A primeira biblioteca de evidências pode usar fontes PDF. Evidências CAD dependem da representação validada em BC-14/15 e, quando nativas, BC-16.

**Risco a verificar:** Um print bonito pode continuar tecnicamente errado se usar geometria diferente da fórmula.

<a id="bc-25"></a>

### BC-25 — Montar memórias e resolver pendências agrupadas

Oferecer a memória completa de cada serviço e uma lista curta de informações realmente necessárias.

**Frontend**

- Memória com descrição, unidade, parcelas, equações, resultado, imagens, premissas e limitações.
- Painel de pendências com fonte, motivo claro, efeito, serviços dependentes e campo de resolução.

**Backend**

- Versionar memória, bases e critérios; guardar dependências, autor da decisão e cobertura das evidências.
- Agrupar causas comuns, propagar resoluções e conservar subtotais conhecidos sem apresentá-los como total completo.

**Aceite demonstrável**

1. Resolver uma altura comum atualiza os itens afetados sem manter múltiplas dúvidas equivalentes.
2. Uma informação externa ausente conserva o subtotal interno e mostra claramente o trecho que falta.
3. Um item excluído possui motivo próprio e usa memória compartilhada quando uma página individual não acrescenta evidência.

**Pré-requisitos:** BC-19, BC-24

**Limites do incremento:** Separar erro de extração, conflito de projeto, decisão de escopo, premissa resolvida e informação externa necessária.

**Risco a verificar:** Usar um texto padrão em todos os itens impede compreender o motivo real da pendência ou exclusão.

<a id="bc-26"></a>

### BC-26 — Entregar Excel com quantitativos e memórias vinculadas

Gerar o arquivo auditável que o cliente consegue revisar fora da plataforma.

**Frontend**

- Prévia de entrega, resumo de cobertura e download da versão identificada.
- Avisos específicos de pendências e compatibilidade do arquivo exportado.

**Backend**

- Adicionar QTD LEVANTADA, estado, memória e considerado/pendência, com fórmulas e imagens vinculadas às bases.
- Recalcular, salvar, reabrir e comparar recursos preservados; testar propagação de premissas e erro de informação ausente.

**Aceite demonstrável**

1. As colunas de quantidade recebida e abas fora do escopo permanecem preservadas.
2. Alterar um driver suportado recalcula resultados e retirar um obrigatório gera pendência, sem zero saudável.
3. O arquivo salvo reabre com fórmulas, links e imagens válidos, dentro dos limites do formato; ausência de teste em Excel nativo é declarada quando aplicável.

**Pré-requisitos:** BC-05, BC-25

**Limites do incremento:** Nenhum subtotal desconhecido é exportado como quantidade completa; recursos não suportados seguem o contrato de compatibilidade.

**Risco a verificar:** Exportação bem-sucedida por uma biblioteca não prova recálculo ou comportamento nativo em todas as versões do Excel.

<a id="bc-27"></a>

### BC-27 — Revisar e aprovar a entrega com histórico

Permitir correções técnicas controladas e distinguir proposta automatizada de revisão humana.

**Frontend**

- Revisão lado a lado de serviço, projeto e memória; ajuste de contornos, vínculos e premissas com justificativa.
- Aprovação por item ou conjunto com identificação do revisor e alcance.

**Backend**

- Guardar versões e eventos de revisão, invalidar resultados afetados e preservar decisões anteriores.
- Aplicar permissões de revisão e impedir aprovação de resultado obsoleto ou bloqueado por informação necessária.

**Aceite demonstrável**

1. Corrigir uma geometria preserva o antes/depois e atualiza os dependentes.
2. A aprovação informa quem revisou, o que foi revisado e qual versão foi entregue.
3. Uma entrega anterior permanece reproduzível e não muda quando a revisão atual recebe uma correção.
4. Antes de um piloto externo, a família e o formato oferecidos têm referência conferida por orçamentista independente, tolerâncias registradas e casos de falha/limite; não esperar a validação ampla de BC-35.

**Pré-requisitos:** BC-26, BC-06

**Limites do incremento:** Aprovação de orçamento não equivale a certificação de projeto executivo ou redimensionamento técnico.

**Risco a verificar:** Uma revisão apenas de formato não deve aparecer como validação técnica de todos os quantitativos.

<a id="bc-28"></a>

### BC-28 — Comparar revisões e recalcular o alcance alterado

Reutilizar medições válidas e mostrar os efeitos de novas versões dos projetos.

**Frontend**

- Comparação de revisões com itens e geometrias adicionados, retirados ou alterados.
- Destaque de resultados desatualizados e escolha explícita da revisão ativa.

**Backend**

- Relacionar entidades/fontes entre versões e identificar dependências atingidas, com fallback para nova análise quando a correspondência não é segura.
- Recalcular apenas o alcance demonstrado e conservar a linhagem das entregas anteriores.

**Aceite demonstrável**

1. Uma mudança em um ambiente marca seus dependentes sem alterar silenciosamente os demais.
2. Uma entidade sem correspondência confiável exige nova conferência e não herda a aprovação antiga.
3. Diferenças são comparadas em unidades e alcances compatíveis, sem comparar resumos com detalhes duplicados.

**Pré-requisitos:** BC-27, BC-09

**Limites do incremento:** Reaproveitamento depende de cobertura e correspondência verificadas; hash de arquivo diferente não determina sozinho a mudança técnica.

**Risco a verificar:** Revisões podem manter aparência semelhante e alterar uma nota que afeta muitos serviços.

## Módulo elétrico

<a id="bc-29"></a>

### BC-29 — Levantar quadros e pontos elétricos

Adicionar contagem elétrica por função, fase e elemento físico, respeitando o escopo selecionado.

**Frontend**

- Camadas de quadros, luminárias, tomadas e comunicação com IDs e distinção de existente/novo/religação.
- Tabela de quantidades por painel físico, local, terminal e conector conforme a unidade contratual.

**Backend**

- Classificar e deduplicar quadros e pontos entre plantas/diagramas; distinguir rótulos de circuitos, terminais e componentes de uma verba.
- Relacionar preservação da arquitetura e projeto elétrico, registrando provisões de religação como estimativas explícitas.

**Aceite demonstrável**

1. Três quadros físicos em várias vistas continuam sendo três unidades.
2. Dois rótulos de circuito não reduzem 16 spots físicos a dois terminais.
3. Câmeras, estações de dados e conectores duplos não se somam em unidades incompatíveis.

**Pré-requisitos:** BC-22, BC-25, BC-27

**Limites do incremento:** Não alterar automaticamente HVAC ou outras disciplinas por medir seus terminais elétricos.

**Risco a verificar:** Um diagrama funcional não comprova a existência de um painel autônomo adicional.

<a id="bc-30"></a>

### BC-30 — Medir infraestrutura elétrica física em planta

Construir os caminhos físicos de eletrodutos, perfilados e eletrocalhas, separados dos circuitos que os ocupam.

**Frontend**

- Trajetos coloridos por material/seção, conexões e complementos adotados identificados.
- Inspeção de troncos compartilhados e representações sobrepostas.

**Backend**

- Extrair ou reconstruir eixos com origem explícita e deduplicar trechos físicos e conexões.
- Separar materiais sem correspondência na lista de serviços, mantendo suas medições de apoio; registrar ligações ocultas por símbolos como estimativas.

**Aceite demonstrável**

1. Um tubo com vários circuitos é medido uma vez como infraestrutura física.
2. Tês e curvas não duplicam extensões já incluídas nos trechos adjacentes.
3. Um caminho de comunicação próprio não desaparece por ter sido medida a rede de força.

**Pré-requisitos:** BC-29, BC-15, BC-21

**Limites do incremento:** Resultado horizontal identificado; comprimento completo depende das verticais de BC-32.

**Risco a verificar:** Uma linha de cabo, spline esquemática e eixo de eletrocalha representam grandezas diferentes.

<a id="bc-31"></a>

### BC-31 — Quantificar cabos por circuito, trecho e função

Medir condutores efetivamente considerados, com seções, funções e reservas identificáveis.

**Frontend**

- Seleção de circuito e bitola mostrando trajeto, F/N/PE/retorno e composição em cada trecho.
- Separação de cabo unipolar, PP multipolar e cabeamento incluído em pacotes.

**Backend**

- Relacionar diagramas e chamadas de fiação aos trechos; calcular comprimento por quantidade de condutores e seção.
- Tratar PE adicional, reserva de ligação e eventual envelope conservador de fiação como parcelas adotadas separadas, nunca como especificação encontrada.

**Aceite demonstrável**

1. Um trecho comum é contado por circuito de cabo e uma vez na infraestrutura física, conforme suas grandezas distintas.
2. Um metro de PP não vira automaticamente três metros do item de cabo unipolar.
3. Bitolas repetidas no BID recebem papéis disjuntos ou conflito explícito; fios sem linha compatível não são recodificados em outra seção.
4. Enquanto as verticais de BC-32 não forem incorporadas, interface, memória e exportação mostram subtotal horizontal PARCIAL — verticais ainda não consideradas; não publicar esse subtotal como total do cabo.

**Pré-requisitos:** BC-30, BC-19

**Limites do incremento:** Metros horizontais e reservas separados; dimensionamento executivo, proteção e aterramento permanecem fora da função de quantificar.

**Risco a verificar:** Adotar a maior chamada de condutores do circuito inteiro pode superestimar; se utilizado, deve ficar como estimativa conservadora e ser revisável.

<a id="bc-32"></a>

### BC-32 — Completar trajetos elétricos com subidas e descidas

Concluir as metragens elétricas em três dimensões usando níveis, planos e alturas verificáveis.

**Frontend**

- Setas de subida/descida, tabela de níveis e fontes das alturas por trecho e terminal.
- Memórias com horizontal, vertical, vias/condutores e reservas separados; subtotal destacado onde faltar percurso.

**Backend**

- Usar datum comum entre pavimentos e calcular verticais por diferença de níveis, sem desenhar diagonais falsas entre plantas.
- Unir intervalos físicos compartilhados de tubo, tratar transições de plano e separar descidas PP/unipolar; propagar premissas ao Excel.

**Aceite demonstrável**

1. Alterar a altura de dois quadros recalcula as duas verticais e os cabos dependentes.
2. Duas tomadas no mesmo tubo e em alturas diferentes não geram duas contagens do trecho comum.
3. Uma entrada originada no subsolo sem percurso/níveis completos conserva o subtotal interno e uma pendência específica, sem comprimento externo zero.

**Pré-requisitos:** BC-12, BC-30, BC-31, BC-26

**Limites do incremento:** Pé-direito, altura de montagem e nível de piso são grandezas diferentes; planos não cotados precisam de adoção explícita.

**Risco a verificar:** Um DWG 2D pode permitir medir planta, mas não fornecer a altura externa que nunca foi modelada nem indicada.

<a id="bc-33"></a>

### BC-33 — Conferir interfaces entre pacotes e materiais

Explicar sobreposições e materiais sem item correspondente antes de usar as quantidades para precificação.

**Frontend**

- Mapa entre itens por ponto/verba e suas metragens analíticas, com alertas de composição sobreposta.
- Relatório de materiais encontrados sem linha compatível e proposta de correspondência revisável.

**Backend**

- Vincular componentes à composição que os remunera sem somar novamente seu alcance.
- Registrar diferenças de seção/material/especificação, decisões adotadas e informações ainda necessárias à engenharia/orçamentação.

**Aceite demonstrável**

1. Cabeamento incluído explicitamente em uma verba não é repetido como compra avulsa sem decisão registrada.
2. AçoØ3 não é lançado automaticamente comoPVCØ3 para preencher uma linha do orçamento.
3. Uma divergência 10 versus 16 mm² mantém ambas as fontes, o valor adotado e o limite da decisão.

**Pré-requisitos:** BC-32, BC-25

**Limites do incremento:** Compatibilização de quantitativos e composições; preços, encargos, BDI e bases de custos não entram implicitamente.

**Risco a verificar:** Quantidades corretas em duas unidades podem gerar cobrança duplicada se a composição de preço não for compatibilizada.

## Produto comercial

<a id="bc-34"></a>

### BC-34 — Colaborar na revisão com permissões e conflitos controlados

Permitir equipes de orçamento revisando a mesma obra com autoria e versões claras.

**Frontend**

- Convites e gestão de papéis, comentários técnicos por serviço e comparação de alterações.
- Aviso de versão conflitante ao editar uma memória ou premissa já modificada por outra pessoa.

**Backend**

- Aplicar permissões de convite, edição, revisão e entrega; registrar eventos de decisão relevantes.
- Controlar concorrência e manter decisões e comentários associados à versão correta sem expor dados de outra organização.

**Aceite demonstrável**

1. Um leitor consulta a entrega mas não altera quantidades ou convida usuários.
2. Duas edições concorrentes não sobrescrevem silenciosamente a mesma premissa.
3. Um revisor consegue identificar a autoria e o alcance de uma decisão que alterou o quantitativo.

**Pré-requisitos:** BC-06, BC-27, BC-28

**Limites do incremento:** Colaboração dentro da organização e compartilhamento autorizado; nenhuma publicação pública automática das obras.

**Risco a verificar:** Comentários não podem alterar uma aprovação técnica sem um evento de revisão correspondente.

<a id="bc-35"></a>

### BC-35 — Validar o produto em obras conferidas independentemente

Medir qualidade e esforço de revisão antes de prometer cobertura comercial ampla.

**Frontend**

- Painel interno de resultados de validação por família de serviço, método e tipo de documento.
- Comparação de divergências com ligação aos casos e às evidências analisadas.

**Backend**

- Executar um conjunto de casos públicos/sintéticos e pilotos autorizados com referência conferida por orçamentista independente.
- Registrar cobertura, omissões, duplicações, diferenças de quantidade, tempo e intervenções; testar regressões de corredor, mezanino, vãos e trajetos 3D.

**Aceite demonstrável**

1. Métricas distinguem área, comprimento, contagem e estimativa, sem apresentar um único percentual inventado de precisão.
2. Metas de aceite por módulo são definidas e registradas antes de aprovar a liberação correspondente.
3. Falhas viram casos reproduzíveis e tarefas Beads; corrigir um caso não aprova automaticamente toda a disciplina.

**Pré-requisitos:** BC-28, BC-34

**Limites do incremento:** As correções conhecidas do KFC inspiram testes; a obra sozinha não comprova generalização e seus arquivos não devem ser publicados sem autorização. Consolida generalização entre obras dos módulos liberáveis; a elétrica requer BC-33 antes de sua própria liberação. Não exige finalizar toda disciplina para avaliar uma família civil já pronta.

**Risco a verificar:** Conferir apenas a consistência interna do código não detecta um escopo arquitetônico interpretado incorretamente.

<a id="bc-36"></a>

### BC-36 — Controlar custos, cotas e operação dos processamentos

Acrescentar gestão comercial de consumo, cotas e operação sobre os limites essenciais já entregues em BC-08/17.

**Frontend**

- Prévia do alcance a processar, limites do plano e consumo da obra, com estimativas identificadas como tais.
- Histórico de execuções, retomada e mensagem específica de cota ou custo máximo atingido.

**Backend**

- Medir consumo real de modelo, CAD, processamento e armazenamento por execução/organização.
- Aplicar limites, cache por fonte/revisão, repetição idempotente e parada por orçamento configurado; registrar versões de modelos e ferramentas.

**Aceite demonstrável**

1. Um limite atingido encerra novas chamadas e preserva o resultado parcial identificado.
2. Reprocessar uma fonte inalterada reutiliza somente resultados cuja validade foi comprovada.
3. O consumo exibido pode ser reconciliado com os registros da execução e não confunde orçamento da obra com custo do serviço.

**Pré-requisitos:** BC-08, BC-17, BC-35

**Limites do incremento:** Valores de preço e planos comerciais dependem de custos observados; não prometer processamento ilimitado como consequência da arquitetura. Não adiar a proteção contra gasto até esta etapa: tentativas, tempo, orçamento e telemetria por execução já são obrigatórios em BC-08, BC-16 e BC-17.

**Risco a verificar:** Sessões desktop e arquivos extensos podem ter custos muito diferentes de um upload simples de Excel.

<a id="bc-37"></a>

### BC-37 — Vender planos e projetos com cobrança integrada

Permitir contratação com direitos de uso e limites consistentes entre interface e backend.

**Frontend**

- Página de oferta, contratação, resumo de limites e portal de gestão da assinatura ou compra por projeto.
- Estados de pagamento pendente, confirmado, cancelado e acesso conforme a contratação.

**Backend**

- Integrar um provedor de pagamentos em modo de teste, com webhooks assinados, idempotência e reconciliação de direitos de uso.
- Separar cobrança da plataforma, dados do orçamento e consumo de execução; definir cancelamento e acesso às entregas já produzidas.

**Aceite demonstrável**

1. Repetir um webhook não duplica crédito, compra ou acesso.
2. Pagamento aprovado no navegador sem confirmação válida do backend não libera recursos indevidamente.
3. Fluxos de contratação e cancelamento são demonstrados em sandbox antes da autorização para ativar cobrança real.

**Pré-requisitos:** BC-06, BC-36

**Limites do incremento:** Nesta tarefa futura, ativação de cobrança real, condições comerciais e compromissos legais terão decisão explícita do responsável.

**Risco a verificar:** Vincular acesso apenas ao retorno do checkout permite inconsistências e uso indevido.

<a id="bc-38"></a>

### BC-38 — Publicar e operar a versão comercial com critérios de liberação

Colocar em produção uma versão com cobertura declarada, recuperação testada e suporte ao cliente.

**Frontend**

- Ambiente de produção com onboarding, documentação de formatos/alcances suportados, ajuda e comunicação de falhas.
- Canal de relato técnico que referencia obra, serviço e execução sem publicação indevida de arquivos.

**Backend**

- Automatizar implantação, segredos, migrações compatíveis, backups e restauração; monitorar API, filas, renderizadores e workersCAD/VM.
- Validar retenção/exclusão, isolamento, limites, resposta a incidentes e cobertura aprovada por módulo; usar VM/containers inicialmente e avaliar orquestração conforme a necessidade observada.

**Aceite demonstrável**

1. Um backup é restaurado em ambiente separado e recupera obras, fontes, decisões e entregas com referências válidas.
2. Uma execução com falha pode ser identificada, interrompida e retomada conforme a política, sem duplicar a entrega.
3. A liberação registra revisão técnica, testes de isolamento/cobrança/operação e formatos suportados; módulos reprovados permanecem indisponíveis.

**Pré-requisitos:** BC-07, BC-35, BC-36, BC-37

**Limites do incremento:** Produto final deste PRD: levantamento assistido e auditável; expansão para novas disciplinas, precificação e BIM tem especificações próprias.

**Risco a verificar:** A infraestrutura de produção não resolve sozinha falhas de interpretação, qualidade geométrica ou revisão técnica.
