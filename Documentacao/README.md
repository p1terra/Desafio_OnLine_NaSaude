# 📚 Documentação: Desafio da Saúde 3.0

## 1. Visão Geral do Projeto
Trata-se de uma aplicação *Single Page Application* (SPA) contida em um único arquivo HTML (com CSS e JS embutidos). O objetivo é registrar e calcular a pontuação diária, semanal e mensal de participantes de um desafio de hábitos saudáveis, armazenando os dados na nuvem em tempo real.

## 2. Tecnologias Utilizadas (Via CDN)
* **HTML5 / CSS3:** Estruturação e estilização base.
* **Tailwind CSS:** Framework utilitário para estilização rápida via classes.
* **Firebase (v10.12.2):** Backend as a Service (BaaS) da Google.
    * *Firebase Auth:* Autenticação anônima para permissão de leitura/escrita.
    * *Firestore:* Banco de dados NoSQL em tempo real.
* **Chart.js:** Biblioteca para a renderização do gráfico de evolução ("Ranking dos Meses").
* **html2canvas:** Biblioteca para converter a div de "Resumo" em uma imagem PNG baixável.
* **SheetJS (xlsx):** Biblioteca para gerar planilhas Excel (Relatórios Mensal e Geral).

---

## 3. Configurações Globais (Onde alterar a cada nova edição)
No início da tag `<script type="module">`, existe um bloco identificado como `// --- CONFIGURAÇÃO 3.0 ---`. É aqui que você fará a maior parte das manutenções anuais:

* **`participants`:** Array com os nomes dos participantes. *(Ex: Para adicionar alguém, basta incluir 'Nome' na lista).*
* **`participantColors`:** Objeto mapeando cada participante a uma cor hexadecimal (padrão Okabe-Ito, acessível para daltônicos). Usado no gráfico do Chart.js.
* **`challengeStartDate` e `challengeEndDate`:** Definem o período de vigência do desafio. Check-ins fora dessa data são bloqueados pelo script.
* **`state`:** Objeto central que guarda o estado da aplicação:
    * `selectedParticipant`: Quem está fazendo o check-in.
    * `selectedDate`: A data do check-in.
    * `allData`: Cópia local (em memória) de todo o banco de dados.
    * `appId`: Identificador do banco de dados (atualmente `'desafio-saude-v3'`).

---

## 4. Estrutura de Interface (Telas e Modais)
O HTML é dividido em contêineres principais, que são mostrados ou ocultados alternando a classe `.hidden` do Tailwind:

1. **`#loading-screen`:** Tela inicial de carregamento enquanto conecta ao Firebase.
2. **`#setup-screen`:** Tela de login/setup. Onde o usuário escolhe o participante e a data. Também contém os botões para ver regras, relatórios e rankings.
3. **`#main-app`:** O formulário principal de check-in diário e o mini-ranking lateral (Ranking Atual Acumulado).
4. **Modais (Telas sobrepostas):**
    * `#ranking-modal`: Gráfico de linhas com evolução dos meses.
    * `#summary-modal`: Cartão verde exibido logo após salvar o check-in, com opção de baixar imagem.
    * `#thank-you-screen`: Tela de agradecimento final contendo citações motivacionais e a Tabela de Pontos 2.0.
    * `#score-table-modal`: Modal acionado no setup para apenas visualizar as regras.
    * `#monthly-ranking-modal`: Ranking específico do mês atual.

---

## 5. Lógica de Pontuação e Regras de Negócio
As regras de cálculo estão centralizadas nas "Helper Functions". Se a regra do jogo mudar, é aqui que você deve alterar:

* **`getAerobicScore(minutes)`:** Define os pontos semanais baseados nos minutos (Ex: 150+ min = 175 pts).
* **`getStrengthScore(daysCount)`:** Avalia quantos dias de força foram feitos na semana (Ex: 2+ dias = 105 pts).
* **`getFruitScore(portions)`:** Pontua as porções diárias de frutas/vegetais (Ex: 5+ = 20 pts).
* **`calculateMonthlyScore(...)`:** **(A função mais complexa)**. Ela varre o banco de dados filtrando pelos dias do mês em questão. Ela soma os pontos *diários* (frutas, água, sono, alimentação limpa) e calcula os pontos *semanais* iterando sobre as semanas que *começam* dentro daquele mês específico.
* **`calculateAllScores(...)`:** Faz um loop pelos 12 meses do ano chamando a função acima para compilar o total geral, o mês atual e o histórico mensal para o gráfico.

---

## 6. Banco de Dados (Firebase)
A estrutura de dados salva no Firestore segue este caminho e formato:
**Path:** `artifacts/desafio-saude-v3/public/data/healthChallenge/{NOME_DO_PARTICIPANTE}`

**Modelo do Documento:**
```json
{
  "dailyLogs": {
    "2026-01-12": {
      "inputs": {
        "aerobicMinutes": 45,
        "strengthSessionBoolean": true,
        "fruitPortions": 3,
        "hydration": true,
        "cleanEating": { "noSweets": true, "noSoda": true, "noFastFood": true, "noFried": false },
        "sleep": true,
        "selfCareActivities": ["Meditação ou Mindfulness"]
      }
    }
  }
}