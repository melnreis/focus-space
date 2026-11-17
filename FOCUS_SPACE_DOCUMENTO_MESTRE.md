# Focus Space — Documento Mestre do Produto
Versão documentada: MVP 1.2 | 16/11/2026
Site: https://melnreis.github.io/focus-space/
Repositório: https://github.com/melnreis/focus-space

## 1. Visão do produto
**Nome:** Focus Space. **Slogan:** Menos ruído. Mais progresso.
Aplicativo web responsivo de produtividade pessoal, estudos, trabalho, projetos e hábitos. Combina organização simples de tarefas (referência: Todoist), sessões de foco e evolução visual (referência: Forest) e registro de constância (referência: StudyRats), sem copiar suas marcas ou interfaces.

**Problema:** fragmentação entre listas de tarefas, cronômetros e acompanhamento de hábitos; excesso de configuração.
**Proposta:** planejar → focar → concluir → registrar evidência opcional → receber recompensa → acompanhar progresso.
**Público inicial:** estudantes e usuários que buscam organização pessoal; piloto com três adolescentes, com participação voluntária e feedback sem exposição pública de dados.
**Princípios:** simplicidade, privacidade, progressão não punitiva, poucos cliques, mobile-first, acessibilidade.

## 2. Estado atual e escopo comprovado no código local
Fonte técnica inspecionada: `focus_space_v1_2.html` (arquivo do MVP produzido na conversa). A captura enviada mostra o site publicado com o painel de Foco. Não foram executados testes completos de ponta a ponta nesta documentação.

### 2.1 Navegação e dashboard
- Quatro abas: **Meu dia**, **Foco**, **Hábitos**, **Progresso**.
- Saudação conforme horário, data, slogan e sequência de atividade.
- Indicadores: tarefas concluídas hoje, minutos de foco concluídos hoje, check-ins de hábitos e sequência de dias ativos.

### 2.2 Tarefas
- Criar tarefa com nome de até 140 caracteres.
- Categorias: Faculdade, Trabalho, Projetos, Pessoal.
- Prioridades: Normal e Alta.
- Marcar/desmarcar conclusão; excluir tarefa.
- Tarefas vinculadas à data de criação; exibição diária.
- Vincular uma tarefa pendente a uma sessão de foco, ou escolher foco livre.
- Foto opcional após marcar conclusão; visualizar, substituir e remover foto.

### 2.3 Fotos
- Arquivos selecionados pelo usuário; compactação antes de armazenamento.
- Fotos armazenadas no **IndexedDB**, em banco `focus-space-photos-v1`, store `photos`, associadas ao ID da tarefa.
- Indicador da existência da foto no registro da tarefa.
- **Limitação:** fotos não integram o backup JSON; não há sincronização entre aparelhos.

### 2.4 Pomodoro / foco
- Presets: 25, 45, 60 minutos; pausa de 5 minutos.
- Duração personalizada: de 1 a 180 minutos, números inteiros.
- Iniciar, pausar, continuar e reiniciar.
- Cronômetro usa horário-limite (`Date.now`) enquanto a página está aberta; fechar a aba interrompe sessão ativa.
- Sessão de foco concluída é registrada com data, duração e eventual ID de tarefa; pausa de 5 minutos não concede pontos de foco.
- Conclusão de foco não marca automaticamente a tarefa vinculada como concluída.

### 2.5 Hábitos
- Criar hábitos com nome até 100 caracteres.
- Check-in e desmarcação por data; excluir hábito.
- Progresso percentual diário.

### 2.6 Gamificação
- Escolha entre árvore, gato e raposa; seleção persistida.
- Pontos calculados dinamicamente: tarefa concluída +10; cada check-in de hábito +5; sessão de foco concluída +2 por bloco de 5 minutos, arredondado, mínimo 1 bloco.
- Nível = piso(pontos/100)+1; barra de progresso até próximo nível.
- Estágio visual avança a cada 75 pontos, limitado a cinco estágios representados por emojis.
- Floresta mostra até 12 árvores, com base no total de sessões concluídas.
- **Atenção:** ao excluir tarefa/hábito ou desmarcar conclusão, os pontos recalculados podem diminuir. Não há economia persistente de moedas, acessórios ou cuidados com pet.

### 2.7 Progresso e backup
- Visualização de atividade dos últimos sete dias.
- Totais de minutos de foco, sessões concluídas e tarefas concluídas.
- Exportação e importação de backup JSON, substituindo dados atuais mediante confirmação.
- **Bug conhecido a corrigir:** a importação de backup filtra sessões com duração superior a 60 minutos, apesar de o timer aceitar até 180 minutos. Ajustar validação para `<=180`.

### 2.8 Persistência e arquitetura
- HTML + CSS + JavaScript puro, arquivo único, sem backend.
- Dados principais no `localStorage`, chave `focus-space-v1`.
- Fotos no IndexedDB.
- Hospedagem estática no GitHub Pages.
- Sem login, sem nuvem, sem compartilhamento de tarefas, sem banco remoto.
- Dados são isolados por navegador, dispositivo e origem do site; limpar armazenamento pode apagá-los.
- Não prometer funcionamento offline garantido sem service worker/PWA testado; arquivo HTML local pode ser aberto sem rede.

## 3. Modelo de dados atual (conceitual)
```json
{
  "tasks": [{"id":"id", "name":"Estudar", "category":"Faculdade", "priority":"alta", "date":"2026-11-16", "done":false, "hasPhoto":false}],
  "habits": [{"id":"id", "name":"Ler", "checks":["2026-11-16"]}],
  "focus": [{"id":"id", "date":"2026-11-16", "minutes":25, "taskId":null}],
  "activity": {"2026-11-16":true},
  "pet": {"type":"tree"},
  "settings": {"minutes":25}
}
```
Fotos são blobs separados no IndexedDB e não estão representadas nesse JSON.

## 4. Identidade visual
- Dark minimalista, fundo quase preto, painéis em cinza-esverdeado, texto claro, acentos verde-menta.
- Interface responsiva, cartões de estatísticas, navegação em abas, botões discretos e hierarquia tipográfica clara.
- Slogan visível no cabeçalho: **Menos ruído. Mais progresso.**
- Companheiro representado por emojis no MVP; ilustrações próprias e animações são possibilidades futuras.

## 5. O que NÃO faz parte do MVP atual
Autenticação, perfis multiusuário, sincronização, rankings, feed social, notificações push, IA, recompensas resgatáveis, aplicativos nativos, compartilhamento de fotos, chat, monitoramento real de bloqueio de aplicativos, analytics centralizados, backups automáticos em nuvem.

## 6. Plano de teste piloto (três adolescentes)
Duração sugerida: 7 dias, sem exigir tarefas diárias. Explicar que o sistema guarda informações no navegador, que as fotos são opcionais e que não devem conter rostos, endereços, escolas ou dados pessoais de terceiros. Solicitar autorização apropriada dos responsáveis quando necessário; não coletar fotos ou dados dos adolescentes para análise sem necessidade.

**Roteiro de teste:**
1. Abrir site no celular e no computador, se disponível.
2. Criar duas tarefas, marcar uma e adicionar foto opcional sem informação sensível.
3. Criar um hábito e fazer check-in.
4. Configurar foco de 1 minuto, iniciar, pausar, continuar e concluir.
5. Escolher companheiro e conferir pontos.
6. Recarregar página e confirmar persistência.
7. Abrir Progresso e exportar backup.
8. Reportar dispositivo/navegador, etapa, comportamento esperado e observado, sem enviar dados privados.

**Perguntas de feedback:** Foi fácil começar? Qual função foi mais útil? Algo confundiu? O companheiro motivou? O que removeria? O que acrescentaria? Usaria por mais uma semana? (opcional).

**Critérios de aceitação:** interface abre sem erro, tarefas/hábitos persistem após recarregar, sessão concluída aparece nas métricas, pet evolui, fotos opcionais podem ser abertas, backup exporta e importa registros válidos. Testar importação de sessões >60 min após correção.

## 7. Backlog de evolução (propostas, não implementadas)
**P0 — corrigir:** importação de sessões de 61–180 min; verificar bugs no celular; confirmar fotos após recarregar; revisar acessibilidade e estados de erro.
**P1 — melhorar:** editar tarefas e hábitos; histórico de tarefas; alertas de perda de dados; exportação opcional de fotos; melhor experiência de sessão em segundo plano; lembretes locais opt-in.
**P2 — gamificação:** nomear pet, acessórios, ambiente evolutivo, metas semanais, animações suaves, recompensas sem punição.
**P3 — escala:** contas e sincronização via backend, regras de privacidade, consentimento e exclusão de dados, somente se testes justificarem.

## 8. Publicação e versionamento
Repositório: `https://github.com/melnreis/focus-space`.
Página: `https://melnreis.github.io/focus-space/`.
Para publicar, colocar o conteúdo do MVP em **`index.html`** na raiz da branch `main`, configurar Settings → Pages → Deploy from a branch → main → /(root). Evitar `index.html.html`. Fazer commit e aguardar publicação. Recomendação: manter tags de versões, README e CHANGELOG.

## 9. Prompt mestre de criação e continuidade
```text
Atue como Product Manager, UX/UI Designer e Engenheiro de Software Full-Stack sênior. Trabalhe no produto Focus Space, slogan "Menos ruído. Mais progresso".

OBJETIVO
Desenvolver e evoluir um aplicativo web simples e responsivo de produtividade pessoal para estudos, trabalho, projetos e hábitos. Referências conceituais: organização de tarefas do Todoist, foco gamificado do Forest e constância/check-ins do StudyRats. Não copiar identidade visual, logotipos ou código proprietário.

ESTADO ATUAL
MVP 1.2 publicado em https://melnreis.github.io/focus-space/ e código no repositório https://github.com/melnreis/focus-space. Implementação atual: arquivo HTML único com CSS/JavaScript; quatro abas Meu dia, Foco, Hábitos, Progresso; tarefas por categoria (Faculdade, Trabalho, Projetos, Pessoal), prioridade Normal/Alta e checklist; foto opcional de conclusão armazenada em IndexedDB; cronômetro com presets 25/45/60 e pausa 5, duração personalizada 1–180 minutos, pausar/continuar/reiniciar e vínculo opcional a tarefa; hábitos com check-in diário; árvore/gato/raposa com evolução por pontos; métricas diárias, últimos 7 dias, exportação/importação JSON; dados principais em localStorage.

REGRAS DE RECOMPENSA
+10 por tarefa concluída; +5 por check-in de hábito; +2 por cada bloco de 5 minutos de foco concluído (arredondado; mínimo 1 bloco); 100 pontos por nível; estágio visual a cada 75 pontos até 5 estágios. Pontos são recalculados a partir dos registros. Pausas não pontuam. Não punir faltas ou descanso.

RESTRIÇÕES
Preservar visual dark minimalista com verde-menta e slogan. Não introduzir funcionalidades complexas sem justificativa. Manter uso simples em celular e desktop. Sem dependência de instalação local, serviços pagos ou backend para o MVP. Não alegar sincronização, autenticação, armazenamento em nuvem ou offline garantido. Fotos são opcionais e privadas ao dispositivo; não fazem parte do backup JSON atual.

BUG CONHECIDO
A importação JSON atual descarta sessões com mais de 60 minutos, embora o cronômetro permita até 180. Corrigir para <=180 e criar teste de regressão.

PÚBLICO DE TESTE
Piloto voluntário com três adolescentes. Minimizar coleta de dados, evitar compartilhamento de imagens pessoais, observar privacidade e consentimentos pertinentes.

MODO DE EXECUÇÃO
1. Antes de alterar, inspecione o código real e identifique estrutura e comportamento.
2. Apresente uma lista curta de problemas reais e melhorias priorizadas.
3. Implemente mudanças pequenas e verificáveis sem perder dados existentes.
4. Preserve chaves de armazenamento ou forneça migração segura.
5. Teste tarefas, fotos, foco, pausa, pontos, hábitos, backup, responsividade e recarregamento.
6. Entregue os arquivos completos prontos para publicar no GitHub Pages, incluindo index.html, README e CHANGELOG quando adequado.
7. Explique como publicar, testar e reverter em linguagem acessível para uma pessoa iniciante que desenvolve com apoio de IA.
8. Separe claramente funcionalidade implementada, testada, pendente e proposta.

PRIMEIRA ENTREGA
Auditar o MVP 1.2, corrigir importação de sessões acima de 60 minutos, validar fluxos no navegador e produzir uma versão 1.2.1 sem mudanças desnecessárias de design.
```

## 10. Definição de pronto
Uma versão só é considerada pronta quando: arquivos são gerados; sintaxe validada; fluxos principais testados no navegador; dados persistem após atualização; regressões conhecidas corrigidas ou explicitamente documentadas; instruções de atualização e reversão fornecidas.
