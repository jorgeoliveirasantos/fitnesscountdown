# Documentação do Cronômetro Fitness

## Visão Geral

O **Cronômetro Fitness** é um aplicativo web progressivo (PWA) desenvolvido para auxiliar na prática de exercícios físicos com controle de tempo intervalado. Ele combina um cronômetro configurável com sistema de ciclos, armazenamento de perfis personalizados, estatísticas de treino e recursos de acessibilidade como bloqueio de tela ativo durante o uso. Seu design responsivo e foco na experiência do usuário o tornam adequado tanto para uso em dispositivos móveis quanto em computadores.

## Funcionalidades Principais

### 1. Cronômetro de Ciclos
- Contagem regressiva configurável em segundos.
- Número de ciclos ajustável (mínimo 1).
- Barra de progresso visual para cada ciclo.
- Exibição do tempo restante total e ciclo atual.
- Alerta sonoro e visual ao final de cada ciclo.
- Efeito pulsante nos últimos 10 segundos.

### 2. Perfis Personalizados
- Salvamento de configurações (nome, ciclos, tempo).
- Lista de perfis armazenada localmente.
- Carregamento rápido com um clique.
- Exclusão de perfis individuais.
- Persistência entre sessões via localStorage.

### 3. Estatísticas de Treino
- Registro automático ao completar todos os ciclos.
- Dias consecutivos de treino.
- Maior sequência histórica.
- Total de dias treinados.
- Estimativa de meses em atividade.
- Metas progressivas (4, 8 e 12 meses).
- Exportação de dados em JSON.
- Opção de zerar histórico.

### 4. Recursos de Interface
- Atalhos de teclado (Enter: reiniciar e iniciar, Esc: abrir/fechar configurações).
- Bloqueio de tela ativo durante o treino (Wake Lock API).
- Animações suaves e feedback visual.
- Design responsivo e acessível.
- Tema escuro com gradientes.
- Barra de rolagem personalizada.

## Guia de Uso

### Iniciando um Treino
1. Abra o aplicativo no navegador.
2. Verifique as configurações padrão (4 ciclos de 90 segundos).
3. Clique em **Iniciar** ou pressione **Enter**.
4. O cronômetro começará a contagem regressiva.
5. Ao final de cada ciclo, um alerta sonoro será emitido.
6. Ao final de todos os ciclos, o treino é registrado nas estatísticas.

### Configurando Ciclos
1. Clique no botão **Configurar** ou pressione **Esc**.
2. No painel, ajuste o número de **Ciclos** e o **Tempo por ciclo**.
3. Clique em **Aplicar** para salvar as alterações.
4. O cronômetro será reiniciado com os novos valores.

### Gerenciando Perfis
1. No painel de configurações, digite um nome no campo **Nome do perfil**.
2. Ajuste os valores de ciclos e tempo desejados.
3. Clique em **Salvar perfil**.
4. O perfil aparecerá na lista abaixo.
5. Para usar um perfil salvo, clique em **Carregar**.
6. Para remover, clique no ícone de **lixeira**.

### Acompanhando Estatísticas
- As estatísticas são exibidas no painel de configurações.
- São atualizadas automaticamente ao concluir um treino.
- Os números mostram:
  - Dias consecutivos
  - Maior sequência já alcançada
  - Total de dias treinados
  - Meses estimados de prática
- As metas mostram quantos dias faltam para cada objetivo.
- Para exportar, clique em **Exportar resumo** (arquivo JSON).
- Para reiniciar a contagem, clique em **Zerar resumo**.

## Requisitos Técnicos

### Navegadores Suportados
- Google Chrome (versão 74+)
- Microsoft Edge (versão 74+)
- Safari (versão 15+)
- Firefox (versão 76+ - com suporte parcial a Wake Lock)

### Tecnologias Utilizadas
- HTML5
- CSS3 (Flexbox, Grid, Animações)
- JavaScript (ES6+)
- localStorage para persistência
- Wake Lock API
- Áudio em Base64

### Permissões Necessárias
- Nenhuma permissão especial é exigida.
- O recurso de Wake Lock pode solicitar ativação em alguns navegadores.

## Detalhamento Técnico

### Estrutura de Arquivos
- **HTML único**: todo o código está contido em um único arquivo.
- **CSS embutido**: estilos organizados por seções.
- **JavaScript embutido**: lógica completa dentro da IIFE.

### Principais Variáveis Globais
```javascript
let TOTAL_CYCLES = 4;        // Número de ciclos
let CYCLE_TIME = 90;         // Tempo por ciclo (segundos)
let timeLeft = CYCLE_TIME;   // Tempo restante no ciclo atual
let currentCycle = 1;        // Ciclo em andamento
let totalTimeLeft = TOTAL_CYCLES * CYCLE_TIME; // Tempo total restante
let isRunning = false;       // Estado do cronômetro
let timerInterval = null;    // Referência do setInterval
let wakeLock = null;         // Referência do Wake Lock
```

### Fluxo de Funcionamento
1. O usuário inicia o timer.
2. A cada segundo, `timeLeft` e `totalTimeLeft` são decrementados.
3. A interface é atualizada via `updateDisplay()`.
4. Quando `timeLeft` chega a zero:
   - O áudio é tocado.
   - Aguarda 3 segundos.
   - Chama `nextCycle()`.
5. Em `nextCycle()`:
   - Se não for o último ciclo, avança para o próximo.
   - Se for o último, registra o treino e libera o Wake Lock.

### Armazenamento Local
**Chaves utilizadas:**
- `treinoData`: armazena objeto com:
  - `lastTrainingDate` (última data de treino)
  - `consecutiveDays` (dias consecutivos)
  - `bestConsecutiveDays` (maior sequência)
  - `totalTrainedDays` (total de dias)
  - `startDate` (data inicial)
- `trainingProfiles`: array de objetos com:
  - `name` (nome do perfil)
  - `cycles` (número de ciclos)
  - `time` (tempo por ciclo)

### Wake Lock
- Ativado quando o cronômetro está rodando.
- Liberado ao pausar ou finalizar.
- Reativado se a página voltar ao foco com o timer ativo.
- Tratamento de fallback para navegadores sem suporte.

## Personalização

### Alterando o Som
O som é codificado em Base64 na variável `audio64`. Para substituí-lo:
1. Converta um arquivo MP3 para Base64.
2. Substitua o conteúdo da variável.
3. Mantenha o mesmo tipo MIME: `data:audio/mpeg;base64,...`

### Modificando Cores
As cores principais podem ser alteradas no CSS:
- Gradiente do título: `linear-gradient(to right, #2dd4aa, #111dff)`
- Botão iniciar: `linear-gradient(to right, #4cc9f0, #4361ee)`
- Botão reiniciar: `linear-gradient(to right, #f72585, #b5179e)`
- Botão configurar: `linear-gradient(to right, #25f7a7, #1746b5)`
- Fundo: `linear-gradient(135deg, #1a1a2e 0%, #16213e 100%)`

### Ajustando Metas
As metas estão fixadas em 120, 240 e 360 dias. Para alterá-las:
1. Localize a linha que define `goals = [120, 240, 360]` (na função de resumo).
2. Altere os valores conforme desejado.
3. Atualize também os IDs `g4`, `g8`, `g12` se necessário.

## Solução de Problemas

### O som não funciona
- Verifique se o áudio está carregado (o Base64 pode estar corrompido).
- O navegador pode bloquear áudio sem interação do usuário. O app já trata isso com um clique inicial.
- Teste em outro navegador.

### Wake Lock não ativa
- Confirme se o navegador suporta a API (Chrome/Edge/Safari recentes).
- Verifique se o timer está realmente rodando.
- Alguns dispositivos podem exigir ativação manual em configurações.

### Perfis não salvam
- O localStorage pode estar cheio ou desabilitado.
- Verifique se não está em modo anônimo/privado (alguns navegadores limitam).
- Tente limpar o cache.

### Estatísticas não atualizam
- O registro só ocorre ao completar **todos os ciclos**.
- Verifique se a data do dispositivo está correta.
- O resumo é atualizado na abertura do modal e ao final do treino.

## Considerações Finais

O Cronômetro Fitness foi projetado para ser uma ferramenta confiável e de fácil uso, sem depender de conexão com internet após o carregamento inicial. Seu código aberto e bem estruturado permite que desenvolvedores com conhecimento básico possam adaptá-lo para necessidades específicas, como integração com sensores ou exportação para outras plataformas.

Para contribuir ou relatar problemas, entre em contato através dos canais oficiais ou abra uma issue no repositório do projeto (se disponível). Sua participação ajuda a tornar o aplicativo ainda melhor para toda a comunidade fitness.

**Aproveite seus treinos e evolua com consistência!** 💪
