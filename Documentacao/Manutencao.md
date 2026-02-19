7. Guia Rápido de Manutenção
Como adicionar um novo participante?

Adicione o nome no array participants.

Adicione uma cor para ele no objeto participantColors.

Como alterar a pontuação de hidratação?

Vá até o submit listener (linha dailyInputs.hydration ? 15 : 0 dentro do showSummary e também na função calculateMonthlyScore). Altere o 15 para o novo valor.

Altere o texto no HTML para refletir o novo valor na interface do usuário (ex: <label for="hydration"...>Bebi 2 Litros... (Novo Valor pts)</label>).

Como reiniciar o desafio para o próximo ano?

Altere challengeStartDate e challengeEndDate para as datas do novo ano.

Mude o texto "Edição 2026" no HTML.

No objeto state, altere o appId para 'desafio-saude-v4' (por exemplo). Isso criará uma nova pasta no Firebase, mantendo os dados antigos intactos e zerando o jogo para a nova edição.

Na função calculateAllScores, altere o ano na chamada: calculateMonthlyScore(participantName, m, 2027).